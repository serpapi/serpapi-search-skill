# Lessons — serpapi-web-search

## [tags: quota, 429, rate-limit, fallback] Quota exhaustion recovery
- 429 = monthly limit. Check: `serpapi account` → `total_searches_left` (includes extra_credits).
- Fallback order: switch to `_light` → reduce `num` to 3 → use `serpapi archive <id>` for re-reads.
- `no_cache=true` burns a credit; skip unless freshness critical.
- All engines share one quota pool (no per-engine pools).

## [tags: token, context, fields, jq, compact] Minimizing token usage
- `--fields "organic_results[0:5]"` = server-side filter (API returns only those fields).
- `--jq "[.[]|{title,link,snippet}]"` = client-side transform (after full response received).
- Combine both for minimum tokens. For MCP: `mode="compact"` strips metadata (~200 tokens/call).
- `serpapi archive <id>` re-fetches without burning a credit.

## [tags: location, geo, gl, hl, locale] Geographic targeting
- `gl=us` = country. Add `location=Austin, Texas` for city-level precision.
- Location values must match canonical list: `serpapi locations q="Austin"` or [locations API](https://serpapi.com/locations-api).
- For local queries: `google_maps` + `ll=@lat,lng,zoom` > `google_light` + `location`.
- `hl` affects both UI language AND ranking weight of same-language pages.

## [tags: pagination, all-pages, serpapi_pagination] Pagination
- `serpapi_pagination.next` = full URL for next page — use directly.
- CLI: `--all-pages --max-pages 3` auto-concatenates result arrays.
- Manual: increment `start` by `num`. Some engines use `next_page_token` (Maps, YouTube).

## [tags: search_index, own-index, first-party] SerpApi's search_index
- First-party web index — no Google/Bing dependency, no scraping.
- Best for: reproducible, non-personalized results independent of Google ranking.
- Alpha: smaller index, no knowledge graph, no featured snippets.
- Same structure as google_light: `organic_results` with `{title, link, snippet, position}`.
- Supports: `q`, `num`, `start`, `safe`, `hl`, `gl`, `site:` operator.

## [tags: news, freshness, tbs, time-filter] Time-filtered search
- `tbs=qdr:h` (hour), `qdr:d` (day), `qdr:w` (week — best freshness/coverage balance).
- For "latest" requests: prefer `google_news_light` over `google_light` + `tbs` (better freshness signals).
- `google_trends` complements news with search volume spikes.

## [tags: maps, local, reviews, data_id] Local business intelligence
- Two-step: `google_maps q="business city"` → grab `data_id` → `google_maps_reviews data_id=<id>`.
- Maps returns lat/lng, rating, reviews count, hours, phone — richer than google_light for local.
- Competitor analysis: category + location (`q="coffee shop Austin TX"`) → reviews for top results.
- `sort_by=newestFirst` on reviews for freshest signal.

## [tags: scholar, academic, research, citation] Academic research
- `google_scholar q="topic" as_ylo=2024` — restrict to recent papers.
- `cited_by.total` in results = paper importance signal.
- `google_scholar_author author_id=<id>` for full publication list.
- Combine with `google_light q="paper title site:arxiv.org"` for preprints.

## [tags: shopping, price, product, comparison] Product price intelligence
- `extracted_price` field = numeric (for comparison). `source` = retailer name.
- Price tracking: same query + `no_cache=true` at intervals.
- Cross-reference: `google_shopping_light` (aggregator) vs `amazon` engine (direct) for price gaps.
- `google_shopping_filters` returns facets (brand, price range, condition).

## [tags: context, compaction, tokens, budget, agent-loop] Context pressure
- Agent runtimes auto-compact at 50–85% context window. Search results from early turns vanish.
- Always extract findings immediately after each search — don't defer.
- Budget-gated: check `total_searches_left` before fan-out. If < 20, single-engine mode + `num=5`.
- `serpapi archive <search_id>` re-fetches without credit cost — store IDs in working notes.

## [tags: subagent, delegation, isolation, parallel, agent-sdk] Subagent delegation
- Subagent runtimes start with fresh context. Delegate search when parent context is large.
- Pattern: parent → subagent runs 3–5 searches → returns structured summary → parent continues.
- For parallel research: multiple subagents (one per topic), each with `serpapi_search` access.
- Extract facts/URLs into concise handoff, never pass raw JSON between agents.
