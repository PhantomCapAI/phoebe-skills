---
name: brave-search
description: Web search using Brave Search API. Superior to DDG for structured results, news, and freshness. Use env var BRAVE_API_KEY.
---

# Brave Search API

Fast, high-quality web search. Better than DDG for recent results and structured data.

## Search the Web

```
web_fetch(
  url="https://api.search.brave.com/res/v1/web/search?q=QUERY&count=10",
  headers={"X-Subscription-Token": "$BRAVE_API_KEY", "Accept": "application/json"},
  extractMode="text",
  maxChars=15000
)
```

URL-encode the query. Use `+` for spaces.

## Parameters

| Param | Description |
|-------|-------------|
| `q` | Search query (required) |
| `count` | Results per page (1-20, default 10) |
| `offset` | Pagination offset |
| `freshness` | `pd` (past day), `pw` (past week), `pm` (past month), `py` (past year) |
| `country` | 2-letter code: `us`, `gb`, `de`, `au` |

## Examples

### Recent news
```
?q=solana+defi+news&count=5&freshness=pd
```

### Specific region
```
?q=crypto+regulations&country=us&count=10
```

## Response Format

JSON with `web.results[]` array. Each result has:
- `title` — page title
- `url` — page URL
- `description` — snippet
- `age` — how old the result is

## Search-then-Read Pattern

1. Search Brave for URLs
2. Pick best results
3. `web_fetch` each URL for full content

## When to Use

- **Use Brave** for: news, recent events, structured data, when freshness matters
- **Use DDG** as fallback if Brave fails or for quick lookups
- **Use Tavily** for AI-summarized research answers
