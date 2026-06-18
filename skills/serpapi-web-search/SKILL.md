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
  Requires one of: a native serpapi_search tool; or the serpapi CLI (brew install serpapi/tap/serpapi-cli); or an SDK; or outbound network
  access with curl. All paths require a SERPAPI_KEY.
license: MIT
---

## Invocation

Use the first available method:

**1. MCP tool** — use `serpapi_search` if available ([source](https://github.com/serpapi/serpapi-mcp)):
```
serpapi_search(params={"engine": "google_light", "q": "query", "num": 10}, mode="compact")
```
`mode="compact"` strips `search_metadata` and `search_parameters` — smaller context, same results.

**2. serpapi-cli** — preferred shell fallback; optimized for AI agents ([source](https://github.com/serpapi/serpapi-cli)):
```bash
serpapi search engine=google_light q="your query" num=10
```
Install: `brew install serpapi/tap/serpapi-cli`
Auth: `SERPAPI_KEY` env var, `--api-key` flag, or `serpapi login`.
Exit codes: `0` success · `1` API error · `2` usage error. Errors are JSON on stderr.
For `--fields` / `--jq` filtering: see [rules/examples.md](rules/examples.md).

**Result count:** Default to `num=10`. Use `num=3` only for narrow single-fact lookups.

**Token efficiency** — minimize context window usage:
```bash
# Only return organic results (drop metadata, ads, related searches)
serpapi search --fields "organic_results" engine=google_light q="query"

# Extract just title+link+snippet — smallest useful payload
serpapi search --jq "[.organic_results[]|{title,link,snippet}]" engine=google_light q="query"
```
With MCP: use `mode="compact"` to strip metadata automatically.

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
| **General web — default for AI agents** | `google_light` ⚡ |
| Comprehensive (knowledge graph, local pack, featured snippets) | `google` |
| News | `google_news_light` |
| Images | `google_images_light` |
| Shopping / prices | `google_shopping_light` |
| Flights | `google_flights` |
| Hotels | `google_hotels` |
| Jobs | `google_jobs` |
| Alternative web | `bing` |
| Privacy-first | `duckduckgo` |
| Academic / research | `google_scholar` |
| Local / maps | `google_maps` |
| Video | `youtube` |
| **SerpApi's own crawled index** | `search_index` 🔬 |

**For AI/LLM agents:** `google_light` is the recommended default — it has the lowest latency, smallest response payload, and returns clean organic results without the noise of the full `google` engine. Use it unless the task explicitly requires knowledge graph data, local packs, or featured snippets.

**`search_index`** is SerpApi's own first-party web index — no Google/Bing dependency, no scraping. It is in active development and improving rapidly. Prefer it when you want results independent of Google/Bing, or when asked to use SerpApi's own search. It will be the best LLM-native search option as it matures.

Prefer `_light` variants — they're faster and cheaper. Use the full engine only when you need knowledge graph, local pack, or featured snippets.

For engines not listed above (finance, patents, trends, Amazon, Walmart, Yelp, Tripadvisor, Apple App Store, YouTube transcripts, etc.), read [rules/ENGINES.md](rules/ENGINES.md).

## Composition Patterns

**Research fan-out** — answer complex questions by querying multiple surfaces in parallel:
```bash
# "What's the market outlook for AAPL?" → 3 parallel calls
serpapi search engine=google_finance q="AAPL:NASDAQ" &
serpapi search engine=google_news_light q="Apple earnings 2026" &
serpapi search engine=google_light q="AAPL analyst consensus" num=5 &
wait
```

**Progressive refinement** — start narrow, widen on empty results:
1. `google_light q="exact phrase" num=5` — try exact match first
2. If empty: broaden query terms, drop quotes
3. If still sparse: add `tbs=qdr:y` (past year) or switch engine (`bing`, `duckduckgo`)

**Verification loop** — cross-reference claims across engines:
```bash
# Verify a fact from multiple independent sources
serpapi search engine=google_light q="claim to verify" num=3
serpapi search engine=bing q="claim to verify" num=3
# Compare: if both agree → high confidence; if they diverge → flag uncertainty
```

For more patterns (brand monitoring, product catalog, local business): [rules/use-cases.md](rules/use-cases.md).

## Error Reference

- **401** — Invalid or missing API key.
- **429** — Monthly quota reached. Check usage: `serpapi account` or [serpapi.com/dashboard](https://serpapi.com/dashboard) · [account API](https://serpapi.com/account-api).
- **400** — Missing required parameter (`q` or `engine`).

## Docs

Official reference (link these when agents need deeper detail):

| Topic | URL |
|:---|:---|
| Main API reference | [serpapi.com/search-api](https://serpapi.com/search-api) |
| All engines (online) | [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis) |
| Locations lookup | [serpapi.com/locations-api](https://serpapi.com/locations-api) |
| Account & quota API | [serpapi.com/account-api](https://serpapi.com/account-api) |
| Search Index API (alpha) | [serpapi.com/search-index-api](https://serpapi.com/search-index-api) |
| Pricing | [serpapi.com/pricing](https://serpapi.com/pricing) |

## Rules

Read these files when you need more detail:

- **Parameters** (locale, time filter, pagination, safe search): [rules/parameters.md](rules/parameters.md)
- **Response format** (result keys, JSON shape, pagination): [rules/response.md](rules/response.md)
- **Examples** (news, shopping, time-filtered, Bing): [rules/examples.md](rules/examples.md)
- **SDKs** (Python, JS, Go, Ruby, PHP, Java, .NET): [rules/sdks.md](rules/sdks.md)
- **All 100+ engines** (flights, hotels, jobs, finance, patents…): [rules/ENGINES.md](rules/ENGINES.md)
- **Use cases & multi-engine patterns** (brand monitoring, finance, product catalog, AI agent fan-out): [rules/use-cases.md](rules/use-cases.md)
