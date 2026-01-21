# RateLimiter Module

> **Purpose:** Centralized rate limiting for all external API calls
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

RateLimiter is the **traffic controller** for all external API calls. It ensures we don't exceed provider rate limits, manages request queuing, and provides backoff strategies when limits are hit.

```
Request → RateLimiter.canProceed() → Allowed/Queued → Module proceeds
```

**RateLimiter protects against:**
- Exceeding API provider limits
- Getting banned/throttled
- Wasting money on failed requests
- Overwhelming external services

---

## Roles & Rules

### RateLimiter DOES:
- Track request counts per provider/endpoint
- Enforce rate limits (requests/minute, tokens/minute)
- Queue requests when limits are near
- Implement exponential backoff on 429 errors
- Provide wait time estimates
- Track rate limit headers from responses

### RateLimiter does NOT:
- Make API calls (modules do that)
- Decide which provider to use (ModelManager does that)
- Handle retries (FailureHandler coordinates that)
- Track costs (CostBudgetManager does that)

### Boundaries:
- Read-only access to rate limit configs
- Cannot bypass limits
- Must respect provider-specific rules
- Updates limits based on response headers

---

## Rate Limit Types

### Per-Provider Limits

| Provider | Requests/Min | Tokens/Min | Daily Limit |
|----------|--------------|------------|-------------|
| OpenAI | 60 | 90,000 | 10,000 requests |
| Anthropic | 60 | 100,000 | Unlimited |
| Google | 60 | 32,000 | 1,500 requests |
| OpenRouter | 200 | 200,000 | Based on tier |

### Per-Endpoint Limits

| Endpoint Type | Limit | Notes |
|---------------|-------|-------|
| Chat Completions | 60/min | Standard generation |
| Embeddings | 3000/min | Higher throughput |
| Image Generation | 5/min | Lower limit |
| Moderation | 1000/min | High throughput |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `canProceed(request)` | Check if request can proceed | request | decision |
| `recordRequest(request)` | Record a request was made | request | recorded |
| `recordResponse(response)` | Update limits from response headers | response | updated |
| `getWaitTime(provider)` | Get wait time until can proceed | provider | waitMs |
| `getLimitStatus(provider)` | Get current limit status | provider | status |
| `resetLimits(provider)` | Manually reset limits | provider | reset |

---

## Detailed Function Specs

### canProceed(request)

Check if a request can proceed now.

```javascript
// Input
{
  provider: "openai",
  endpoint: "chat/completions",
  estimatedTokens: 500
}

// Output - Can proceed
{
  canProceed: true,
  provider: "openai",
  remaining: {
    requests: 45,
    tokens: 75000
  },
  resetIn: 42000  // ms until limits reset
}

// Output - Must wait
{
  canProceed: false,
  provider: "openai",
  reason: "request_limit",
  waitTime: 15000,  // ms to wait
  suggestion: "Wait 15 seconds or use alternative provider",
  alternatives: ["anthropic", "google"]
}
```

### recordRequest(request)

Record that a request was made (call before API call).

```javascript
// Input
{
  provider: "openai",
  endpoint: "chat/completions",
  estimatedTokens: 500,
  requestId: "req_abc123"
}

// Output
{
  recorded: true,
  requestId: "req_abc123",
  counters: {
    requests: { current: 46, limit: 60, period: "minute" },
    tokens: { current: 75500, limit: 90000, period: "minute" }
  }
}
```

### recordResponse(response)

Update limits based on response headers.

```javascript
// Input
{
  provider: "openai",
  requestId: "req_abc123",
  headers: {
    "x-ratelimit-limit-requests": "60",
    "x-ratelimit-remaining-requests": "45",
    "x-ratelimit-reset-requests": "42s",
    "x-ratelimit-limit-tokens": "90000",
    "x-ratelimit-remaining-tokens": "75000"
  },
  actualTokens: 470
}

// Output
{
  updated: true,
  provider: "openai",
  limits: {
    requests: { remaining: 45, resetIn: 42000 },
    tokens: { remaining: 75000, resetIn: 42000 }
  }
}
```

### getWaitTime(provider)

Get time to wait before next request is allowed.

```javascript
// Input
{
  provider: "openai"
}

// Output
{
  waitTime: 0,  // Can proceed now
  nextWindow: "2026-01-17T14:31:00Z",
  status: "available"
}

// Or must wait
{
  waitTime: 15000,  // Wait 15 seconds
  nextWindow: "2026-01-17T14:31:00Z",
  status: "rate_limited",
  reason: "request_limit_exceeded"
}
```

### getLimitStatus(provider)

Get current limit status for a provider.

```javascript
// Input
{
  provider: "openai"
}

// Output
{
  provider: "openai",
  status: "healthy",  // "healthy" | "approaching" | "limited" | "backoff"
  limits: {
    requests: {
      used: 15,
      limit: 60,
      remaining: 45,
      percentUsed: 25,
      resetAt: "2026-01-17T14:31:00Z"
    },
    tokens: {
      used: 15000,
      limit: 90000,
      remaining: 75000,
      percentUsed: 17,
      resetAt: "2026-01-17T14:31:00Z"
    }
  },
  backoff: null
}

// When in backoff
{
  provider: "openai",
  status: "backoff",
  backoff: {
    reason: "429_received",
    startedAt: "2026-01-17T14:30:45Z",
    waitUntil: "2026-01-17T14:31:45Z",
    attempt: 2
  }
}
```

---

## Backoff Strategy

When a 429 (rate limit) error is received:

```javascript
// Exponential backoff with jitter
{
  attempt: 1,
  baseDelay: 1000,      // 1 second
  maxDelay: 60000,      // 60 seconds max
  multiplier: 2,
  jitter: 0.1           // ±10% randomization
}

// Resulting delays:
// Attempt 1: ~1 second
// Attempt 2: ~2 seconds
// Attempt 3: ~4 seconds
// Attempt 4: ~8 seconds
// Attempt 5: ~16 seconds
// ... up to 60 seconds max
```

---

## Sliding Window

RateLimiter uses sliding window for accurate counting:

```javascript
// Instead of fixed windows (e.g., reset every minute on the minute)
// Use sliding window (last 60 seconds from now)

{
  window: "sliding",
  duration: 60000,  // 60 seconds

  // At 14:30:45, counts requests from 14:29:45 to 14:30:45
  // At 14:30:46, counts requests from 14:29:46 to 14:30:46

  // This prevents burst issues at window boundaries
}
```

---

## Multi-Provider Coordination

When one provider is rate limited, suggest alternatives:

```javascript
// OpenAI rate limited
{
  canProceed: false,
  provider: "openai",
  alternatives: [
    {
      provider: "anthropic",
      status: "available",
      waitTime: 0,
      recommendation: "primary_alternative"
    },
    {
      provider: "google",
      status: "available",
      waitTime: 0,
      recommendation: "secondary_alternative"
    }
  ],
  suggestion: "Switch to Anthropic for immediate processing"
}
```

---

## Config

### module.config.json

```json
{
  "module": "RateLimiter",
  "version": "1.0.0",
  "description": "Centralized rate limiting",
  "type": "system-service",

  "functions": [
    {
      "name": "canProceed",
      "description": "Check if request can proceed",
      "input": ["request"],
      "output": "decision"
    },
    {
      "name": "recordRequest",
      "description": "Record a request was made",
      "input": ["request"],
      "output": "recorded"
    },
    {
      "name": "recordResponse",
      "description": "Update limits from response",
      "input": ["response"],
      "output": "updated"
    },
    {
      "name": "getWaitTime",
      "description": "Get wait time until can proceed",
      "input": ["provider"],
      "output": "waitMs"
    },
    {
      "name": "getLimitStatus",
      "description": "Get current limit status",
      "input": ["provider"],
      "output": "status"
    },
    {
      "name": "resetLimits",
      "description": "Manually reset limits",
      "input": ["provider"],
      "output": "reset"
    }
  ],

  "config": {
    "windowType": "sliding",
    "windowDuration": 60000,
    "backoffBase": 1000,
    "backoffMax": 60000,
    "backoffMultiplier": 2,
    "warningThreshold": 0.8
  },

  "providers": {
    "openai": {
      "requestsPerMinute": 60,
      "tokensPerMinute": 90000,
      "dailyRequests": 10000
    },
    "anthropic": {
      "requestsPerMinute": 60,
      "tokensPerMinute": 100000
    },
    "google": {
      "requestsPerMinute": 60,
      "tokensPerMinute": 32000,
      "dailyRequests": 1500
    }
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Logger | Logging rate limit events |

---

## Used By

| Component | How |
|-----------|-----|
| AICaller | Check before API calls |
| AISystemManager | Provider status |
| PolicyEngine | Rate limit policy checks |

---

*Last updated: 2026-01-17*
