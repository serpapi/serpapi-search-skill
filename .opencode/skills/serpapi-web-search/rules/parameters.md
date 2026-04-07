# SerpApi Parameters

All parameters for `GET https://serpapi.com/search.json`.

## Required

| Parameter | Type | Description |
|:---|:---|:---|
| `engine` | string | The search engine to use (e.g., `google_light`). |
| `q` | string | The search query. Note: `youtube` uses `search_query` instead. |
| `api_key` | string | Your SerpApi API key. |

## Pagination

| Parameter | Type | Description |
|:---|:---|:---|
| `num` | integer | Results per page (max 100, default 10). |
| `start` | integer | Result offset. Use `start=10&num=10` for page 2. |

## Locale Targeting

Set these based on the user's context — they improve result relevance and ranking order.

| Parameter | Type | Description |
|:---|:---|:---|
| `gl` | string | Country code (e.g., `us`, `uk`, `de`). Default: `us`. |
| `hl` | string | Language code (e.g., `en`, `es`, `fr`). Default: `en`. |
| `location` | string | Canonical city/region for precise geo-targeting (e.g., `Austin, Texas`). Takes precedence over `gl`. Look up valid values at [serpapi.com/locations-api](https://serpapi.com/locations-api). |

**Example — French results from France:**
```bash
serpapi search engine=google_light q="restaurants paris" gl=fr hl=fr
```

## Time Filtering

Use `tbs` to restrict results to a time range.

| Value | Meaning |
|:---|:---|
| `qdr:d` | Past 24 hours |
| `qdr:w` | Past week |
| `qdr:m` | Past month |
| `qdr:y` | Past year |

**Example — news from the past week:**
```bash
serpapi search engine=google_light q="AI announcements" tbs=qdr:w
```

## Other Parameters

| Parameter | Type | Description |
|:---|:---|:---|
| `safe` | string | Safe search: `active` or `off`. |
| `no_cache` | string | Pass `"true"` to bypass cached results and force a live crawl. |

---
Full parameter reference: [serpapi.com/search-api](https://serpapi.com/search-api) · Locations lookup: [serpapi.com/locations-api](https://serpapi.com/locations-api)
