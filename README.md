# serpapi-search-skill [![MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Search the web from any AI agent — 100+ engines via one MCP tool.

## Quick Start

1. Get your key: [serpapi.com/dashboard](https://serpapi.com/dashboard)
2. Add to your MCP config (Cursor, Windsurf, Codex, Claude Desktop — same shape):

```json
{
  "mcpServers": {
    "serpapi": {
      "command": "npx",
      "args": ["-y", "@serpapi/serpapi-mcp"],
      "env": { "SERPAPI_KEY": "your_key_here" }
    }
  }
}
```

**Claude Code CLI:**
```bash
claude mcp add serpapi -- npx -y @serpapi/serpapi-mcp
```
Set the key: `claude mcp env serpapi SERPAPI_KEY your_key_here`

## Verify

Ask your agent: **"What search tools do you have?"** — expect `serpapi_search`.

## Local Execution

Replace `"args"` with `["-y", "@serpapi/serpapi-mcp", "--local"]` in the config above.

## Why MCP

| Method | Agent discovery rate |
|--------|---------------------|
| MCP tool registration | ~100% |
| Skill file on disk | 0% |

## CI/CD

Set `SERPAPI_KEY` as a repo secret. Never commit keys.

## See Also

- [`skills/serpapi-web-search/SKILL.md`](skills/serpapi-web-search/SKILL.md) — engine selection, examples, parameters
- [`@serpapi/serpapi-mcp`](https://github.com/serpapi/serpapi-mcp) — MCP server source
- [`serpapi-cli`](https://github.com/serpapi/serpapi-cli) — terminal usage
- [serpapi.com/docs](https://serpapi.com/docs) — full API reference

## License

MIT
