# AnalyticsHandler Module

> **Purpose:** Usage analytics tracking and pattern analysis
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

AnalyticsHandler tracks **usage patterns and system performance**. It collects metrics, analyzes trends, and provides insights for optimization.

```
Events occur → AnalyticsHandler.track() → Aggregated → Insights generated
```

**AnalyticsHandler answers:**
- Which chains are used most?
- What's the average generation time?
- When is peak usage?
- Which brands are most active?

---

## Roles & Rules

### AnalyticsHandler DOES:
- Track usage events
- Aggregate metrics
- Analyze patterns
- Generate reports
- Identify trends
- Provide insights

### AnalyticsHandler does NOT:
- Track costs (CostTracker does that)
- Log for audit (Logger does that)
- Monitor status (StatusHandler does that)
- Execute jobs (Orchestrator does that)

### Boundaries:
- Analytics and insights only
- Cannot modify system behavior
- Anonymizes data where needed
- Read-heavy, write-light

---

## Metric Categories

| Category | Metrics | Purpose |
|----------|---------|---------|
| Usage | Calls, tokens, jobs | Volume tracking |
| Performance | Latency, duration, throughput | Speed optimization |
| Quality | Success rate, retry rate | Reliability |
| Patterns | Time of day, chain usage | Behavior insights |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `track(event)` | Track an event | event | tracked |
| `getMetrics(type, period)` | Get metrics summary | type, period | metrics |
| `getTrends(metric, period)` | Get metric trends | metric, period | trends |
| `getInsights()` | Get automated insights | - | insights |
| `getReport(type, period)` | Generate report | type, period | report |
| `getTopN(category, n, period)` | Get top N items | category, n, period | items |

---

## Detailed Function Specs

### track(event)

Track an analytics event.

```javascript
// Input
{
  event: "chain_completed",
  data: {
    chainName: "generate-blog-post",
    brandId: "acme",
    duration: 4523,
    tokens: 470,
    success: true
  },
  timestamp: "2026-01-17T14:31:05Z"
}

// Output
{
  tracked: true,
  eventId: "evt_abc123"
}
```

### getMetrics(type, period)

Get metrics summary.

```javascript
// Input
{
  type: "usage",
  period: "day"
}

// Output
{
  period: "2026-01-17",
  metrics: {
    totalJobs: 45,
    successfulJobs: 43,
    failedJobs: 2,
    successRate: 0.956,
    totalTokens: 125000,
    avgTokensPerJob: 2778,
    uniqueBrands: 5,
    uniqueChains: 8
  }
}
```

### getTrends(metric, period)

Get metric trends over time.

```javascript
// Input
{
  metric: "job_count",
  period: "week",
  interval: "day"
}

// Output
{
  metric: "job_count",
  period: { start: "2026-01-11", end: "2026-01-17" },
  trend: [
    { date: "2026-01-11", value: 40 },
    { date: "2026-01-12", value: 45 },
    { date: "2026-01-13", value: 35 },
    { date: "2026-01-14", value: 55 },
    { date: "2026-01-15", value: 42 },
    { date: "2026-01-16", value: 50 },
    { date: "2026-01-17", value: 45 }
  ],
  summary: {
    average: 44.57,
    min: 35,
    max: 55,
    trend: "stable",  // "increasing" | "stable" | "decreasing"
    changePercent: 2.5
  }
}
```

### getInsights()

Get automated insights.

```javascript
// Output
{
  insights: [
    {
      type: "pattern",
      title: "Peak usage detected",
      description: "Job volume increases 40% between 9-11 AM",
      recommendation: "Consider scheduling batch jobs outside peak hours",
      confidence: 0.85
    },
    {
      type: "optimization",
      title: "High retry rate on OpenAI",
      description: "15% of OpenAI calls require retry (vs 3% for Anthropic)",
      recommendation: "Consider switching primary provider to Anthropic",
      confidence: 0.72
    },
    {
      type: "cost",
      title: "Expensive chain identified",
      description: "generate-long-form costs 5x average per call",
      recommendation: "Review prompt length or switch to cheaper model",
      confidence: 0.90
    }
  ],
  generatedAt: "2026-01-17T14:30:00Z"
}
```

### getTopN(category, n, period)

Get top N items in category.

```javascript
// Input
{
  category: "chains",
  n: 5,
  period: "week"
}

// Output
{
  category: "chains",
  period: "2026-01-11 to 2026-01-17",
  items: [
    { chain: "generate-blog-post", count: 125, percent: 35 },
    { chain: "generate-email", count: 80, percent: 22 },
    { chain: "generate-social", count: 65, percent: 18 },
    { chain: "build-brand", count: 45, percent: 13 },
    { chain: "generate-image", count: 42, percent: 12 }
  ]
}
```

---

## Event Types

| Event | Triggers | Data |
|-------|----------|------|
| `chain_started` | Job begins | chainName, brandId |
| `chain_completed` | Job succeeds | chainName, duration, tokens |
| `chain_failed` | Job fails | chainName, error, duration |
| `ai_call` | API call made | provider, model, tokens |
| `user_action` | User interaction | action, context |

---

## Report Types

| Report | Content | Use Case |
|--------|---------|----------|
| `daily_summary` | Day's activity | Daily review |
| `weekly_analysis` | Week trends | Planning |
| `monthly_review` | Month performance | Business review |
| `brand_report` | Per-brand metrics | Client reporting |
| `performance_report` | Speed/reliability | Optimization |

---

## Config

### module.config.json

```json
{
  "module": "AnalyticsHandler",
  "version": "1.0.0",
  "description": "Usage analytics and pattern analysis",
  "type": "system-service",

  "functions": [
    {
      "name": "track",
      "description": "Track an event",
      "input": ["event"],
      "output": "tracked"
    },
    {
      "name": "getMetrics",
      "description": "Get metrics summary",
      "input": ["type", "period"],
      "output": "metrics"
    },
    {
      "name": "getTrends",
      "description": "Get metric trends",
      "input": ["metric", "period"],
      "output": "trends"
    },
    {
      "name": "getInsights",
      "description": "Get automated insights",
      "input": [],
      "output": "insights"
    },
    {
      "name": "getReport",
      "description": "Generate report",
      "input": ["type", "period"],
      "output": "report"
    },
    {
      "name": "getTopN",
      "description": "Get top N items",
      "input": ["category", "n", "period"],
      "output": "items"
    }
  ],

  "config": {
    "storagePath": "/repos/analytics/",
    "retentionDays": 365,
    "aggregationInterval": 3600000,
    "insightsEnabled": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading/writing analytics |
| Logger | Event source |

---

## Used By

| Component | How |
|-----------|-----|
| UI/Dashboard | Analytics display |
| Reports | Report generation |

---

*Last updated: 2026-01-17*
