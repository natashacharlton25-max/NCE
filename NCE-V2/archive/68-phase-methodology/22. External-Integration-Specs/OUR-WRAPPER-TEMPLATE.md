# {{Provider}} {{Service}} — Our Wrapper

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document describes OUR wrapper functions around the external API — what we expose to our internal systems.

---

## Wrapper Overview

| Field | Value |
|-------|-------|
| **Module Name** | `{{provider}}{{Service}}Client` |
| **Exposes** | {{n}} functions |
| **Used By** | {{list consuming systems}} |
| **Singleton** | Yes / No |

---

## Design Principles

- **Simplify:** Hide provider complexity from consumers
- **Translate:** Convert their types to our types
- **Standardize:** Use our error codes, not theirs
- **Protect:** Handle rate limits, retries internally

---

## Public Functions

### initialize()

Initialize the client. Must be called before other functions.

**Signature:**
```typescript
initialize(config: {{Service}}Config): Promise<InitResult>
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| config | {{Service}}Config | Yes | Client configuration |

**Config Type:**
```typescript
{
  // Required
  apiKey?: string;          // Or use environment variable
  
  // Optional
  timeout?: number;         // Default: 30000ms
  retries?: number;         // Default: 3
  retryDelay?: number;      // Default: 1000ms
}
```

**Returns:**
```typescript
{
  success: boolean;
  clientId: string;
  connectedAt: string;      // ISO timestamp
}
```

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| {{PREFIX}}_INIT_FAILED | Config invalid | Fix config |
| {{PREFIX}}_AUTH_FAILED | Bad credentials | Check API key |

---

### {{functionName}}()

{{One sentence describing what this function does}}

**Signature:**
```typescript
{{functionName}}({{params}}): Promise<{{ReturnType}}>
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {{param}} | {{type}} | Yes/No | {{default}} | {{description}} |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<{{Type}}> | {{description}} |

**Maps to Provider API:**

| Our Function | Their Endpoint | Notes |
|--------------|----------------|-------|
| `{{functionName}}()` | `{{METHOD}} /path` | {{notes}} |

**Process:**
1. Validate input
2. Transform to their format (see FIELD-MAPPING.md)
3. Call provider API
4. Handle errors (see ERRORS.md)
5. Transform response to our format
6. Return result

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| {{PREFIX}}_XXX | {{when}} | {{how}} |

**Example:**
```typescript
// Input
const input = {
  {{field}}: {{value}}
};

// Call
const result = await client.{{functionName}}(input);

// Output
{
  success: true,
  data: {
    {{field}}: {{value}}
  }
}
```

---

### {{functionName2}}()

{{Description}}

**Signature:**
```typescript
{{functionName2}}({{params}}): Promise<{{ReturnType}}>
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| {{param}} | {{type}} | Yes/No | {{description}} |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<{{Type}}> | {{description}} |

**Maps to Provider API:**

| Our Function | Their Endpoint |
|--------------|----------------|
| `{{functionName2}}()` | `{{METHOD}} /path` |

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| {{PREFIX}}_XXX | {{when}} | {{how}} |

---

### {{functionName3}}()

(Repeat pattern for each wrapper function)

---

## Batch Operations

### {{batchFunction}}()

**Signature:**
```typescript
{{batchFunction}}(items: {{ItemType}}[]): Promise<BatchResult>
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| items | {{ItemType}}[] | Yes | Items to process |

**Constraints:**

| Constraint | Value |
|------------|-------|
| Max batch size | {{n}} |
| Parallel requests | {{n}} |
| Timeout per item | {{n}}ms |

**Returns:**
```typescript
{
  success: boolean;
  results: {
    index: number;
    success: boolean;
    data?: {{ResultType}};
    error?: ErrorDetail;
  }[];
  succeeded: number;
  failed: number;
}
```

---

## List/Query Operations

### list{{Resource}}()

**Signature:**
```typescript
list{{Resource}}(options?: ListOptions): Promise<ListResult>
```

**Options:**
```typescript
{
  filter?: {
    {{field}}?: {{type}};
  };
  limit?: number;       // Default: 50, Max: 100
  cursor?: string;      // For pagination
}
```

**Returns:**
```typescript
{
  success: boolean;
  items: {{ResourceType}}[];
  total: number;
  hasMore: boolean;
  nextCursor?: string;
}
```

**Pagination:**
- Uses cursor-based pagination
- Handles provider's pagination internally
- Returns standardized format

---

## Internal Functions (Not Exposed)

### _makeRequest()

Internal function for HTTP calls.

**Signature:**
```typescript
_makeRequest(
  method: string,
  path: string,
  data?: any,
  options?: RequestOptions
): Promise<ProviderResponse>
```

**Handles:**
- Authentication headers
- Rate limit detection
- Retry logic
- Timeout handling
- Error transformation

---

### _handleRateLimit()

Internal rate limit handling.

**Behaviour:**
1. Detect 429 response
2. Read `Retry-After` header
3. Wait specified time
4. Retry request
5. If still limited, emit `{{PREFIX}}_RATE_LIMITED`

---

### _transformError()

Transform provider errors to our error codes.

**Mapping:** See ERRORS.md for full mapping.

---

## Configuration

### Required Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{PREFIX}}_API_KEY` | API key for auth | `sk_live_xxx` |
| `{{PREFIX}}_ENVIRONMENT` | Environment | `production` / `sandbox` |

### Optional Configuration

| Option | Default | Description |
|--------|---------|-------------|
| timeout | 30000 | Request timeout (ms) |
| retries | 3 | Max retry attempts |
| retryDelay | 1000 | Initial retry delay (ms) |
| maxConcurrent | 5 | Max parallel requests |

---

## Error Handling Strategy

| Scenario | Behaviour |
|----------|-----------|
| Network error | Retry up to 3x with backoff |
| Rate limited | Wait + retry (respect Retry-After) |
| Auth error | Fail immediately (no retry) |
| Validation error | Fail immediately (no retry) |
| Provider 5xx | Retry up to 3x with backoff |
| Timeout | Retry once with longer timeout |

---

## Logging

| Event | Level | Data Logged |
|-------|-------|-------------|
| Request start | DEBUG | Method, path (no sensitive data) |
| Request success | DEBUG | Status, duration |
| Request failure | WARN | Status, error code, duration |
| Rate limited | WARN | Retry-after value |
| Auth failure | ERROR | Error code (no credentials) |

**Never log:** API keys, tokens, sensitive request/response data

---

## Health Check

### checkHealth()

**Signature:**
```typescript
checkHealth(): Promise<HealthStatus>
```

**Returns:**
```typescript
{
  healthy: boolean;
  provider: "{{provider}}";
  service: "{{service}}";
  latency: number;        // ms
  lastCheck: string;      // ISO timestamp
  details?: string;       // If unhealthy
}
```

---

## Notes

- All functions are async (return Promises)
- All functions use our error codes (see ERRORS.md)
- All data transformations documented in FIELD-MAPPING.md
- Rate limits handled internally

---
Source: PRE-SPEC-NOTES.md sections 3, 4
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
