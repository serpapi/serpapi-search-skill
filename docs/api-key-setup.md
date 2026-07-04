# API Key Setup

Get key at [serpapi.com/dashboard](https://serpapi.com/dashboard). Store as `SERPAPI_KEY` env var — never commit, rotate if exposed.

```bash
export SERPAPI_KEY=your_key_here
```

## MCP Config

Same JSON for Claude Code (`~/.claude/settings.json`), Cursor (`.cursor/mcp.json`), Windsurf (`.windsurf/mcp.json`):
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

Claude Code CLI: `claude mcp add serpapi -- npx -y @serpapi/serpapi-mcp`

## CI/CD

GitHub Actions ([store as secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)):
```yaml
env:
  SERPAPI_KEY: ${{ secrets.SERPAPI_KEY }}
```

GitLab CI:
```yaml
variables:
  SERPAPI_KEY: $SERPAPI_KEY
```

Docker: `docker run -e SERPAPI_KEY=your_key_here your-agent-image`
