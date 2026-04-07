# SerpApi Search Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Universal web search skill for AI coding agents with support for 100+ search engines.

Search the web, news, images, shopping, videos, maps, flights, hotels, jobs, and academic databases directly from your AI agent. Powered by [SerpApi](https://serpapi.com).

## Quick Start

1.  Get your API key from the [SerpApi Dashboard](https://serpapi.com/dashboard).
2.  Set the environment variable: `export SERPAPI_KEY=your_key_here`
3.  Install the skill:
    ```bash
    npx skills add serpapi/serpapi-search-skill
    ```
4.  Start searching! See [SKILL.md](skills/serpapi-search/SKILL.md) for usage.

## What's Included

- [SKILL.md](skills/serpapi-search/SKILL.md): Core skill definition (routing document).
- [rules/ENGINES.md](skills/serpapi-search/rules/ENGINES.md): Catalog of 100+ supported search engines.
- [rules/parameters.md](skills/serpapi-search/rules/parameters.md): All query parameters with examples.
- [rules/response.md](skills/serpapi-search/rules/response.md): Response format and result key reference.
- [rules/examples.md](skills/serpapi-search/rules/examples.md): curl examples for common search types.
- [api-key-setup.md](docs/api-key-setup.md): Detailed configuration guide for all agents.
- [AGENTS.md](AGENTS.md): Discovery file for agent integration.
- [LICENSE](LICENSE): MIT License terms.

## Installation

The easiest way to install across all your agents at once:

```bash
npx skills add serpapi/serpapi-search-skill
```

This installs via the [skills CLI](https://github.com/vercel-labs/skills) and supports Claude Code, Cursor, Codex, OpenCode, Windsurf, and [40+ more agents](https://github.com/vercel-labs/skills#supported-agents).

For agent-specific or manual installation:

### Claude Code
```bash
git clone https://github.com/serpapi/serpapi-search-skill.git
# Global install (available to all projects):
cp -r serpapi-search-skill/skills/serpapi-search ~/.claude/skills/
# Project-scoped install:
cp -r serpapi-search-skill/skills/serpapi-search .claude/skills/
```
See [api-key-setup.md](docs/api-key-setup.md#claude-code) for MCP configuration.

### Cursor
```bash
cp -r skills/serpapi-search .cursor/skills/
```
Or use the Remote Rules URL pointing to your repository's `SKILL.md`.

### Codex
```bash
cp -r skills/serpapi-search .agents/skills/
```

### Windsurf
```bash
cp -r skills/serpapi-search .windsurf/skills/
```

### OpenClaw
```bash
cp -r skills/serpapi-search ~/.openclaw/skills/
```

### NemoClaw (inside sandbox)

```bash
# 1. Install serpapi-cli inside the sandbox
go install github.com/serpapi/serpapi-cli/cmd/serpapi@latest
export SERPAPI_KEY=your_key_here

# 2. Copy the skill into the workspace
cp -r skills/serpapi-search skills/serpapi-search

# 3. Apply the network policy
openshell policy set skills/serpapi-search/serpapi.yaml

# 4. Add to ~/.openclaw/openclaw.json
# { "skills": { "entries": { "serpapi-search": { "enabled": true,
#   "apiKey": { "source": "env", "provider": "default", "id": "SERPAPI_KEY" } } } } }

# 5. Make permanent
nemoclaw onboard
```

Or paste this into any AI assistant with access to your NemoClaw workspace:

```
Fetch https://raw.githubusercontent.com/serpapi/serpapi-search-skill/main/skills/serpapi-search/SKILL.md
and save it to skills/serpapi-search/SKILL.md.

Fetch https://raw.githubusercontent.com/serpapi/serpapi-search-skill/main/skills/serpapi-search/serpapi.yaml
and save it to nemoclaw-blueprint/policies/presets/serpapi.yaml.

Add this to ~/.openclaw/openclaw.json (home directory, not workspace):
{
  "skills": {
    "entries": {
      "serpapi-search": {
        "enabled": true,
        "apiKey": { "source": "env", "provider": "default", "id": "SERPAPI_KEY" }
      }
    }
  }
}

Then run: nemoclaw onboard
```

### OpenCode
```bash
cp -r skills/serpapi-search .opencode/skills/
```
OpenCode also automatically reads skills from `.claude/skills/` and `.agents/skills/`.

### Universal (curl)
Download the skill definition directly to any directory:
```bash
curl -O https://raw.githubusercontent.com/serpapi/serpapi-search-skill/main/skills/serpapi-search/SKILL.md
```

### serpapi CLI
If you prefer a CLI over raw curl, install the [serpapi CLI](https://github.com/serpapi/serpapi-cli):
```bash
brew install serpapi/serpapi-cli/serpapi
```
Then search directly from your shell:
```bash
export SERPAPI_KEY=your_key_here
serpapi search engine=google_light q="coffee shops in Austin"
```

## API Key Setup

Configure your `SERPAPI_KEY` for secure access. Detailed instructions for environment variables, MCP settings, and CI/CD are available in [api-key-setup.md](docs/api-key-setup.md).

## Available Engines

Search across 100+ platforms including Google, Bing, DuckDuckGo, YouTube, and Amazon. Use **Light** endpoints for faster responses and lower cost:

- `google_light`: Fastest general web search (default).
- `google_images_light`: Optimized image search.
- `google_news_light`: Latest news results.
- `google_shopping_light`: Product pricing and availability.
- `google_videos_light`: Video search.
- `duckduckgo_light`: Privacy-focused web results.

See [rules/ENGINES.md](skills/serpapi-search/rules/ENGINES.md) for the full list of 107 engines.

## Links

- [SerpApi Website](https://serpapi.com)
- [API Dashboard](https://serpapi.com/dashboard)
- [Search Playground](https://serpapi.com/playground)
- [Documentation](https://serpapi.com/search-api)

## License

MIT License. See [LICENSE](LICENSE) for details.
