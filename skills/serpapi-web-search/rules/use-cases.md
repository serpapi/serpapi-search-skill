# SerpApi Use Cases & Multi-Engine Patterns

Common use cases, the engine stacks that serve them, and parallel query patterns for AI agents.

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

Run independent engine calls concurrently — don't serialize them.

```python
import serpapi, asyncio

client = serpapi.Client(api_key="your_key_here")

async def search(params):
    return await asyncio.to_thread(client.search, params)

async def main():
    # Brand risk monitoring: 3 surfaces in parallel
    results = await asyncio.gather(
        search({"engine": "google_news_light", "q": '"Acme Corp" (lawsuit OR breach OR recall)'}),
        search({"engine": "google_light",      "q": 'site:sec.gov "Acme Corp"'}),
        search({"engine": "google_maps",       "q": "Acme Corp headquarters"}),
    )
    news, web, maps = results
    return news, web, maps

asyncio.run(main())
```

```bash
# CLI: run in parallel with & and wait
serpapi search engine=google_news_light q='"Acme Corp" lawsuit' &
serpapi search engine=google_light      q='site:sec.gov "Acme Corp"' &
wait
```

## AI Agent Fan-Out Pattern

One user prompt typically triggers multiple sub-queries. Plan capacity accordingly.

```
1 user question
  → 1 primary web query          (google_light)
  → 1 news freshness check       (google_news_light)
  → 1–3 follow-up clarifications (google_light, num=3)
  ─────────────────────────────────────────────
  = 3–5 API calls per agent turn
```

For research agents with citation requirements, multiply by the number of claims to verify. A 10-claim research report typically generates 20–50 API calls.

## Usage Estimation

```
monthly_searches = entities × query_variants × engines × cadence_multiplier

cadence_multiplier:
  real-time / continuous  →  30 (daily) to 720 (hourly)
  daily                   →  30
  weekly                  →  4
  on-demand / batch       →  1
```

### Examples by segment

| Segment | Formula | Example |
|---|---|---|
| Ticker enrichment | tickers × engines × queries/ticker × monthly refreshes | 500 × 3 × 5 × 4 = 30,000/mo |
| Brand monitoring | entities × query_variants × engines × cadence | 200 × 3 × 3 engines × 30 days = 54,000/mo |
| Product catalog | SKUs × (organic + shopping) × refresh rate | 1,000 × 2 × 8 = 16,000/mo |
| KYB verification | applications/mo × surfaces/entity | 500 × 4 = 2,000/mo |
| AI agent (research) | sessions/day × fan-out × 30 | 100 × 15 × 30 = 45,000/mo |

## Financial Intelligence Stack

```bash
# Price + news + trends for a ticker — 3 parallel calls
serpapi search engine=google_finance           q="AAPL:NASDAQ" &
serpapi search engine=google_news_light        q='"AAPL" (earnings OR acquisition OR investigation)' &
serpapi search engine=google_trends            q="Apple stock" geo=US date="today 3-m" &
wait
```

## Product Catalog Intelligence Stack

```bash
# Organic ranking + shopping prices + ad presence
serpapi search engine=google_light         q="noise cancelling headphones" gl=us &
serpapi search engine=google_shopping_light q="noise cancelling headphones" gl=us &
wait
```

## Local Business Verification Stack

```bash
# Step 1: find the place and get data_id
serpapi search engine=google_maps q="Acme Auto Repair Austin TX"
# → grab local_results[0].data_id from the response

# Step 2: pull reviews using that data_id
serpapi search engine=google_maps_reviews data_id="<data_id from step 1>"
```

---

See [ENGINES.md](ENGINES.md) for the full engine list · [parameters.md](parameters.md) for locale/time filtering · [examples.md](examples.md) for CLI patterns.
