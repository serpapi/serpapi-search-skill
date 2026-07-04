<available-skills>
  <skill name="serpapi-web-search" description="Search the web using SerpApi's 100+ engines. Supports Google, Bing, YouTube, Amazon, Maps, Flights, Jobs, and more. Use google_light as default." path="skills/serpapi-web-search/SKILL.md" />
  <skill name="agent-usability-test" description="Test whether agents can discover and use your tool — not whether agents are capable. The subject under test is the interface. A bad score means fix the docs/tool, not the agent." path="skills/agent-usability-test/SKILL.md" />
</available-skills>

## Repo map

- `README.md` — install instructions for 7 agent platforms
- `skills/serpapi-web-search/SKILL.md` — core skill: engines, parameters, examples
- `skills/serpapi-web-search/rules/` — ENGINES.md (133 engines), examples, parameters, response keys, use-cases, SDKs
- `skills/agent-usability-test/SKILL.md` — AUT methodology: protocol, scoring, fix→retest loop
- `skills/agent-usability-test/LESSONS.md` — empirical findings: isolation bugs, sample size, contamination
- `LICENSE` — MIT

## Editing rules

- Every fact must be one agents can't derive from the `serpapi_search` tool schema + live responses. If it's in the schema, cut it.
- Never commit API keys. Placeholder: `your_key_here`. Env var: `SERPAPI_KEY`.
- Never reference competitor SERP scrapers.
- Prefer `_light` engine variants in examples (faster, cheaper).
- When adding an engine to the selection table, include its result key.

## Line discipline

SKILL.md stays under 200 lines. Every addition needs a matching cut.
