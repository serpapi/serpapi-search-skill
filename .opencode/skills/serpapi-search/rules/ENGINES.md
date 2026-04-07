# SerpApi Search Engines Catalog

Complete list of 107 SerpApi search engines. Use the `engine` parameter to select the desired search engine.

## Light vs Full Engines

SerpApi offers `_light` variants of popular engines. Prefer them — they're faster, cheaper, and return essential data. Use the full engine when you need knowledge graph entries, local packs, or featured snippets.

**Light engines:**

| Engine | Full Equivalent |
|:---|:---|
| `google_light` | `google` |
| `google_news_light` | `google_news` |
| `google_images_light` | `google_images` |
| `google_shopping_light` | `google_shopping` |
| `google_videos_light` | `google_videos` |
| `duckduckgo_light` | `duckduckgo` |

## Result Keys

Each engine returns results under a specific top-level key. See [rules/response.md](rules/response.md) for the full mapping.

## Google (74 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| google | Main Google Search results | q, location, gl, hl, google_domain |
| google_light | Fast, essential Google Search results | q, location, gl, hl |
| google_ai_mode | Google AI-powered search mode | q, gl, hl |
| google_ai_overview | Google AI Overview results | q, gl, hl |
| google_about_this_result | Google "About This Result" feature data | q, google_domain |
| google_autocomplete | Google search autocomplete suggestions | q, gl, hl |
| google_related_questions | Google "People Also Ask" questions | q, gl, hl |
| google_images | Google Images search | q, location, ijn, safe |
| google_images_light | Fast Google Images results | q, location, gl, hl |
| google_images_related_content | Related content for Google Images | image_url, gl, hl |
| google_reverse_image | Google reverse image search | image_url, gl, hl |
| google_lens | Google Lens visual search | url, image_url |
| google_news | Google News results | q, gl, hl, tbm=nws |
| google_news_light | Fast Google News results | q, gl, hl |
| google_shopping | Google Shopping product search | q, location, direct_link |
| google_shopping_light | Fast Google Shopping results | q, location, gl, hl |
| google_shopping_filters | Google Shopping filters and facets | q, tbs, gl, hl |
| google_videos | Google Videos search | q, location, gl, hl |
| google_videos_light | Fast Google Videos results | q, location, gl, hl |
| google_short_videos | Google Short Videos (Shorts/TikTok style) | q, gl, hl |
| google_local | Google Local results | q, location, gl, hl |
| google_local_services | Google Local Services results | q, location, category |
| google_maps | Google Maps search | q, ll, type |
| google_maps_reviews | Reviews for a Google Maps place | data_id, hl, sort |
| google_maps_directions | Directions from Google Maps | origin, destination |
| google_maps_photos | Photos for a Google Maps place | data_id, hl |
| google_maps_photo_meta | Metadata for Google Maps photos | data_id, photo_id |
| google_maps_posts | Posts for a Google Maps place | data_id, hl |
| google_maps_autocomplete | Google Maps autocomplete suggestions | q, ll |
| google_maps_contributor_reviews | Google Maps contributor reviews | contributor_id, gl, hl |
| google_jobs | Google for Jobs search | q, location, chips |
| google_jobs_listing | Detailed Google Jobs listing | q, j_id, htidocid |
| google_scholar | Google Scholar results | q, as_ylo, as_yhi |
| google_scholar_author | Google Scholar author profile | author_id, hl |
| google_scholar_cite | Google Scholar citation details | q, hl |
| google_scholar_profiles | Google Scholar user profiles | mapex, hl |
| google_patents | Google Patents results | q, patent_number, country |
| google_patents_details | Individual patent details | patent_id, hl |
| google_finance | Google Finance stock/market data | q |
| google_finance_markets | Google Finance market overview | market |
| google_flights | Google Flights search | departure_id, arrival_id, outbound_date |
| google_flights_airports | Google Flights airport information | q, hl |
| google_flights_autocomplete | Google Flights autocomplete | q, hl |
| google_hotels | Google Hotels search | q, check_in_date, check_out_date |
| google_hotels_ads | Google Hotels advertisements | q, hl |
| google_hotels_photos | Google Hotels photos | q, hl |
| google_hotels_reviews | Google Hotels reviews | q, hl |
| google_hotels_properties | Google Hotels property results | q, hl |
| google_trends | Google Trends results | q, geo, date |
| google_trends_autocomplete | Google Trends autocomplete | q, hl |
| google_trends_news | Google Trends news articles | q, geo, hl |
| google_trends_trending_now | Google Trends trending searches | geo, hl |
| google_events | Google Events search | q, location, gl, hl |
| google_play | Google Play Store results | q, store, gl, hl |
| google_play_product | Google Play product details | product_id, gl |
| google_play_reviews | Google Play product reviews | product_id, gl |
| google_play_books | Google Play Books search | q, gl, hl |
| google_play_games | Google Play Games search | q, gl, hl |
| google_play_movies | Google Play Movies search | q, gl, hl |
| google_ads_transparency_center | Google Ads Transparency Center results | q, customer_id |
| google_ads_transparency_center_ad_details | Individual ad details | advertiser_id, creative_id |
| google_immersive_product | Google Immersive Product results | q, product_id |
| google_forums | Google Forums results | q, gl, hl |
| google_travel | Google Travel results | q, gl, hl |
| google_travel_explore | Google Travel Explore destinations | travel_type, destination |

## Bing (9 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| bing | Main Bing Search results | q, location, cc, mkt |
| bing_images | Bing Images search | q, mkt, safeSearch |
| bing_news | Bing News search | q, mkt, freshness |
| bing_shopping | Bing Shopping search | q, mkt, price |
| bing_videos | Bing Videos search | q, mkt, count |
| bing_copilot | Bing Copilot results | q, cc |
| bing_maps | Bing Maps search | q, cp, lat, lon |
| bing_product | Bing Product results | product_id, mkt |
| bing_reverse_image | Bing reverse image search | image_url, cc, mkt |

## DuckDuckGo (4 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| duckduckgo | Main DuckDuckGo Search results | q, kl, l |
| duckduckgo_light | Fast DuckDuckGo Search results | q, kl, l |
| duckduckgo_maps | DuckDuckGo Maps results | q, kl, l, lat, lon |
| duckduckgo_news | DuckDuckGo News results | q, kl, l, df |

## Yahoo, Yandex, Baidu, Naver (15 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| yahoo | Yahoo! Search results | p, vc, pz |
| yahoo_images | Yahoo! Images results | p, pz, imgtype |
| yahoo_shopping | Yahoo! Shopping results | p, pz, sort |
| yahoo_videos | Yahoo! Videos results | p, pz, duration |
| yandex | Yandex Search results | text, lr, p |
| yandex_images | Yandex Images results | text, lr, p |
| yandex_videos | Yandex Videos search | text, lr, duration |
| baidu | Baidu Search results | wd, pn, rn |
| baidu_news | Baidu News search | word, pn, rn |
| naver | Naver Search results | query, where, start |
| naver_ai_overview | Naver AI Overview results | query |
| naver_images | Naver Images results | query, where, start |
| naver_news | Naver News results | query, where, start |
| naver_shopping | Naver Shopping results | query, where, start |
| naver_videos | Naver Videos results | query, where, start |

## Shopping (13 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| amazon | Amazon product search | field-keywords, page, sort |
| amazon_product | Amazon product details | asin |
| amazon_reviews | Amazon product reviews | asin |
| ebay | eBay product search | _nkw, _pgn, _sop |
| ebay_product | eBay product details | item_id, epid |
| ebay_deals | eBay deals results | q |
| walmart | Walmart product search | query, page, sort |
| walmart_product | Walmart product details | product_id |
| walmart_reviews | Walmart product reviews | product_id, page |
| walmart_product_sellers | Walmart product sellers | product_id |
| home_depot | Home Depot product search | q, page, sort |
| home_depot_product | Home Depot product details | product_id |
| home_depot_product_reviews | Home Depot product reviews | product_id, page |

## Travel & Local (7 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| tripadvisor | Tripadvisor results | q, location_id |
| tripadvisor_place | Tripadvisor place details | data_id |
| yelp | Yelp business search | find_desc, find_loc |
| yelp_place | Yelp business details | place_id |
| yelp_reviews | Yelp reviews | place_id, start |
| opentable | OpenTable results | q, metroId |
| open_table_reviews | OpenTable reviews | restaurant_id |

## Media (9 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| youtube | YouTube Search results | search_query, sp |
| youtube_video | YouTube video details | v, video_id |
| youtube_video_transcript | YouTube video transcript | v, lang |
| youtube_channel | YouTube channel results | channel_id, sp |
| youtube_playlist | YouTube playlist results | list, sp |
| youtube_shorts | YouTube shorts results | search_query, sp |
| apple_app_store | Apple App Store results | term, country |
| apple_product | Apple App Store product details | id, country |
| apple_reviews | Apple App Store reviews | id, country |

## Academic (5 engines)

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| google_scholar | Google Scholar results | q, as_ylo, as_yhi |
| google_scholar_author | Google Scholar author profile | author_id |
| google_scholar_cite | Google Scholar citation export | q |
| google_patents | Google Patents results | q, patent_number |
| google_patents_details | Patent details | patent_id |

## Other

| Engine | Description | Key Parameters |
|--------|-------------|----------------|
| facebook_profile | Facebook public profile data | profile_id |

---
See [serpapi.com/pricing](https://serpapi.com/pricing) for details on credit costs. Light endpoint names and usage are covered in [SKILL.md](../../skills/serpapi-search/SKILL.md#light-endpoints).

