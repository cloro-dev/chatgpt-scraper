# ChatGPT Scraper

[![ChatGPT scraper by cloro](https://github.com/cloro-dev/chatgpt-scraper/blob/main/chatgpt-scraper-hero-image.png)](https://cloro.dev/chatgpt/?utm_source=github)

[![cloro](https://img.shields.io/badge/Powered%20by-cloro-blue?style=for-the-badge)](https://cloro.dev/)

The [ChatGPT Scraper](https://cloro.dev/chatgpt/) by cloro lets developers programmatically interact with ChatGPT and collect AI-generated responses along with structured metadata. Instead of manual data collection, you can retrieve results as parsed JSON, raw HTML, or other formats for integration into your workflows.

You can use cloro's ChatGPT Scraper for AI research, content generation automation, competitive analysis, and conversation monitoring. It handles dynamic AI-generated content, supports real-time extraction, and removes the need to manage authentication, sessions, or anti-bot systems.

## How it works

The ChatGPT scraper handles the rendering, parsing, and delivery of results in your requested format. You provide your prompt, API credentials, and optional parameters as shown below.

### Request sample (Python)

```python
import json
import requests

# API parameters
payload = {
    'prompt': 'Compare the top 3 programming languages for web development in 2025',
    'country': 'US',
    'include': {
        'markdown': True,
        'rawResponse': True,
        'searchQueries': True,
        'ads': True
    }
}

# Get a response
response = requests.post(
    'https://api.cloro.dev/v1/monitor/chatgpt',
    headers={'Authorization': 'Bearer YOUR_API_KEY'},
    json=payload
)

# Print response to stdout
print(response.json())

# Save response to a JSON file
with open('response.json', 'w') as file:
    json.dump(response.json(), file, indent=2)
```

### Request sample (cURL)

```bash
curl -X POST https://api.cloro.dev/v1/monitor/chatgpt \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Compare the top 3 programming languages for web development in 2025",
    "country": "US",
    "include": {
      "markdown": true,
      "rawResponse": true,
      "searchQueries": true,
      "ads": true
    }
  }'
```

### Request sample (Node.js)

```javascript
const axios = require("axios");

const payload = {
  prompt: "Compare the top 3 programming languages for web development in 2025",
  country: "US",
  include: {
    markdown: true,
    rawResponse: true,
    searchQueries: true,
    ads: true,
  },
};

axios
  .post("https://api.cloro.dev/v1/monitor/chatgpt", payload, {
    headers: {
      Authorization: "Bearer YOUR_API_KEY",
      "Content-Type": "application/json",
    },
  })
  .then((response) => {
    console.log(response.data);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

### Request parameters

| Parameter               | Description                                                                    | Default value |
| ----------------------- | ------------------------------------------------------------------------------ | ------------- |
| `prompt`\*              | The prompt or question to send to ChatGPT (1-10,000 characters)                | –             |
| `country`               | Optional country/region code for localized monitoring (e.g., `US`, `GB`, `DE`) | –             |
| `state`                 | Optional US state code for state-level geo-targeting (e.g., `CA`, `TX`, `NY`). Only valid when `country` is `US`. See [supported codes](https://api.cloro.dev/v1/states?country=US). | –             |
| `include.markdown`      | Include response in Markdown format when set to true                           | `false`       |
| `include.rawResponse`   | Include raw streaming response events for debugging                            | `false`       |
| `include.searchQueries` | Include query fan-out ChatGPT used to generate response                        | `false`       |
| `include.ads`           | Include ads displayed in ChatGPT response when set to true                     | `false`       |
| `include.shopping`      | Include shopping cards and inline products with pricing and offers             | `false`       |
| `include.html`          | Include URL to full HTML response when set to true (URL expires after 24h)     | `false`       |

\* Mandatory parameters

**Note:** Enabling any of `rawResponse`, `searchQueries`, `ads`, or `shopping` (or any combination) adds +2 credits to the base cost.

---

### Output samples

The ChatGPT Scraper API returns either an HTML document or a structured JSON object containing ChatGPT's response and metadata.

**Structured JSON output snippet:**

```json
{
  "status": "success",
  "result": {
    "model": "gpt-5-3-mini",
    "text": "When comparing programming languages for web development in 2025, three languages stand out...",
    "markdown": "**When comparing programming languages for web development in 2025**, three languages stand out...",
    "html": "https://storage.cloro.dev/results/c45a5081-808d-4ed3-9c86-e4baf16c8ab8/page-1.html",  // URL expires after 24 hours
    "shoppingCards": [
      {
        "tags": ["electronics", "deals"],
        "products": [
          {
            "title": "Example Product",
            "url": "https://example.com/product",
            "price": "$57.00",
            "featured_tag": "electronics",
            "merchant": "Example Store + others",
            "image_urls": ["https://example.com/image.jpg"],
            "rating": 4.5,
            "num_reviews": 128,
            "id": "prod_123",
            "offers": [
              {
                "merchant_name": "Example Store",
                "product_name": "Example Product",
                "url": "https://example.com/offer",
                "price": "$57.00",
                "details": "In stock, ships in 2-3 days",
                "available": true,
                "checkoutable": true,
                "price_details": {
                  "base": "$57.00",
                  "total": "$57.00"
                },
                "tag": {
                  "text": "Best price",
                  "type": "promotion"
                }
              }
            ],
            "rating_grouped_citation": {
              "title": "Product Reviews",
              "url": "https://example.com/reviews",
              "supporting_websites": ["example.com"]
            }
          }
        ]
      }
    ],
    "searchQueries": ["web development languages 2025"],
    "rawResponse": [...]
  }
}
```

Alternatively, you can extract the data in Markdown format for easier integration with AI tools and documentation workflows.

## Shopping cards

cloro's ChatGPT scraper extracts shopping cards when ChatGPT returns product or commercial information. Set `include.shopping: true` in your request to include shopping cards in the response.

### Shopping card features

- **Product data:** Each shopping card includes product titles, prices, ratings, reviews, images, and merchant information
- **Multiple offers:** Compare prices across different merchants for the same product
- **Rating citations:** Track the source of product ratings and supporting websites
- **Promotional tags:** Identify special offers like "Best price" or promotional deals
- **Stock information:** Check product availability and delivery details

### Use cases for shopping cards

- **E-commerce monitoring:** Track how ChatGPT surfaces and recommends products
- **Price comparison:** Monitor pricing across different merchants and marketplaces
- **Product research:** Extract structured product data for competitive analysis
- **Marketing intelligence:** Understand which products ChatGPT highlights in responses

## Inline products

Inline products are individual product references that appear embedded in ChatGPT's response text (in comparison tables, featured recommendations, or inline mentions). Unlike shopping cards which group multiple products together, inline products are stand-alone product references with detailed data.

To include inline products in your response, set `include.shopping: true` in your request.

### Inline product features

- **Pricing and offers:** Each inline product includes current pricing and offers from multiple merchants
- **Product images:** Image URLs for product display
- **Ratings:** Aggregated rating scores and review counts
- **Rendering hints:** Display guidance with `render_as` field ("inline", "hero", or "block")

### Use cases for inline products

- **Product monitoring:** Track individual product recommendations across ChatGPT responses
- **Competitive intelligence:** Monitor how ChatGPT recommends specific products
- **Price tracking:** Track pricing and merchant availability for individual products

## Citation pills

Citation pills are inline citations that appear within ChatGPT responses when it references specific sources. The `markdown` field contains these citations as inline links, while the `text` field contains plain text without links.

**Example response with citation pills:**

```json
{
  "success": true,
  "result": {
    "text": "**ChatGPT** — Most versatile for writing, reasoning, and general problem-solving.\n**Claude** — Strong focus on safety and ethical reasoning.",
    "markdown": "**ChatGPT** — Most versatile for writing, reasoning, and general problem-solving. [Top 10 Best AI Apps in 2025](https://www.top10.com/best-lists/best-ai-apps)\n**Claude** — Strong focus on safety and ethical reasoning. [Best AI Platforms Compared](https://www.godofprompt.ai/blog/best-ai)",
    "citationPills": [
      {
        "label": "Top 10 Best AI Apps in 2025",
        "citationPillId": 0,
        "url": "https://www.top10.com/best-lists/best-ai-apps",
        "domain": "top10.com",
        "description": "A comprehensive guide to the best AI applications available today",
        "position": 1,
        "type": "searchResult",
        "datePublished": "2025-01-15"
      },
      {
        "label": "Best AI Platforms Compared",
        "citationPillId": 1,
        "url": "https://www.godofprompt.ai/blog/best-ai",
        "domain": "godofprompt.ai",
        "description": "Expert comparison of leading AI platforms and their capabilities",
        "position": 2,
        "type": "searchResult",
        "datePublished": "2025-02-01"
      }
    ]
  }
}
```

### Citation pill structure

| Field            | Type    | Description                                                                                                                                          |
| ---------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `label`          | string  | Per-source title of the citation. Always present; may be an empty string when the citation event ships no title                                      |
| `citationPillId` | integer | 0-based identifier shared by all entries from the same chip. **Note:** other providers use 1-based IDs; ChatGPT starts at 0 for historical reasons   |
| `url`            | string  | The URL of the citation source                                                                                                                       |
| `domain`         | string  | Domain of the citation source                                                                                                                        |
| `description`    | string  | Description of the citation content. Omitted when the citation event carries no snippet                                                              |
| `position`       | integer | 1-based position of this source in the sibling `result.sources` array                                                                                |
| `type`           | string  | _ChatGPT-only._ Discriminator: `searchResult` for inline search citations, `groupedWebpage` for grouped-webpage cards                                |
| `datePublished`  | string  | _ChatGPT-only._ ISO 8601 date when the source was published. Omitted when absent                                                                     |

## Business and location data (Map entries)

When ChatGPT returns business or place information (restaurants, stores, services, etc.), cloro automatically extracts business data from providers like Yelp and Google Business. No additional parameters are required. Map entries are included by default when available in the response.

**Example response with map entries:**

```json
{
  "success": true,
  "result": {
    "text": "Here are some highly-rated laundromats in Portland...",
    "map": [
      {
        "id": "rVKID2OBQMyVpraqSjz9sg",
        "provider": "yelp",
        "name": "Spin Laundry Lounge",
        "position": 1,
        "address": "750 N Fremont St, Portland, OR 97227",
        "city": "Portland",
        "state": "OR",
        "zipcode": "97227",
        "countryCode": "US",
        "latitude": 45.54764,
        "longitude": -122.67432,
        "rating": 4.2,
        "reviewCount": 381,
        "ratingScale": 5,
        "categories": ["Laundromat"],
        "price": 2,
        "priceStr": "$$",
        "phone": "+15032844794",
        "websiteUrl": "https://spinlaundrylounge.com",
        "providerUrl": "https://www.yelp.com/biz/spin-laundry-lounge-portland",
        "hours": [
          { "day": 1, "start": "0700", "end": "2100" },
          { "day": 2, "start": "0700", "end": "2100" }
        ],
        "isOpen": true,
        "isClosedPermanently": false,
        "attributes": {
          "wi_fi": "free",
          "business_accepts_credit_cards": true
        }
        // ... 47 total fields with comprehensive business data
      }
    ]
  }
}
```

### Map entry features

- **Mapped data:** Each map entry contains structured business data from Yelp or Google Business with 47 fields using camelCase naming
- **Location:** Full address with GPS coordinates, city, state, country code, and distance from user
- **Business hours:** Weekly schedule with next opening times and special hours
- **Attributes:** Parking options, WiFi availability, accepted payment methods, and accessibility features
- **Multiple images:** Business photos and provider-hosted images
- **Reviews and ratings:** Aggregate ratings with review counts, highlights, and reviews

## Ads

Ads are automatically extracted when ChatGPT displays advertising content in responses. To include ads in your response, set `include.ads: true` in your request.

### Ad extraction features

- **Brand information:** Each ad includes the advertiser's brand name, URL, and favicon
- **Carousel cards:** Multiple promotional cards with titles, descriptions, images, and destination URLs
- **Attribution tracking:** All URLs include ChatGPT attribution parameters
- **Opt-in extraction:** Ads are only extracted when explicitly requested

**Example response with ads:**

```json
{
  "success": true,
  "result": {
    "text": "Here are some excellent running shoes...",
    "ads": [
      {
        "brand": {
          "name": "Acme Shoes",
          "url": "https://www.acmeshoes.com?utm_source=chatgpt.com",
          "favicon": "https://images.openai.com/favicon.ico"
        },
        "cards": [
          {
            "title": "Premium Running Shoes",
            "body": "Lightweight performance running shoes with advanced cushioning technology",
            "url": "https://www.acmeshoes.com/running?utm_source=chatgpt.com",
            "image": "https://images.openai.com/product.jpg"
          },
          {
            "title": "Trail Running Shoes",
            "body": "Durable shoes designed for off-road running with superior grip",
            "url": "https://www.acmeshoes.com/trail?utm_source=chatgpt.com",
            "image": "https://images.openai.com/product2.jpg"
          }
        ]
      }
    ]
  }
}
```

### Ad structure

| Field   | Description                                         | Type   |
| ------- | --------------------------------------------------- | ------ |
| `brand` | Advertiser brand information (name, url, favicon)   | object |
| `cards` | Array of carousel cards promoting products/services | array  |

### Brand structure

| Field     | Description                             | Type   |
| --------- | --------------------------------------- | ------ |
| `name`    | Advertiser brand name                   | string |
| `url`     | Advertiser brand URL with attribution   | string |
| `favicon` | Advertiser brand favicon URL (optional) | string |

### Card structure

| Field   | Description                           | Type   |
| ------- | ------------------------------------- | ------ |
| `title` | Card title/heading                    | string |
| `body`  | Card description text                 | string |
| `url`   | Card destination URL with attribution | string |
| `image` | Card image URL (optional)             | string |

### Use cases for ads

- **Ad monitoring:** Track advertising content displayed in ChatGPT responses
- **Competitive analysis:** Monitor which brands advertise in your industry
- **Marketing intelligence:** Understand ad placement and messaging strategies
- **Brand tracking:** Monitor your own brand's ad appearances in ChatGPT

## Advanced features

### Query fan-out insights

When you set `include.searchQueries` to `true`, cloro reveals the query fan-out ChatGPT used internally to generate its response. This provides insight into:

- How ChatGPT interprets and breaks down your prompt
- What specific searches it performs to gather information
- How to optimize your prompts for better results

### Raw response access

Enable `include.rawResponse` to access the full streaming response payload. This is useful for:

- Debugging and analysis
- Understanding response generation timing
- Accessing low-level response metadata

**Note:** Enabling any of `rawResponse`, `searchQueries`, or `ads` adds +2 credits to the base cost (total +2 credits, not per feature).

## JSON output structure

The structured ChatGPT scraper output includes fields such as `result.model`, `result.text`, and more. The tables below break down the elements returned, along with descriptions and data types.

**Note:** The number of items and fields may vary depending on the submitted prompt and whether shopping cards are available in the response.

### Main response fields

| Field                   | Description                                                                               | Type   |
| ----------------------- | ----------------------------------------------------------------------------------------- | ------ |
| `status`                | Status of the monitoring request (e.g., `success`)                                        | string |
| `result.model`          | The ChatGPT model used to generate the response                                           | string |
| `result.text`           | The complete ChatGPT response as plain text                                               | string |
| `result.markdown`       | The response formatted in Markdown with inline citation links                             | string |
| `result.shoppingCards`  | Array of shopping/product cards extracted from response (when `include.shopping` is true) | array  |
| `result.inlineProducts` | Array of inline products with pricing and offers (when `include.shopping` is true)        | array  |
| `result.map`            | Array of business/location data with 47 structured fields (when available)                | array  |
| `result.citationPills`  | Array of inline citation metadata (corresponds to links in markdown)                      | array  |
| `result.ads`            | Array of ads displayed in ChatGPT response (when `include.ads` is true)                   | array  |
| `result.searchQueries`  | Query fan-out ChatGPT used to generate response (when `include.searchQueries` is true)    | array  |
| `result.rawResponse`    | Array of ChatGPT's streamed response events (when `include.rawResponse` is true)          | array  |

### Shopping cards structure

When available, each shopping card in `result.shoppingCards[]` contains the following fields:

| Field                                | Description                                       | Type    |
| ------------------------------------ | ------------------------------------------------- | ------- |
| `tags`                               | Category tags for the shopping card               | array   |
| `products`                           | Array of product information objects              | array   |
| `products[].title`                   | Product name                                      | string  |
| `products[].url`                     | Product page URL with ChatGPT attribution         | string  |
| `products[].price`                   | Current price                                     | string  |
| `products[].featured_tag`            | Product category or style tag                     | string  |
| `products[].merchant`                | Merchant information                              | string  |
| `products[].image_urls`              | Array of product image URLs                       | array   |
| `products[].rating`                  | Product rating (0-5 scale)                        | float   |
| `products[].num_reviews`             | Number of reviews                                 | integer |
| `products[].id`                      | Unique product identifier                         | string  |
| `products[].offers`                  | Array of shopping offers from different merchants | array   |
| `products[].rating_grouped_citation` | Rating source information                         | object  |

### Product offers structure

Each offer in `products[].offers[]` contains:

| Field           | Description                               | Type    |
| --------------- | ----------------------------------------- | ------- |
| `merchant_name` | Merchant name                             | string  |
| `product_name`  | Product name as listed by merchant        | string  |
| `url`           | Offer URL with ChatGPT attribution        | string  |
| `price`         | Offer price                               | string  |
| `details`       | Stock and delivery information            | string  |
| `available`     | Offer availability status                 | boolean |
| `checkoutable`  | Whether offer can be checked out directly | boolean |
| `price_details` | Detailed price breakdown (base, total)    | object  |
| `tag`           | Promotional tag (e.g., "Best price")      | object  |

### Rating citation structure

The `products[].rating_grouped_citation` object contains:

| Field                 | Description                            | Type   |
| --------------------- | -------------------------------------- | ------ |
| `title`               | Source title                           | string |
| `url`                 | Source URL                             | string |
| `supporting_websites` | Array of supporting website references | array  |

### Inline products structure

Each inline product in `result.inlineProducts[]` contains:

| Field       | Description                                                     | Type   |
| ----------- | --------------------------------------------------------------- | ------ |
| `product`   | Complete product information (same structure as shopping cards) | object |
| `render_as` | Rendering hint: "inline", "hero", or "block"                    | string |

### Ads structure

When `include.ads` is set to `true`, each ad in `result.ads[]` contains:

| Field           | Description                           | Type   |
| --------------- | ------------------------------------- | ------ |
| `brand`         | Advertiser brand information          | object |
| `brand.name`    | Advertiser brand name                 | string |
| `brand.url`     | Advertiser brand URL with attribution | string |
| `brand.favicon` | Advertiser brand favicon URL          | string |
| `cards`         | Array of carousel cards               | array  |
| `cards[].title` | Card title/heading                    | string |
| `cards[].body`  | Card description text                 | string |
| `cards[].url`   | Card destination URL with attribution | string |
| `cards[].image` | Card image URL (optional)             | string |

## Practical ChatGPT scraper use cases

1. **E-commerce tracking:** Monitor product recommendations and pricing with automatic shopping card extraction.
2. **Local business monitoring:** Track how ChatGPT surfaces and recommends businesses with location data including hours, ratings, and amenities.
3. **AI research and benchmarking:** Analyze ChatGPT's responses with raw response data and query fan-out insights.
4. **Content generation at scale:** Automate content creation with structured, monitored responses.
5. **Competitive intelligence:** Track how ChatGPT presents information about your brand, products, or competitors.
6. **Data enrichment:** Add AI-generated insights to datasets in multiple formats (text, Markdown).
7. **SEO and visibility monitoring:** Understand how ChatGPT sources and presents information in your industry.

## Why choose cloro?

- **Simple integration:** API design with documentation and examples.
- **Reliable performance:** >99% uptime and low latencies (P50 < 60s, P90 < 120s)
- **No infrastructure hassle:** We handle rate limiting and browser management.
- **Flexible pricing:** Low-cost subscription model with transparent pricing and volume discount.
- **Developer support:** Responsive support team to help with integration and troubleshooting.

## FAQ

### Is scraping ChatGPT allowed?

Any website is legal to be scraped as long as the information is publicly accessible.

### What makes cloro's ChatGPT scraper unique?

cloro's ChatGPT endpoint offers:

- **Automatic shopping card extraction** for e-commerce data
- **Structured ad extraction** for monitoring advertising content
- **Query fan-out insights** to see ChatGPT's internal search queries
- **Raw response access** for debugging and analysis

### What's the recommended timeout for requests?

We don't recommend putting any timeout, given that our system retries automatically. We recommend setting up a retry mechanism in case of failure.

### How does credit usage work?

The request with Web Search uses 5 credits. Additional features have extra costs:

- `include.rawResponse`, `include.searchQueries`, `include.ads`, and/or `include.shopping`: +2 credits total (enabling any one or all of these adds +2 credits)

### Do shopping cards require extra credits?

Shopping cards are opt-in via `include.shopping: true`. Enabling `shopping` adds +2 credits to the base cost (combined with any of `rawResponse`, `searchQueries`, or `ads`).

## Learn more

For detailed documentation, advanced features, and integration guides, visit:

- **API documentation:** [cloro.dev/docs](https://cloro.dev/docs/)
- **ChatGPT scraper page:** [cloro.dev/chatgpt](https://cloro.dev/chatgpt/)

## Other available scrapers

- **[AI Mode](https://cloro.dev/ai-mode/)** - Extracts structured data from Google AI Mode for general knowledge queries, workflow optimization, and technical guidance.
- **[AI Overview](https://cloro.dev/ai-overview/)** - Extracts structured data from Google AI Overview for search result analysis and AI-curated insights.
- **[ChatGPT](https://cloro.dev/chatgpt/)** - Extracts structured data from ChatGPT with features including shopping cards, raw response data, and query fan-out.
- **[Copilot](https://cloro.dev/copilot/)** - Extracts structured data from Microsoft Copilot for development tools, Microsoft ecosystem research, and enterprise-focused queries.
- **[Gemini](https://cloro.dev/gemini/)** - Extracts structured data from Google Gemini for complex reasoning, content generation, and source confidence scoring.
- **[Google Search](https://cloro.dev/google-search/)** - Extracts structured data from Google Search results, including organic results, People Also Ask questions, related searches, and optional AI Overview data.
- **[Google News](https://cloro.dev/google-news/)** - Extracts structured news articles from Google News with titles, snippets, sources, dates, and thumbnail images for news monitoring and media tracking.
- **[Grok](https://cloro.dev/grok/)** - Extracts structured data from Grok for current events, news tracking, and real-time information gathering.
- **[Perplexity](https://cloro.dev/perplexity/)** - Extracts structured data from Perplexity AI with real-time web sources, automatically detecting and extracting data objects.

## Contact us

If you have questions or need support, reach out to us at [support@cloro.dev](mailto:support@cloro.dev).

---

Built with ❤️ by the cloro team
