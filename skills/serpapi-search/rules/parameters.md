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
| `location` | string | Canonical city/region for precise geo-targeting (e.g., `Austin, Texas`). Takes precedence over `gl`. |

**Example — French results from France:**
```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=restaurants paris" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "gl=fr" \
  --data-urlencode "hl=fr" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
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
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=AI announcements" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "tbs=qdr:w" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

## Other Parameters

| Parameter | Type | Description |
|:---|:---|:---|
| `safe` | string | Safe search: `active` or `off`. |
| `no_cache` | string | Pass `"true"` to bypass cached results and force a live crawl. |
