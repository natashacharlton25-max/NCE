# AIWebSearch Module

> **Purpose:** AI-powered web search for research capabilities
> **Type:** Integration Module
> **Status:** Planning (Future)

---

## Overview

AIWebSearch provides **intelligent web search** capabilities. It combines search APIs with AI to find, filter, and synthesize information from the web.

```
Query → AIWebSearch.search() → Results → AI synthesis → Structured answer
```

**AIWebSearch enables:**
- Research for content creation
- Fact verification
- Trend discovery
- Competitor research

---

## Roles & Rules

### AIWebSearch DOES:
- Search the web using APIs
- Filter and rank results
- Synthesize information with AI
- Cite sources
- Cache results

### AIWebSearch does NOT:
- Scrape content (WebScraper does that)
- Store permanent research
- Bypass search API limits
- Present results without citations

### Boundaries:
- API-based search only
- Always cite sources
- Respect rate limits
- Factual synthesis only

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `search(query, options)` | Search the web | query, options | results |
| `researchTopic(topic, depth)` | Deep research on topic | topic, depth | research |
| `findFacts(claim)` | Verify a claim | claim | verification |
| `summarizeResults(results)` | AI summarize results | results | summary |
| `findTrends(topic, period)` | Find trending content | topic, period | trends |

---

## Detailed Function Specs

### search(query, options)

Search the web.

```javascript
// Input
{
  query: "AI marketing trends 2026",
  options: {
    count: 10,
    freshness: "month",
    market: "en-US",
    safeSearch: "moderate"
  }
}

// Output
{
  query: "AI marketing trends 2026",
  results: [
    {
      title: "Top AI Marketing Trends for 2026",
      url: "https://example.com/ai-trends-2026",
      snippet: "The marketing landscape is evolving with AI...",
      publishDate: "2026-01-10",
      source: "Marketing Weekly"
    }
  ],
  totalResults: 1500000,
  searchTime: 450
}
```

### researchTopic(topic, depth)

Deep research on a topic.

```javascript
// Input
{
  topic: "sustainable packaging trends",
  depth: "comprehensive",  // "quick" | "moderate" | "comprehensive"
  focusAreas: ["innovations", "market size", "key players"]
}

// Output
{
  topic: "sustainable packaging trends",
  summary: "The sustainable packaging market is experiencing rapid growth...",
  sections: [
    {
      heading: "Market Overview",
      content: "The global sustainable packaging market...",
      sources: ["source1.com", "source2.com"]
    },
    {
      heading: "Key Innovations",
      content: "Recent innovations include...",
      sources: ["source3.com"]
    }
  ],
  statistics: [
    { stat: "$450B market size by 2028", source: "Market Research Inc" }
  ],
  keyPlayers: ["Company A", "Company B"],
  sources: [
    { url: "https://example.com", title: "Source Title", accessed: "2026-01-17" }
  ]
}
```

### findFacts(claim)

Verify a factual claim.

```javascript
// Input
{
  claim: "The AI market will reach $500 billion by 2025"
}

// Output
{
  claim: "The AI market will reach $500 billion by 2025",
  verdict: "partially_accurate",
  confidence: 0.75,
  findings: [
    {
      source: "Research Firm A",
      finding: "$450B by 2025",
      url: "https://example.com/report"
    },
    {
      source: "Research Firm B",
      finding: "$500B by 2026",
      url: "https://example2.com/report"
    }
  ],
  correction: "Estimates vary between $450-500B, with most projecting 2025-2026"
}
```

---

## Search Providers

| Provider | Use Case | Quota |
|----------|----------|-------|
| Bing Search API | General search | 1000/month free |
| Google Custom Search | Targeted search | 100/day free |
| Brave Search API | Privacy-focused | 2000/month free |

---

## Config

### module.config.json

```json
{
  "module": "AIWebSearch",
  "version": "1.0.0",
  "description": "AI-powered web search",
  "type": "integration",

  "functions": [
    {
      "name": "search",
      "description": "Search the web",
      "input": ["query", "options"],
      "output": "results"
    },
    {
      "name": "researchTopic",
      "description": "Deep research on topic",
      "input": ["topic", "depth"],
      "output": "research"
    },
    {
      "name": "findFacts",
      "description": "Verify a claim",
      "input": ["claim"],
      "output": "verification"
    },
    {
      "name": "summarizeResults",
      "description": "AI summarize results",
      "input": ["results"],
      "output": "summary"
    },
    {
      "name": "findTrends",
      "description": "Find trending content",
      "input": ["topic", "period"],
      "output": "trends"
    }
  ],

  "config": {
    "primaryProvider": "bing",
    "bingApiKeyRef": "BING_SEARCH_API_KEY",
    "googleApiKeyRef": "GOOGLE_SEARCH_API_KEY",
    "cacheResults": true,
    "cacheDuration": 3600000,
    "alwaysCiteSources": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Search API credentials |
| RequestHandler | API requests |
| AICaller | AI synthesis |
| CacheHandler | Result caching |
| Logger | Logging searches |

---

## Used By

| Component | How |
|-----------|-----|
| ResearchTools | Research capabilities |
| ContentManager | Fact checking |
| BrandManager | Market research |

---

*Last updated: 2026-01-17*
