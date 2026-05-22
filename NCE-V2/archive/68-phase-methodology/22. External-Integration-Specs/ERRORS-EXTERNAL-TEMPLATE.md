# {{Provider}} {{Service}} — Errors

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document maps the provider's error codes to our internal error codes.

**Key principle:** Consumers of our wrapper never see provider errors directly — they see our standardized error codes.

---

## Error Code Prefix

All errors from this integration use prefix: `{{PREFIX}}_`

Example: `{{PREFIX}}_AUTH_FAILED`, `{{PREFIX}}_RATE_LIMITED`

---

## Error Mapping Summary

| Their HTTP | Their Code | Our Code | Type |
|------------|------------|----------|------|
| 400 | `invalid_request` | `{{PREFIX}}_VALIDATION_001` | Permanent |
| 401 | `unauthorized` | `{{PREFIX}}_AUTH_001` | Permanent* |
| 403 | `forbidden` | `{{PREFIX}}_AUTH_002` | Permanent |
| 404 | `not_found` | `{{PREFIX}}_NOT_FOUND_001` | Permanent |
| 429 | `rate_limited` | `{{PREFIX}}_RATE_001` | Transient |
| 500 | `internal_error` | `{{PREFIX}}_PROVIDER_001` | Transient |
| 503 | `unavailable` | `{{PREFIX}}_PROVIDER_002` | Transient |

*May be transient if token refresh can fix it

---

## Our Error Codes

### Validation Errors ({{PREFIX}}_VALIDATION_XXX)

#### {{PREFIX}}_VALIDATION_001 — Invalid Request

| Field | Value |
|-------|-------|
| **Their Code** | `invalid_request`, `bad_request`, 400 |
| **Type** | Permanent |
| **Retryable** | No |
| **HTTP Status** | 400 |

**Trigger:** Request format invalid, missing required fields.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_VALIDATION_001",
    "message": "Invalid request to {{provider}}",
    "details": {
      "provider_code": "{{their_code}}",
      "provider_message": "{{their_message}}",
      "field": "{{affected_field}}"
    }
  }
}
```

**Recovery:** Fix input data and retry.

---

#### {{PREFIX}}_VALIDATION_002 — Invalid Field Value

| Field | Value |
|-------|-------|
| **Their Code** | `invalid_field`, `validation_error` |
| **Type** | Permanent |
| **Retryable** | No |
| **HTTP Status** | 400 |

**Trigger:** Field value doesn't meet provider's requirements.

**Recovery:** Check field constraints, fix value.

---

### Authentication Errors ({{PREFIX}}_AUTH_XXX)

#### {{PREFIX}}_AUTH_001 — Authentication Failed

| Field | Value |
|-------|-------|
| **Their Code** | `unauthorized`, `invalid_api_key`, 401 |
| **Type** | Permanent (or Transient if token expired) |
| **Retryable** | Only if token refresh available |
| **HTTP Status** | 401 |

**Trigger:** Invalid or expired credentials.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_AUTH_001",
    "message": "Authentication failed with {{provider}}",
    "details": {
      "reason": "invalid_token | expired_token | missing_token"
    }
  }
}
```

**Recovery:** 
- If expired: Refresh token, retry once
- If invalid: Check API key configuration

---

#### {{PREFIX}}_AUTH_002 — Permission Denied

| Field | Value |
|-------|-------|
| **Their Code** | `forbidden`, `insufficient_permissions`, 403 |
| **Type** | Permanent |
| **Retryable** | No |
| **HTTP Status** | 403 |

**Trigger:** Valid auth but insufficient permissions/scopes.

**Recovery:** Check required scopes, update API key permissions.

---

### Not Found Errors ({{PREFIX}}_NOT_FOUND_XXX)

#### {{PREFIX}}_NOT_FOUND_001 — Resource Not Found

| Field | Value |
|-------|-------|
| **Their Code** | `not_found`, `resource_not_found`, 404 |
| **Type** | Permanent |
| **Retryable** | No |
| **HTTP Status** | 404 |

**Trigger:** Requested resource doesn't exist at provider.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_NOT_FOUND_001",
    "message": "Resource not found in {{provider}}",
    "details": {
      "resource_type": "{{type}}",
      "resource_id": "{{id}}"
    }
  }
}
```

**Recovery:** Verify resource ID, check if deleted.

---

### Rate Limit Errors ({{PREFIX}}_RATE_XXX)

#### {{PREFIX}}_RATE_001 — Rate Limited

| Field | Value |
|-------|-------|
| **Their Code** | `rate_limited`, `too_many_requests`, 429 |
| **Type** | Transient |
| **Retryable** | Yes (with backoff) |
| **HTTP Status** | 429 |

**Trigger:** Exceeded provider's rate limit.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_RATE_001",
    "message": "Rate limited by {{provider}}",
    "details": {
      "retry_after": 60,
      "limit": "100/minute",
      "reset_at": "2024-01-15T10:31:00Z"
    }
  }
}
```

**Recovery:** Wait for `retry_after` seconds, then retry.

---

#### {{PREFIX}}_RATE_002 — Quota Exceeded

| Field | Value |
|-------|-------|
| **Their Code** | `quota_exceeded`, `limit_exceeded` |
| **Type** | Permanent (until quota resets) |
| **Retryable** | No (until reset) |
| **HTTP Status** | 429 |

**Trigger:** Exceeded daily/monthly quota.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_RATE_002",
    "message": "Quota exceeded for {{provider}}",
    "details": {
      "quota_type": "daily_requests",
      "limit": 10000,
      "used": 10000,
      "resets_at": "2024-01-16T00:00:00Z"
    }
  }
}
```

**Recovery:** Wait for quota reset or upgrade plan.

---

### Provider Errors ({{PREFIX}}_PROVIDER_XXX)

#### {{PREFIX}}_PROVIDER_001 — Provider Internal Error

| Field | Value |
|-------|-------|
| **Their Code** | `internal_error`, `server_error`, 500 |
| **Type** | Transient |
| **Retryable** | Yes |
| **HTTP Status** | 502 |

**Trigger:** Provider's server error.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_PROVIDER_001",
    "message": "{{provider}} internal error",
    "details": {
      "provider_code": "{{their_code}}",
      "retryable": true,
      "retry_after": 5
    },
    "traceId": "{{trace_id}}"
  }
}
```

**Recovery:** Retry with exponential backoff.

---

#### {{PREFIX}}_PROVIDER_002 — Provider Unavailable

| Field | Value |
|-------|-------|
| **Their Code** | `service_unavailable`, 503 |
| **Type** | Transient |
| **Retryable** | Yes |
| **HTTP Status** | 503 |

**Trigger:** Provider is temporarily down.

**Recovery:** Check provider status page, retry with backoff.

---

### Network Errors ({{PREFIX}}_NETWORK_XXX)

#### {{PREFIX}}_NETWORK_001 — Connection Failed

| Field | Value |
|-------|-------|
| **Their Code** | N/A (network level) |
| **Type** | Transient |
| **Retryable** | Yes |
| **HTTP Status** | 503 |

**Trigger:** Cannot connect to provider (DNS, network, firewall).

**Recovery:** Check network, retry.

---

#### {{PREFIX}}_NETWORK_002 — Request Timeout

| Field | Value |
|-------|-------|
| **Their Code** | N/A (timeout) |
| **Type** | Transient |
| **Retryable** | Yes |
| **HTTP Status** | 504 |

**Trigger:** Request took too long.

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_NETWORK_002",
    "message": "Request to {{provider}} timed out",
    "details": {
      "timeout_ms": 30000,
      "retryable": true
    }
  }
}
```

**Recovery:** Retry, possibly with longer timeout.

---

## Error Mapping Function

```typescript
function mapProviderError(
  httpStatus: number,
  providerError: ProviderError
): OurError {
  
  // Rate limits
  if (httpStatus === 429) {
    if (providerError.code === 'quota_exceeded') {
      return { code: '{{PREFIX}}_RATE_002', ... };
    }
    return { code: '{{PREFIX}}_RATE_001', ... };
  }
  
  // Auth errors
  if (httpStatus === 401) {
    return { code: '{{PREFIX}}_AUTH_001', ... };
  }
  if (httpStatus === 403) {
    return { code: '{{PREFIX}}_AUTH_002', ... };
  }
  
  // Not found
  if (httpStatus === 404) {
    return { code: '{{PREFIX}}_NOT_FOUND_001', ... };
  }
  
  // Validation
  if (httpStatus === 400) {
    return { code: '{{PREFIX}}_VALIDATION_001', ... };
  }
  
  // Provider errors
  if (httpStatus >= 500) {
    if (httpStatus === 503) {
      return { code: '{{PREFIX}}_PROVIDER_002', ... };
    }
    return { code: '{{PREFIX}}_PROVIDER_001', ... };
  }
  
  // Unknown
  return { code: '{{PREFIX}}_UNKNOWN', ... };
}
```

---

## Retry Strategy by Error

| Our Code | Retry | Max Attempts | Backoff |
|----------|-------|--------------|---------|
| `{{PREFIX}}_VALIDATION_*` | No | — | — |
| `{{PREFIX}}_AUTH_001` | Once (if refresh) | 1 | — |
| `{{PREFIX}}_AUTH_002` | No | — | — |
| `{{PREFIX}}_NOT_FOUND_*` | No | — | — |
| `{{PREFIX}}_RATE_001` | Yes | 3 | Respect Retry-After |
| `{{PREFIX}}_RATE_002` | No | — | — |
| `{{PREFIX}}_PROVIDER_*` | Yes | 3 | Exponential |
| `{{PREFIX}}_NETWORK_*` | Yes | 3 | Exponential |

---

## Error Response Format

All errors from this integration follow this format:

```typescript
{
  success: false;
  error: {
    code: string;           // Our error code
    message: string;        // Human-readable message
    details?: {
      provider_code?: string;   // Their original code
      provider_message?: string; // Their original message
      field?: string;           // Affected field
      retry_after?: number;     // Seconds to wait
      retryable?: boolean;      // Can retry?
    };
    traceId?: string;       // For debugging
  }
}
```

---

## Notes

- Never expose raw provider errors to consumers
- Always include traceId for debugging
- Log original provider error internally
- Update mapping when provider changes error format

---
Source: PRE-SPEC-NOTES.md section 7
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
