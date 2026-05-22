# {{Component Name}} â€” Errors

## Overview

| Category | Code Range | Count | Description |
|----------|------------|-------|-------------|
| Validation | {{PREFIX}}_001-099 | {{n}} | Input validation errors |
| Business | {{PREFIX}}_100-199 | {{n}} | Business rule violations |
| Dependency | {{PREFIX}}_200-299 | {{n}} | Upstream failures |
| Storage | {{PREFIX}}_300-399 | {{n}} | Database errors |
| External | {{PREFIX}}_400-499 | {{n}} | Third-party failures |
| Internal | {{PREFIX}}_500-599 | {{n}} | Unexpected errors |

---

## Error Code Index

| Code | Name | Type | HTTP | Retry |
|------|------|------|------|-------|
| {{PREFIX}}_001 | MISSING_REQUIRED_FIELD | Permanent | 400 | No |
| {{PREFIX}}_002 | INVALID_FIELD_FORMAT | Permanent | 400 | No |
| {{PREFIX}}_101 | NOT_FOUND | Permanent | 404 | No |
| {{PREFIX}}_201 | DEPENDENCY_UNAVAILABLE | Transient | 503 | Yes |
| {{PREFIX}}_301 | STORAGE_WRITE_FAILED | Transient | 500 | Yes |
| {{PREFIX}}_401 | EXTERNAL_API_ERROR | Transient | 502 | Yes |
| {{PREFIX}}_501 | INTERNAL_ERROR | Transient | 500 | No |

---

## Validation Errors ({{PREFIX}}_001-099)

### {{PREFIX}}_001: MISSING_REQUIRED_FIELD

**Cause:** A required field is missing from input.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 400 Bad Request |
| Retryable | No |
| User Action | Provide missing field |

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_001",
    "message": "Required field is missing",
    "field": "{{fieldName}}",
    "details": {
      "required": ["field1", "field2"]
    }
  }
}
```

**Resolution:**
- Check required fields in TYPES.md
- Ensure all required fields are provided

---

### {{PREFIX}}_002: INVALID_FIELD_FORMAT

**Cause:** A field value doesn't match expected format.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 400 Bad Request |
| Retryable | No |
| User Action | Fix field format |

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_002",
    "message": "Invalid field format",
    "field": "{{fieldName}}",
    "details": {
      "expected": "{{expected format}}",
      "received": "{{actual value}}"
    }
  }
}
```

---

### {{PREFIX}}_003: FIELD_TOO_LONG

**Cause:** Field exceeds maximum length.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 400 Bad Request |
| Retryable | No |
| User Action | Shorten field value |

---

### {{PREFIX}}_004: INVALID_ENUM_VALUE

**Cause:** Value not in allowed enum.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 400 Bad Request |
| Retryable | No |
| User Action | Use valid enum value |

---

## Business Errors ({{PREFIX}}_100-199)

### {{PREFIX}}_101: NOT_FOUND

**Cause:** Requested item doesn't exist.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 404 Not Found |
| Retryable | No |
| User Action | Check ID or create item |

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_101",
    "message": "Item not found",
    "details": {
      "id": "{{requested_id}}"
    }
  }
}
```

---

### {{PREFIX}}_102: ALREADY_EXISTS

**Cause:** Item with same ID already exists.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 409 Conflict |
| Retryable | No |
| User Action | Use different ID or update existing |

---

### {{PREFIX}}_103: INVALID_STATE_TRANSITION

**Cause:** Cannot transition from current state.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 400 Bad Request |
| Retryable | No |
| User Action | Check allowed transitions |

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_103",
    "message": "Invalid state transition",
    "details": {
      "currentState": "draft",
      "requestedState": "archived",
      "allowedTransitions": ["active"]
    }
  }
}
```

---

### {{PREFIX}}_104: HAS_DEPENDENTS

**Cause:** Cannot delete item with dependents.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 409 Conflict |
| Retryable | No |
| User Action | Delete dependents first or use cascade |

---

## Dependency Errors ({{PREFIX}}_200-299)

### {{PREFIX}}_201: DEPENDENCY_UNAVAILABLE

**Cause:** Required upstream component unavailable.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 503 Service Unavailable |
| Retryable | Yes |
| Retry Strategy | Exponential backoff, 3 attempts |

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_201",
    "message": "Dependency unavailable",
    "details": {
      "dependency": "{{component_name}}",
      "reason": "timeout"
    }
  }
}
```

---

### {{PREFIX}}_202: DEPENDENCY_ERROR

**Cause:** Upstream component returned error.

| Attribute | Value |
|-----------|-------|
| Type | Depends on upstream |
| HTTP Status | 502 Bad Gateway |
| Retryable | Depends |
| Retry Strategy | Check upstream error |

---

## Storage Errors ({{PREFIX}}_300-399)

### {{PREFIX}}_301: STORAGE_WRITE_FAILED

**Cause:** Database write operation failed.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 500 Internal Server Error |
| Retryable | Yes |
| Retry Strategy | 3 attempts, exponential backoff |

---

### {{PREFIX}}_302: STORAGE_READ_FAILED

**Cause:** Database read operation failed.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 500 Internal Server Error |
| Retryable | Yes |
| Retry Strategy | 3 attempts |

---

### {{PREFIX}}_303: STORAGE_CONNECTION_FAILED

**Cause:** Cannot connect to database.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 503 Service Unavailable |
| Retryable | Yes |
| Retry Strategy | 5 attempts, longer backoff |

---

## External Errors ({{PREFIX}}_400-499)

### {{PREFIX}}_401: EXTERNAL_API_ERROR

**Cause:** Third-party API returned error.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 502 Bad Gateway |
| Retryable | Yes |
| Retry Strategy | 3 attempts |

---

### {{PREFIX}}_402: EXTERNAL_RATE_LIMITED

**Cause:** Third-party API rate limit exceeded.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 429 Too Many Requests |
| Retryable | Yes |
| Retry Strategy | Wait for reset, then retry |

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_402",
    "message": "Rate limit exceeded",
    "details": {
      "provider": "{{provider}}",
      "resetAt": "2026-01-20T10:05:00Z",
      "retryAfter": 300
    }
  }
}
```

---

### {{PREFIX}}_403: EXTERNAL_TIMEOUT

**Cause:** Third-party API request timed out.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 504 Gateway Timeout |
| Retryable | Yes |
| Retry Strategy | 2 attempts with longer timeout |

---

## Internal Errors ({{PREFIX}}_500-599)

### {{PREFIX}}_501: INTERNAL_ERROR

**Cause:** Unexpected internal error.

| Attribute | Value |
|-----------|-------|
| Type | Transient |
| HTTP Status | 500 Internal Server Error |
| Retryable | No |
| User Action | Contact support |

**Note:** Log full stack trace for debugging.

---

### {{PREFIX}}_502: NOT_INITIALIZED

**Cause:** Component not initialized.

| Attribute | Value |
|-----------|-------|
| Type | Permanent |
| HTTP Status | 500 Internal Server Error |
| Retryable | No |
| User Action | Call `initialize()` first |

---

## Error Response Format

All errors follow this structure:

```typescript
interface ErrorResponse {
  success: false;
  error: {
    /** Error code from this document */
    code: string;
    
    /** Human-readable message */
    message: string;
    
    /** Affected field (for validation errors) */
    field?: string;
    
    /** Additional context */
    details?: {
      [key: string]: unknown;
    };
    
    /** Trace ID for debugging */
    traceId?: string;
  };
}
```

---

## Error Handling Strategy

### By Error Type

| Type | Strategy |
|------|----------|
| Validation | Return immediately, no retry |
| Business | Return immediately, no retry |
| Dependency | Retry with backoff, then fail |
| Storage | Retry with backoff, then fail |
| External | Retry with backoff, then degrade |
| Internal | Log, alert, return generic error |

### Retry Configuration

| Error Category | Retries | Backoff | Max Wait |
|----------------|---------|---------|----------|
| Dependency | 3 | Exponential (100ms base) | 5s |
| Storage | 3 | Exponential (100ms base) | 3s |
| External | 3 | Exponential (1s base) | 30s |

---

## Logging Requirements

| Error Type | Log Level | Include |
|------------|-----------|---------|
| Validation | DEBUG | Input that failed |
| Business | INFO | Business context |
| Dependency | WARN | Dependency name, attempt |
| Storage | ERROR | Query, duration |
| External | WARN | Provider, response |
| Internal | ERROR | Full stack trace |

---

## Notes

- All error codes are prefixed with `{{PREFIX}}_`
- Codes are reserved in ranges for future expansion
- New codes should be added to appropriate range
- Update project ERROR-CODES.md after adding codes

---
Source: PRE-SPEC-NOTES.md section 6
Phase: 21 (Internal Component Specs)
---
