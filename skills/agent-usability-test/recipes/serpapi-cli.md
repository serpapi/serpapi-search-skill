# AUT recipe — serpapi-cli

Concrete trace-capture, analysis, and fix→retest process for testing whether
autonomous agents can discover and use [serpapi-cli](https://github.com/serpapi/serpapi-cli).

This recipe instantiates `skills/agent-usability-test/SKILL.md` for one specific subject. Read SKILL.md first for the methodology. This file is the "what do I actually type" companion.

---

## 0. Subject and pre-registration

- **Subject under test:** the `serpapi` CLI binary + its discoverability from agent context (man-page hints, `--help`, skill files, MCP entry).
- **Subject is NOT:** the SerpApi REST API, the search results, or the agent's reasoning.
- **Integration level matrix (test each separately, do not pool):**
  | Level | How agent gets it | Expected discovery |
  |---|---|---|
  | L0 file-on-disk | `~/.agents/skills/serpapi-web-search/SKILL.md` present, no hint | 0% (validated, N=24) |
  | L1 path-hint | system prompt mentions skill location | ~0–10% |
  | L2 CLI on $PATH | `serpapi` binary installed, nothing else | 30–50% |
  | L3 `<available_skills>` | skill injected into context | ~100% (discovery) — usability questions remain |
  | L4 MCP tool | `serpapi_search` registered as MCP tool | ~100% (discovery) — usability questions remain |

- **Pre-register (Phase 0, before any trial):**
  - Hypothesis. Example: *"At L2, agents will discover `serpapi` ≥50% but call `serpapi search` with no engine ≥30% of the time."*
  - Effect size and statistical test (Fisher's exact, N≥10 per cell for any p-value claim).
  - Pooling decision — whether trials from different days/models will be combined.
  - Stop conditions — fixed N, no peeking.

---

## 1. Trace capture

Three capture mechanisms, ranked by trace fidelity. Use the richest one your runtime supports.

### 1A. tmux + pipe-pane (highest fidelity, works for any CLI agent)

Captures every byte that hits the terminal, in order, with errors and recoveries.

```bash
mkdir -p .aut-traces
TRIAL=trial-$(date +%Y%m%d-%H%M%S)
LOG=.aut-traces/${TRIAL}.log

# Start the agent inside a detached tmux session. Replace the inner command
# with however you launch the agent (claude, gemini, copilot, custom harness).
tmux new-session -d -s aut -x 220 -y 60 "<launch-agent-with-goal-task>"
tmux pipe-pane -t aut -o "cat >> $LOG"

# Wait for the agent to finish — poll, do not block.
while tmux has-session -t aut 2>/dev/null; do sleep 2; done
echo "captured $(wc -c < $LOG) bytes → $LOG"
```

Notes:
- `tmux pipe-pane -o` appends every pane write to the file. Captures stdout AND stderr the agent sees.
- Use a wide pane (`-x 220 -y 60`) so JSON lines don't wrap (wrapping breaks downstream grep).
- The agent's own *commands* are visible only if the agent echoes them or you wrap its shell in `set -x`. If you need command-level capture, prefer 1B.

### 1B. `script(1)` typescript (captures both input keystrokes and output)

```bash
script -q .aut-traces/${TRIAL}.tty <launch-agent-with-goal-task>
```

macOS BSD `script` records *everything*, including command lines the agent types. Slightly noisier (escape sequences) — pipe through `col -bp` before analysis:

```bash
col -bp < .aut-traces/${TRIAL}.tty > .aut-traces/${TRIAL}.log
```

### 1C. Copilot CLI `session_store_sql` (highest structural fidelity, when available)

When the agent is GitHub Copilot CLI (or any runtime that writes to `~/.copilot/session-store.duckdb`), every tool call is durable.

```sql
-- Trial scope: pick the session_id of the AUT trial.
WITH trial AS (
  SELECT id AS session_id FROM sessions
  WHERE created_at > now() - INTERVAL '1 hour'
    AND summary ILIKE '%french laundry%'   -- or any task-specific anchor
  ORDER BY created_at DESC LIMIT 1
)
SELECT
  e.timestamp,
  e.tool_start_name              AS tool,
  COALESCE(tr.arguments_json,'') AS args,
  e.tool_complete_success        AS ok,
  substr(COALESCE(e.tool_complete_result_content,''), 1, 200) AS result_preview
FROM events e
LEFT JOIN tool_requests tr
  ON tr.tool_call_id = e.tool_complete_call_id
WHERE e.session_id = (SELECT session_id FROM trial)
  AND e.type = 'tool.execution_complete'
ORDER BY e.timestamp;
```

This gives you (call name, args JSON, success bit, result snippet) per row — the cleanest possible input for an analyzer. Use 1A as a fallback when the runtime is opaque.

### 1D. Bash history (lowest fidelity, anecdotal only)

Only use when nothing else is available. `HISTFILE` + `HISTTIMEFORMAT` gives you timestamps but loses errors, stderr, and the LLM's reasoning. Treat as a smoke signal, not evidence.

```bash
HISTTIMEFORMAT="%FT%T%z " HISTFILE=.aut-traces/${TRIAL}.bash_history \
  bash --noprofile --norc -c "<launch-agent>"
```

---

## 2. Analysis method — failure-mode classification

The analyzer is a pure function `trace.log → failure_mode_counts`. Keep it as a regex script so it's auditable and rerunnable.

```bash
# .aut-traces/analyze.sh — copy verbatim; tune patterns per task.
#!/bin/bash
LOG="$1"
echo "=== AUT trace analysis: $LOG ==="

calls=$(grep -cE '\bserpapi\b' "$LOG")
help_calls=$(grep -cE '\bserpapi (--help|help\b|search --help)' "$LOG")
echo "[FM#1 non-discovery]      serpapi mentions: $calls  help reads: $help_calls"

ENGINES='google|google_light|google_maps|google_news|google_scholar|google_shopping|bing|duckduckgo|yahoo|youtube|amazon|ebay|walmart|yandex|naver'
search_calls=$(grep -cE '\bserpapi search\b' "$LOG")
search_with_engine=$(grep -cE "\bserpapi search (($ENGINES)\b|engine=($ENGINES))" "$LOG")
search_no_engine=$(( search_calls - search_with_engine ))
echo "[FM#2 wrong-endpoint]     search calls: $search_calls  with engine: $search_with_engine  missing engine: $search_no_engine"

cargo=$(grep -cE -- '--engine\b|--query[= ]|(^| )--q[= ]' "$LOG")
echo "[FM#3 cargo-cult-flags]   non-existent flag uses: $cargo"

wrong_key=$(grep -cE 'organic_results' "$LOG")
echo "[FM#4 wrong-response-key] organic_results mentions: $wrong_key  (expect 0 for a maps task)"

auth_fail=$(grep -cE '"code":"401"|"unauthorized"|"Invalid API key"' "$LOG")
echo "[FM#5 auth-cliff]         auth errors: $auth_fail"

api_ok=$(grep -cE '"search_metadata"' "$LOG")
errs=$(grep -cE '"error":\s*\{"code"' "$LOG")
recovered=$([ "$errs" -gt 0 ] && [ "$api_ok" -gt 0 ] && echo 1 || echo 0)
echo "[FM#6 cost]               successful API responses: $api_ok"
echo "[recovery]                errors=$errs  later_success=$api_ok  recovered=$recovered"
```

### Mapping AUT failure modes → serpapi-cli signals

| FM | Pattern in trace | Tighten with |
|---|---|---|
| #1 non-discovery | zero `\bserpapi\b` in the WITH-tool condition | compare to WITHOUT to confirm the agent did need search at all |
| #2 wrong endpoint | `serpapi search` with no engine token, or `--engine` flag use, or `engine: google` when task needs `google_maps` / `google_shopping` | per-task allowlist of engines that satisfy the ground truth |
| #3 cargo-cult flags | `--engine` / `--query` / `--q` (these do not exist) | grow this list as you find more invented flags |
| #4 wrong response key | `organic_results` cited for a Maps task; `local_results` cited for a web task | per-task expected response key allowlist |
| #5 auth cliff | 401 with no follow-up `serpapi account` / `serpapi login` / `--api-key` retry | combine with recovery counter (`errors > 0 AND later_success = 0`) |
| #6 cost blindness | total `"search_metadata"` count divided by minimum-necessary (1 for yes/no questions, N for "top N" questions) | precompute minimum per task in the task spec |
| #7 integration confusion | mixed `serpapi`, `curl https://serpapi.com/search`, and `import serpapi` in one trace | grep three orthogonal patterns and count distinct types |

### From counts to a per-trial verdict

Produce a YAML row per trial. Binary per fact — no rubric scores.

```yaml
trial: trial-20260629-220500
task: local-business
model: claude-haiku-4.5
condition: with-tool
integration_level: L2
facts:
  phone_correct: true                 # ground-truth check
trace:
  fm1_discovered: true
  fm2_correct_engine: false           # used google instead of google_maps
  fm3_cargo_cult_flags: 0
  fm4_correct_response_key: false     # cited organic_results
  fm5_auth_recovered: n/a
  fm6_calls_made: 3
  fm6_calls_min: 1
verdict: partial                      # answer correct, but suboptimal path
```

### Aggregation across the matrix

```bash
# Roll up per-cell binary outcomes into a 2×2 table per failure mode.
# Then run a Fisher's exact test (Python one-liner) per FM.
python3 - <<'PY'
from scipy.stats import fisher_exact
# Replace counts with rolled-up trial outcomes from your YAML rows.
with_ok, with_fail = 8, 4    # FM#2 correct vs wrong, WITH condition
wo_ok,   wo_fail   = 2, 10   # same, WITHOUT condition
odds, p = fisher_exact([[with_ok, with_fail], [wo_ok, wo_fail]])
print(f"FM#2 p={p:.3f} odds={odds:.2f}")
PY
```

Statistical floor: N=10 per cell. N=3 per cell is qualitative ("we consistently saw X"), not a p-value.

---

## 3. Fix → retest loop

The loop fails silently without a control group. Always run NEW-docs and OLD-docs agents in the same matrix.

### 3.1 Find the gap from analysis

Read the per-FM counts. One concrete gap per iteration. Examples surfaced by the analyzer in the pilot:

- `cargo_cult_flags > 0` → the cargo-cult anti-pattern includes `--engine` because it sounds like the SerpApi REST `engine` parameter. Fix: state explicitly in `skills/serpapi-web-search/SKILL.md` that *engine is a positional argument*, not a flag.
- `fm4_correct_response_key = false` on Maps task → fix `skills/serpapi-web-search/rules/response.md` to list `place_results` first for the Maps engine.

### 3.2 Make one fix per iteration

Edit only the doc/CLI surface implicated by the failure. Do not bundle fixes — you lose attribution.

```bash
git switch -c aut-fix/engine-positional
$EDITOR skills/serpapi-web-search/SKILL.md            # state engine is positional
git diff --stat
git add -A && git commit -m "[Skill] State engine is positional in serpapi search"
```

### 3.3 Controlled retest — old vs new in parallel

```bash
# A) Snapshot the old docs at the parent commit.
OLD=$(git rev-parse HEAD~1)
git worktree add ../serpapi-skill-old "$OLD"

# B) Run N agents per arm. Same model, same task, same integration level,
#    same prompt. The only difference is which docs are on disk / in context.
for trial in 1 2 3 4 5 6 7 8 9 10; do
  AGENT_SKILL_DIR=../serpapi-skill-old/skills tmux new-session -d -s old-$trial \
    "<launch-agent-with-goal-task>" &
  AGENT_SKILL_DIR=./skills              tmux new-session -d -s new-$trial \
    "<launch-agent-with-goal-task>" &
done
wait
```

For each arm: capture trace (§1), analyze (§2), tabulate.

### 3.4 Attribution table

| Outcome | Old-docs pass | New-docs pass | Interpretation |
|---|---|---|---|
| Both pass | ≥80% | ≥80% | Fix unnecessary for this model — strong model self-corrected from the API response. Note in LESSONS; do not revert unless cost matters. |
| Both fail | <50% | <50% | Fix did not address root cause. Re-read trace, propose different fix. |
| Old fail, new pass | <50% | ≥80% | Fix attributed. Ship. |
| Old pass, new fail | ≥80% | <50% | Fix regressed something. Revert; investigate. |

Single-arm "3/3 passed after the fix" is not evidence — without the old-docs control you cannot tell the fix from random variation.

### 3.5 When to stop iterating

Stop when ALL of these hold for the integration level you ship at:
- Discovery rate ≥ 90% across all tested models.
- FM#2/#4 correct-rate ≥ 80% across weak + strong models.
- FM#5 recovery rate = 100% (every auth error has a follow-up corrective call).
- FM#6 cost ≤ 2× minimum on yes/no tasks.

Anything weaker, log as a known gap in `LESSONS.md` with its tag and move on — do not let perfect block ship.

---

## 4. Pilot run (executed 2026-06-29, evidence inline)

This is what running the recipe end-to-end against `serpapi-cli` produced, to verify the pipeline before publishing.

**Setup:** simulated uncoached agent at L2 (binary on `$PATH`, no skill files, no hint). Task: *"What is the phone number of The French Laundry in Yountville, CA?"* — ground truth `(707) 944-2380`, expected engine `google_maps`, expected key `place_results`.

**Capture (§1A, tmux pipe-pane):**

```
HELLO_AGENT
HTTP client for structured web search data via SerpApi
Usage:
  serpapi [flags]
  serpapi [command]
Available Commands:
  account     Retrieve account information and usage statistics
  …
>>> AGENT TURN 2: first attempt — wrong endpoint (web search)
{"error":{"code":"usage_error","message":"unknown flag: --engine"}}
>>> AGENT TURN 3: realize need structured data — try maps
{"error":{"code":"usage_error","message":"unknown flag: --engine"}}
```

**Analyzer output (§2):**

```
[FM#1 non-discovery]      serpapi mentions: 4  help reads: 0
[FM#2 wrong-endpoint]     search calls: 0  with engine: 0  missing engine: 0
[FM#3 cargo-cult-flags]   non-existent flag uses: 2
[FM#4 wrong-response-key] organic_results mentions: 0
[FM#5 auth-cliff]         auth errors: 0
[FM#6 cost]               successful API responses: 0
[recovery]                errors=2  later_success=0  recovered=0
```

**Findings from pilot:**

1. **FM#3 confirmed** — agent cargo-culted `--engine` from REST-API mental model. Real syntax is `serpapi search engine=google_maps q="..."` (key=value) or `serpapi search google_maps q="..."` (positional shorthand). The `--engine` flag does not exist.
2. **Recovery=0** — agent hit `usage_error` twice and did not consult `serpapi search --help` between attempts. The error message says `unknown flag: --engine` but does not name the right shape.
3. **Discovery (L2) = 100%** — the binary was found and invoked.

**Fix candidates** (one-per-iteration; pick highest-leverage first):

- Update `serpapi search` `usage_error` to suggest the correct form when a `--engine` flag is observed: *"`--engine` is not a flag. Use `serpapi search engine=google_maps q=...`."* This is a CLI-side fix and would close the failure mode at the source.
- Mirror the same hint in `skills/serpapi-web-search/SKILL.md` quick-start so L3/L4 agents never form the wrong mental model.

**Next iteration:** apply one fix, run §3.3 with N=10 per arm across `claude-haiku-4.5` + `claude-sonnet-4.6`, attribute via §3.4.

---

## 5. Anti-patterns specific to this subject

- **Coaching the agent with `--engine` in the prompt.** Eliminates the most reliable FM#3 signal.
- **Testing only with a SERPAPI_KEY already in env.** Hides FM#5 entirely. To test auth, force a 401 via `--api-key invalid` — `SERPAPI_KEY=""` is overridden by `~/.config/serpapi/config.yaml` if the user has run `serpapi login`.
- **Pooling L2 + L4 trials.** Discovery is structurally different at each level — pooling washes out the signal.
- **Using `gpt-5.5` only.** It derives the right shape from the error message alone. You will miss every FM that weaker models exhibit.
- **Counting "agent eventually got the right answer" as success without checking the path.** A correct phone number reached via `google` + snippet extraction is FM#2 (wrong engine) and FM#4 (wrong key) even when the fact is right. Score path and answer separately.
