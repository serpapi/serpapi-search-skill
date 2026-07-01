---
name: agent-usability-test
description: >-
  Test whether agents can discover and use your tool — not whether agents are
  capable. The subject under test is the interface. A bad score means fix the
  docs/tool, not the agent.
license: MIT
version: "0.9"
---

## Core idea

Give agent a goal. Make tool available. Don't mention the tool. Observe what happens.

Run the same task WITHOUT the tool as baseline. The delta is **lift** — the only metric that matters.

## What to test

| # | Failure | Signal |
|---|---------|--------|
| 1 | Non-discovery | Tool never called despite being available and relevant |
| 2 | Wrong selection | Agent picks suboptimal tool/endpoint when multiple are available |
| 3 | Parameter cargo-culting | Agent copies doc examples instead of adapting to task |
| 4 | Response-schema blindness | Correct call, wrong field extracted |
| 5 | Auth/error cliff | Error (401, 429, timeout) → agent gives up instead of recovering |

## How discovery depends on integration level

```
MCP tool registered    ~100%    (in agent's tool list)
System prompt hint     ~50-80%  (estimate)
CLI on $PATH           ~30-50%  (N=4)
File on disk           0%       (N=12, 4 models)
```

Test at your deployment level. File-on-disk test for an MCP-deployed tool = false negative.

## Protocol

**1. Hypothesize.** State expected outcome before running. Fisher's exact test for N<20.

**2. Design tasks** with verifiable answers — a specific fact the agent either gets right or doesn't.

```yaml
- goal: "What is the phone number of The French Laundry in Yountville, CA?"
  ground_truth: "(707) 944-2380"
  scoring: binary

- goal: "What is the starting price of MacBook Air M4 on apple.com?"
  ground_truth: "$999"
  scoring: binary
```

Don't encode the methodology in the task. "Can agents use this tool?" → good. "Test whether the interface is the problem" → bad (teaches the answer).

**3. Run matrix.** ≥2 models × 2 conditions (WITH tool, WITHOUT tool). Agent prompt:
```
You are an AI research assistant. Answer this question:
"[GOAL]"
Report your answer and cite your source.
```

**Competition variant:** When testing tool selection (FM#2), give agents ALL competing tools (e.g., Tavily + Exa + SerpApi) and score which gets picked per task. Three conditions: YOUR-TOOL-ONLY, ALL-TOOLS, NO-TOOLS.

**4. Observe via trace** — not self-report. For CLI tools, tmux side-by-side:
```
tmux new-session -s aut-with    # agent WITH tool on $PATH
tmux split-window -h            # agent WITHOUT tool, same goal
```

For Copilot CLI: `copilot -p "goal" --output-format json` gives full JSONL trace (tool calls, args, responses). For other runtimes, use whatever trace mechanism exposes tool invocations — tmux `pipe-pane`, `script(1)`, or structured logs. See `recipes/` for concrete capture examples.

**5. Score binary per fact.** Correct/incorrect. No 0-100 rubrics.

**6. Fix → Retest.** Fix the docs, not the agent. Retest with OLD docs as control — "3/3 passed after fix" means nothing without "0/3 passed on old docs" in the same run.

## Adversarial conditions

Beyond happy-path discovery, test resilience:
- **Rate limit (429):** Does the agent retry with backoff or give up?
- **Network timeout:** Does the agent fall back to alternative tool or fail silently?
- **Malformed response:** Does the agent handle unexpected JSON shape?
- **Deprecated endpoint:** Does the agent find the current one from error message?

Score: binary (recovered/didn't). These test your error messages and docs, not agent intelligence.

## Don't

- Coach the agent ("use this tool")
- Ask agents to self-report friction
- Test one model only
- Skip the WITHOUT baseline
- Use 0-100 rubric scores
- Claim significance at N=3
- Inject the answer in WITH condition (tests reading, not behavior)
- Score post-hoc without stating criteria first

## Sample size

- N=1-3/cell → directional only
- N=10/cell → Fisher's exact, detects large effects
- N=12/cell → 80% power for moderate effects

## Not this

| Approach | Tests | Subject |
|----------|-------|---------|
| WebBench/WebArena | Can agent complete web tasks? | Agent capability |
| API-Bank/ToolBench | Can agent follow API specs? | Agent tool-use skill |
| UXAgent/UXCascade | Is web UI usable for humans? | Human-facing interface |
| Search API benchmarks | Which API gives better results? | API output quality |
| **AUT** | **Can agents discover and use it?** | **Agent-facing interface** |

*Empirical findings in LESSONS.md. Load both files.*
