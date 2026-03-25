---
name: tavily-search
description: AI-powered search via Tavily API. Returns summarized answers with sources — ideal for research questions. Use env var TAVILY_API_KEY.
---

# Tavily AI Search

AI-powered search that returns summarized answers with cited sources. Best for research queries where you need synthesized answers, not just links.

## Search

```bash
exec curl -s -X POST https://api.tavily.com/search \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "'$TAVILY_API_KEY'",
    "query": "YOUR QUERY HERE",
    "search_depth": "advanced",
    "include_answer": true,
    "max_results": 5
  }'
```

## Parameters

| Param | Description |
|-------|-------------|
| `query` | Search query (required) |
| `search_depth` | `basic` (fast) or `advanced` (thorough) |
| `include_answer` | `true` to get AI-generated summary |
| `max_results` | Number of sources (1-10) |
| `include_domains` | Array of domains to search within |
| `exclude_domains` | Array of domains to exclude |
| `topic` | `general` or `news` |

## Response Format

```json
{
  "answer": "AI-generated summary of findings...",
  "results": [
    {
      "title": "Page Title",
      "url": "https://...",
      "content": "Relevant excerpt...",
      "score": 0.95
    }
  ]
}
```

## When to Use

- **Use Tavily** for: research questions, "what is X", comparisons, analysis
- **Use Brave** for: finding specific pages, news, recent events
- **Use DDG** as fallback when others fail

## Examples

### Market research
```json
{"query": "OpenClaw skill marketplace competitors 2026", "search_depth": "advanced", "include_answer": true}
```

### Technical research
```json
{"query": "best practices express.js stripe webhook verification", "search_depth": "basic", "max_results": 3}
```
