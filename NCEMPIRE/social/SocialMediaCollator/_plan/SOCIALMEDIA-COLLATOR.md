# SocialMediaCollator Module

> **Purpose:** Collect, aggregate, and analyze social media data and mentions
> **Type:** Service Module
> **Status:** Planning

---

## Overview

SocialMediaCollator gathers social media data - brand mentions, hashtag tracking, competitor monitoring, sentiment analysis. It collects and aggregates data for reporting and insights. Think of it as the listening ear of social media.

```
Tracking rules → SocialMediaCollator.collect() → Aggregated data → Reports
```

**Capabilities:**
- Brand mention tracking
- Hashtag monitoring
- Competitor tracking
- Sentiment analysis
- Trend detection
- Engagement aggregation

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `trackMentions(brandId, platforms)` | Track brand mentions | brandId, platforms | mentions |
| `trackHashtag(hashtag, options)` | Monitor hashtag | hashtag, options | hashtagData |
| `trackCompetitor(competitor, metrics)` | Monitor competitor | competitor, metrics | competitorData |
| `aggregateEngagement(brandId, period)` | Aggregate engagement metrics | brandId, period | aggregated |
| `analyzeSentiment(mentions)` | Analyze sentiment of mentions | mentions | sentiment |
| `detectTrends(brandId, period)` | Detect trends in data | brandId, period | trends |
| `generateReport(brandId, type, period)` | Generate social report | brandId, type, period | report |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `SocialMediaCollator.dailyCollect` | Daily data collection | trackMentions → aggregateEngagement |
| `SocialMediaCollator.weeklyReport` | Weekly report generation | aggregateEngagement → analyzeSentiment → generateReport |

---

## Detailed Function Specs

### trackMentions(brandId, platforms)

Tracks brand mentions across platforms.

```javascript
// Input
{
  brandId: "acme",
  platforms: ["instagram", "twitter", "linkedin"],
  keywords: ["acme", "acmecorp", "@acmecorp"],
  period: {
    start: "2026-01-10",
    end: "2026-01-17"
  }
}

// Process
1. Query each platform API for mentions
2. Filter by keywords
3. Deduplicate
4. Enrich with context

// Output
{
  brandId: "acme",
  period: { start: "2026-01-10", end: "2026-01-17" },
  totalMentions: 156,
  byPlatform: {
    instagram: {
      count: 78,
      mentions: [
        {
          id: "ig_mention_001",
          type: "post",
          author: "@user123",
          content: "Love the new @acmecorp branding!",
          engagement: { likes: 45, comments: 3 },
          sentiment: "positive",
          timestamp: "2026-01-15T14:30:00Z"
        }
      ]
    },
    twitter: { count: 56, mentions: [...] },
    linkedin: { count: 22, mentions: [...] }
  }
}
```

### trackHashtag(hashtag, options)

Monitors a hashtag's performance.

```javascript
// Input
{
  hashtag: "#AcmeInnovation",
  options: {
    platforms: ["instagram", "twitter"],
    period: "7days",
    includeRelated: true
  }
}

// Output
{
  hashtag: "#AcmeInnovation",
  period: { start: "2026-01-10", end: "2026-01-17" },
  metrics: {
    totalPosts: 234,
    totalReach: 125000,
    totalEngagement: 8750,
    uniqueAuthors: 189,
    growth: {
      postsChange: "+15%",
      reachChange: "+23%"
    }
  },
  topPosts: [...],
  relatedHashtags: [
    { tag: "#Innovation", coOccurrence: 45 },
    { tag: "#Technology", coOccurrence: 38 }
  ]
}
```

### aggregateEngagement(brandId, period)

Aggregates all engagement metrics.

```javascript
// Input
{
  brandId: "acme",
  period: {
    type: "week",
    start: "2026-01-10",
    end: "2026-01-17"
  }
}

// Output
{
  brandId: "acme",
  period: { start: "2026-01-10", end: "2026-01-17" },
  aggregate: {
    totalFollowers: 45230,
    followerGrowth: 523,
    totalPosts: 28,
    totalEngagement: 4521,
    engagementRate: 3.2,
    totalReach: 89400,
    impressions: 156000
  },
  byPlatform: {
    instagram: {
      followers: 25000,
      posts: 12,
      engagement: 2800,
      engagementRate: 4.1
    },
    linkedin: {
      followers: 12000,
      posts: 8,
      engagement: 1200,
      engagementRate: 2.8
    },
    twitter: {
      followers: 8230,
      posts: 8,
      engagement: 521,
      engagementRate: 1.9
    }
  },
  topPerforming: [
    { postId: "ig_123", platform: "instagram", engagement: 450 }
  ]
}
```

### analyzeSentiment(mentions)

Analyzes sentiment of collected mentions.

```javascript
// Input
{
  mentions: [...] // From trackMentions
}

// Output
{
  overall: {
    score: 0.72,  // -1 to 1
    label: "positive",
    distribution: {
      positive: 68,
      neutral: 24,
      negative: 8
    }
  },
  themes: [
    { theme: "product quality", sentiment: 0.85, count: 45 },
    { theme: "customer service", sentiment: 0.65, count: 23 },
    { theme: "pricing", sentiment: 0.42, count: 12 }
  ],
  alerts: [
    {
      type: "negative_spike",
      theme: "delivery issues",
      count: 5,
      severity: "low"
    }
  ]
}
```

### generateReport(brandId, type, period)

Generates comprehensive social media report.

```javascript
// Input
{
  brandId: "acme",
  type: "weekly",  // daily, weekly, monthly, campaign
  period: {
    start: "2026-01-10",
    end: "2026-01-17"
  }
}

// Output
{
  reportId: "report_acme_w03_2026",
  brandId: "acme",
  type: "weekly",
  period: { ... },
  sections: {
    overview: {
      summary: "Strong week with 15% engagement increase...",
      highlights: [
        "Instagram reached 25k followers",
        "Product launch post went viral"
      ]
    },
    engagement: { ... },
    mentions: { ... },
    sentiment: { ... },
    competitors: { ... },
    recommendations: [
      "Increase LinkedIn posting frequency",
      "Engage more with user-generated content"
    ]
  },
  exportFormats: ["pdf", "html", "json"]
}
```

---

## Tracking Configuration

```json
{
  "brandId": "acme",
  "tracking": {
    "mentions": {
      "keywords": ["acme", "acmecorp", "acme corp"],
      "handles": ["@acmecorp"],
      "hashtags": ["#acme", "#acmecorp"]
    },
    "competitors": [
      { "name": "CompetitorA", "handles": ["@competitora"] },
      { "name": "CompetitorB", "handles": ["@competitorb"] }
    ],
    "platforms": ["instagram", "twitter", "linkedin"],
    "frequency": "hourly"
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "SocialMediaCollator",
  "version": "1.0.0",
  "description": "Collect and analyze social media data",
  "type": "service",

  "functions": [
    {
      "name": "trackMentions",
      "description": "Track brand mentions",
      "input": ["brandId", "platforms"],
      "output": "mentions"
    },
    {
      "name": "trackHashtag",
      "description": "Monitor hashtag",
      "input": ["hashtag", "options"],
      "output": "hashtagData"
    },
    {
      "name": "trackCompetitor",
      "description": "Monitor competitor",
      "input": ["competitor", "metrics"],
      "output": "competitorData"
    },
    {
      "name": "aggregateEngagement",
      "description": "Aggregate engagement metrics",
      "input": ["brandId", "period"],
      "output": "aggregated"
    },
    {
      "name": "analyzeSentiment",
      "description": "Analyze sentiment of mentions",
      "input": ["mentions"],
      "output": "sentiment"
    },
    {
      "name": "detectTrends",
      "description": "Detect trends in data",
      "input": ["brandId", "period"],
      "output": "trends"
    },
    {
      "name": "generateReport",
      "description": "Generate social report",
      "input": ["brandId", "type", "period"],
      "output": "report"
    }
  ],

  "steps": [
    {
      "name": "dailyCollect",
      "description": "Daily data collection",
      "sequence": ["trackMentions", "aggregateEngagement"]
    },
    {
      "name": "weeklyReport",
      "description": "Weekly report generation",
      "sequence": ["aggregateEngagement", "analyzeSentiment", "generateReport"]
    }
  ],

  "config": {
    "defaultCollectionFrequency": "hourly",
    "sentimentModel": "vader",
    "retainDataDays": 90
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| SocialMediaManager | Platform API access |
| LanguageCheck | Sentiment analysis |
| FileManager | Storing collected data |
| PDFMaker | Report generation |

---

## Used By

| Module | How |
|--------|-----|
| Reports | Social media reports |
| Notification | Mention alerts |
| SocialMediaPlanner | Optimization insights |

---

*Last updated: 2026-01-17*
