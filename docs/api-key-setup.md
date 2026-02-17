# API Key Setup

This guide covers how to get and set up your SerpApi API key for AI coding agents.

## Getting Your API Key

1.  Sign up for an account at [serpapi.com](https://serpapi.com).
2.  Go to the [Dashboard](https://serpapi.com/dashboard) to find your key.
3.  Copy the API key for use in the configurations below.

## Security Guidance

Security is vital. Follow these practices to keep your account safe:

*   **Environment Variables**: Always store your key in an environment variable named `SERPAPI_API_KEY`.
*   **Never Commit Keys**: Do not commit your API key to any version control system. Never include it in files like `SKILL.md` or public configuration files.
*   **Rotate Keys**: If you think your key is exposed, rotate it immediately in the SerpApi dashboard.

## Per-Agent Configuration

### Claude Code

To add SerpApi search to Claude Code, use the MCP configuration.

**MCP Configuration**
Add this to your MCP settings or run the command:

```bash
claude mcp add-json '{
  "mcpServers": {
    "serpapi": {
      "command": "npx",
      "args": ["-y", "@serpapi/serpapi-mcp"],
      "env": {
        "SERPAPI_API_KEY": "your_key_here"
      }
    }
  }
}'
```

### Cursor

Cursor allows you to add MCP servers through the UI or configuration files.

**UI Setup**
1.  Go to Settings > Features > MCP.
2.  Add a new server with the name `serpapi`.
3.  Set the type to `command`.
4.  Use this command: `export SERPAPI_API_KEY=your_key_here && npx -y @serpapi/serpapi-mcp`.

### Codex

Codex uses the `.agents/skills/` directory for skill discovery.

1.  Place the skill in `.agents/skills/serpapi-search/`.
2.  Set the environment variable in your terminal:
    ```bash
    export SERPAPI_API_KEY=your_key_here
    ```

### Windsurf

Windsurf loads skills from `.windsurf/skills/`.

1.  Install the skill to `.windsurf/skills/serpapi-search/`.
2.  Ensure `SERPAPI_API_KEY` is set in your environment:
    ```bash
    export SERPAPI_API_KEY=your_key_here
    ```

### OpenClaw

OpenClaw searches for skills in `.openclaw/skills/`.

1.  Copy the skill files to `.openclaw/skills/serpapi-search/`.
2.  Provide your key via the environment:
    ```bash
    export SERPAPI_API_KEY=your_key_here
    ```

### OpenCode

OpenCode natively supports skills and looks in `.opencode/skills/`.

1.  Install the skill to `.opencode/skills/serpapi-search-skill/`.
2.  The agent will use the `SERPAPI_API_KEY` environment variable for all requests.

## Shell Environment

For local development, add the key to your shell profile (`.zshrc` or `.bashrc`):

```bash
export SERPAPI_API_KEY=your_key_here
```

Reload your profile with `source ~/.zshrc` or `source ~/.bashrc`.

## CI/CD Configuration

### GitHub Actions

Store your key as a secret in your repository settings.

**Usage Example**
Add this to your workflow file:

```yaml
jobs:
  search:
    runs-on: ubuntu-latest
    steps:
      - name: Search with SerpApi
        env:
          SERPAPI_API_KEY: ${{ secrets.SERPAPI_API_KEY }}
        run: |
          # The agent or script will pick up the key from the environment
          echo "Running search task..."
```
