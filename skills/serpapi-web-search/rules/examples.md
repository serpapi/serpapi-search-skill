# SerpApi Examples

All examples use `serpapi-cli`. curl: `curl -G "https://serpapi.com/search.json" --data-urlencode "q=Y" --data-urlencode "engine=X" --data-urlencode "api_key=${SERPAPI_KEY}"`.

> Some engines use non-standard query params: `search_query` (YouTube), `k` (Amazon), `data_id` (Google Maps Reviews). See [parameters.md](parameters.md).

## Google News
```bash
serpapi search engine=google_news_light q="artificial intelligence"
```
Result key: `news_results`

## Google Shopping
```bash
serpapi search engine=google_shopping_light q="iphone 16 pro"
```
Result key: `shopping_results`

## Time-Filtered Search
```bash
serpapi search engine=google_light q="latest AI models" tbs=qdr:w
```
See [parameters.md](parameters.md) for all `tbs` values.

## Result Filtering (`--fields` / `--jq`)

```bash
# Server-side: reduces API payload
serpapi search --fields "organic_results[0:10]" engine=google_light q="coffee"

# Client-side: transform after receiving full response
serpapi search --jq ".organic_results[0:10]|[.[]|{title,link,snippet}]" engine=google_light q="coffee"

# Both: minimum bandwidth + minimum tokens
serpapi search --fields "organic_results[0:10]" --jq "[.organic_results[]|{title,link,snippet}]" engine=google_light q="coffee"
```

curl (`fields=` + `jq`):
```bash
curl -s -G "https://serpapi.com/search.json" \
  --data-urlencode "engine=google_light" --data-urlencode "q=coffee" \
  --data-urlencode "api_key=${SERPAPI_KEY}" --data-urlencode "fields=organic_results" \
  | jq '[.organic_results[]|{title,link,snippet}]'
```

`--fields` maps to `fields=` in REST API. `--jq` is CLI-only.

## Google Maps
```bash
serpapi search engine=google_maps q="The French Laundry Yountville California"
serpapi search engine=google_maps q="coffee shops" ll="@37.7749,-122.4194,15z"
```

## Google Finance
```bash
serpapi search engine=google_finance q="AAPL:NASDAQ" --jq '.summary | {price, currency, previous_close}'
```
Result keys: `summary`, `graph`, `news_results`

## Search Index
```bash
serpapi search engine=search_index q="serpapi documentation"
serpapi search --jq ".organic_results[0:10]|[.[]|{title,link,snippet}]" engine=search_index q="coffee"
```
Result key: `organic_results`

## Pagination
```bash
serpapi search engine=google q="coffee" --all-pages --max-pages 3
```

## Non-Standard Query Parameters
```bash
serpapi search engine=youtube search_query="machine learning tutorial"
serpapi search engine=amazon k="wireless headphones"
serpapi search engine=google_maps_reviews data_id="0x89c25090129c363d:0x40c6a5770d25022b"
serpapi search engine=ebay _nkw="vintage watch"
```
Result keys: YouTube → `video_results`, Amazon → `organic_results`, Maps Reviews → `reviews`, eBay → `organic_results`.

## Cached Search
```bash
serpapi archive <search-id>
```
