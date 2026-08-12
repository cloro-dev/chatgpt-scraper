# ChatGPT Scraper

[![ChatGPT Scraper by cloro](https://github.com/cloro-dev/chatgpt-scraper/blob/main/chatgpt-scraper-hero-image.png)](https://cloro.dev/chatgpt/?utm_source=github)

[![cloro](https://img.shields.io/badge/Powered%20by-cloro-blue?style=for-the-badge)](https://cloro.dev/)

The [ChatGPT scraper](https://cloro.dev/chatgpt/?utm_source=github) by cloro returns what the ChatGPT web interface shows, as structured JSON: answer text and markdown, cited sources, citation pills, shopping cards, ads, inline products and the query fan-out terms behind the answer.

## How do you scrape ChatGPT?

1. Get an API key at [cloro.dev](https://cloro.dev/?utm_source=github&utm_medium=readme).
2. POST a prompt to `https://api.cloro.dev/v1/monitor/chatgpt`.
3. Read the parsed fields from the JSON response.

The official OpenAI API cannot answer brand-monitoring questions, because citations, shopping cards and the decision to search the web rather than answer from memory exist only in the web UI. Reading that UI means getting past Cloudflare JA4 TLS fingerprinting and Turnstile, assembling a Server-Sent Events stream, and tracking CSS class names that change between deploys. cloro handles all of it server-side.

### Request sample (Python)

```python
import requests

payload = {
    'prompt': 'best project management software for remote teams',
    'country': 'US',
    'include': {'markdown': True, 'searchQueries': True},
}

response = requests.post(
    'https://api.cloro.dev/v1/monitor/chatgpt',
    headers={'Authorization': 'Bearer YOUR_API_KEY'},
    json=payload,
)

print(response.json())
```

### Request sample (cURL)

```bash
curl -X POST https://api.cloro.dev/v1/monitor/chatgpt \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": "best running shoes for flat feet", "country": "US", "include": {"shopping": true}}'
```

Node.js and async/webhook examples are in the [endpoint documentation](https://cloro.dev/docs/api-reference/endpoint/monitor-chatgpt).

### Request parameters

| Parameter | Description | Default |
| --- | --- | --- |
| `prompt`\* | The query or question (1-10,000 characters) | – |
| `country` | Country code for localized results (`US`, `GB`, `DE`) | `US` |
| `state` | US state code for finer localization | – |
| `include.markdown` | Return the answer as Markdown | `false` |
| `include.html` | Return a URL to the full HTML (expires after 24h) | `false` |
| `include.rawResponse` | Return the unparsed upstream payload | `false` |
| `include.searchQueries` | Return the query fan-out terms ChatGPT searched | `false` |
| `include.shopping` | Return shopping cards and inline products | `false` |
| `include.ads` | Return sponsored blocks | `false` |

\* Required

## What data does the ChatGPT scraper return?

```json
{
  "success": true,
  "result": {
    "model": "gpt-5",
    "text": "For remote teams, the strongest options are...",
    "citationPills": [{ "citationPillId": "a1b2", "label": "Asana", "url": "https://asana.com", "domain": "asana.com" }],
    "shoppingCards": [{ "title": "Standing Desk", "price": { "value": 499.0, "currency": "$" }, "merchant_name": "Wayfair", "checkoutable": true }],
    "searchQueries": ["best project management software 2026", "asana vs monday remote teams"],
    "markdown": "For remote teams, the strongest options are **Asana**..."
  }
}
```

Alongside `text` and `markdown`:

1. **`citationPills`** — the inline citation chips, one entry per cited source, sharing a `citationPillId` when a pill cites several.
2. **`searchQueries`** — the query fan-out. ChatGPT decomposes one prompt into several searches, and these are the terms it actually ran, which is the clearest signal of how it read the question.
3. **`shoppingCards`** — product cards with price, merchant, availability and whether the item is checkoutable in-chat.
4. **`inlineProducts`** — products embedded in the answer text, separate from the carousels.
5. **`ads`** — sponsored blocks, parsed.
6. **`map`** — business and location entries with rating, reviews and address.
7. **`rawResponse`** — the unparsed upstream payload, when you want to parse it yourself.

Full field-level schemas are in the [endpoint reference](https://cloro.dev/docs/api-reference/endpoint/monitor-chatgpt).

## Use cases

- **Brand monitoring** — whether ChatGPT names you, and which sources it cites when it does.
- **Competitive intelligence** — who else is named on the prompts your buyers ask.
- **Shopping and commerce** — track how products surface in ChatGPT's shopping cards.
- **Query fan-out research** — see the sub-queries behind an answer, which is what you can actually optimize for.

## FAQ

### Why not use the official OpenAI API?

It returns raw model output. It does not browse, cite sources, render shopping cards, or decide per-query whether to search the web. If you are measuring what users see, the API is the wrong surface.

### Is scraping ChatGPT allowed?

cloro reads publicly visible responses from its own sessions. Do not use it to access other users' conversations or private data, and check your own jurisdiction and terms.

### How often do the selectors break?

OpenAI ships UI changes roughly weekly, and dynamic CSS class names change between deploys. That maintenance is the point of a managed endpoint; a DIY scraper returns empty results rather than errors when it breaks, so alert on success rate rather than exceptions.

### What is the recommended timeout?

60 seconds. Responses stream, so a long answer takes noticeably longer than a static page fetch. Use the async endpoint for batches.

## Learn more

- **Endpoint reference:** [cloro.dev/docs](https://cloro.dev/docs/api-reference/endpoint/monitor-chatgpt)
- **Product page:** [cloro.dev/chatgpt](https://cloro.dev/chatgpt/)

## Other cloro scrapers

[AI Mode](https://cloro.dev/ai-mode/) · [AI Overview](https://cloro.dev/ai-overview/) · [Copilot](https://cloro.dev/copilot/) · [Gemini](https://cloro.dev/gemini/) · [Google Search](https://cloro.dev/google-search/) · [Google News](https://cloro.dev/google-news/) · [Grok](https://cloro.dev/grok/) · [Perplexity](https://cloro.dev/perplexity/)

## Contact us

Questions or support: [r/cloroapi](https://www.reddit.com/r/cloroapi/).
