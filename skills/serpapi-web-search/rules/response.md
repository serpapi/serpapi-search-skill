# SerpApi Response Format

All engines return JSON with `search_metadata` and `search_parameters` at the top level. Result arrays and `serpapi_pagination` are absent on empty results or errors.

```json
{
  "search_metadata": { "status": "Success", "created_at": "..." },
  "search_parameters": { "engine": "google_light", "q": "..." },
  "organic_results": [
    {
      "position": 1,
      "title": "Example Result",
      "link": "https://example.com",
      "snippet": "Brief description of the result."
    }
  ],
  "serpapi_pagination": { "next": "https://serpapi.com/search.json?..." }
}
```

`mode="compact"` (native tool) strips `search_metadata` and `search_parameters`.

## Result Key by Engine

| Engine Category | Result Key |
|:---|:---|
| Web (`google_light`, `google`, `bing`, `duckduckgo`) | `organic_results` |
| News (`google_news_light`, `bing_news`, `duckduckgo_news`) | `news_results` |
| Images (`google_images_light`, `google_images`) | `images_results` |
| Shopping (`google_shopping_light`, `google_shopping`) | `shopping_results` |
| Immersive product (`google_immersive_product`) | `product_results` (all seller listings) |
| Amazon, Walmart, eBay | `organic_results` |
| Product detail (`amazon_product`, `walmart_product`, `ebay_product`) | `product`, `product_result`, `product_results` |
| Jobs (`google_jobs`) | `jobs_results` |
| Maps (`google_maps`) — list | `local_results` |
| Maps (`google_maps`) — single place | `place_results` |
| Maps Reviews (`google_maps_reviews`) | `reviews` |
| Lens (`google_lens`) | `visual_matches`, `exact_matches`, `related_content` |
| Events (`google_events`) | `events_results` |
| Videos (`google_videos_light`) | `video_results` |
| YouTube (`youtube`) | `video_results` |
| Scholar (`google_scholar`) | `organic_results` |
| Flights (`google_flights`) | `best_flights`, `other_flights` |
| Finance (`google_finance`) | `summary`, `graph`, `news_results` |
| Trends (`google_trends`) — depends on `data_type` | `TIMESERIES`→`interest_over_time`; `GEO_MAP`→`interest_by_region`; `RELATED_TOPICS`→`related_topics`; `RELATED_QUERIES`→`related_queries` |
| Autocomplete (`google_autocomplete`) | `suggestions` |
| Tripadvisor (`tripadvisor`) | `places`, `restaurants`, `hotels`, `attractions` |

## Pagination

Use `serpapi_pagination.next` for the next page — includes all current params plus correct offset. Pass directly without modification.

## Token follow-ups (deep-dive engines)

Some engines return a token you feed into a second engine for richer data:

| First call | Token in response | Second engine |
|:---|:---|:---|
| `google_shopping_light` | `shopping_results[].serpapi_link` → `page_token` | `google_immersive_product` (all seller prices) |
| `google` / `google_light` | `ai_overview.page_token` | `google_ai_overview` (full AI answer) |
| `google_maps` | `place_results.data_id` / `local_results[].data_id` | `google_maps_reviews` |

`ai_overview.page_token` expires ~1 minute after the original search — call `google_ai_overview` immediately.
