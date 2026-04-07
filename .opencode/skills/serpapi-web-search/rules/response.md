# SerpApi Response Format

## JSON Structure

All engines return JSON. The top-level keys are always present:

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

When using `mode="compact"` with the native tool, `search_metadata` and `search_parameters` are stripped from the response.

## Result Key by Engine

Different engines use different top-level array keys for their results:

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

## Pagination

Use `serpapi_pagination.next` as the URL for the next page of results. It includes all current parameters plus the updated `start` offset — you can use it directly.

---
Full response schema: [serpapi.com/search-api](https://serpapi.com/search-api)
