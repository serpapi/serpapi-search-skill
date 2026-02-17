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

Use the [serpapi-mcp](https://github.com/serpapi/serpapi-mcp) server for Model Context Protocol integration.

### Claude Code

## Shell Environment

For local development, add the key to your shell profile (`.zshrc` or `.bashrc`):

```
export SERPAPI_API_KEY=your_key_here
```

Reload your profile with `source ~/.zshrc` or `source ~/.bashrc`.

## CI/CD Configuration

### GitHub Actions

Store your key as a secret in your repository settings.

**Usage Example**
Add this to your workflow file:

```
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
