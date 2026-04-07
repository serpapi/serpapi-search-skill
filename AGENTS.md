# SerpApi Search Skill

Universal web search across 100+ search engines and result types.

<available-skills>
  <skill name="serpapi-web-search" description="Search the web, news, images, shopping, videos, maps, flights, hotels, jobs, and more using SerpApi's 100+ search engines. Supports Google, Bing, DuckDuckGo, Yahoo, YouTube, Amazon, and dozens more. Use google_light as default for fast results." path="skills/serpapi-web-search/SKILL.md" />
</available-skills>

## Overview

Documentation-only skill package for AI coding agents. No executable code — the deliverable is Markdown files that agents consume. Powered by [SerpApi](https://serpapi.com) REST API via `serpapi_search` MCP tool, the [serpapi CLI](https://github.com/serpapi/serpapi-cli) (preferred), official SDKs, or `curl`.

## Structure

```
./
├── AGENTS.md                                    # This file — skill discovery + project knowledge
├── README.md                                    # GitHub landing page, installation instructions
├── LICENSE                                      # MIT
├── docs/
│   └── api-key-setup.md                         # SERPAPI_KEY config per agent + CI/CD
└── skills/serpapi-web-search/
    ├── SKILL.md                                 # Core skill definition (frontmatter + usage)
    ├── serpapi.yaml                             # Network policy preset
    └── rules/
        ├── ENGINES.md                           # Full catalog of 107 search engines
        ├── examples.md                          # CLI examples for common search types
        ├── parameters.md                        # All query parameters with examples
        ├── response.md                          # Response format and result key reference
        └── sdks.md                              # SDK quickstart: Python, JS, Go, Ruby, PHP, Java, .NET
```

Hidden (not tracked in git):
- `.opencode/` — OpenCode plugin packaging (node_modules, duplicate SKILL.md)
- `.sisyphus/` — Build/QA evidence and plans

## Where to Look

| Task | Location | Notes |
|------|----------|-------|
| Add SDK quickstart | `skills/serpapi-web-search/rules/sdks.md` | Python, JS, Go, Ruby, PHP, Java, .NET |
| Edit skill behavior/examples | `skills/serpapi-web-search/SKILL.md` | Canonical agent-facing artifact |
| Add/update search engines | `skills/serpapi-web-search/rules/ENGINES.md` | 107 engines in categorized tables |
| Change API key instructions | `docs/api-key-setup.md` | Per-agent setup (Claude Code, Cursor, etc.) |
| Update install instructions | `README.md` | 7 agent platforms + universal curl |
| Skill discovery metadata | `AGENTS.md` (this file) | `<available-skills>` XML block |

## Conventions

- **Default engine**: `google_light` — always recommend Light endpoints first for speed/cost.
- **API key placeholder**: Use `your_key_here` consistently (never hardcode real keys).
- **Env var name**: `SERPAPI_KEY` — standardized across all docs and examples.
- **Invocation order**: MCP tool (`serpapi_search`) → serpapi-cli → SDK (if writing code) → curl (last resort).
- **serpapi-cli agent tips**: Use `--fields` for server-side filtering (reduces API payload), `--jq` for client-side filtering. Combine both for minimum token usage.
- **curl examples**: All use `${SERPAPI_KEY}` variable reference.
- **SKILL.md frontmatter**: YAML block with `name`, `description`, `license` fields.
- **No executable code**: This repo is pure Markdown. No scripts, no tests, no build step.

## Anti-Patterns

- **Never** commit real API keys or hex strings that look like keys.
- **Never** reference competitor products (Brave, etc.) in any file.
- **Never** add executable code — this is a docs-only skill package.
- **Light vs Full**: Always default to Light engines. Only suggest full engine when user needs knowledge graph, local pack, or featured snippets.

## Commands

No build/test/lint commands. Pure documentation repo.

```bash
# Verify no keys leaked
grep -rE "[a-f0-9]{32,}" --include="*.md" .

# Validate internal links exist
grep -ohE '\(([^)]+\.md[^)]*)\)' *.md docs/*.md skills/**/*.md | tr -d '()' | sort -u
```

## Notes

- **macOS gotcha**: `grep -P` (Perl regex) unavailable on Darwin — use `grep -E` or `sed` instead.
- **Link paths in AGENTS.md**: Must be relative to repo root (not to the file's parent). Previously had a broken `references/ENGINES.md` link — fixed to `skills/serpapi-web-search/rules/ENGINES.md`.
- **OpenCode packaging**: `.opencode/` contains a bundled copy of the skill + `@opencode-ai/plugin` dependency. This is separate from the canonical `skills/` directory.
- **Git history**: 5 atomic commits on `master`. No branches, no CI pipeline.
- **Submissions**: Skill is publishable to agentskills.guide, SkillMD.ai, skills.rest, and ClawHub. See `.sisyphus/evidence/task-11-submissions.md` for submission details.
