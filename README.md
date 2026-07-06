# serpapi-search-skill [![MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Search the web from any AI agent — 100+ engines via one MCP tool.

## Quick Start

1. Get your key: [serpapi.com/dashboard](https://serpapi.com/dashboard)
2. Add to your MCP config (Claude Desktop, Cursor, Windsurf, Codex — same shape):

```json
{
  "mcpServers": {
    "serpapi": {
      "type": "http",
      "url": "https://mcp.serpapi.com/YOUR_SERPAPI_API_KEY/mcp"
    }
  }
}
```

**Claude Code CLI:**
```bash
claude mcp add --transport http serpapi https://mcp.serpapi.com/YOUR_SERPAPI_API_KEY/mcp
```

## Verify

Ask your agent: **"What search tools do you have?"** — expect `search`.

## Self-Hosting

```bash
git clone https://github.com/serpapi/serpapi-mcp.git && cd serpapi-mcp
uv sync && uv run src/server.py
```
Then point config to `http://localhost:8000/YOUR_SERPAPI_API_KEY/mcp`.

## Why MCP

| Method | Agent discovery rate |
|--------|---------------------|
| MCP tool registration | ~100% |
| Skill file on disk | 0% |

## CI/CD

Set `SERPAPI_KEY` as a repo secret. Never commit keys.

## See Also

- [`skills/serpapi-web-search/SKILL.md`](skills/serpapi-web-search/SKILL.md) — engine selection, examples, parameters
- [`serpapi-mcp`](https://github.com/serpapi/serpapi-mcp) — MCP server (hosted at mcp.serpapi.com)
- [`serpapi-cli`](https://github.com/serpapi/serpapi-cli) — terminal usage
- [serpapi.com/docs](https://serpapi.com/docs) — full API reference

## License

MIT
