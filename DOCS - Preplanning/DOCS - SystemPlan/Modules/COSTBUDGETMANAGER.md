# CostBudgetManager Module

> **Purpose:** Track costs, manage budgets, and provide spending analytics
> **Type:** Service Module
> **Status:** Planning

---

## Overview

CostBudgetManager is the financial controller for the system. It tracks all costs (API calls, services, storage), manages budgets per brand/project, provides spending analytics, and can block operations when budgets are exceeded.

```
Operation → CostBudgetManager.checkBudget() → Approved/Denied
After operation → CostBudgetManager.recordCost() → Update budgets
```

**Tracks:**
- AI API costs (tokens)
- Third-party API costs
- Storage costs
- Service subscriptions
- Per-brand spending
- Per-project spending

---

## Roles & Rules

### CostBudgetManager DOES:
- Track all system costs (AI, storage, APIs)
- Check budgets before operations
- Record costs after operations
- Send alerts at thresholds
- Provide spending analytics and forecasts
- Block operations when budget exceeded

### CostBudgetManager does NOT:
- Make API calls (just tracks costs)
- Set budget limits (human/admin only)
- Process payments
- Handle billing

### Boundaries:
- Cannot approve over-budget operations (unless overridden)
- Cannot modify past cost records
- Cannot access external billing systems
- Read-only for budget limits

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `checkBudget(brandId, estimatedCost)` | Check if operation is within budget | brandId, estimatedCost | approval |
| `recordCost(record)` | Record a cost | record | recorded |
| `getBudget(brandId)` | Get budget status | brandId | budgetStatus |
| `setBudget(brandId, budget)` | Set budget limits | brandId, budget | updated |
| `getSpending(filter)` | Get spending report | filter | spending |
| `forecast(brandId, period)` | Forecast spending | brandId, period | forecast |
| `alert(type, data)` | Trigger budget alert | type, data | alerted |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `CostBudgetManager.preApprove` | Pre-approve operation cost | checkBudget |
| `CostBudgetManager.trackAndAlert` | Record cost and check alerts | recordCost → checkThresholds → alert |

---

## Detailed Function Specs

### checkBudget(brandId, estimatedCost)

Checks if estimated cost is within budget.

```javascript
// Input
{
  brandId: "acme",
  estimatedCost: {
    amount: 0.15,
    currency: "USD",
    category: "ai-api",
    operation: "content-generation"
  }
}

// Process
1. Get brand budget
2. Get current period spending
3. Calculate if within limits
4. Return approval

// Output (approved)
{
  approved: true,
  brandId: "acme",
  budget: {
    monthly: 100.00,
    used: 45.50,
    remaining: 54.50,
    percent: 45.5
  },
  estimate: 0.15,
  afterOperation: {
    used: 45.65,
    remaining: 54.35,
    percent: 45.65
  }
}

// Output (denied)
{
  approved: false,
  brandId: "acme",
  reason: "budget_exceeded",
  budget: {
    monthly: 100.00,
    used: 99.90,
    remaining: 0.10
  },
  estimate: 0.15,
  shortfall: 0.05,
  suggestion: "Increase budget or wait until next billing period"
}
```

### recordCost(record)

Records a cost after operation.

```javascript
// Input
{
  record: {
    brandId: "acme",
    jobId: "job_20260117_143052",
    category: "ai-api",
    subcategory: "openai",
    operation: "content-generation",
    amount: 0.14,
    currency: "USD",
    details: {
      model: "gpt-4-turbo",
      tokens: { input: 500, output: 1200 }
    },
    timestamp: "2026-01-17T14:30:52Z"
  }
}

// Output
{
  recorded: true,
  costId: "cost_abc123",
  budgetUpdate: {
    used: 45.64,
    remaining: 54.36,
    percent: 45.64
  }
}
```

### getSpending(filter)

Gets spending report.

```javascript
// Input
{
  filter: {
    brandId: "acme",
    period: "month",
    startDate: "2026-01-01",
    groupBy: "category"
  }
}

// Output
{
  brandId: "acme",
  period: {
    start: "2026-01-01",
    end: "2026-01-31"
  },
  total: 45.64,
  currency: "USD",
  byCategory: {
    "ai-api": {
      total: 38.50,
      subcategories: {
        "openai": 32.00,
        "anthropic": 6.50
      }
    },
    "storage": {
      total: 5.00
    },
    "third-party-api": {
      total: 2.14,
      subcategories: {
        "canva": 1.50,
        "unsplash": 0.64
      }
    }
  },
  trend: {
    dailyAverage: 2.69,
    projectedMonthEnd: 83.39
  }
}
```

### forecast(brandId, period)

Forecasts future spending.

```javascript
// Input
{
  brandId: "acme",
  period: "month"  // or "quarter", "year"
}

// Output
{
  brandId: "acme",
  currentSpending: 45.64,
  forecast: {
    period: "month",
    projected: 83.39,
    confidence: 0.85,
    basis: "14 days of data"
  },
  budget: 100.00,
  status: "on_track",  // on_track, warning, over_budget
  recommendations: [
    "Current pace will use 83% of monthly budget",
    "Consider reducing content generation volume to stay under budget"
  ]
}
```

---

## Budget Structure

```json
{
  "brandId": "acme",
  "budgets": {
    "monthly": {
      "total": 100.00,
      "byCategory": {
        "ai-api": 70.00,
        "storage": 10.00,
        "third-party-api": 20.00
      }
    },
    "perJob": {
      "max": 5.00,
      "warning": 3.00
    }
  },
  "alerts": {
    "thresholds": [0.5, 0.75, 0.9, 1.0],
    "channels": ["email", "dashboard"]
  },
  "actions": {
    "onExceed": "block",  // block, warn, notify
    "onWarning": "notify"
  },
  "rollover": false,
  "currency": "USD"
}
```

---

## Cost Categories

| Category | Subcategories | Tracked By |
|----------|---------------|------------|
| `ai-api` | openai, anthropic, google | TokenManager |
| `storage` | local, cloud, canva | StorageSync |
| `third-party-api` | canva, unsplash, pexels | Integration modules |
| `compute` | batch-processing | BatchProcessor |
| `email` | sendgrid, smtp | Emaillit |

---

## Config

### module.config.json

```json
{
  "module": "CostBudgetManager",
  "version": "1.0.0",
  "description": "Track costs and manage budgets",
  "type": "service",

  "functions": [
    {
      "name": "checkBudget",
      "description": "Check if operation is within budget",
      "input": ["brandId", "estimatedCost"],
      "output": "approval"
    },
    {
      "name": "recordCost",
      "description": "Record a cost",
      "input": ["record"],
      "output": "recorded"
    },
    {
      "name": "getBudget",
      "description": "Get budget status",
      "input": ["brandId"],
      "output": "budgetStatus"
    },
    {
      "name": "setBudget",
      "description": "Set budget limits",
      "input": ["brandId", "budget"],
      "output": "updated"
    },
    {
      "name": "getSpending",
      "description": "Get spending report",
      "input": ["filter"],
      "output": "spending"
    },
    {
      "name": "forecast",
      "description": "Forecast spending",
      "input": ["brandId", "period"],
      "output": "forecast"
    },
    {
      "name": "alert",
      "description": "Trigger budget alert",
      "input": ["type", "data"],
      "output": "alerted"
    }
  ],

  "steps": [
    {
      "name": "preApprove",
      "description": "Pre-approve operation cost",
      "sequence": ["checkBudget"]
    },
    {
      "name": "trackAndAlert",
      "description": "Record cost and check alerts",
      "sequence": ["recordCost", "alert"]
    }
  ],

  "config": {
    "defaultCurrency": "USD",
    "alertThresholds": [0.5, 0.75, 0.9, 1.0],
    "defaultAction": "warn",
    "trackingGranularity": "job"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Storing cost records, budgets |
| Notification | Sending budget alerts |
| Archivist | Saving cost history |

---

## Used By

| Module | How |
|--------|-----|
| TokenManager | AI API cost tracking |
| AICaller | Pre-call budget check |
| All integration modules | API cost tracking |
| Orchestrator | Job-level cost tracking |

---

*Last updated: 2026-01-17*
