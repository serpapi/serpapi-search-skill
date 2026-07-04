# SerpApi Use Cases

## Use Case → Engine Mapping

| Use Case | Primary Engine | Secondary Engines |
|---|---|---|
| AI agent / general web research | `google_light` | `google_news_light`, `google_ai_overview` |
| Brand / AEO monitoring | `google_ai_overview` | `google_light`, `google_news_light` |
| Competitive ad intelligence | `google_ads` | `google_shopping_light` |
| Product pricing / catalog | `google_shopping_light` | `google_light`, `amazon` |
| Compliance / risk / KYC | `google_news_light` | `google_light`, `google_maps` |
| Financial / ticker data | `google_finance` | `google_light`, `google_news_light`, `google_trends` |
| Hiring / labor signals | `google_jobs` | `google_trends` |
| Consumer demand signals | `google_trends` | `google_news_light` |
| Local / maps intelligence | `google_maps` | `google_maps_reviews` |
| Travel / flight pricing | `google_flights` | `google_hotels` |
| Lead gen / enrichment | `google_light` | `google_maps`, `google_shopping_light` |
| Academic / research | `google_scholar` | `google_light` |
| Video content | `youtube` | `google_videos_light` |

## Multi-Engine Parallel Pattern

```bash
serpapi search engine=google_news_light q='"Acme Corp" lawsuit' &
serpapi search engine=google_light      q='site:sec.gov "Acme Corp"' &
wait
```

```python
import serpapi, asyncio
client = serpapi.Client(api_key="your_key_here")

async def main():
    results = await asyncio.gather(
        asyncio.to_thread(client.search, {"engine": "google_news_light", "q": '"Acme Corp" (lawsuit OR breach OR recall)'}),
        asyncio.to_thread(client.search, {"engine": "google_light",      "q": 'site:sec.gov "Acme Corp"'}),
        asyncio.to_thread(client.search, {"engine": "google_maps",       "q": "Acme Corp headquarters"}),
    )
    return results

asyncio.run(main())
```

## Financial Intelligence Stack

```bash
serpapi search engine=google_finance    q="AAPL:NASDAQ" &
serpapi search engine=google_news_light q='"AAPL" (earnings OR acquisition OR investigation)' &
serpapi search engine=google_trends     q="Apple stock" geo=US date="today 3-m" &
wait
```

## Product Catalog Intelligence Stack

```bash
serpapi search engine=google_light          q="noise cancelling headphones" gl=us &
serpapi search engine=google_shopping_light q="noise cancelling headphones" gl=us &
wait
```

## Local Business Verification Stack

```bash
# Step 1: find the place and get data_id
serpapi search engine=google_maps q="Acme Auto Repair Austin TX"

# Step 2: pull reviews using that data_id
serpapi search engine=google_maps_reviews data_id="<data_id from step 1>"
```

## Budget-Gated Fan-Out

```bash
REMAINING=$(serpapi account | grep -o '"total_searches_left":[0-9]*' | grep -o '[0-9]*')
if [ "$REMAINING" -lt 20 ]; then
  serpapi search engine=google_light q="$QUERY" num=5
else
  serpapi search engine=google_light      q="$QUERY" num=20 &
  serpapi search engine=google_news_light q="$QUERY" &
  serpapi search engine=google_scholar    q="$QUERY" &
  wait
fi
```
