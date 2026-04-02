---
name: serpapi-search
description: Search the web using SerpApi's 100+ search engines including Google, Bing, DuckDuckGo, YouTube, Amazon, and more. Use google_light as default for fast results. Supports news, images, shopping, videos, maps, flights, hotels, jobs, academic, and local search.
license: MIT
---

## Tool Detection

If you have a `serpapi_search` tool available, use it directly. Always pass `mode="compact"` to strip heavy metadata fields (`search_metadata`, `search_parameters`) for cleaner, token-efficient LLM responses:

```
serpapi_search(
  params={
    "engine": "google_light",
    "q": "query"
  },
  mode="compact"
)
```

> **Note:** The block above is MCP pseudocode. Exact invocation syntax depends on your agent runtime.

The instructions below are for environments without a native tool.

## Quick Start

Get fast web results using the `google_light` engine:

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=coffee shops in Austin" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

Or with the [serpapi CLI](https://github.com/serpapi/serpapi-cli) (install: `brew install serpapi/serpapi-cli/serpapi`):

```bash
export SERPAPI_KEY=your_key_here
serpapi search engine=google_light q="coffee shops in Austin"
```

## Endpoint

`GET https://serpapi.com/search.json`

## When to Use Which Engine

Use the following table to select the best engine for your use case.

| Use Case | Recommended Engine | Description |
|:---|:---|:---|
| General Web | `google_light` | Fastest, standard organic results. Use by default. |
| Comprehensive | `google` | Full Google results including knowledge graph, local pack. |
| News | `google_news_light` | Latest news results. |
| Images | `google_images_light` | Visual search and image results. |
| Shopping | `google_shopping_light`| Product pricing and availability. |
| Global Search | `bing` | Microsoft's search engine for alternative results. |
| Privacy-first | `duckduckgo` | Privacy-oriented web search. |
| Academic | `google_scholar` | Research papers, citations, and patents. |
| Local/Maps | `google_maps` | Places, reviews, and coordinates. |
| Video | `youtube` | Video content and metadata. |

**Light vs Full:** Use Light by default. Use full engine when you need: knowledge graph, local pack, inline shopping, or featured snippets.

## Core Parameters

| Parameter | Type | Required | Description |
|:---|:---|:---|:---|
| `engine` | string | Yes | The search engine to use (e.g., `google_light`). |
| `q` | string | Yes | The search query. |
| `api_key` | string | Yes | Your SerpApi API key. |
| `num` | integer | No | Number of results to return (max 100 for most engines). Default: 10. |
| `start` | integer | No | Result offset for pagination. Use with `num` (e.g., `start=10&num=10` for page 2). |
| `gl` | string | No | Country code for locale (e.g., `us`, `uk`). Default: `us`. Use for country-level targeting. |
| `hl` | string | No | Language code (e.g., `en`, `es`). Default: `en`. |
| `location` | string | No | Canonical city/region for precise geo-targeting (e.g., `Austin, Texas`). Takes precedence over `gl`. |
| `tbs` | string | No | Time-based filter. Common values: `qdr:d` (past day), `qdr:w` (past week), `qdr:m` (past month). |
| `safe` | string | No | Safe search level: `active` or `off`. |
| `no_cache` | string | No | Pass `"true"` to bypass cached results and force a fresh crawl. |

> **Note:** The `youtube` engine uses `search_query` instead of `q` as its query parameter. All other engines use `q`.

## Light Endpoints

SerpApi provides "Light" versions of popular engines that are optimized for speed and cost.

- `google_light`
- `google_images_light`
- `google_news_light`
- `google_shopping_light`
- `google_videos_light`
- `duckduckgo_light`

Faster response, lower cost, essential data. Use by default.

## Response Format

All engines return JSON with `search_metadata`, `search_parameters`, and an engine-specific results array (see Engine Result Keys below).

```
{
  "search_metadata": { "status": "Success", "created_at": "..." },
  "search_parameters": { "engine": "google_light", "q": "..." },
  "organic_results": [
    {
      "position": 1,
      "title": "Example Result",
      "link": "https://example.com",
      "snippet": "This is a brief description of the result."
    }
  ],
  "serpapi_pagination": { "next": "..." }
}
```

**Engine Result Keys:** Different engines use different top-level array keys. Check the correct key for your engine:

| Engine Category | Result Key |
|:---|:---|
| Web (`google_light`, `google`, `bing`, `duckduckgo`) | `organic_results` |
| News (`google_news_light`, `bing_news`, `duckduckgo_news`) | `news_results` |
| Images (`google_images_light`, `google_images`) | `images_results` |
| Shopping (`google_shopping_light`, `amazon`, `walmart`) | `shopping_results` |
| Jobs (`google_jobs`) | `jobs_results` |
| Maps (`google_maps`) | `local_results` |
| Videos (`google_videos_light`) | `video_results` |
| YouTube (`youtube`) | `organic_results` |

*Use `serpapi_pagination.next` to fetch subsequent pages.*

## Examples

### Google News (Light)
Search for the latest technology news.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=artificial intelligence" \
  --data-urlencode "engine=google_news_light" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

### Bing Web Search
Alternative results for the same query.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=serpapi documentation" \
  --data-urlencode "engine=bing" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

### Google Shopping (Light)
Find product prices and availability.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=iphone 15" \
  --data-urlencode "engine=google_shopping_light" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

### Time-Filtered Search
Find results from the past week using `tbs`.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=latest AI models" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "tbs=qdr:w" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

## Locale

Set `gl` and `hl` based on the user's context. Correct locale improves result relevance, ranking order, and availability of local features like knowledge panels.

## Limits & Pricing

Free tier: 250 searches/month. Plans: [serpapi.com/pricing](https://serpapi.com/pricing)

## Error Reference

- **401 Unauthorized**: Missing or invalid API key. Check your dashboard.
- **429 Rate Limit**: Monthly limit reached. Upgrade your plan.
- **400 Bad Request**: Missing required parameters like `q` or `engine`.

## Links

- [Full Documentation](https://serpapi.com/search-api)
- [SerpApi Playground](https://serpapi.com/playground)
- [API Dashboard](https://serpapi.com/dashboard)
- [Engine Catalog](references/ENGINES.md)
