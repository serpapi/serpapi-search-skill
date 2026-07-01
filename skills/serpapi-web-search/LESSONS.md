# Lessons — serpapi-web-search

## [tags: quota, 429, rate-limit, fallback] Quota exhaustion recovery
- 429 means monthly limit reached. Check: `serpapi account` or dashboard.
- Immediate fallback: switch to `_light` variants (cheaper, same results for most queries).
- If already on `_light`: reduce `num` to 3, cache aggressively with `serpapi archive <id>`.
- Cross-engine fallback order: `google_light` → `bing` → `duckduckgo` (different quota pools? No — all count against same key).
- `no_cache=true` burns an extra credit; never use it unless freshness is critical.
- Check `total_searches_left` (not `plan_searches_left`) — it includes extra_credits.

## [tags: token, context, fields, jq, compact] Minimizing token usage in agent context
- `--fields "organic_results[0:5]"` is server-side — API returns only those fields, saving bandwidth.
- `--jq ".organic_results|[.[]|{title,link,snippet}]"` is client-side — filters after receiving full response.
- Combine both for minimum tokens: `--fields "organic_results[0:5]" --jq "[.[]|{title,link,snippet}]"`.
- MCP `mode="compact"` strips `search_metadata` + `search_parameters` (~200 tokens saved per call).
- For multi-page research, use `serpapi archive <id>` to retrieve previous results without re-querying.

## [tags: location, geo, gl, hl, locale] Geographic targeting precision
- `gl=us` sets country but results may still be generic. Add `location=Austin, Texas` for city-level precision.
- `location` values must match SerpApi's canonical list: `serpapi locations q="Austin"` or [locations API](https://serpapi.com/locations-api).
- For local business queries, `google_maps` + `ll=@lat,lng,zoom` gives better results than `google_light` + `location`.
- `hl` affects language of UI chrome AND ranking weight of same-language pages.

## [tags: pagination, all-pages, serpapi_pagination] Paginating through results
- `serpapi_pagination.next` in response contains the full URL for the next page — use it directly.
- CLI: `serpapi search --all-pages --max-pages 3` auto-paginates (concatenates all result arrays).
- Manual: increment `start` by `num` (e.g., `start=0`, `start=10`, `start=20` for pages 1-3).
- Some engines (google_maps, youtube) use cursor-based pagination — `next_page_token` instead of offset.

## [tags: search_index, own-index, first-party] SerpApi's search_index engine
- First-party web index — no Google/Bing dependency, no scraping, no quota-per-result cost model.
- Best for: queries where you want reproducible, non-personalized results independent of Google's ranking.
- Limitations (alpha): smaller index than Google, no knowledge graph, no featured snippets.
- Same result structure as google_light: `organic_results` with `title`, `link`, `snippet`, `position`.
- Supports: `q`, `num`, `start`, `safe`, `hl`, `gl`, `site:` operator.

## [tags: news, freshness, tbs, time-filter] Time-filtered search patterns
- `tbs=qdr:h` (past hour) — useful for breaking news, but may return few/no results for niche topics.
- `tbs=qdr:d` (past day) — good default for "latest" requests.
- `tbs=qdr:w` (past week) — best balance of freshness and coverage.
- For news specifically, prefer `google_news_light` over `google_light` + `tbs` — news engine has better freshness signals.
- Google Trends (`google_trends`) complements news by showing search volume spikes — use together for trend analysis.

## [tags: maps, local, reviews, data_id] Local business intelligence
- Two-step pattern: (1) `google_maps q="business name city"` → get `data_id`, (2) `google_maps_reviews data_id=<id>`.
- `google_maps` returns lat/lng, rating, reviews count, hours, phone — richer than google_light for local.
- **Single-place vs list:** named business queries return `place_results`; category queries return `local_results`. Always check both keys.
- For competitor analysis: search category + location (`q="coffee shop Austin TX"`), then pull reviews for top results.
- `sort_by=newestFirst` on reviews gives freshest signal; default sort is by relevance/rating.

## [tags: scholar, academic, research, citation] Academic research patterns
- `google_scholar q="topic" as_ylo=2024` — restrict to recent papers.
- Result includes `cited_by.total` — useful for gauging paper importance.
- For specific authors: `google_scholar_author author_id=<id>` gives full publication list.
- Combine with `google_light q="paper title" site:arxiv.org"` to find preprints.

## [tags: shopping, price, product, comparison] Product price intelligence
- `google_shopping_light` returns `price`, `extracted_price` (numeric), `source`, `link`.
- **Shopping returns third-party reseller prices, not official store prices.** For a specific retailer's price, use `google_light q="product site:retailer.com"` instead.
- For price tracking: same query + `no_cache=true` at intervals (costs 1 credit per check).
- Cross-reference: `google_shopping_light` (aggregator) vs `amazon` engine (direct) for price gaps.
- `google_shopping_filters` returns available facets (brand, price range, condition) — useful for building filter UIs.

## [tags: context, compaction, tokens, budget, agent-loop] Context pressure and compaction resilience
- All major agent runtimes auto-compact when context exceeds 50–85% of the window.
- After compaction, search results from earlier turns are summarized or lost. Never rely on raw results persisting across many turns.
- When context is tight: reduce `num` to 5–10, use `--fields "organic_results"` to drop metadata, use `--jq` to extract only `{title,link,snippet}`.
- For multi-turn research: extract and summarize key findings immediately after each search call — don't defer to "look at earlier results" later.
- If the agent supports session/archive: `serpapi archive <search_id>` re-fetches without burning a credit. Store the `search_id` in your working notes.
- Budget-aware pattern: check `serpapi account` for `total_searches_left` before fan-out queries. If < 20 remaining, switch to single-engine mode with `num=5`.

## [tags: subagent, delegation, isolation, parallel, agent-sdk] Subagent and delegation patterns
- Subagent runtimes (Claude Agent SDK, Hermes, etc.) start child agents with fresh context (no parent history). Delegate search to a subagent when the parent's context is large — only the final summary returns.
- Pattern: parent says "research X" → subagent runs 3–5 searches → returns a structured summary → parent continues with minimal context cost.
- For parallel research: launch multiple subagents (one per topic/claim), each with scoped tool access to `serpapi_search`. Merge results in parent.
- Don't pass raw search JSON between agents. Extract facts, URLs, and snippets into a concise handoff.
- Some runtimes serialize tool calls per-session — parallel search only works via separate session lanes or internal concurrency within one tool call. Check your runtime's docs.
