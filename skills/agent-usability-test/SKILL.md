---
name: agent-usability-test
description: >-
  Test whether agents can discover and use your tool — not whether agents are
  capable. The subject under test is the interface. A bad score means fix the
  docs/tool, not the agent.
license: MIT
version: "1.0"
---

Give agent a goal. Make tool available. Don't mention the tool. Observe.
Run WITHOUT baseline. Delta = **lift** — the only metric that matters.
Lift is inversely proportional to model capability: docs matter most for weak models; strong models self-correct from API responses.

## Failure modes

| # | Failure | Signal |
|---|---------|--------|
| 1 | Non-discovery | Tool never called despite being available and relevant |
| 2 | Wrong selection | Agent picks suboptimal tool when multiple are available |
| 3 | Parameter cargo-culting | Agent copies doc examples instead of adapting |
| 4 | Response-schema blindness | Correct call, wrong field extracted |
| 5 | Auth/error cliff | 401/429/timeout → agent gives up instead of recovering |

## Discovery by integration level

```
MCP tool registered    ~100%    (in agent's tool list)
System prompt hint     ~50-80%  (estimate)
CLI on $PATH           ~30-50%  (N=4)
File on disk           0%       (N=12, 4 models)
```

Test at your deployment level. File-on-disk test for MCP-deployed tool = false negative.

## Protocol

1. **Hypothesize.** State expected outcome before running. Fisher's exact for N<20.
2. **Design tasks** with verifiable answers (binary: correct/incorrect). Don't encode methodology in the prompt.
3. **Run matrix.** ≥2 models × 2 conditions (WITH/WITHOUT). Uncoached prompt: *"Answer this: [GOAL]. Cite your source."*
4. **Competition variant (FM#2):** Give ALL competing tools simultaneously. Score which gets picked, not just whether yours works alone. Three conditions: YOURS-ONLY, ALL-TOOLS, NONE.
5. **Observe via trace** — not self-report. Metrics: discovery rate, selection rate, efficiency (calls to correct answer), recovery rate, lift.
6. **Score binary per fact.** No 0-100 rubrics.
7. **Fix → Retest.** Fix docs, not agent. Run old docs as control in same session.
8. **For valid WITHOUT baseline:** physically remove the skill/tool — task-tool agents inherit parent context and contaminate the WITHOUT condition.

## Adversarial conditions (tests your error messages, not agent intelligence)

- **429 rate limit:** retry with backoff or give up?
- **Network timeout:** fall back or fail silently?
- **Malformed response:** handle unexpected JSON shape?
- **Deprecated endpoint:** find current one from error message?

Score: binary (recovered / didn't).

## Don't

- Coach the agent ("use this tool") — tests reading, not behavior
- Ask agents to self-report friction
- Test one model only
- Skip the WITHOUT baseline
- Use 0-100 rubric scores
- Claim significance at N<10

## Sample size

N=1-3/cell → directional only · N=10/cell → Fisher's exact, large effects · N=12/cell → 80% power, moderate effects

## Not this

| Approach | Tests | Subject |
|----------|-------|---------|
| WebBench/WebArena | Can agent complete web tasks? | Agent capability |
| API-Bank/ToolBench | Can agent follow API specs? | Agent tool-use skill |
| UXAgent/UXCascade | Is web UI usable for humans? | Human-facing interface |
| Search API benchmarks | Which API gives better results? | API output quality |
| **AUT** | **Can agents discover and use it?** | **Agent-facing interface** |
