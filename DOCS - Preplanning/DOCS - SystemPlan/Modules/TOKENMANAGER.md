# TokenManager Module

> **Purpose:** Token estimation, budget tracking, and usage recording
> **Type:** Service Module
> **Status:** Planning

---

## Overview

TokenManager handles everything token-related: estimating costs before API calls and recording actual usage after calls. Works as a chain step - reads AICaller's raw response to extract token usage.

```
Before call: Content → Estimate tokens → Check budget → Proceed or block
After call:  AICaller response file → TokenManager reads → Record usage
```

---

## Roles & Rules

### TokenManager DOES:
- Estimate tokens before API calls
- Calculate estimated costs
- Check budgets before proceeding
- Read raw AI responses to extract actual usage
- Record all usage to repos
- Provide usage reports and analytics

### TokenManager does NOT:
- Make API calls (AICaller does that)
- Approve budget overages (Level 4 human escalation)
- Track costs (CostBudgetManager does deeper cost analysis)
- Block jobs itself (returns status, Orchestrator decides)

### Boundaries:
- Cannot increase budgets (human only)
- Cannot modify AI responses
- Cannot skip recording (all usage must be logged)

---

## Chain Integration

TokenManager runs AFTER AICaller in the chain, reading from AICaller's output:

```json
{
  "steps": [
    {
      "step": 3,
      "module": "AICaller",
      "function": "call",
      "outputPath": "/jobs/temp/{{jobId}}_airesponse.json"
    },
    {
      "step": 4,
      "module": "TokenManager",
      "function": "recordUsage",
      "input": {
        "responsePath": "/jobs/temp/{{jobId}}_airesponse.json",
        "brandId": "{{brandId}}",
        "jobId": "{{jobId}}"
      }
    }
  ]
}
```

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `estimateTokens(content, model)` | Estimate token count | content, model | tokenEstimate |
| `estimateCost(tokens, model)` | Calculate cost from tokens | tokens, model | costEstimate |
| `checkBudget(brandId, estimatedCost)` | Check if within budget | brandId, estimatedCost | budgetStatus |
| `recordUsage(responsePath, brandId, jobId)` | Record usage from AI response | responsePath, brandId, jobId | usageRecord |
| `getBudget(brandId)` | Get current budget status | brandId | budgetInfo |
| `getUsage(brandId, period)` | Get usage for period | brandId, period | usageStats |
| `setBudget(brandId, limits)` | Set budget limits | brandId, limits | updated |

---

## Detailed Function Specs

### estimateTokens(content, model)

Estimates token count for content before API call.

```javascript
// Input
{
  content: {
    system: "You are a brand strategist...",
    user: "Create an introduction for Acme Corp..."
  },
  model: "gpt-4-turbo"
}

// Process
1. Get model's tokenizer type (cl100k_base for GPT-4, etc.)
2. Count tokens in system message
3. Count tokens in user message
4. Add buffer for expected response

// Output
{
  input: {
    system: 45,
    user: 120,
    total: 165
  },
  estimatedOutput: 400,
  total: 565,
  model: "gpt-4-turbo",
  tokenizer: "cl100k_base"
}
```

### estimateCost(tokens, model)

Calculates estimated cost from token counts.

```javascript
// Input
{
  tokens: { input: 165, estimatedOutput: 400 },
  model: "gpt-4-turbo"
}

// Process
1. FileManager.get('model-cards', model) → get pricing
2. Calculate: (input/1000 * inputPer1k) + (output/1000 * outputPer1k)

// Output
{
  inputCost: 0.00165,   // 165 tokens * $0.01/1k
  outputCost: 0.012,    // 400 tokens * $0.03/1k
  totalCost: 0.01365,
  currency: "USD",
  model: "gpt-4-turbo"
}
```

### checkBudget(brandId, estimatedCost)

Checks if estimated cost is within budget.

```javascript
// Input
{
  brandId: "acme",
  estimatedCost: 0.01365
}

// Process
1. getBudget(brandId)
2. Compare estimatedCost against remaining budget
3. Return status

// Output (ok)
{
  status: "ok",
  canProceed: true,
  budgetRemaining: 45.23,
  estimatedCost: 0.01365,
  percentUsed: 9.56
}

// Output (warning - approaching limit)
{
  status: "warning",
  canProceed: true,
  budgetRemaining: 5.23,
  estimatedCost: 0.01365,
  percentUsed: 89.5,
  message: "Budget at 89.5% - approaching limit"
}

// Output (blocked - over budget)
{
  status: "blocked",
  canProceed: false,
  budgetRemaining: 0.005,
  estimatedCost: 0.01365,
  percentUsed: 100,
  message: "Budget exceeded - request blocked",
  escalateTo: "FailureHandler"
}
```

### recordUsage(responsePath, brandId, jobId)

**KEY FUNCTION** - Reads AICaller's raw response and records usage.

```javascript
// Input
{
  responsePath: "/jobs/temp/job_20260117_143052_airesponse.json",
  brandId: "acme",
  jobId: "job_20260117_143052_brandkit_abc"
}

// Process
1. FileManager.read(responsePath)
2. Extract from response:
   - _meta.model
   - _meta.durationMs
   - raw.usage.prompt_tokens
   - raw.usage.completion_tokens
   - raw.usage.total_tokens
3. Get model pricing from ModelManager
4. Calculate actual cost
5. Create usage record
6. Archivist.save('token-usage', record)
7. Update budget tracking

// Response file format (from AICaller)
{
  "_meta": {
    "callId": "call_20260117_143052_abc",
    "jobId": "job_20260117_143052_brandkit_xyz",
    "provider": "openai",
    "model": "gpt-4-turbo",
    "timestamp": "2026-01-17T14:31:05Z",
    "durationMs": 4523
  },
  "raw": {
    "usage": {
      "prompt_tokens": 165,
      "completion_tokens": 387,
      "total_tokens": 552
    }
  }
}

// Output
{
  recorded: true,
  usageId: "usage_20260117_143052_abc",
  tokens: {
    prompt: 165,
    completion: 387,
    total: 552
  },
  cost: {
    input: 0.00165,
    output: 0.01161,
    total: 0.01326,
    currency: "USD"
  },
  budgetRemaining: 45.22,
  budgetPercentUsed: 9.58
}
```

### getBudget(brandId)

Gets current budget status for a brand.

```javascript
// Input
"acme"

// Output
{
  brandId: "acme",
  period: "monthly",
  periodStart: "2026-01-01",
  periodEnd: "2026-01-31",
  budget: 50.00,
  used: 4.78,
  remaining: 45.22,
  percentUsed: 9.56,
  currency: "USD",
  warningThreshold: 0.75,
  blockThreshold: 1.0
}
```

### getUsage(brandId, period)

Gets usage statistics for reporting.

```javascript
// Input
{
  brandId: "acme",
  period: "monthly",  // or "daily", "weekly", "all-time"
  startDate: "2026-01-01",
  endDate: "2026-01-17"
}

// Output
{
  brandId: "acme",
  period: "monthly",
  totalTokens: 125000,
  totalCost: 4.78,
  currency: "USD",
  breakdown: {
    byModel: {
      "gpt-4-turbo": { tokens: 85000, cost: 3.20 },
      "gpt-4o-mini": { tokens: 40000, cost: 1.58 }
    },
    byChain: {
      "brand-kit": { tokens: 50000, cost: 2.10 },
      "content-blog": { tokens: 45000, cost: 1.68 },
      "social-batch": { tokens: 30000, cost: 1.00 }
    }
  },
  jobCount: 45
}
```

---

## Provider Token Extraction

Different providers return usage differently. TokenManager handles all:

| Provider | Token Location | Notes |
|----------|----------------|-------|
| OpenAI | `raw.usage.prompt_tokens`, `raw.usage.completion_tokens` | Standard format |
| Anthropic | `raw.usage.input_tokens`, `raw.usage.output_tokens` | Different field names |
| Google | `raw.usageMetadata.promptTokenCount`, `raw.usageMetadata.candidatesTokenCount` | Nested differently |
| OpenRouter | `raw.usage.prompt_tokens`, `raw.usage.completion_tokens` | Follows OpenAI format |

```javascript
// Provider-specific extraction
function extractUsage(response) {
  const provider = response._meta.provider;

  switch (provider) {
    case 'openai':
    case 'openrouter':
      return {
        prompt: response.raw.usage.prompt_tokens,
        completion: response.raw.usage.completion_tokens
      };

    case 'anthropic':
      return {
        prompt: response.raw.usage.input_tokens,
        completion: response.raw.usage.output_tokens
      };

    case 'google':
      return {
        prompt: response.raw.usageMetadata.promptTokenCount,
        completion: response.raw.usageMetadata.candidatesTokenCount
      };
  }
}
```

---

## Budget Configuration

Budget settings stored in brand profile:

```json
{
  "id": "acme",
  "name": "Acme Corp",
  "budget": {
    "period": "monthly",
    "limit": 50.00,
    "currency": "USD",
    "warningThreshold": 0.75,
    "blockThreshold": 1.0,
    "rollover": false,
    "alerts": {
      "onWarning": true,
      "onBlock": true,
      "channels": ["ui", "email"]
    }
  }
}
```

---

## Usage Record Format

Stored in `/repos/token-usage/YYYY-MM/`:

```json
{
  "id": "usage_20260117_143052_abc",
  "jobId": "job_20260117_143052_brandkit_abc123",
  "callId": "call_20260117_143052_abc",
  "brandId": "acme",
  "model": "gpt-4-turbo",
  "provider": "openai",
  "timestamp": "2026-01-17T14:30:52Z",
  "durationMs": 4523,

  "tokens": {
    "prompt": 165,
    "completion": 387,
    "total": 552
  },

  "cost": {
    "input": 0.00165,
    "output": 0.01161,
    "total": 0.01326,
    "currency": "USD"
  },

  "context": {
    "chain": "brand-kit",
    "step": 3,
    "templateId": "brand-intro"
  }
}
```

---

## Error Handling

| Error | Handling |
|-------|----------|
| Response file not found | Fail with `USAGE_FILE_NOT_FOUND` |
| Missing usage data in response | Log warning, record 0 tokens |
| Unknown provider format | Attempt extraction, log warning |
| Budget check fails | Return blocked status, don't escalate |
| Recording fails | Retry once, then log and continue |

---

## Config

### module.config.json

```json
{
  "module": "TokenManager",
  "version": "2.0.0",
  "description": "Token estimation, budgets, and usage tracking",
  "type": "service",

  "functions": [
    {
      "name": "estimateTokens",
      "description": "Estimate token count for content",
      "input": ["content", "model"],
      "output": "tokenEstimate"
    },
    {
      "name": "estimateCost",
      "description": "Calculate cost from tokens",
      "input": ["tokens", "model"],
      "output": "costEstimate"
    },
    {
      "name": "checkBudget",
      "description": "Check if within budget",
      "input": ["brandId", "estimatedCost"],
      "output": "budgetStatus"
    },
    {
      "name": "recordUsage",
      "description": "Record usage from AI response file",
      "input": ["responsePath", "brandId", "jobId"],
      "output": "usageRecord"
    },
    {
      "name": "getBudget",
      "description": "Get current budget status",
      "input": ["brandId"],
      "output": "budgetInfo"
    },
    {
      "name": "getUsage",
      "description": "Get usage for period",
      "input": ["brandId", "period"],
      "output": "usageStats"
    },
    {
      "name": "setBudget",
      "description": "Set budget limits",
      "input": ["brandId", "limits"],
      "output": "updated"
    }
  ],

  "steps": [
    {
      "name": "validateAndTrack",
      "description": "Full pre-call validation",
      "sequence": ["estimateTokens", "estimateCost", "checkBudget"]
    },
    {
      "name": "recordFromResponse",
      "description": "Read AI response and record usage",
      "sequence": ["recordUsage"]
    }
  ],

  "config": {
    "defaultWarningThreshold": 0.75,
    "defaultBlockThreshold": 1.0,
    "defaultPeriod": "monthly",
    "usageRetentionDays": 365,
    "supportedProviders": ["openai", "anthropic", "google", "openrouter"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading response files, model pricing, brand budgets |
| ModelManager | Getting model pricing info |
| Archivist | Saving usage records |

---

## Used By

| Module | How |
|--------|-----|
| Orchestrator | Pre-call budget check (chain step) |
| Chain | Post-call recording (chain step after AICaller) |
| CostBudgetManager | Detailed cost analysis |
| Reports | Usage analytics |

---

*Last updated: 2026-01-17*
