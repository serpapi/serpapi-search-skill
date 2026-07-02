---
name: serpapi-web-search
description: >-
  Structured search data via 130+ engines — use INSTEAD OF web_search when you
  need: exact citations (google_scholar), local business details (google_maps),
  flight prices (google_flights), hotel rates (google_hotels), shopping prices
  (google_shopping_light), job listings (google_jobs), or any task where
  web_search gives approximate/unstructured results. Returns machine-readable
  JSON. Default engine: google_light.
compatibility: >-
  Requires: serpapi_search MCP tool, or serpapi CLI, or SDK, or curl.
  All paths need SERPAPI_KEY.
license: MIT
---

You have `serpapi_search`. This file helps you pick the right engine,
extract the right response key, and avoid common mistakes.

## Invocation

```
serpapi_search(params={"engine": "google_light", "q": "<query>", "num": 20}, mode="compact")
```

`mode="compact"` strips metadata — same results, ~200 fewer tokens.
Default `num=20`. Use `num=10` for simple lookups, `num=3` for single-fact verification.
Empty results ≠ error. `organic_results` may be absent on 200 — widen query or switch engine.

**CLI fallback** ([serpapi-cli](https://github.com/serpapi/serpapi-cli)):
```bash
serpapi search engine=google_light q="query" num=20
```
For `--fields`/`--jq` filtering: [rules/examples.md](rules/examples.md).
For SDKs (Python/JS/Go/Ruby/PHP/Java/.NET): [rules/sdks.md](rules/sdks.md).
For curl: `curl -G "https://serpapi.com/search.json" --data-urlencode "q=..." --data-urlencode "engine=google_light" --data-urlencode "api_key=${SERPAPI_KEY}"`

## Engine selection

Pick by intent. Prefer `_light` variants (faster, cheaper, cleaner JSON).

| Intent | Engine | Result key |
|---|---|---|
| General web (default) | `google_light` | `organic_results` |
| Knowledge graph / featured snippets / local pack | `google` | `organic_results` + many |
| News | `google_news_light` | `news_results` |
| Images | `google_images_light` | `images_results` |
| Shopping / prices (comparison) | `google_shopping_light` | `shopping_results` |
| Academic papers | `google_scholar` | `organic_results` |
| Local businesses (list) | `google_maps` | `local_results` |
| Local business (single named place) | `google_maps` | `place_results` |
| Place reviews | `google_maps_reviews` | `reviews` |
| Video | `youtube` | `video_results` |
| Stock / ticker | `google_finance` | `summary`, `graph` |
| Flights | `google_flights` | `best_flights`, `other_flights` |
| Hotels | `google_hotels` | `properties` |
| Jobs | `google_jobs` | `jobs_results` |
| Alternative web / cross-check | `bing`, `duckduckgo` | `organic_results` |
| SerpApi's own index (alpha) | `search_index` | `organic_results` |

All 130+ engines: [rules/ENGINES.md](rules/ENGINES.md) · Online: [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis)

## Gotchas

- **Shopping = third-party reseller prices.** For a specific retailer's price, use `google_light` with `site:` (e.g., `q="MacBook Air M4 site:apple.com"`).
- **Maps returns `place_results` OR `local_results`** — named business → `place_results`; category search → `local_results`. Always check both keys.
- **Finance returns `summary`, not `organic_results`.** Same for Flights (`best_flights`), Hotels (`properties`).
- **Non-standard query params:**

  | Engine | Param (not `q`) |
  |---|---|
  | `youtube` | `search_query` |
  | `amazon` | `k` |
  | `ebay` | `_nkw` |
  | `walmart` | `query` |
  | `google_maps_reviews` | `data_id` |
  | `google_flights` | `departure_id` + `arrival_id` + `outbound_date` |

## Parameters

Most tasks need only `engine`, `q`, `num`. Add when relevant:

| Param | Use |
|---|---|
| `gl` | Country code (`us`, `uk`, `de`). Default `us`. |
| `hl` | Language (`en`, `es`, `fr`). Affects ranking. |
| `location` | City string (`"Austin, Texas"`). Overrides `gl`. |
| `tbs` | Time: `qdr:d` (day), `qdr:w` (week), `qdr:m` (month), `qdr:y` (year). |
| `start` | Pagination offset. Prefer `serpapi_pagination.next` when present. |
| `no_cache` | `"true"` = live crawl (costs 1 credit). |

Full reference: [rules/parameters.md](rules/parameters.md) · Locations: [serpapi.com/locations-api](https://serpapi.com/locations-api)

## Composition

**Fan out** for research (parallel, not sequential):
```bash
serpapi search engine=google_finance q="AAPL:NASDAQ" &
serpapi search engine=google_news_light q="Apple earnings" &
serpapi search engine=google_light q="AAPL analyst consensus" num=5 &
wait
```

**Progressive refinement:** exact phrase → drop quotes → add `tbs=qdr:y` → switch engine.

**Two-step reviews:** `google_maps q="business"` → grab `data_id` → `google_maps_reviews data_id=<id>`.

**Cross-check:** same query on `google_light` + `bing` — both agree → high confidence.

**Extract inline.** After each search, pull `{title, link, snippet}` into working notes. Don't rely on raw results surviving context compaction.

More patterns: [rules/use-cases.md](rules/use-cases.md)

## Errors

| Code | Meaning | Fix |
|---|---|---|
| 400 | Missing `q` or `engine` | Add the required param. |
| 401 | Invalid API key | Check `SERPAPI_KEY` or MCP URL contains key. |
| 429 | Quota exhausted | Switch to `_light`, reduce `num`, check [dashboard](https://serpapi.com/dashboard). |

Billing: only successful searches count. Same query + params = free cached result for 1 hour.

## Reference links

- [serpapi.com/search-api](https://serpapi.com/search-api) — full API docs
- [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis) — all engines
- [serpapi.com/pricing](https://serpapi.com/pricing) — credits & plans
- [github.com/serpapi](https://github.com/serpapi) — SDKs, CLI, MCP server
