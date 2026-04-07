# SerpApi curl Examples

All examples follow the same pattern — swap `engine` and `q` as needed.

## Google News (Light)

Latest news on a topic:

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=artificial intelligence" \
  --data-urlencode "engine=google_news_light" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

Result key: `news_results`

## Google Shopping (Light)

Product prices and availability:

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=iphone 16 pro" \
  --data-urlencode "engine=google_shopping_light" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

Result key: `shopping_results`

## Bing Web Search

Alternative web results (useful when Google results are too familiar or for cross-referencing):

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=serpapi documentation" \
  --data-urlencode "engine=bing" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

Result key: `organic_results`

## Time-Filtered Search

Results from the past week using `tbs=qdr:w`:

```bash
curl -G "https://serpapi.com/search.json" \
  --data-urlencode "q=latest AI models" \
  --data-urlencode "engine=google_light" \
  --data-urlencode "tbs=qdr:w" \
  --data-urlencode "api_key=${SERPAPI_KEY}"
```

See [parameters.md](parameters.md) for all `tbs` values.
