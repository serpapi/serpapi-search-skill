# SerpApi Examples

All examples use `serpapi-cli` (preferred). For curl equivalents, swap `serpapi search engine=X q=Y` with `curl -G "https://serpapi.com/search.json" --data-urlencode "q=Y" --data-urlencode "engine=X" --data-urlencode "api_key=${SERPAPI_KEY}"`.

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

## Token-Efficient Search

Fetch only what you need — fewer tokens, faster results:

```bash
# Server-side: only return top 3 organic results (reduces API payload)
serpapi search --fields "organic_results[0:3]" engine=google_light q="coffee"

# Client-side: extract title + link after receiving full response
serpapi search --jq ".organic_results[0:3]|[.[]|{title,link}]" engine=google_light q="coffee"

# Both combined: minimum bandwidth + minimum context window tokens
serpapi search --fields "organic_results[0:3]" --jq "[.organic_results[]|{title,link}]" engine=google_light q="coffee"
```

## Paginate All Results

```bash
serpapi search engine=google q="coffee" --all-pages --max-pages 3
```

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
