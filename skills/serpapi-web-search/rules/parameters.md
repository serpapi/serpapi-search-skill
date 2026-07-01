# SerpApi Parameters

All parameters for `GET https://serpapi.com/search.json`.

## Required

| Parameter | Type | Description |
|:---|:---|:---|
| `engine` | string | The search engine to use (e.g., `google_light`). |
| `q` | string | The search query. Required for most engines. Exceptions (not exhaustive): `youtube` uses `search_query`; `amazon` uses `k`; `instagram_profile` uses `profile_id`; `google_maps_reviews` uses `data_id`. Check per-engine docs at [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis) for non-standard engines. |
| `api_key` | string | Your SerpApi API key. |

## Pagination

| Parameter | Type | Description |
|:---|:---|:---|
| `num` | integer | Results per page (max 100, default 10). Agents should explicitly pass `num=20` for richer context — see [SKILL.md](../SKILL.md). |
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
| `zero_trace` | string | Enterprise only. Pass `"true"` to enable ZeroTrace (ZDR — Zero Data Retention) — search parameters, files, and metadata are not stored on SerpApi servers. Works on all engines. |
| `device` | string | `desktop` (default), `tablet`, or `mobile`. |
| `async` | string | Pass `"true"` to submit search and retrieve later via [Searches Archive API](https://serpapi.com/search-archive-api). |
| `output` | string | `json` (default) or `html` (raw HTML from the search engine). |

**Parameter conflicts:**
- `no_cache` + `async` — **incompatible** (per docs: "should not be used together").
- `async` should not be used on accounts with [Ludicrous Speed](https://serpapi.com/ludicrous-speed) enabled.
- `zero_trace` — works with all engines and all other parameters. Searches won't appear in your dashboard or archive.

## Caching & Billing

- Only **successful** searches count toward your quota. Cached, errored, and failed searches are free.
- Cache: same query + same parameters = free cached result for 1 hour.
- `no_cache=true` forces a live crawl (costs 1 credit).
- `zero_trace=true` searches always cost 1 credit (no caching possible).
- Response size doesn't matter — 100 results or 0 results both count as 1 search.
- Check quota: `serpapi account` → look at `total_searches_left` (includes extra_credits).

---
Full parameter reference: [serpapi.com/search-api](https://serpapi.com/search-api) · Locations lookup: [serpapi.com/locations-api](https://serpapi.com/locations-api) · ZeroTrace: [serpapi.com/zero-trace-mode](https://serpapi.com/zero-trace-mode)
