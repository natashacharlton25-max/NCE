# AISystemManager Module

> **Purpose:** Coordinate AI-related modules as a unified subsystem
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

AISystemManager is the **coordinator** for all AI-related operations. It manages the relationship between AICaller, ModelManager, TokenManager, and CostBudgetManager - ensuring they work together smoothly.

```
Request → AISystemManager → ModelManager → AICaller → TokenManager → CostBudgetManager
```

Think of AISystemManager as the "AI department head" - it doesn't do the work itself but ensures all AI modules coordinate properly.

---

## Roles & Rules

### AISystemManager DOES:
- Coordinate AI module interactions
- Provide unified interface for AI operations
- Manage AI provider health and fallbacks
- Track AI subsystem metrics
- Handle AI-specific configuration
- Route to correct model based on requirements
- Coordinate cost estimation before calls

### AISystemManager does NOT:
- Make AI calls directly (AICaller does that)
- Select specific models (ModelManager does that)
- Track tokens (TokenManager does that)
- Track costs (CostBudgetManager does that)
- Build prompts (PromptBuilder does that)

### Boundaries:
- Coordination only - no direct API calls
- Cannot bypass ModelManager for model selection
- Cannot skip TokenManager for usage tracking
- Must respect CostBudgetManager limits
- All operations logged via Logger

---

## Why AISystemManager?

**Problem:** Multiple AI modules need coordination:
- ModelManager selects models
- AICaller makes calls
- TokenManager tracks usage
- CostBudgetManager tracks costs
- RateLimiter controls throughput

**Solution:** AISystemManager provides:
- Unified interface for AI operations
- Pre-call cost estimation
- Post-call tracking coordination
- Provider health monitoring
- Fallback orchestration

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `executeAICall(request)` | Coordinated AI call | request | result |
| `estimateCost(request)` | Pre-call cost estimate | request | estimate |
| `getProviderStatus()` | Check all provider health | - | status |
| `getUsageStats(period)` | Get usage statistics | period | stats |
| `setProviderPriority(priorities)` | Set provider preferences | priorities | updated |
| `getRecommendedModel(requirements)` | Get model recommendation | requirements | model |

---

## Detailed Function Specs

### executeAICall(request)

Coordinates a full AI call through all modules.

```javascript
// Input
{
  jobId: "job_20260117_143052_content_abc",
  promptPath: "/jobs/temp/xxx_prompt.json",
  requirements: {
    type: "text",
    quality: "high",
    maxTokens: 2000
  },
  outputPath: "/jobs/temp/xxx_response.json"
}

// Process
1. Check cost estimate with CostBudgetManager
2. Select model with ModelManager
3. Execute call with AICaller
4. Record usage with TokenManager
5. Record cost with CostBudgetManager
6. Return consolidated result

// Output
{
  success: true,
  jobId: "job_20260117_143052_content_abc",
  outputPath: "/jobs/temp/xxx_response.json",
  model: {
    provider: "openai",
    model: "gpt-4-turbo",
    selected: "quality_match"
  },
  usage: {
    promptTokens: 150,
    completionTokens: 320,
    totalTokens: 470
  },
  cost: {
    estimated: 0.014,
    actual: 0.014
  },
  timing: {
    total: 4523,
    modelSelection: 50,
    apiCall: 4200,
    tracking: 273
  }
}
```

### estimateCost(request)

Pre-call cost estimation.

```javascript
// Input
{
  requirements: {
    type: "text",
    quality: "high",
    maxTokens: 2000
  },
  promptTokens: 500
}

// Output
{
  estimatedCost: 0.015,
  breakdown: {
    promptCost: 0.005,
    completionCost: 0.010
  },
  model: "gpt-4-turbo",
  confidence: "high"
}
```

### getProviderStatus()

Check health of all AI providers.

```javascript
// Output
{
  providers: {
    openai: {
      status: "healthy",
      latency: 450,
      lastCheck: "2026-01-17T14:30:00Z",
      models: ["gpt-4-turbo", "gpt-4o", "gpt-3.5-turbo"]
    },
    anthropic: {
      status: "healthy",
      latency: 380,
      lastCheck: "2026-01-17T14:30:00Z",
      models: ["claude-3-opus", "claude-3-sonnet"]
    },
    google: {
      status: "degraded",
      latency: 2500,
      lastCheck: "2026-01-17T14:30:00Z",
      issue: "high_latency"
    }
  },
  recommended: "anthropic",
  lastFullCheck: "2026-01-17T14:30:00Z"
}
```

### getUsageStats(period)

Get usage statistics for a period.

```javascript
// Input
{
  period: "day",
  brandId: "acme"  // optional
}

// Output
{
  period: "2026-01-17",
  totals: {
    calls: 45,
    tokens: 125000,
    cost: 12.50
  },
  byProvider: {
    openai: { calls: 30, tokens: 80000, cost: 8.00 },
    anthropic: { calls: 15, tokens: 45000, cost: 4.50 }
  },
  byModel: {
    "gpt-4-turbo": { calls: 20, tokens: 50000 },
    "gpt-4o": { calls: 10, tokens: 30000 },
    "claude-3-sonnet": { calls: 15, tokens: 45000 }
  }
}
```

---

## Coordination Flow

```
┌─────────────────────────────────────────────────────────────┐
│  AISystemManager.executeAICall()                            │
│                                                             │
│  1. CostBudgetManager.checkBudget()                         │
│     └── "Can we afford this call?"                          │
│                                                             │
│  2. ModelManager.selectModel()                              │
│     └── "Which model should we use?"                        │
│                                                             │
│  3. AICaller.call()                                         │
│     └── "Make the actual API call"                          │
│                                                             │
│  4. TokenManager.recordUsage()                              │
│     └── "Track tokens used"                                 │
│                                                             │
│  5. CostBudgetManager.recordCost()                          │
│     └── "Track actual cost"                                 │
│                                                             │
│  → Return consolidated result                               │
└─────────────────────────────────────────────────────────────┘
```

---

## Provider Failover

When a provider fails, AISystemManager coordinates fallback:

```javascript
// Primary provider fails
{
  action: "failover",
  from: { provider: "openai", error: "rate_limit" },
  to: { provider: "anthropic", model: "claude-3-sonnet" },
  reason: "primary_rate_limited",
  delay: 0  // immediate failover
}

// All providers degraded
{
  action: "queue",
  reason: "all_providers_degraded",
  retryAfter: 60000,
  notification: "ai_subsystem_degraded"
}
```

---

## Config

### module.config.json

```json
{
  "module": "AISystemManager",
  "version": "1.0.0",
  "description": "Coordinate AI-related modules",
  "type": "system-service",

  "functions": [
    {
      "name": "executeAICall",
      "description": "Coordinated AI call",
      "input": ["request"],
      "output": "result"
    },
    {
      "name": "estimateCost",
      "description": "Pre-call cost estimate",
      "input": ["request"],
      "output": "estimate"
    },
    {
      "name": "getProviderStatus",
      "description": "Check all provider health",
      "input": [],
      "output": "status"
    },
    {
      "name": "getUsageStats",
      "description": "Get usage statistics",
      "input": ["period"],
      "output": "stats"
    },
    {
      "name": "setProviderPriority",
      "description": "Set provider preferences",
      "input": ["priorities"],
      "output": "updated"
    },
    {
      "name": "getRecommendedModel",
      "description": "Get model recommendation",
      "input": ["requirements"],
      "output": "model"
    }
  ],

  "config": {
    "healthCheckInterval": 60000,
    "failoverEnabled": true,
    "costTrackingEnabled": true,
    "defaultProvider": "openai",
    "providerPriority": ["openai", "anthropic", "google"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| AICaller | Making API calls |
| ModelManager | Model selection |
| TokenManager | Usage tracking |
| CostBudgetManager | Cost tracking |
| RateLimiter | Rate limit status |
| Logger | Logging operations |

---

## Used By

| Component | How |
|-----------|-----|
| Orchestrator | AI operations in chains |
| Managers | Domain AI operations |

---

*Last updated: 2026-01-17*
