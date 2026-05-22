# {{Provider}} {{Service}} — API Surface

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document describes the EXTERNAL API we consume — what the provider exposes, not what we build.

---

## API Overview

| Field | Value |
|-------|-------|
| **Provider** | {{provider name}} |
| **Service/API** | {{service name}} |
| **Base URL** | `{{base_url}}` |
| **API Version** | {{version}} |
| **Documentation** | {{link to official docs}} |
| **Status Page** | {{link to status page}} |

---

## Authentication

| Field | Value |
|-------|-------|
| **Method** | API Key / OAuth 2.0 / Bearer Token / Service Account |
| **Header/Param** | `{{header or param name}}` |
| **Scopes Required** | {{list scopes}} |
| **Token Lifetime** | {{duration or N/A}} |
| **Refresh Mechanism** | {{how to refresh or N/A}} |

**Notes:**
- {{any auth-specific notes}}

---

## Endpoints We Use

### {{Endpoint Name}}

| Field | Value |
|-------|-------|
| **Method** | GET / POST / PUT / PATCH / DELETE |
| **Path** | `{{/path/to/endpoint}}` |
| **Purpose** | {{what it does}} |
| **Rate Limit** | {{X requests per Y}} |

**Request:**

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| {{param}} | path / query / header / body | {{type}} | Yes / No | {{description}} |
| {{param}} | {{location}} | {{type}} | Yes / No | {{description}} |

**Request Body (if applicable):**
```json
{
  "{{field}}": "{{type}} — {{description}}",
  "{{field}}": "{{type}} — {{description}}"
}
```

**Response (Success):**

| Field | Type | Description |
|-------|------|-------------|
| {{field}} | {{type}} | {{description}} |
| {{field}} | {{type}} | {{description}} |

**Response Example:**
```json
{
  "{{field}}": "{{example_value}}",
  "{{field}}": "{{example_value}}"
}
```

**Errors:**

| Status | Code | Meaning |
|--------|------|---------|
| 400 | {{code}} | {{meaning}} |
| 401 | {{code}} | {{meaning}} |
| 404 | {{code}} | {{meaning}} |
| 429 | {{code}} | {{meaning}} |

---

### {{Endpoint Name 2}}

| Field | Value |
|-------|-------|
| **Method** | {{method}} |
| **Path** | `{{path}}` |
| **Purpose** | {{purpose}} |
| **Rate Limit** | {{limit}} |

**Request:**

| Parameter | Location | Type | Required | Description |
|-----------|----------|------|----------|-------------|
| {{param}} | {{location}} | {{type}} | Yes / No | {{description}} |

**Response (Success):**

| Field | Type | Description |
|-------|------|-------------|
| {{field}} | {{type}} | {{description}} |

---

### {{Endpoint Name 3}}

(Repeat pattern for each endpoint we use)

---

## Endpoints We Do NOT Use

| Endpoint | Why Not Used |
|----------|--------------|
| `{{/path}}` | {{reason}} |
| `{{/path}}` | {{reason}} |

---

## Data Types (Their Schema)

### {{TypeName}}

Their definition of this object:

```json
{
  "id": "string — unique identifier",
  "name": "string — display name",
  "created_at": "string — ISO 8601 timestamp",
  "status": "string — enum: active, inactive, deleted",
  "metadata": {
    "{{field}}": "{{type}}"
  }
}
```

| Field | Type | Nullable | Description |
|-------|------|----------|-------------|
| id | string | No | {{description}} |
| name | string | No | {{description}} |
| created_at | string | No | {{description}} |
| status | string | No | {{description}} |
| metadata | object | Yes | {{description}} |

---

### {{TypeName 2}}

(Repeat for each type we receive from them)

---

## Pagination

| Field | Value |
|-------|-------|
| **Style** | Offset / Cursor / Page Number / None |
| **Default Page Size** | {{n}} |
| **Max Page Size** | {{n}} |
| **Params** | `{{param names}}` |

**Example:**
```
GET /items?limit=20&cursor=abc123
```

**Response includes:**
```json
{
  "data": [...],
  "next_cursor": "def456",
  "has_more": true
}
```

---

## Rate Limits

| Limit Type | Value | Window | Notes |
|------------|-------|--------|-------|
| Requests | {{n}} | per minute / hour / day | {{notes}} |
| Throughput | {{n}} MB/s | — | {{notes}} |
| Concurrent | {{n}} | connections | {{notes}} |

**Rate Limit Headers:**

| Header | Meaning |
|--------|---------|
| `X-RateLimit-Limit` | {{meaning}} |
| `X-RateLimit-Remaining` | {{meaning}} |
| `X-RateLimit-Reset` | {{meaning}} |

---

## Error Response Format

Their standard error format:

```json
{
  "error": {
    "code": "{{error_code}}",
    "message": "{{human readable message}}",
    "details": {
      "{{field}}": "{{value}}"
    }
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| error.code | string | Machine-readable error code |
| error.message | string | Human-readable message |
| error.details | object | Additional context (optional) |

---

## Common Error Codes

| HTTP Status | Their Code | Meaning | Retryable |
|-------------|------------|---------|-----------|
| 400 | `invalid_request` | Bad request format | No |
| 401 | `unauthorized` | Invalid/expired auth | Maybe (refresh) |
| 403 | `forbidden` | Insufficient permissions | No |
| 404 | `not_found` | Resource doesn't exist | No |
| 429 | `rate_limited` | Too many requests | Yes (backoff) |
| 500 | `internal_error` | Their server error | Yes |
| 503 | `service_unavailable` | Temporary outage | Yes |

---

## Webhooks (if applicable)

### {{Webhook Event}}

| Field | Value |
|-------|-------|
| **Event Type** | `{{event.type}}` |
| **Trigger** | {{when it fires}} |
| **Payload** | See below |

**Payload:**
```json
{
  "event": "{{event.type}}",
  "timestamp": "{{ISO 8601}}",
  "data": {
    "{{field}}": "{{value}}"
  }
}
```

---

## API Versioning

| Field | Value |
|-------|-------|
| **Current Version** | {{version}} |
| **Version in URL** | Yes (`/v1/`) / No |
| **Version in Header** | `{{header name}}` / No |
| **Deprecation Policy** | {{policy}} |
| **Sunset Header** | Yes / No |

---

## SDK / Client Libraries

| Language | Official SDK | Notes |
|----------|--------------|-------|
| JavaScript | {{package name or N/A}} | {{notes}} |
| Python | {{package name or N/A}} | {{notes}} |
| Go | {{package name or N/A}} | {{notes}} |

**Our approach:** Use SDK / Use raw HTTP / {{other}}

---

## Notes

- {{Any additional notes about their API}}
- {{Quirks, gotchas, undocumented behavior}}

---
Source: PRE-SPEC-NOTES.md section 5
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
