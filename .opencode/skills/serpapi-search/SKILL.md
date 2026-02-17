---
name: serpapi-search
description: Search the web using SerpApi's 100+ search engines including Google, Bing, DuckDuckGo, YouTube, Amazon, and more. Use google_light as default for fast results. Supports news, images, shopping, videos, maps, flights, hotels, jobs, academic, and local search.
license: MIT
---

## Tool Detection

If you have a `serpapi_search` tool available, use it directly:

```json
serpapi_search(
  params={
    "engine": "google_light",
    "q": "query"
  },
  mode="compact"
)
```

The instructions below are for environments without a native tool.

## Quick Start

Get fast web results using the `google_light` engine:

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=coffee shops in Austin" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "api_key=${SERPAPI_API_KEY}"
```

## Endpoint

`GET https://serpapi.com/search.json`

## When to Use Which Engine

Use the following table to select the best engine for your use case.

| Use Case | Recommended Engine | Description |
|:---|:---|:---|
| General Web | `google_light` | Fastest, standard organic results. Use by default. |
| Comprehensive | `google` | Full Google results including knowledge graph, local pack. |
| News | `google_news_light` | Real-time news results. |
| Images | `google_images` | Visual search and image results. |
| Shopping | `google_shopping_light`| Product pricing and availability. |
| Global Search | `bing` | Microsoft's search engine for alternative results. |
| Privacy-first | `duckduckgo` | Privacy-oriented web search. |
| Academic | `google_scholar` | Research papers, citations, and patents. |
| Local/Maps | `google_maps` | Places, reviews, and coordinates. |
| Video | `youtube_search` | Video content and metadata. |

**Light vs Full:** Use Light by default. Use full engine when you need: knowledge graph, local pack, inline shopping, or featured snippets.

## Core Parameters

| Parameter | Type | Required | Description |
|:---|:---|:---|:---|
| `engine` | string | Yes | The search engine to use (e.g., `google_light`). |
| `q` | string | Yes | The search query. |
| `api_key` | string | Yes | Your SerpApi API key. |
| `num` | integer | No | Number of results to return. |
| `start` | integer | No | Result offset for pagination. |
| `gl` | string | No | Country code (e.g., `us`, `uk`). Default: `us`. |
| `hl` | string | No | Language code (e.g., `en`, `es`). Default: `en`. |
| `location` | string | No | Canonical location (e.g., `Austin, Texas`). |
| `no_cache` | boolean | No | Set to `true` to force a fresh crawl. |

## Light Endpoints

SerpApi provides "Light" versions of popular engines that are optimized for speed and cost.

- `google_light`
- `google_news_light`
- `google_shopping_light`
- `google_reverse_image_light`
- `google_product_light`
- `google_play_product_light`

Faster response, lower cost, essential data. Use by default.

## Response Format

All engines return a similar JSON structure centered around `organic_results`.

```json
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

*Other engines follow a similar structure. See engine-specific docs at serpapi.com.*

## Examples

### Google News (Light)
Search for the latest technology news.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=artificial intelligence" \
  --data-urlencode "engine=google_news_light" \
  --data-urlencode "api_key=${SERPAPI_API_KEY}"
```

### Bing Web Search
Alternative results for the same query.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=serpapi documentation" \
  --data-urlencode "engine=bing" \
  --data-urlencode "api_key=${SERPAPI_API_KEY}"
```

### Google Shopping (Light)
Find product prices and availability.

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=iphone 15" \
  --data-urlencode "engine=google_shopping_light" \
  --data-urlencode "api_key=${SERPAPI_API_KEY}"
```

## Mode Parameter

For tool users: Recommend `mode: "compact"`. This strips heavy metadata fields like `search_metadata` and `search_parameters`, providing a cleaner response for LLMs.

## Locale

Set `gl` (country) and `hl` (language) based on the user's context. Default is `gl=us`, `hl=en`. Using correct locale improves result relevance for local queries.

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
