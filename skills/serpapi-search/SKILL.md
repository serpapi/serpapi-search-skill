---
name: serpapi-search
description: >-
  Search the web using SerpApi's 100+ search engines. Use this skill whenever
  the user needs current or web-sourced information: researching a topic,
  checking recent news, comparing products or prices, finding local businesses,
  searching images or videos, or looking up academic papers, flights, hotels,
  or stocks — even if they don't explicitly ask to "search the web." Default
  to google_light for speed. Supports Google, Bing, DuckDuckGo, YouTube,
  Amazon, Maps, Scholar, and more.
compatibility: >-
  Requires either a native serpapi_search tool, or outbound network access
  plus a SERPAPI_KEY environment variable for curl/CLI fallback.
license: MIT
---

## Tool Detection

If you have a `serpapi_search` tool available, use it directly. Pass `mode="compact"` to strip heavy metadata fields for cleaner, token-efficient responses:

```
serpapi_search(params={"engine": "google_light", "q": "query"}, mode="compact")
```

Otherwise, use curl:

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
- **More examples** (news, shopping, time-filtered, Bing): [rules/examples.md](rules/examples.md)
