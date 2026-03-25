# API Key Setup

This guide covers how to get and set up your SerpApi API key for AI coding agents.

## Getting Your API Key

1.  Sign up for an account at [serpapi.com](https://serpapi.com).
2.  Go to the [Dashboard](https://serpapi.com/dashboard) to find your key.
3.  Copy the API key for use in the configurations below.

## Security Guidance

*   **Environment Variables**: Always store your key in an environment variable named `SERPAPI_API_KEY`.
*   **Never Commit Keys**: Do not commit your API key to any version control system.
*   **Rotate Keys**: If you suspect exposure, rotate your key immediately in the SerpApi dashboard.

## Shell Environment

For local development, add the key to your shell profile (`.zshrc` or `.bashrc`):

```bash
export SERPAPI_API_KEY=your_key_here
```

Reload your profile:

```bash
source ~/.zshrc   # or source ~/.bashrc
```

## Per-Agent Configuration

Use the [serpapi-mcp](https://github.com/serpapi/serpapi-mcp) server for Model Context Protocol integration.

### Claude Code

**Option A — CLI (recommended):**

```bash
claude mcp add serpapi -- npx -y @serpapi/serpapi-mcp
```

Then set your API key in the environment (see Shell Environment above) or pass it via `SERPAPI_API_KEY=your_key_here claude mcp add ...`.

**Option B — Manual config:**

Add to `~/.claude/settings.json` (global) or `.claude/settings.json` (project-scoped):

```json
{
  "mcpServers": {
    "serpapi": {
      "command": "npx",
      "args": ["-y", "@serpapi/serpapi-mcp"],
      "env": {
        "SERPAPI_API_KEY": "your_key_here"
      }
    }
  }
}
```

### Cursor

Add to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "serpapi": {
      "command": "npx",
      "args": ["-y", "@serpapi/serpapi-mcp"],
      "env": {
        "SERPAPI_API_KEY": "your_key_here"
      }
    }
  }
}
```

### Windsurf

Add to `.windsurf/mcp.json`:

```json
{
  "mcpServers": {
    "serpapi": {
      "command": "npx",
      "args": ["-y", "@serpapi/serpapi-mcp"],
      "env": {
        "SERPAPI_API_KEY": "your_key_here"
      }
    }
  }
}
```

### OpenCode

Add to `.opencode/config.json` or rely on the shell environment variable above.

### Codex / Other Agents

Set the environment variable before launching the agent:

```bash
export SERPAPI_API_KEY=your_key_here
```

## CI/CD Configuration

### GitHub Actions

Store your key as a [repository secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets) named `SERPAPI_API_KEY`, then reference it in your workflow:

```yaml
jobs:
  search:
    runs-on: ubuntu-latest
    steps:
      - name: Run agent with SerpApi
        env:
          SERPAPI_API_KEY: ${{ secrets.SERPAPI_API_KEY }}
        run: |
          echo "Agent will pick up SERPAPI_API_KEY from the environment"
```

### GitLab CI

Store the key as a [CI/CD variable](https://docs.gitlab.com/ee/ci/variables/) in your project settings:

```yaml
search-job:
  script:
    - echo "Agent will pick up SERPAPI_API_KEY from CI environment"
  variables:
    SERPAPI_API_KEY: $SERPAPI_API_KEY
```

### Generic / Docker

Pass the key at runtime:

```bash
docker run -e SERPAPI_API_KEY=your_key_here your-agent-image
```

