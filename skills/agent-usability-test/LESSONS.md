# Lessons — agent-usability-test

## Discovery

- File-on-disk skills have 0% autonomous discovery rate. MCP tool registration is the only reliable discovery mechanism.
- Agents satisfice with whatever tool is already in their tool list. They don't explore directories.
- Match your test condition to how real users install. Testing file-on-disk for an MCP-deployed product gives false negatives.
- Once discovery is ~100% (MCP), shift focus to usability: correct endpoint selection, response key extraction, error recovery.

## Test design

- Coached tests produce 100% false pass rates. Always use uncoached task prompts.
- Agent self-reported friction is confabulation. Observe trace, never ask.
- Binary per-fact scoring only. No 0-100 rubrics. Every task needs a specific verifiable answer.
- Pre-register hypotheses before running. Scoring criteria decided post-hoc = exploratory, not confirmatory.
- N=2/cell is anecdotal. N=10/cell for Fisher's exact. N=12/cell for 80% power at moderate effect.
- Don't say "be thorough" in the prompt — it confounds cost measurement.

## Baselines and controls

- "3/3 passed after fix" means nothing without "0/3 on old docs" in the same run.
- Subagent/task-tool baselines are contaminated by parent session context. Use isolated processes for WITHOUT condition.
- When injecting context for A/B tests: inject CONSTRAINTS ("X failed"), never ANSWERS ("correct value is Z"). The latter tests reading, not behavior.
- Constraints help most when a known-bad attractor exists — an approach that looks reasonable but fails. Without the constraint, agents converge on it.
- For tasks with a single derivable correct answer, context injection doesn't change outcomes.

## What transfers from human UX testing

- Realistic task framing, behavior observation, condition variation, outside-in perspective, distractor tools.
- What doesn't: think-aloud (agents generate, not think), small-N qualitative (agent runs are cheap — run many), "fresh user" (unenforceable with pretrained models).

## Model-tier effects

- Doc quality matters most for weak models. Strong models self-correct from actual API responses.
- Skill lift is inversely proportional to model capability. Mid-tier models need the skill for everything; frontier models derive methodology independently.
- The controlled fix→retest loop is the last element models derive on their own — it's the skill's unique contribution for top-tier models.

## Competition and selection

- Real agents have multiple search tools available simultaneously. Test which gets picked, not just whether yours works in isolation.
- Agents select tools based on capability-task match in descriptions. Specialized engine routing (Scholar, Maps, Flights) wins over single-endpoint simplicity when the task requires it.
- Breadth (133 engines) is invisible without docs to surface it. A simpler competitor may win on selection ease.

## Error recovery

- Error recovery tests the docs and error messages, not the agent's intelligence.
- Good error messages guide recovery without doc fixes. Bad ones require doc intervention.
- Test auth failure with `--api-key "invalid"`, not empty env var (stored credentials override it).

## Observation methods

- Trace-based metrics: discovery (tool called?), selection (correct endpoint?), efficiency (call count vs minimum), recovery (error → retry → success?), lift (WITH vs WITHOUT correctness).
- Never benchmark manually what agents will do — you're benchmarking yourself, not the interface.
- For valid isolation: physically remove the skill file during WITHOUT runs, not just omit a hint.
