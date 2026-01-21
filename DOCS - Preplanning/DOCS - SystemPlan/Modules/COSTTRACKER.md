# CostTracker Module

> **Purpose:** Real-time cost tracking and monitoring
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

CostTracker provides **real-time visibility** into costs as they accumulate. It works alongside CostBudgetManager - while CostBudgetManager enforces budgets, CostTracker monitors and reports on spending.

```
AI Call completes → CostTracker records → Dashboard updated → Alerts if thresholds crossed
```

**CostTracker answers:**
- How much have we spent today?
- What's the cost per brand?
- Which chains are most expensive?
- Are we on track with our budget?

---

## Roles & Rules

### CostTracker DOES:
- Track costs in real-time
- Calculate cost breakdowns (by brand, chain, model)
- Trigger alerts when thresholds are crossed
- Provide cost analytics and trends
- Generate cost reports
- Estimate remaining budget duration

### CostTracker does NOT:
- Enforce budgets (CostBudgetManager does that)
- Approve/deny operations (PolicyEngine does that)
- Make AI calls (AICaller does that)
- Track tokens (TokenManager does that)

### Boundaries:
- Read-write to cost tracking data
- Cannot modify budgets
- Cannot block operations (just reports)
- Alerts are informational only

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `recordCost(costEntry)` | Record a cost entry | costEntry | recorded |
| `getCostSummary(period, filters)` | Get cost summary | period, filters | summary |
| `getCostBreakdown(period, groupBy)` | Get cost breakdown | period, groupBy | breakdown |
| `getRunningTotal(brandId)` | Get real-time running total | brandId | total |
| `checkThreshold(brandId, threshold)` | Check if threshold crossed | brandId, threshold | status |
| `getCostTrend(period, interval)` | Get cost trend over time | period, interval | trend |
| `estimateBurnRate(brandId)` | Estimate daily burn rate | brandId | estimate |
| `generateReport(period, format)` | Generate cost report | period, format | report |

---

## Detailed Function Specs

### recordCost(costEntry)

Record a cost entry after an operation.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc",
  brandId: "acme",
  chainName: "generate-blog-post",
  provider: "openai",
  model: "gpt-4-turbo",
  cost: 0.0145,
  tokens: {
    prompt: 150,
    completion: 320,
    total: 470
  },
  timestamp: "2026-01-17T14:31:05Z"
}

// Output
{
  recorded: true,
  entryId: "cost_20260117_143105_abc",
  runningTotals: {
    daily: 12.65,
    monthly: 150.15,
    brand: 45.15
  }
}
```

### getCostSummary(period, filters)

Get cost summary for a period.

```javascript
// Input
{
  period: "day",  // "hour" | "day" | "week" | "month" | "custom"
  filters: {
    brandId: "acme",  // optional
    chainName: null,  // optional
    provider: null    // optional
  }
}

// Output
{
  period: {
    start: "2026-01-17T00:00:00Z",
    end: "2026-01-17T23:59:59Z"
  },
  summary: {
    totalCost: 12.65,
    totalCalls: 45,
    totalTokens: 125000,
    averageCostPerCall: 0.28,
    averageTokensPerCall: 2778
  },
  budget: {
    daily: { limit: 50.00, used: 12.65, remaining: 37.35, percent: 25.3 },
    monthly: { limit: 500.00, used: 150.15, remaining: 349.85, percent: 30.0 }
  }
}
```

### getCostBreakdown(period, groupBy)

Get costs grouped by dimension.

```javascript
// Input
{
  period: "day",
  groupBy: "brand"  // "brand" | "chain" | "provider" | "model" | "hour"
}

// Output
{
  period: "2026-01-17",
  groupBy: "brand",
  breakdown: [
    { brand: "acme", cost: 8.50, calls: 30, percent: 67.2 },
    { brand: "globex", cost: 3.15, calls: 12, percent: 24.9 },
    { brand: "initech", cost: 1.00, calls: 3, percent: 7.9 }
  ],
  total: 12.65
}

// By model
{
  period: "2026-01-17",
  groupBy: "model",
  breakdown: [
    { model: "gpt-4-turbo", cost: 8.00, calls: 20, percent: 63.2 },
    { model: "claude-3-sonnet", cost: 3.50, calls: 15, percent: 27.7 },
    { model: "gpt-4o", cost: 1.15, calls: 10, percent: 9.1 }
  ]
}
```

### getCostTrend(period, interval)

Get cost trend over time.

```javascript
// Input
{
  period: "week",
  interval: "day"
}

// Output
{
  period: {
    start: "2026-01-11",
    end: "2026-01-17"
  },
  interval: "day",
  trend: [
    { date: "2026-01-11", cost: 10.50, calls: 40 },
    { date: "2026-01-12", cost: 12.00, calls: 45 },
    { date: "2026-01-13", cost: 8.75, calls: 35 },
    { date: "2026-01-14", cost: 15.25, calls: 55 },
    { date: "2026-01-15", cost: 11.00, calls: 42 },
    { date: "2026-01-16", cost: 14.50, calls: 50 },
    { date: "2026-01-17", cost: 12.65, calls: 45 }
  ],
  summary: {
    average: 12.09,
    min: 8.75,
    max: 15.25,
    total: 84.65
  }
}
```

### estimateBurnRate(brandId)

Estimate daily burn rate and budget duration.

```javascript
// Input
{
  brandId: "acme"
}

// Output
{
  brandId: "acme",
  burnRate: {
    daily: 12.50,
    weekly: 87.50,
    monthly: 375.00
  },
  budget: {
    remaining: 125.00,
    daysRemaining: 10,
    exhaustionDate: "2026-01-27"
  },
  trend: "stable",  // "increasing" | "stable" | "decreasing"
  recommendation: "On track - budget will last through month"
}
```

---

## Alert Thresholds

CostTracker can trigger alerts:

```javascript
// Threshold configuration
{
  thresholds: {
    daily: {
      warning: 0.7,    // Alert at 70% of daily limit
      critical: 0.9    // Alert at 90% of daily limit
    },
    monthly: {
      warning: 0.6,    // Alert at 60% of monthly limit
      critical: 0.85   // Alert at 85% of monthly limit
    },
    perBrand: {
      warning: 0.75,
      critical: 0.95
    }
  }
}

// Alert output
{
  type: "cost_threshold",
  level: "warning",  // "warning" | "critical"
  message: "Daily cost at 72% of limit",
  details: {
    current: 36.00,
    limit: 50.00,
    percent: 72,
    projectedEnd: 45.00
  },
  action: "Consider reducing operations or increasing limit"
}
```

---

## Cost Storage

```json
// /repos/costs/2026-01/daily/2026-01-17.json
{
  "date": "2026-01-17",
  "entries": [
    {
      "entryId": "cost_20260117_143105_abc",
      "timestamp": "2026-01-17T14:31:05Z",
      "jobId": "job_20260117_143052_content_abc",
      "brandId": "acme",
      "chainName": "generate-blog-post",
      "provider": "openai",
      "model": "gpt-4-turbo",
      "cost": 0.0145,
      "tokens": { "prompt": 150, "completion": 320 }
    }
  ],
  "totals": {
    "cost": 12.65,
    "calls": 45,
    "tokens": 125000
  },
  "byBrand": {
    "acme": { "cost": 8.50, "calls": 30 }
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "CostTracker",
  "version": "1.0.0",
  "description": "Real-time cost tracking and monitoring",
  "type": "system-service",

  "functions": [
    {
      "name": "recordCost",
      "description": "Record a cost entry",
      "input": ["costEntry"],
      "output": "recorded"
    },
    {
      "name": "getCostSummary",
      "description": "Get cost summary",
      "input": ["period", "filters"],
      "output": "summary"
    },
    {
      "name": "getCostBreakdown",
      "description": "Get cost breakdown",
      "input": ["period", "groupBy"],
      "output": "breakdown"
    },
    {
      "name": "getRunningTotal",
      "description": "Get real-time running total",
      "input": ["brandId"],
      "output": "total"
    },
    {
      "name": "checkThreshold",
      "description": "Check if threshold crossed",
      "input": ["brandId", "threshold"],
      "output": "status"
    },
    {
      "name": "getCostTrend",
      "description": "Get cost trend over time",
      "input": ["period", "interval"],
      "output": "trend"
    },
    {
      "name": "estimateBurnRate",
      "description": "Estimate daily burn rate",
      "input": ["brandId"],
      "output": "estimate"
    },
    {
      "name": "generateReport",
      "description": "Generate cost report",
      "input": ["period", "format"],
      "output": "report"
    }
  ],

  "config": {
    "storagePath": "/repos/costs/",
    "retentionDays": 365,
    "alertsEnabled": true,
    "realTimeUpdates": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading/writing cost data |
| Logger | Logging cost events |
| Notification | Sending alerts |

---

## Used By

| Component | How |
|-----------|-----|
| CostBudgetManager | Cost data source |
| PolicyEngine | Cost status checks |
| UI/Dashboard | Cost display |

---

*Last updated: 2026-01-17*
