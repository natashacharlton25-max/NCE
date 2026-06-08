# ResearchTools Module

> **Purpose:** Multi-source information gathering and research utilities
> **Type:** Integration Module
> **Status:** Planning (Future)

---

## Overview

ResearchTools is a **research coordinator** that combines multiple sources (web search, scraping, databases) to gather comprehensive information for content creation.

```
Research request → ResearchTools.research() → Multiple sources → Synthesized report
```

**ResearchTools enables:**
- Comprehensive topic research
- Competitor analysis
- Market research
- Content inspiration
- Fact compilation

---

## Roles & Rules

### ResearchTools DOES:
- Coordinate multiple research sources
- Synthesize findings
- Cite all sources
- Cache research results
- Generate research reports

### ResearchTools does NOT:
- Scrape directly (uses WebScraper)
- Search directly (uses AIWebSearch)
- Store permanent research
- Make content decisions

### Boundaries:
- Research coordination only
- Always cite sources
- Respect source limits
- Cache for efficiency

---

## Research Sources

| Source | Module | Use |
|--------|--------|-----|
| Web Search | AIWebSearch | Find relevant pages |
| Web Scraping | WebScraper | Extract content |
| Internal Repos | Librarian | Past research |
| External APIs | Various | Specialized data |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `research(topic, options)` | Full research on topic | topic, options | report |
| `competitorAnalysis(competitors)` | Analyze competitors | competitors | analysis |
| `marketResearch(market, aspects)` | Market research | market, aspects | research |
| `findInspirations(topic, count)` | Find content inspiration | topic, count | inspirations |
| `compileStats(topic)` | Gather statistics | topic | stats |
| `generateBrief(topic, audience)` | Create content brief | topic, audience | brief |

---

## Detailed Function Specs

### research(topic, options)

Full research on a topic.

```javascript
// Input
{
  topic: "sustainable fashion trends 2026",
  options: {
    depth: "comprehensive",
    sources: ["web", "news", "social"],
    focusAreas: ["consumer behavior", "brands", "materials"],
    maxSources: 20
  }
}

// Output
{
  topic: "sustainable fashion trends 2026",
  summary: "Sustainable fashion continues to grow with focus on...",

  sections: [
    {
      title: "Consumer Trends",
      content: "Gen Z and Millennials are driving demand...",
      keyPoints: [
        "73% of consumers willing to pay more for sustainable products",
        "Second-hand market growing 25% annually"
      ],
      sources: ["source1.com", "source2.com"]
    },
    {
      title: "Leading Brands",
      content: "Major brands are committing to sustainability...",
      keyPoints: [...],
      sources: [...]
    }
  ],

  statistics: [
    { stat: "$8.25B market by 2028", source: "Industry Report 2026" },
    { stat: "40% reduction in water usage", source: "Sustainability Index" }
  ],

  quotes: [
    { quote: "Sustainability is no longer optional...", author: "Industry Expert", source: "..." }
  ],

  sources: [
    {
      url: "https://example.com/article",
      title: "Sustainable Fashion Trends",
      type: "article",
      accessedAt: "2026-01-17",
      relevance: 0.95
    }
  ],

  researchedAt: "2026-01-17T14:30:00Z",
  sourcesUsed: 15
}
```

### competitorAnalysis(competitors)

Analyze competitors.

```javascript
// Input
{
  competitors: ["brand-a.com", "brand-b.com", "brand-c.com"],
  aspects: ["messaging", "offerings", "social_presence"]
}

// Output
{
  analysis: [
    {
      competitor: "brand-a.com",
      overview: "Premium positioning with focus on innovation...",
      messaging: {
        tagline: "Innovation for Tomorrow",
        tone: "professional, aspirational",
        keyMessages: ["innovation", "quality", "sustainability"]
      },
      offerings: {
        products: ["Product A", "Product B"],
        priceRange: "$50-500",
        uniqueFeatures: ["Feature X", "Feature Y"]
      },
      socialPresence: {
        instagram: { followers: 150000, engagement: 3.5 },
        twitter: { followers: 50000, engagement: 1.2 }
      }
    }
  ],
  comparison: {
    strengths: { "brand-a": ["innovation"], "brand-b": ["price"] },
    opportunities: "Gap in mid-market sustainable products"
  }
}
```

### generateBrief(topic, audience)

Generate a content brief for writers.

```javascript
// Input
{
  topic: "benefits of remote work",
  audience: "HR professionals",
  contentType: "blog-post"
}

// Output
{
  brief: {
    topic: "benefits of remote work",
    audience: "HR professionals",
    angle: "How HR can leverage remote work for talent acquisition",

    keyPoints: [
      "Access to global talent pool",
      "Cost savings on office space",
      "Improved employee satisfaction",
      "Challenges and solutions"
    ],

    statistics: [
      { stat: "65% of workers prefer remote options", source: "Survey 2026" }
    ],

    competitors: [
      { title: "Similar Article", url: "...", approach: "General overview" }
    ],

    suggestedStructure: [
      "Introduction - The remote work shift",
      "Key Benefits for HR",
      "Implementation Challenges",
      "Best Practices",
      "Conclusion"
    ],

    keywords: ["remote work", "HR strategy", "talent acquisition"],

    sources: [...]
  }
}
```

---

## Research Caching

```javascript
{
  caching: {
    enabled: true,
    duration: 86400000,  // 24 hours
    keyFormat: "research:{topic}:{depth}:{date}"
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "ResearchTools",
  "version": "1.0.0",
  "description": "Multi-source research utilities",
  "type": "integration",

  "functions": [
    {
      "name": "research",
      "description": "Full research on topic",
      "input": ["topic", "options"],
      "output": "report"
    },
    {
      "name": "competitorAnalysis",
      "description": "Analyze competitors",
      "input": ["competitors"],
      "output": "analysis"
    },
    {
      "name": "marketResearch",
      "description": "Market research",
      "input": ["market", "aspects"],
      "output": "research"
    },
    {
      "name": "findInspirations",
      "description": "Find content inspiration",
      "input": ["topic", "count"],
      "output": "inspirations"
    },
    {
      "name": "compileStats",
      "description": "Gather statistics",
      "input": ["topic"],
      "output": "stats"
    },
    {
      "name": "generateBrief",
      "description": "Create content brief",
      "input": ["topic", "audience"],
      "output": "brief"
    }
  ],

  "config": {
    "maxSourcesPerResearch": 30,
    "cacheEnabled": true,
    "cacheDuration": 86400000,
    "alwaysCiteSources": true,
    "synthesizeWithAI": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| AIWebSearch | Web searching |
| WebScraper | Content extraction |
| AICaller | AI synthesis |
| Librarian | Internal research |
| CacheHandler | Result caching |
| Logger | Logging operations |

---

## Used By

| Component | How |
|-----------|-----|
| ContentManager | Content research |
| BrandManager | Brand research |
| Writers | Content briefs |

---

*Last updated: 2026-01-17*
