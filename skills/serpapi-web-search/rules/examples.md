# SerpApi Examples

All examples use `serpapi-cli` (preferred). For curl equivalents, swap `serpapi search engine=X q=Y` with `curl -G "https://serpapi.com/search.json" --data-urlencode "q=Y" --data-urlencode "engine=X" --data-urlencode "api_key=${SERPAPI_KEY}"`.

> **Note:** Most examples use `q=` as the query parameter. Some engines use different parameter names — e.g., `search_query` (YouTube), `k` (Amazon), `data_id` (Google Maps Reviews). See [parameters.md](parameters.md) for the full list.

## Google News

Latest news on a topic:

```bash
serpapi search engine=google_news_light q="artificial intelligence"
```

Result key: `news_results`

## Google Shopping

Product prices and availability:

```bash
serpapi search engine=google_shopping_light q="iphone 16 pro"
```

Result key: `shopping_results`

## Bing Web Search

Alternative web results (cross-reference or privacy):

```bash
serpapi search engine=bing q="serpapi documentation"
```

Result key: `organic_results`

## Time-Filtered Search

Results from the past week:

```bash
serpapi search engine=google_light q="latest AI models" tbs=qdr:w
```

See [parameters.md](parameters.md) for all `tbs` values.

## Result Filtering

Fetch only what you need — fewer tokens, faster processing:

```bash
# Server-side: only return top 10 organic results (reduces API payload)
# Response shape: {"organic_results": [...10 items...]} — same key name, other keys dropped
serpapi search --fields "organic_results[0:10]" engine=google_light q="coffee"

# Client-side: extract title + link + snippet after receiving full response
# Response shape: [{title, link, snippet}, ...] — transformed by jq
serpapi search --jq ".organic_results[0:10]|[.[]|{title,link,snippet}]" engine=google_light q="coffee"

# Both combined: minimum bandwidth + minimum context window tokens
# --fields slices server-side, --jq transforms client-side
serpapi search --fields "organic_results[0:10]" --jq "[.organic_results[]|{title,link,snippet}]" engine=google_light q="coffee"
```

**curl equivalent** — use the `fields` query parameter for server-side filtering, pipe to `jq` for client-side:
```bash
# Server-side filtering with curl (fields= parameter)
curl -s -G "https://serpapi.com/search.json" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "q=coffee" \
  --data-urlencode "num=10" \
  --data-urlencode "api_key=${SERPAPI_KEY}" \
  --data-urlencode "fields=organic_results" \
  | jq '[.organic_results[]|{title,link,snippet}]'
```

Note: `--fields` (CLI) maps to the `fields=` query parameter in the REST API. `--jq` is CLI-only — use the `jq` command-line tool to achieve the same result with curl.

## Google Maps

Find a local business (single place):

```bash
serpapi search engine=google_maps q="The French Laundry Yountville California"
```

Result key: `place_results` (single place) — includes `title`, `address`, `phone`, `rating`, `gps_coordinates`.

Search for nearby businesses (list):

```bash
serpapi search engine=google_maps q="coffee shops" ll="@37.7749,-122.4194,15z"
```

Result key: `local_results` (list of places).

**Gotcha:** Single-place queries return `place_results`, not `local_results`. Always check both keys.

## Google Finance

Stock quote with price data:

```bash
serpapi search engine=google_finance q="AAPL:NASDAQ" --jq '.summary | {price, currency, previous_close}'
```

Result key: `summary` (quote data), `graph` (price history), `news_results` (related news).

## Search Index (SerpApi's Own Index)

Query SerpApi's first-party web index — no Google/Bing dependency, direct index access:

```bash
serpapi search engine=search_index q="serpapi documentation"

# With field filtering
serpapi search --jq ".organic_results[0:10]|[.[]|{title,link,snippet}]" engine=search_index q="coffee"
```

Result key: `organic_results` (same structure as `google_light`)

## Paginate All Results

```bash
serpapi search engine=google q="coffee" --all-pages --max-pages 3
```

## Non-Standard Query Parameters

Some engines use a different parameter instead of `q`. Here are worked examples:

```bash
# YouTube — uses `search_query` instead of `q`
serpapi search engine=youtube search_query="machine learning tutorial"

# Amazon — uses `k` instead of `q`
serpapi search engine=amazon k="wireless headphones"

# Google Maps Reviews — uses `data_id` (no free-text query)
serpapi search engine=google_maps_reviews data_id="0x89c25090129c363d:0x40c6a5770d25022b"

# eBay — uses `_nkw` instead of `q`
serpapi search engine=ebay _nkw="vintage watch"
```

Result keys: YouTube → `video_results`, Amazon → `organic_results`, Maps Reviews → `reviews`, eBay → `organic_results`. See [response.md](response.md) for the full mapping.

## Retrieve a Cached Search

Every SerpApi response includes a `search_metadata.id`. Retrieve it later without an extra quota cost:

```bash
serpapi archive <search-id>
```

## Account Usage

Check remaining quota:

```bash
serpapi account
```
