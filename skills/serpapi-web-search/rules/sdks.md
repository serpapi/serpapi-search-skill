# SerpApi SDKs

All official SDKs wrap the same `/search.json` endpoint. Use the SDK for your target language instead of raw HTTP.

## Python

```bash
pip install serpapi
```

```python
import serpapi

client = serpapi.Client(api_key="your_key_here")
results = client.search({"engine": "google_light", "q": "coffee"})
print(results["organic_results"])
```

Repo: [github.com/serpapi/serpapi-python](https://github.com/serpapi/serpapi-python)

## JavaScript / Node.js

```bash
npm install serpapi
```

```js
import SerpApi from "serpapi";

const client = new SerpApi.Client({ apiKey: "your_key_here" });
const results = await client.json({ engine: "google_light", q: "coffee" });
console.log(results.organic_results);
```

Repo: [github.com/serpapi/serpapi-javascript](https://github.com/serpapi/serpapi-javascript)

## Ruby

```bash
gem install serpapi
```

```ruby
require "serpapi"

client = SerpApi::Client.new(api_key: "your_key_here")
results = client.search(engine: "google_light", q: "coffee")
puts results[:organic_results]
```

Repo: [github.com/serpapi/serpapi-ruby](https://github.com/serpapi/serpapi-ruby)

## Go

```bash
go get github.com/serpapi/serpapi-golang
```

```go
import "github.com/serpapi/serpapi-golang"

client := serpapi.NewClient(serpapi.ClientConfig{APIKey: "your_key_here"})
results, _ := client.Search(map[string]string{"engine": "google_light", "q": "coffee"})
```

Repo: [github.com/serpapi/serpapi-golang](https://github.com/serpapi/serpapi-golang)

## PHP

```bash
composer require serpapi/serpapi
```

Repo: [github.com/serpapi/serpapi-php](https://github.com/serpapi/serpapi-php)

## Java

Repo: [github.com/serpapi/serpapi-java](https://github.com/serpapi/serpapi-java)

## .NET / C#

Repo: [github.com/serpapi/serpapi-dotnet](https://github.com/serpapi/serpapi-dotnet)

---

All SDKs accept the same parameters as the REST API. See [parameters.md](parameters.md) for the full parameter list and [serpapi.com/search-api](https://serpapi.com/search-api) for the canonical reference.
