---
name: firecrawl
description: Web scraping and crawling via Firecrawl API. Extracts clean markdown from any URL. Crawls entire sites. Use env var FIRECRAWL_API_KEY.
---

# Firecrawl — Web Scraping & Crawling

Extracts clean, LLM-ready content from any webpage. Handles JavaScript rendering, anti-bot measures, and outputs clean markdown.

## Scrape a Single Page

```bash
exec curl -s -X POST https://api.firecrawl.dev/v1/scrape \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/page",
    "formats": ["markdown"]
  }'
```

Returns `data.markdown` with clean content.

## Crawl an Entire Site

```bash
exec curl -s -X POST https://api.firecrawl.dev/v1/crawl \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com",
    "limit": 10,
    "scrapeOptions": {"formats": ["markdown"]}
  }'
```

Returns a job ID. Check status:

```bash
exec curl -s https://api.firecrawl.dev/v1/crawl/JOB_ID \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY"
```

## Extract Structured Data

```bash
exec curl -s -X POST https://api.firecrawl.dev/v1/scrape \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/pricing",
    "formats": ["extract"],
    "extract": {
      "prompt": "Extract all pricing tiers with names, prices, and features"
    }
  }'
```

## When to Use

- **Scrape**: Read a specific page as clean markdown (articles, docs, product pages)
- **Crawl**: Map an entire site (competitor analysis, content audit)
- **Extract**: Pull structured data from pages (prices, contacts, specs)
- **Prefer over web_fetch** when: page uses JavaScript, has anti-bot, or you need clean markdown
