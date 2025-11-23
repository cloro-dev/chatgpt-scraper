# ChatGPT Scraper

[![ChatGPT scraper by cloro](https://github.com/cloro-dev/chatgpt-scraper/blob/main/chatgpt-scraper-hero-image.png)](https://cloro.dev/chatgpt/?utm_source=github.com)

[![cloro](https://img.shields.io/badge/Powered%20by-Cloro-blue?style=for-the-badge)](https://cloro.dev/)

The [ChatGPT Scraper](https://cloro.dev/chatgpt/) by cloro enables developers to programmatically interact with ChatGPT and automatically collect AI-generated responses along with structured metadata. Instead of manual data collection, you can retrieve results as parsed JSON, raw HTML, or other formats for seamless integration into your workflows.

You can use cloro's ChatGPT Scraper for AI research, content generation automation, competitive analysis, and conversation monitoring. It handles dynamic AI-generated content, supports real-time extraction, and eliminates the need to manage authentication, sessions, or anti-bot systems.

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
        'searchQueries': True
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
      "searchQueries": true
    }
  }'
```

### Request sample (Node.js)

```javascript
const axios = require('axios');

const payload = {
  prompt: 'Compare the top 3 programming languages for web development in 2025',
  country: 'US',
  include: {
    markdown: true,
    rawResponse: true,
    searchQueries: true
  }
};

axios.post('https://api.cloro.dev/v1/monitor/chatgpt', payload, {
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
})
.then(response => {
  console.log(response.data);
})
.catch(error => {
  console.error('Error:', error);
});
```

**Note:** Set an appropriate timeout (e.g., 120-180 seconds) when making requests, as ChatGPT responses may take time to generate depending on complexity.

### Request parameters

| Parameter | Description | Default value |
|-----------|-------------|---------------|
| `prompt`* | The prompt or question to send to ChatGPT (1-10,000 characters) | – |
| `country` | Optional country/region code for localized monitoring (e.g., `US`, `GB`, `DE`) | – |
| `include.markdown` | Include response in Markdown format when set to true | `false` |
| `include.rawResponse` | Include raw streaming response events for debugging (+2 credits) | `false` |
| `include.searchQueries` | Include query fan-out ChatGPT used to generate response (+2 credits) | `false` |

\* Mandatory parameters

---

### Output samples

The ChatGPT Scraper API returns either an HTML document or a structured JSON object containing ChatGPT's response and metadata.

**Structured JSON output snippet:**

```json
{
  "status": "success",
  "result": {
    "model": "gpt-5-mini",
    "text": "When comparing programming languages for web development in 2025, three languages stand out...",
    "markdown": "**When comparing programming languages for web development in 2025**, three languages stand out...",
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
            "imageUrls": ["https://example.com/image.jpg"],
            "rating": 4.5,
            "numReviews": 128,
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

One of the unique features of cloro's ChatGPT scraper is automatic extraction of shopping cards when ChatGPT returns product or commercial information. No additional parameters are required - shopping cards are included by default when available in the response.

### Shopping card features

- **Comprehensive product data:** Each shopping card includes product titles, prices, ratings, reviews, images, and merchant information
- **Multiple offers:** Compare prices across different merchants for the same product
- **Rating citations:** Track the source of product ratings and supporting websites
- **Promotional tags:** Identify special offers like "Best price" or promotional deals
- **Stock information:** Check product availability and delivery details

### Use cases for shopping cards

- **E-commerce monitoring:** Track how ChatGPT surfaces and recommends products
- **Price comparison:** Monitor pricing across different merchants and marketplaces
- **Product research:** Extract structured product data for competitive analysis
- **Marketing intelligence:** Understand which products ChatGPT highlights in responses

## Advanced features

### Query fan-out insights

When you set `include.searchQueries` to `true` (+2 credits), cloro reveals the actual query fan-out ChatGPT used internally to generate its response. This provides valuable insight into:

- How ChatGPT interprets and breaks down your prompt
- What specific searches it performs to gather information
- How to optimize your prompts for better results

### Raw response access

Enable `include.rawResponse` (+2 credits) to access the complete streaming response payload. This is useful for:

- Advanced debugging and analysis
- Understanding response generation timing
- Accessing low-level response metadata

## JSON output structure

The structured ChatGPT scraper output includes fields such as  `result.model`, `result.text`, and more. The tables below break down the elements returned, along with descriptions and data types.

**Note:** The number of items and fields may vary depending on the submitted prompt and whether shopping cards are available in the response.

### Main response fields

| Field | Description | Type |
|-------|-------------|------|
| `status` | Status of the monitoring request (e.g., `success`) | string |
| `result.model` | The ChatGPT model used to generate the response | string |
| `result.text` | The complete ChatGPT response as plain text | string |
| `result.markdown` | The response formatted in Markdown (when `include.markdown` is true) | string |
| `result.shoppingCards` | Array of shopping/product cards extracted from response | array |
| `result.searchQueries` | Query fan-out ChatGPT used to generate response (when `include.searchQueries` is true) | array |
| `result.rawResponse` | Array of ChatGPT's streamed response events (when `include.rawResponse` is true) | array |

### Shopping cards structure

When available, each shopping card in `result.shoppingCards[]` contains the following fields:

| Field | Description | Type |
|-------|-------------|------|
| `tags` | Category tags for the shopping card | array |
| `products` | Array of product information objects | array |
| `products[].title` | Product name | string |
| `products[].url` | Product page URL with ChatGPT attribution | string |
| `products[].price` | Current price | string |
| `products[].featured_tag` | Product category or style tag | string |
| `products[].merchant` | Merchant information | string |
| `products[].imageUrls` | Array of product image URLs | array |
| `products[].rating` | Product rating (0-5 scale) | float |
| `products[].numReviews` | Number of reviews | integer |
| `products[].id` | Unique product identifier | string |
| `products[].offers` | Array of shopping offers from different merchants | array |
| `products[].rating_grouped_citation` | Rating source information | object |

### Product offers structure

Each offer in `products[].offers[]` contains:

| Field | Description | Type |
|-------|-------------|------|
| `merchant_name` | Merchant name | string |
| `product_name` | Product name as listed by merchant | string |
| `url` | Offer URL with ChatGPT attribution | string |
| `price` | Offer price | string |
| `details` | Stock and delivery information | string |
| `available` | Offer availability status | boolean |
| `checkoutable` | Whether offer can be checked out directly | boolean |
| `price_details` | Detailed price breakdown (base, total) | object |
| `tag` | Promotional tag (e.g., "Best price") | object |

### Rating citation structure

The `products[].rating_grouped_citation` object contains:

| Field | Description | Type |
|-------|-------------|------|
| `title` | Source title | string |
| `url` | Source URL | string |
| `supporting_websites` | Array of supporting website references | array |

## Practical ChatGPT scraper use cases

1. **E-commerce tracking:** Monitor product recommendations and pricing with automatic shopping card extraction.
2. **AI research and benchmarking:** Analyze ChatGPT's responses with raw response data and query fan-out insights.
3. **Content generation at scale:** Automate content creation with structured, monitored responses.
4. **Competitive intelligence:** Track how ChatGPT presents information about your brand, products, or competitors.
5. **Data enrichment:** Enhance datasets with AI-generated insights in multiple formats (text, Markdown).
6. **SEO and visibility monitoring:** Understand how ChatGPT sources and presents information in your industry.

## Why choose cloro?

- **Simple integration:** Clean API design with comprehensive documentation and examples.
- **Reliable performance:** >99% uptime and low latencies (P50 < 60s, P90 < 120s)
- **No infrastructure hassle:** We handle rate limiting and browser management.
- **Flexible pricing:** Low-cost subscription model with transparent pricing and volume discount.
- **Developer support:** Responsive support team to help with integration and troubleshooting.

## FAQ

### Is scraping ChatGPT allowed?

Any website is legal to be scraped as long as the information is publicly accessible.

### What makes cloro's ChatGPT scraper unique?

cloro's ChatGPT endpoint is the most reliable & comprehensive monitoring solution available, offering:
- **Automatic shopping card extraction** for e-commerce data
- **Query fan-out insights** to see ChatGPT's internal search queries
- **Raw response access** for advanced debugging and analysis

### What's the recommended timeout for requests?

We don't recommend putting any timeout, given that our system retries automatically. We recommend setting up a retry mechanism in case of failure.

### How does credit usage work?

The request with Web Search uses 5 credits. Additional features have extra costs:
- `include.rawResponse`: +2 credits per request
- `include.searchQueries`: +2 credits per request

### Do shopping cards require extra credits?

No, shopping cards are automatically extracted when available at no additional cost. They're included by default in the response structure.

## Learn more

For detailed documentation, advanced features, and integration guides, visit:

- **API documentation:** [docs.cloro.dev](https://docs.cloro.dev)
- **ChatGPT scraper page:** [cloro.dev/chatgpt](https://cloro.dev/chatgpt/)

## Other available scrapers

- **[AI Mode](https://cloro.dev/ai-mode/)** - Extracts structured data from Google AI Mode for general knowledge queries, workflow optimization, and technical guidance.
- **[AI Overview](https://cloro.dev/ai-overview/)** - Extracts structured data from Google AI Overview for comprehensive search result analysis and AI-curated insights.
- **[Gemini](https://cloro.dev/gemini/)** - Extracts structured data from Google Gemini for complex reasoning, content generation, and source confidence scoring.
- **[ChatGPT](https://cloro.dev/chatgpt/)** - Extracts structured data from ChatGPT with advanced features including shopping cards, raw response data, and query fan-out.
- **[Copilot](https://cloro.dev/copilot/)** - Extracts structured data from Microsoft Copilot for development tools, Microsoft ecosystem research, and enterprise-focused queries.
- **[Google](https://cloro.dev/google-search/)** - Extracts structured data from Google Search results, including organic results, People Also Ask questions, related searches, and optional AI Overview data.
- **[Perplexity](https://cloro.dev/perplexity/)** - Extracts comprehensive structured data from Perplexity AI with real-time web sources, automatically detecting and extracting rich data objects.

## Contact us

If you have questions or need support, reach out to us on [our contact page](https://cloro.dev/contact).

---

Built with ❤️ by the cloro team
