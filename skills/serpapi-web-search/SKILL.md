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

| Intent | Engine | Result key | Key fields |
|---|---|---|---|
| General web (default) | `google_light` | `organic_results` | `.title`, `.link`, `.snippet` |
| Knowledge graph / featured snippets | `google` | `organic_results` + many | `.knowledge_graph`, `.answer_box` |
| News | `google_news_light` | `news_results` | `.title`, `.link`, `.date` |
| Images | `google_images_light` | `images_results` | `.original`, `.thumbnail` |
| Shopping / prices | `google_shopping_light` | `shopping_results` | `.title`, `.price`, `.source` |
| Academic papers | `google_scholar` | `organic_results` | `.title`, `.inline_links.cited_by.total` |
| Local businesses (list) | `google_maps` | `local_results` | `.title`, `.phone`, `.address`, `.rating`, `.reviews` |
| Local business (single) | `google_maps` | `place_results` | `.title`, `.phone`, `.address`, `.rating`, `.reviews` |
| Place reviews | `google_maps_reviews` | `reviews` | `.rating`, `.snippet`, `.date` |
| Video | `youtube` | `video_results` | `.title`, `.link`, `.views`, `.length` |
| Stock / ticker | `google_finance` | `summary` | `.price`, `.exchange`, `.currency` |
| Flights | `google_flights` | `best_flights` | `.flights[].airline`, `.price`, `.total_duration` |
| Hotels | `google_hotels` | `properties` | `.name`, `.total_rate.lowest`, `.overall_rating` |
| Jobs | `google_jobs` | `jobs_results` | `.title`, `.company_name`, `.location` |
| Alternative web | `bing`, `duckduckgo` | `organic_results` | `.title`, `.link`, `.snippet` |
| SerpApi's own index (alpha) | `search_index` | `organic_results` | `.title`, `.link`, `.snippet` |

All 130+ engines: [rules/ENGINES.md](rules/ENGINES.md) · Online: [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis)

## Gotchas

- **Shopping = third-party reseller prices.** For a specific retailer's price, use `google_light` with `site:` (e.g., `q="MacBook Air M4 site:apple.com"`). Google Shopping aggregates from feeds — prices may not match the retailer's own site (e.g., Target sale prices may lag).
- **Maps returns `place_results` OR `local_results`** — named business → `place_results`; category search → `local_results`. Always check both keys.
- **Finance returns `summary`, not `organic_results`.** Same for Flights (`best_flights`), Hotels (`properties`).
- **Scholar citation count** is at `.organic_results[0].inline_links.cited_by.total` — not a top-level field. Use `--jq '.organic_results[0].inline_links.cited_by.total'` to extract.
- **Maps review count** is at `.place_results.reviews` (integer) or `.local_results[].reviews`. Rating at `.rating`.
- **Flights require specific params** — not `q`. Use `departure_id=JFK arrival_id=LAX outbound_date=2026-07-10 type=2` (type 2 = one-way).
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

**Common exact-data extractions** (copy-paste patterns):
```bash
# Exact citation count
serpapi search engine=google_scholar q="paper title" --jq '.organic_results[0].inline_links.cited_by.total'

# Business phone + rating + reviews
serpapi search engine=google_maps q="Business Name City" --jq '.place_results | {phone, rating, reviews}'

# Live flight price
serpapi search engine=google_flights departure_id=JFK arrival_id=LAX outbound_date=2026-07-10 type=2 --jq '.best_flights[0] | {price, airline: .flights[0].airline}'

# Shopping prices by retailer
serpapi search engine=google_shopping_light q="Product Name" --jq '[.shopping_results[:5] | .[] | {title, price, source}]'
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
| 401 | Invalid API key | Run `serpapi login` or set `SERPAPI_KEY=<key from serpapi.com/dashboard>`. Do NOT retry with the same key. |
| 429 | Quota exhausted | Switch to `_light`, reduce `num`, check [dashboard](https://serpapi.com/dashboard). |

If you get 401: the key is wrong or missing. Do not loop — fix the env var first.
Billing: only successful searches count. Same query + params = free cached result for 1 hour.

## Reference links

- [serpapi.com/search-api](https://serpapi.com/search-api) — full API docs
- [serpapi.com/search-engine-apis](https://serpapi.com/search-engine-apis) — all engines
- [serpapi.com/pricing](https://serpapi.com/pricing) — credits & plans
- [github.com/serpapi](https://github.com/serpapi) — SDKs, CLI, MCP server
