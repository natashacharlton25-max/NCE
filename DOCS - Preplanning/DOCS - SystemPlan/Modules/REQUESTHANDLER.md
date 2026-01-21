# RequestHandler Module

> **Purpose:** Standardized HTTP request management for external API calls
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

RequestHandler provides a **unified interface** for all external HTTP requests. It handles retries, timeouts, error normalization, and logging - so individual modules don't have to.

```
Module → RequestHandler.request() → External API → Normalized response
```

**RequestHandler standardizes:**
- Request formatting
- Retry logic
- Timeout handling
- Error normalization
- Response parsing
- Request logging

---

## Roles & Rules

### RequestHandler DOES:
- Make HTTP requests (GET, POST, PUT, DELETE)
- Handle retries with backoff
- Normalize errors across APIs
- Log all external requests
- Manage timeouts
- Parse responses

### RequestHandler does NOT:
- Rate limit (RateLimiter does that)
- Authenticate providers (APIKeyManager does that)
- Cache responses (CacheHandler does that)
- Decide which endpoint to call (modules do that)

### Boundaries:
- HTTP operations only
- Cannot bypass rate limiter
- All requests logged
- Must use configured timeouts

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `request(config)` | Make HTTP request | config | response |
| `get(url, options)` | GET request | url, options | response |
| `post(url, body, options)` | POST request | url, body, options | response |
| `put(url, body, options)` | PUT request | url, body, options | response |
| `delete(url, options)` | DELETE request | url, options | response |

---

## Detailed Function Specs

### request(config)

Make an HTTP request.

```javascript
// Input
{
  method: "POST",
  url: "https://api.openai.com/v1/chat/completions",
  headers: {
    "Authorization": "Bearer {{apiKey}}",
    "Content-Type": "application/json"
  },
  body: {
    model: "gpt-4-turbo",
    messages: [...]
  },
  options: {
    timeout: 180000,
    retries: 3,
    retryOn: [429, 500, 502, 503, 504],
    backoff: "exponential"
  }
}

// Output - Success
{
  success: true,
  status: 200,
  headers: {
    "x-ratelimit-remaining": "45",
    "content-type": "application/json"
  },
  body: {
    id: "chatcmpl-abc123",
    choices: [...]
  },
  timing: {
    total: 4523,
    dns: 50,
    connect: 100,
    ttfb: 4000,
    download: 373
  },
  attempts: 1
}

// Output - Failure
{
  success: false,
  status: 429,
  error: {
    type: "rate_limit",
    message: "Rate limit exceeded",
    retryAfter: 60,
    provider: "openai"
  },
  attempts: 3,
  timing: {
    total: 15000
  }
}
```

### get/post/put/delete shortcuts

Convenience methods for common operations.

```javascript
// GET
const response = await RequestHandler.get(
  "https://api.example.com/data",
  { headers: {...}, timeout: 30000 }
);

// POST
const response = await RequestHandler.post(
  "https://api.example.com/create",
  { name: "New Item" },
  { headers: {...} }
);
```

---

## Retry Logic

```javascript
{
  retries: 3,
  retryOn: [429, 500, 502, 503, 504],
  backoff: {
    type: "exponential",
    initial: 1000,
    max: 30000,
    multiplier: 2
  }
}

// Retry timeline:
// Attempt 1: Immediate
// Attempt 2: After 1 second (on retryable error)
// Attempt 3: After 2 seconds
// Attempt 4: After 4 seconds (max retries reached)
```

---

## Error Normalization

All errors normalized to consistent format:

```javascript
// Input: Various provider error formats
// OpenAI: { error: { message: "...", type: "..." } }
// Anthropic: { error: { type: "...", message: "..." } }
// Generic: { message: "..." }

// Output: Normalized format
{
  type: "rate_limit",      // Normalized error type
  message: "Rate limit exceeded",
  status: 429,
  provider: "openai",
  retryable: true,
  retryAfter: 60,         // seconds
  originalError: {...}    // Raw error for debugging
}

// Error types
// - rate_limit
// - timeout
// - server_error
// - auth_error
// - bad_request
// - network_error
// - content_filter
// - unknown
```

---

## Timeout Handling

```javascript
{
  timeouts: {
    default: 30000,       // 30 seconds
    ai_call: 180000,      // 3 minutes
    image_gen: 300000,    // 5 minutes
    file_upload: 60000    // 1 minute
  }
}

// Timeout error
{
  success: false,
  error: {
    type: "timeout",
    message: "Request timed out after 30000ms",
    duration: 30000,
    retryable: true
  }
}
```

---

## Request Logging

All requests logged for debugging:

```json
{
  "timestamp": "2026-01-17T14:30:52Z",
  "requestId": "req_abc123",
  "method": "POST",
  "url": "https://api.openai.com/v1/chat/completions",
  "status": 200,
  "duration": 4523,
  "attempts": 1,
  "success": true
}
```

Sensitive data (API keys, tokens) are redacted in logs.

---

## Config

### module.config.json

```json
{
  "module": "RequestHandler",
  "version": "1.0.0",
  "description": "Standardized HTTP request management",
  "type": "system-service",

  "functions": [
    {
      "name": "request",
      "description": "Make HTTP request",
      "input": ["config"],
      "output": "response"
    },
    {
      "name": "get",
      "description": "GET request",
      "input": ["url", "options"],
      "output": "response"
    },
    {
      "name": "post",
      "description": "POST request",
      "input": ["url", "body", "options"],
      "output": "response"
    },
    {
      "name": "put",
      "description": "PUT request",
      "input": ["url", "body", "options"],
      "output": "response"
    },
    {
      "name": "delete",
      "description": "DELETE request",
      "input": ["url", "options"],
      "output": "response"
    }
  ],

  "config": {
    "defaultTimeout": 30000,
    "maxRetries": 3,
    "retryableStatus": [429, 500, 502, 503, 504],
    "userAgent": "NCE-System/1.0",
    "logRequests": true,
    "redactKeys": ["authorization", "api-key", "x-api-key"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| RateLimiter | Rate limit checks |
| Logger | Request logging |

---

## Used By

| Component | How |
|-----------|-----|
| AICaller | AI API requests |
| All integrations | External API calls |

---

*Last updated: 2026-01-17*
