# SerpApi Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

AI agent skills: web search across 100+ engines and agent-facing usability testing. Powered by [SerpApi](https://serpapi.com).

## Quick Start

Get an API key from [serpapi.com/dashboard](https://serpapi.com/dashboard), then connect your agent:

**Claude Code:**
```bash
claude mcp add serpapi -- npx -y @serpapi/serpapi-mcp
```

**Cursor / Windsurf / Claude Desktop:**

Add to your MCP config (`.cursor/mcp.json`, `.windsurf/mcp.json`, or Claude Desktop settings):
```json
{
  "mcpServers": {
    "serpapi": {
      "url": "https://mcp.serpapi.com/your_key_here/mcp"
    }
  }
}
```

**Verify it works** — ask your agent:
> "What search tools do you have?"
>
> Expected: the agent lists `serpapi_search` among its tools.

That's it. Your agent can now search 100+ engines. No files to copy, no CLI to install.

## Why MCP First

We tested skill discovery with uncoached agents across multiple models. Results:

| Integration method | Discovery rate | How it works |
|---|---|---|
| **MCP tool** (registered in tool list) | **~100%** | Agent sees the tool, uses it |
| **Skill file on disk** (no runtime injection) | **0%** | Agent never explores the directory unprompted |

Same API. Same docs. Different shelf. **MCP registration is the only path to reliable discovery.**

Skill files are useful as supplementary documentation (engine selection, parameter reference, composition patterns) once MCP provides the tool — but they are not a reliable discovery mechanism unless the runtime injects them into the agent's context.

## Installation

### MCP (recommended — tool appears in agent's tool list)

**Hosted** (zero install, lowest friction):
```json
{ "url": "https://mcp.serpapi.com/your_key_here/mcp" }
```

**Local** (full control, works offline):
```json
{
  "command": "npx",
  "args": ["-y", "@serpapi/serpapi-mcp"],
  "env": { "SERPAPI_KEY": "your_key_here" }
}
```

| Agent | Config file | Transport |
|-------|-------------|-----------|
| Claude Code | `claude mcp add serpapi ...` | stdio (local) or `--transport http` (hosted) |
| Claude Desktop | Settings → MCP | hosted URL |
| Cursor | `.cursor/mcp.json` | hosted URL or local |
| Windsurf | `.windsurf/mcp.json` | hosted URL or local |
| Codex | `.codex/mcp.json` | hosted URL or local |

See [api-key-setup.md](docs/api-key-setup.md) for full config examples per platform and CI/CD setup.

### Skills CLI (cross-platform file install)

```bash
npx skills add serpapi/skills
```

Installs via the [skills CLI](https://github.com/vercel-labs/skills). Supports Claude Code, Cursor, Codex, OpenCode, Windsurf, and [40+ agents](https://github.com/vercel-labs/skills#supported-agents). Note: this copies skill files to disk — [discovery depends on the agent platform](#why-mcp-first).

### Manual file install

For agents that read skill directories but don't support MCP:

```bash
# Clone once:
git clone https://github.com/serpapi/skills.git

# Then copy to your agent's skill directory:
cp -r skills/serpapi-web-search ~/.claude/skills/   # Claude Code (global)
cp -r skills/serpapi-web-search .cursor/skills/      # Cursor (project)
cp -r skills/serpapi-web-search .agents/skills/       # Codex / Copilot CLI
cp -r skills/serpapi-web-search .windsurf/skills/     # Windsurf
cp -r skills/serpapi-web-search .opencode/skills/     # OpenCode

# AUT methodology (no API key needed):
cp -r skills/agent-usability-test ~/.claude/skills/  # or any agent directory above
```

### serpapi CLI

Direct shell access without MCP:

```bash
brew install serpapi/tap/serpapi-cli
serpapi login
serpapi search engine=google_light q="coffee shops in Austin"
```

### Sandboxed runtimes (OpenClaw / NemoClaw)

<details>
<summary>Expand for sandboxed agent setup</summary>

```bash
# 1. Install serpapi-cli inside the sandbox
go install github.com/serpapi/serpapi-cli/cmd/serpapi@latest
export SERPAPI_KEY=your_key_here

# 2. Copy the skill and network policy
cp -r skills/serpapi-web-search skills/serpapi-web-search
openshell policy set skills/serpapi-web-search/serpapi.yaml

# 3. Register in ~/.openclaw/openclaw.json
# { "skills": { "entries": { "serpapi-web-search": { "enabled": true,
#   "apiKey": { "source": "env", "provider": "default", "id": "SERPAPI_KEY" } } } } }

# 4. Make permanent
nemoclaw onboard
```

</details>

### Claude Agent SDK (programmatic)

<details>
<summary>Expand for SDK integration</summary>

```python
from claude_agent_sdk import query, ClaudeAgentOptions

async for msg in query(
    prompt="Search for the latest AI news",
    options=ClaudeAgentOptions(
        allowed_tools=["serpapi_search"],
        setting_sources=["project"],
    ),
):
    handle(msg)
```

Clone this repo into your project's `skills/` directory. The SDK discovers `SKILL.md` files automatically via `settingSources`.

> **Note:** The Agent SDK is evolving — verify the API surface against the [latest docs](https://code.claude.com/docs/en/agent-sdk).

</details>

## What's Included

### serpapi-web-search

| File | Purpose |
|------|---------|
| [SKILL.md](skills/serpapi-web-search/SKILL.md) | Core skill — invocation, engine selection, composition patterns |
| [LESSONS.md](skills/serpapi-web-search/LESSONS.md) | Deep knowledge — quota recovery, geo targeting, pagination |
| [rules/ENGINES.md](skills/serpapi-web-search/rules/ENGINES.md) | All 133 search engines |
| [rules/parameters.md](skills/serpapi-web-search/rules/parameters.md) | Query parameters with examples |
| [rules/response.md](skills/serpapi-web-search/rules/response.md) | Response format and result keys |
| [rules/examples.md](skills/serpapi-web-search/rules/examples.md) | CLI examples for common searches |
| [rules/use-cases.md](skills/serpapi-web-search/rules/use-cases.md) | Multi-engine patterns and fan-out |
| [rules/sdks.md](skills/serpapi-web-search/rules/sdks.md) | SDK quickstart: Python, JS, Go, Ruby, PHP, Java, .NET |
| [api-key-setup.md](docs/api-key-setup.md) | Per-agent and CI/CD key configuration |

### agent-usability-test

Tests whether docs, APIs, tools, or skills are discoverable and usable by autonomous agents. The subject under test is the interface — a bad score means fix the docs/tool, not the agent.

| File | Purpose |
|------|---------|
| [SKILL.md](skills/agent-usability-test/SKILL.md) | AUT methodology — failure modes, protocol, scoring, fix→retest |
| [LESSONS.md](skills/agent-usability-test/LESSONS.md) | Empirical findings from real test runs |
| [recipes/serpapi-cli.md](skills/agent-usability-test/recipes/serpapi-cli.md) | Concrete trace-capture recipe for testing serpapi-cli |

**No API key or MCP server needed.** AUT is a methodology skill — it guides agents through designing and running usability tests. Works with any tool or API as the test subject.

Quick start:
```
Ask your agent: "Can AI agents use [your tool]? Design a testing plan."
With this skill available, the agent will produce an AUT-style plan
(uncoached tasks, WITH/WITHOUT baseline, binary scoring).
Without it, agents default to traditional eval/QA plans.
```

Validated: agents with AUT skill produce correct methodology; without it they default to traditional eval/QA plans.

## Available Engines

`google_light` is the default — fastest and cheapest. Use the full engine only when you need knowledge graph, local pack, or featured snippets.

| Engine | Use case |
|--------|----------|
| `google_light` | General web search (default) |
| `google_news_light` | Latest news |
| `google_images_light` | Image search |
| `google_shopping_light` | Product pricing |
| `google_scholar` | Academic papers |
| `google_maps` | Local businesses |
| `youtube` | Video search |
| `bing` / `duckduckgo` | Alternative web search |

See [rules/ENGINES.md](skills/serpapi-web-search/rules/ENGINES.md) for all 133 engines.

## Links

- [SerpApi](https://serpapi.com) · [Dashboard](https://serpapi.com/dashboard) · [Playground](https://serpapi.com/playground) · [Docs](https://serpapi.com/search-api) · [MCP Server](https://github.com/serpapi/serpapi-mcp) · [CLI](https://github.com/serpapi/serpapi-cli)

## License

MIT. See [LICENSE](LICENSE).
