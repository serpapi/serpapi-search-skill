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
| Amazon, Walmart, eBay | `organic_results` |
| Jobs (`google_jobs`) | `jobs_results` |
| Maps (`google_maps`) — list | `local_results` |
| Maps (`google_maps`) — single place | `place_results` |
| Maps Reviews (`google_maps_reviews`) | `reviews` |
| Videos (`google_videos_light`) | `video_results` |
| YouTube (`youtube`) | `video_results` |
| Scholar (`google_scholar`) | `organic_results` |
| Flights (`google_flights`) | `best_flights`, `other_flights` |
| Finance (`google_finance`) | `summary`, `graph`, `news_results` |
| Trends (`google_trends`) | `interest_over_time`, `related_queries`, `related_topics` |
| Sports (`google_sports`) | `game_results`, `league_results`, `team_results` (by `type`) |

## Pagination

Use `serpapi_pagination.next` for the next page — includes all current params plus correct offset. Pass directly without modification.
