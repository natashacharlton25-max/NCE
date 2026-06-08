# AICaller Service

> Central gatekeeper for ALL AI API calls. No app calls AI directly.
> **Status:** Planning | **Priority:** HIGH
> **Location:** `C:\Users\Business\AICaller\`

---

## Overview

The AICaller service is the single point of access for all AI operations. Apps send prompts to AICaller - they never call AI APIs directly. AICaller handles model selection, rate limiting, token tracking, cost calculation, retries, and analytics.

### Current State (BrandKit Workflow)

AI calling is currently embedded in BrandKit Workflow:

```
BrandKit Workflow/
├── lib/server/
│   ├── ai-client.ts            # API calls to providers
│   └── types/ai-client.ts      # Type definitions
│
└── _config/
    └── section-models.json     # Model mapping per section
```

### Target State (AICaller Service)

```
AICaller/
├── src/
│   ├── index.ts                    # Public API
│   ├── scripts/
│   │   ├── call-model.ts           # Make API request
│   │   ├── select-model.ts         # Pick best model for task
│   │   ├── check-rate-limit.ts     # Queue if at limit
│   │   ├── count-tokens.ts         # Estimate/count tokens
│   │   ├── handle-stream.ts        # Streaming responses
│   │   ├── retry-failed.ts         # Retry with backoff
│   │   ├── track-usage.ts          # Log usage/cost
│   │   └── test-connection.ts      # Verify API connectivity
│   └── types/
│       └── index.ts                # Type definitions
├── _config/
│   ├── models.json                 # Available models + capabilities
│   ├── providers.json              # Provider endpoints + auth
│   ├── rate-limits.json            # Limits per provider
│   ├── costs.json                  # Cost per token
│   └── section-models.json         # Model mapping per task type
├── analytics/
│   ├── usage-by-app.json           # Usage per app
│   ├── usage-by-model.json         # Usage per model
│   ├── costs-daily.json            # Daily cost tracking
│   └── errors.json                 # Error tracking
├── queue/
│   └── pending.json                # Rate-limited requests
├── package.json
└── tsconfig.json
```

---

## Extraction Mapping

### Files to Extract

| Current Location | Target Location | Changes Needed |
|------------------|-----------------|----------------|
| `lib/server/ai-client.ts` | `src/scripts/call-model.ts` | Add rate limiting, tracking |
| `lib/server/types/ai-client.ts` | `src/types/index.ts` | Extend types |
| `_config/section-models.json` | `_config/section-models.json` | Direct copy |

### Functions to Extract

| Current Function | Target Script | Additions |
|------------------|---------------|-----------|
| `callAI()` | `call-model.ts` | Rate check, tracking |
| `generateSection()` | Remove | App handles orchestration |
| `testAIConnection()` | `test-connection.ts` | Multi-provider support |
| `getModelForSection()` | `select-model.ts` | Enhanced selection |

---

## New Functionality

### 1. Rate Limiting

```typescript
// _config/rate-limits.json
{
  "providers": {
    "groq": {
      "requests_per_minute": 30,
      "tokens_per_minute": 100000
    },
    "openai": {
      "requests_per_minute": 60,
      "tokens_per_minute": 150000
    },
    "anthropic": {
      "requests_per_minute": 50,
      "tokens_per_minute": 100000
    }
  }
}

// src/scripts/check-rate-limit.ts
interface RateLimitStatus {
  allowed: boolean;
  waitTime?: number;        // ms to wait if not allowed
  remainingRequests: number;
  remainingTokens: number;
}

export async function checkRateLimit(
  provider: string,
  estimatedTokens: number
): Promise<RateLimitStatus> {
  const limits = loadRateLimits()[provider];
  const usage = await getCurrentMinuteUsage(provider);

  const remainingRequests = limits.requests_per_minute - usage.requests;
  const remainingTokens = limits.tokens_per_minute - usage.tokens;

  if (remainingRequests <= 0 || remainingTokens < estimatedTokens) {
    return {
      allowed: false,
      waitTime: calculateWaitTime(usage),
      remainingRequests: Math.max(0, remainingRequests),
      remainingTokens: Math.max(0, remainingTokens)
    };
  }

  return {
    allowed: true,
    remainingRequests,
    remainingTokens
  };
}

export async function waitForRateLimit(provider: string, estimatedTokens: number): Promise<void> {
  let status = await checkRateLimit(provider, estimatedTokens);

  while (!status.allowed) {
    await sleep(status.waitTime);
    status = await checkRateLimit(provider, estimatedTokens);
  }
}
```

### 2. Token Tracking

```typescript
// src/scripts/track-usage.ts
interface UsageRecord {
  timestamp: string;
  app: string;
  provider: string;
  model: string;
  promptTokens: number;
  completionTokens: number;
  totalTokens: number;
  cost: number;
  duration: number;
  success: boolean;
  error?: string;
}

export async function trackUsage(record: UsageRecord): Promise<void> {
  // Update usage-by-app
  await updateAnalytics('usage-by-app', record.app, {
    totalCalls: increment(),
    totalTokens: add(record.totalTokens),
    totalCost: add(record.cost),
    lastCall: record.timestamp
  });

  // Update usage-by-model
  await updateAnalytics('usage-by-model', `${record.provider}/${record.model}`, {
    totalCalls: increment(),
    totalTokens: add(record.totalTokens),
    avgDuration: average(record.duration),
    errorRate: calculateErrorRate(record.success)
  });

  // Update daily costs
  const date = record.timestamp.split('T')[0];
  await updateAnalytics('costs-daily', date, {
    totalCost: add(record.cost),
    byProvider: { [record.provider]: add(record.cost) },
    byApp: { [record.app]: add(record.cost) }
  });
}
```

### 3. Cost Calculation

```typescript
// _config/costs.json
{
  "models": {
    "groq/llama-3.3-70b-versatile": {
      "input_per_1k": 0.00059,
      "output_per_1k": 0.00079
    },
    "openai/gpt-4o": {
      "input_per_1k": 0.005,
      "output_per_1k": 0.015
    },
    "anthropic/claude-3-sonnet": {
      "input_per_1k": 0.003,
      "output_per_1k": 0.015
    }
  }
}

// src/scripts/calculate-cost.ts
export function calculateCost(
  provider: string,
  model: string,
  promptTokens: number,
  completionTokens: number
): number {
  const costs = loadCosts();
  const modelKey = `${provider}/${model}`;
  const modelCost = costs.models[modelKey];

  if (!modelCost) {
    console.warn(`No cost data for ${modelKey}, using estimate`);
    return (promptTokens + completionTokens) * 0.00001; // Fallback estimate
  }

  const inputCost = (promptTokens / 1000) * modelCost.input_per_1k;
  const outputCost = (completionTokens / 1000) * modelCost.output_per_1k;

  return inputCost + outputCost;
}
```

### 4. Model Selection

```typescript
// _config/models.json
{
  "models": {
    "groq/llama-3.3-70b-versatile": {
      "provider": "groq",
      "capabilities": ["text", "json"],
      "maxTokens": 8192,
      "speed": "fast",
      "quality": "high",
      "costTier": "low"
    },
    "openai/gpt-4o": {
      "provider": "openai",
      "capabilities": ["text", "json", "vision"],
      "maxTokens": 4096,
      "speed": "medium",
      "quality": "very_high",
      "costTier": "high"
    }
  },
  "defaults": {
    "fast": "groq/llama-3.3-70b-versatile",
    "quality": "openai/gpt-4o",
    "vision": "openai/gpt-4o"
  }
}

// src/scripts/select-model.ts
export function selectModel(options: SelectModelOptions): ModelSelection {
  const { taskType, sectionId, preferSpeed, preferQuality, requireVision } = options;

  // Check section-specific override
  const sectionModels = loadSectionModels();
  if (sectionId && sectionModels.overrides?.[sectionId]) {
    return sectionModels.overrides[sectionId];
  }

  // Check task type default
  if (taskType && sectionModels.pathwayDefaults?.[taskType]) {
    return sectionModels.pathwayDefaults[taskType];
  }

  // Use preference-based selection
  const models = loadModels();
  if (requireVision) return models.defaults.vision;
  if (preferSpeed) return models.defaults.fast;
  if (preferQuality) return models.defaults.quality;

  return models.defaults.fast; // Default to fast
}
```

### 5. Retry Logic

```typescript
// src/scripts/retry-failed.ts
interface RetryOptions {
  maxRetries: number;
  initialDelay: number;    // ms
  maxDelay: number;        // ms
  backoffFactor: number;
  retryableErrors: string[];
}

const DEFAULT_RETRY_OPTIONS: RetryOptions = {
  maxRetries: 3,
  initialDelay: 1000,
  maxDelay: 30000,
  backoffFactor: 2,
  retryableErrors: ['rate_limit', 'timeout', 'server_error']
};

export async function callWithRetry<T>(
  fn: () => Promise<T>,
  options: Partial<RetryOptions> = {}
): Promise<T> {
  const opts = { ...DEFAULT_RETRY_OPTIONS, ...options };
  let lastError: Error;
  let delay = opts.initialDelay;

  for (let attempt = 1; attempt <= opts.maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      lastError = error;

      if (!isRetryable(error, opts.retryableErrors)) {
        throw error;
      }

      if (attempt < opts.maxRetries) {
        await sleep(delay);
        delay = Math.min(delay * opts.backoffFactor, opts.maxDelay);
      }
    }
  }

  throw lastError;
}
```

---

## Public API

```typescript
// src/index.ts - AICaller Public API

// Main call function
export async function call(
  app: string,
  prompt: AIPrompt,
  options?: CallOptions
): Promise<AIResponse>;

// Streaming call
export async function stream(
  app: string,
  prompt: AIPrompt,
  options?: StreamOptions
): AsyncGenerator<string>;

// Get model for task
export function selectModel(options: SelectModelOptions): ModelSelection;

// Check rate limit status
export async function checkRateLimit(
  provider: string,
  estimatedTokens: number
): Promise<RateLimitStatus>;

// Test connection to provider
export async function testConnection(provider: string): Promise<ConnectionTest>;

// Get usage stats
export async function getUsageStats(app: string): Promise<UsageStats>;

// Get cost report
export async function getCostReport(
  startDate: string,
  endDate: string
): Promise<CostReport>;
```

---

## Migration Steps

### Step 1: Create AICaller Shell

```bash
mkdir C:\Users\Business\AICaller
cd C:\Users\Business\AICaller
npm init -y
npm install typescript ts-node
mkdir src src/scripts src/types _config analytics queue
```

### Step 2: Create Config Files

```bash
# _config/models.json - Available models
# _config/providers.json - Provider endpoints
# _config/rate-limits.json - Rate limits
# _config/costs.json - Cost per token
# _config/section-models.json - Copy from BrandKit
```

### Step 3: Extract Core Function

```typescript
// Copy callAI() from ai-client.ts to call-model.ts
// Add:
// - Contract checking (which apps can call)
// - Rate limit checking
// - Usage tracking
// - Cost calculation
```

### Step 4: Add New Features

```bash
# Add:
# - check-rate-limit.ts
# - track-usage.ts
# - retry-failed.ts
# - select-model.ts
```

### Step 5: Create Public API

```typescript
// src/index.ts
// Unified interface with app identification
```

### Step 6: Update BrandKit Workflow

```typescript
// Before
import { callAI } from '$lib/server/ai-client';
const response = await callAI(prompt, { provider: 'groq', model: 'llama-3.3-70b-versatile' });

// After
import { AICaller } from '@services/ai-caller';
const response = await AICaller.call('brandkit-workflow', prompt, {
  taskType: 'brand-generation',
  sectionId: 'voice/tone'
});
```

---

## Integration with Current Build

### During Migration

```typescript
// lib/server/ai-client.ts - Add forwarding
export async function callAI(prompt: string, options: AIOptions): Promise<AIResponse> {
  if (process.env.USE_AICALLER_SERVICE === 'true') {
    return AICaller.call('brandkit-workflow', {
      system: options.systemPrompt,
      user: prompt
    }, {
      provider: options.provider,
      model: options.model,
      responseFormat: options.responseFormat
    });
  }

  // Local implementation
  return localCallAI(prompt, options);
}
```

### Provider Configuration

```json
// _config/providers.json
{
  "providers": {
    "groq": {
      "baseUrl": "https://api.groq.com/openai/v1",
      "keyService": "groq",
      "format": "openai-compatible"
    },
    "openai": {
      "baseUrl": "https://api.openai.com/v1",
      "keyService": "openai",
      "format": "openai"
    },
    "anthropic": {
      "baseUrl": "https://api.anthropic.com/v1",
      "keyService": "anthropic",
      "format": "anthropic"
    }
  }
}
```

---

## Dependencies

| Service | Dependency Type | Purpose |
|---------|-----------------|---------|
| **APIKeyManager** | Required | Get API keys for providers |
| **None else required** | - | AICaller is mostly self-contained |

---

## Analytics Dashboard Data

AICaller provides data for monitoring:

```typescript
interface DashboardData {
  // Real-time
  currentRateLimitStatus: Record<string, RateLimitStatus>;
  queuedRequests: number;

  // Daily
  totalCalls: number;
  totalTokens: number;
  totalCost: number;
  errorRate: number;
  avgLatency: number;

  // By dimension
  callsByApp: Record<string, number>;
  callsByModel: Record<string, number>;
  costsByApp: Record<string, number>;
  costsByModel: Record<string, number>;

  // Trends
  dailyCosts: Array<{ date: string; cost: number }>;
  dailyTokens: Array<{ date: string; tokens: number }>;
}
```

---

## Notes

- Every call requires app identification for tracking
- Rate limits are per-provider, enforced before calls
- Costs are calculated and tracked automatically
- Failed calls are retried with exponential backoff
- Model selection considers task type and preferences
- Streaming is supported for real-time responses
- Queue system handles rate-limited requests

---

*Last updated: 2026-01-16*
