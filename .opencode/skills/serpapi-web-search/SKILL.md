---
name: serpapi-web-search
description: >-
  Search the web using SerpApi's 100+ search engines. Use this skill whenever
  the user needs current or web-sourced information: researching a topic,
  checking recent news, comparing products or prices, finding local businesses,
  searching images or videos, or looking up academic papers, flights, hotels,
  or stocks — even if they don't explicitly ask to "search the web." Default
  to google_light for speed. Supports Google, Bing, DuckDuckGo, YouTube,
  Amazon, Maps, Scholar, and more.
compatibility: >-
  Requires one of: a native serpapi_search tool; or the serpapi CLI (brew tap
  serpapi/homebrew-tap && brew install serpapi); or an SDK; or outbound network
  access with curl. All paths require a SERPAPI_KEY.
license: MIT
---

## Invocation

Use the first available method:

**1. MCP tool** — use `serpapi_search` if available:
```
serpapi_search(params={"engine": "google_light", "q": "query"}, mode="compact")
```

**2. serpapi-cli** — preferred shell fallback; optimized for AI agents:
```bash
serpapi search engine=google_light q="your query"

# --fields: server-side filtering — reduces response size at the API level
serpapi search --fields "organic_results[0:3]" engine=google_light q="your query"

# --jq: client-side filtering (like gh --jq)
serpapi search --jq ".organic_results[0:3]|[.[]|{title,link}]" engine=google_light q="your query"
```
Install: `brew tap serpapi/homebrew-tap && brew install serpapi`  
Auth: `SERPAPI_KEY` env var, `--api-key` flag, or `serpapi login`.  
Exit codes: `0` success · `1` API error · `2` usage error. Errors are JSON on stderr.

**3. SDK** — when writing code: see [rules/sdks.md](rules/sdks.md) — Python, JS, Go, Ruby, PHP, Java, .NET.

**4. curl** — universal fallback:
```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=your query" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

## Engine Selection

Pick the engine that matches the user's intent:

| Use Case | Engine |
|:---|:---|
| General web (default) | `google_light` |
| Comprehensive (knowledge graph, local pack) | `google` |
| News | `google_news_light` |
| Images | `google_images_light` |
| Shopping / prices | `google_shopping_light` |
| Alternative web | `bing` |
| Privacy-first | `duckduckgo` |
| Academic / research | `google_scholar` |
| Local / maps | `google_maps` |
| Video | `youtube` |
| SerpApi's own index (alpha, no Google/Bing) | `search_index` |

Prefer `_light` variants — they're faster and cheaper. Use the full engine only when you need knowledge graph, local pack, or featured snippets.

For engines not listed above (flights, hotels, jobs, finance, patents, etc.), read [rules/ENGINES.md](rules/ENGINES.md).

## Error Reference

- **401** — Invalid or missing API key.
- **429** — Monthly quota reached. Check [serpapi.com/dashboard](https://serpapi.com/dashboard).
- **400** — Missing required parameter (`q` or `engine`).

## Rules

Read these files when you need more detail:

- **Parameters** (locale, time filter, pagination, safe search): [rules/parameters.md](rules/parameters.md)
- **Response format** (result keys, JSON shape, pagination): [rules/response.md](rules/response.md)
- **Examples** (news, shopping, time-filtered, Bing): [rules/examples.md](rules/examples.md)
- **SDKs** (Python, JS, Go, Ruby, PHP, Java, .NET): [rules/sdks.md](rules/sdks.md)
- **All 100+ engines** (flights, hotels, jobs, finance, patents…): [rules/ENGINES.md](rules/ENGINES.md)
