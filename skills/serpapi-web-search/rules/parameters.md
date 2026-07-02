# SerpApi Parameters

`GET https://serpapi.com/search.json`

## Required

| Parameter | Type | Description |
|:---|:---|:---|
| `engine` | string | Search engine to use (e.g., `google_light`). |
| `q` | string | Search query. Most engines use `q`; exceptions: `youtube` → `search_query`, `amazon` → `k`, `instagram_profile` → `profile_id`, `google_maps_reviews` → `data_id`. Per-engine docs: [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis). |
| `api_key` | string | Your SerpApi API key. |

## Pagination

| Parameter | Type | Description |
|:---|:---|:---|
| `num` | integer | Results per page (max 100, default 10). |
| `start` | integer | Result offset. `start=10&num=10` = page 2. |

## Locale Targeting

| Parameter | Type | Description |
|:---|:---|:---|
| `gl` | string | Country code (e.g., `us`, `uk`, `de`). Default: `us`. |
| `hl` | string | Language code (e.g., `en`, `es`, `fr`). Default: `en`. |
| `location` | string | Canonical city/region (e.g., `Austin, Texas`). Overrides `gl`. Valid values: [serpapi.com/locations-api](https://serpapi.com/locations-api). |

```bash
serpapi search engine=google_light q="restaurants paris" gl=fr hl=fr
```

## Time Filtering (`tbs`)

| Value | Meaning |
|:---|:---|
| `qdr:d` | Past 24 hours |
| `qdr:w` | Past week |
| `qdr:m` | Past month |
| `qdr:y` | Past year |

```bash
serpapi search engine=google_light q="AI announcements" tbs=qdr:w
```

## Other Parameters

| Parameter | Type | Description |
|:---|:---|:---|
| `safe` | string | `active` or `off`. |
| `no_cache` | string | `"true"` forces live crawl. |
| `zero_trace` | string | Enterprise. ZDR — no data stored on SerpApi. |
| `device` | string | `desktop` (default), `tablet`, `mobile`. |
| `async` | string | Async submit; retrieve via [Archive API](https://serpapi.com/search-archive-api). |
| `output` | string | `json` (default) or `html`. |

**Conflicts:** `no_cache` + `async` incompatible. `async` not for [Ludicrous Speed](https://serpapi.com/ludicrous-speed). `zero_trace` always costs 1 credit.

## Caching & Billing

- Successful searches only count toward quota. Same query+params = free cache for 1 hour.
- `no_cache=true` forces live crawl (1 credit). Response size doesn't affect cost.
- Check quota: `serpapi account` → `total_searches_left`.
