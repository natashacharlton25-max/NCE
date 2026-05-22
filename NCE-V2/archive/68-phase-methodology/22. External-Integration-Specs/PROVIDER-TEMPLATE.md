# {{Provider Name}} — Provider Configuration

---
Provider: {{provider_name}}
Type: External Integration Provider (System-Level)
Services: {{list of services}} (each is Subsystem-Level)
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
Location: /integrations/{{provider}}/PROVIDER.md
---

## Hierarchy

```
integrations/
└── {{provider}}/              ← THIS FILE (Provider = System-level)
    ├── PROVIDER.md            ← Shared auth, config, rate limits
    ├── {{service-1}}/         ← Service = Subsystem-level
    │   └── spec/              ← Full 9-file spec
    ├── {{service-2}}/
    │   └── spec/
    └── {{service-3}}/
        └── spec/
```

**Provider** owns shared configuration (auth, base URL, global rate limits).
**Services** own specific API operations (each gets full spec treatment).

---

## Overview

{{1-2 sentences describing this provider and what it offers}}

---

## Provider Information

| Attribute | Value |
|-----------|-------|
| Provider | {{provider_name}} |
| Website | {{url}} |
| Documentation | {{docs_url}} |
| API Version | {{version}} |
| Status Page | {{status_url}} |

---

## Authentication

### Method

| Attribute | Value |
|-----------|-------|
| Auth Type | API Key / OAuth 2.0 / JWT / Basic |
| Key Location | Header / Query / Body |
| Header Name | `{{header_name}}` (e.g., Authorization, X-API-Key) |
| Format | `Bearer {{token}}` / `{{key}}` |

### Credentials Required

| Credential | Environment Variable | Required | Description |
|------------|---------------------|----------|-------------|
| API Key | `{{PROVIDER}}_API_KEY` | Yes | Primary API key |
| Secret Key | `{{PROVIDER}}_SECRET_KEY` | Yes/No | For signing (if needed) |
| Webhook Secret | `{{PROVIDER}}_WEBHOOK_SECRET` | No | For validating webhooks |

### Environment Setup

```bash
# .env.example
{{PROVIDER}}_API_KEY=sk_test_...
{{PROVIDER}}_SECRET_KEY=whsec_...
{{PROVIDER}}_WEBHOOK_SECRET=whsec_...
{{PROVIDER}}_API_URL=https://api.{{provider}}.com  # Optional override
```

### Test vs Production

| Environment | Key Prefix | API URL |
|-------------|------------|---------|
| Test/Sandbox | `sk_test_` | `https://api.{{provider}}.com` |
| Production | `sk_live_` | `https://api.{{provider}}.com` |

---

## Base Configuration

### API Base URL

| Environment | URL |
|-------------|-----|
| Production | `https://api.{{provider}}.com/v1` |
| Sandbox | `https://sandbox.{{provider}}.com/v1` |

### Default Headers

```typescript
{
  "Authorization": "Bearer {{API_KEY}}",
  "Content-Type": "application/json",
  "{{Provider}}-Version": "{{api_version}}"  // If versioned
}
```

### Request Configuration

| Setting | Value | Notes |
|---------|-------|-------|
| Timeout | {{seconds}}s | Per-request timeout |
| Retries | 3 | With exponential backoff |
| Base delay | 100ms | Initial retry delay |
| Max delay | 5s | Maximum retry delay |

---

## Services

This provider offers the following services:

| Service | Purpose | Folder |
|---------|---------|--------|
| {{service_name}} | {{purpose}} | `./{{service}}/` |
| {{service_name}} | {{purpose}} | `./{{service}}/` |
| {{service_name}} | {{purpose}} | `./{{service}}/` |

### Service Relationships

```
{{Provider}}
├── {{service_a}}           # Independent
├── {{service_b}}           # Uses {{service_a}} data
└── {{service_c}}           # Independent
```

---

## Global Rate Limits

Limits that apply across ALL services for this provider:

| Limit Type | Value | Scope | Notes |
|------------|-------|-------|-------|
| Requests/second | {{n}} | Per API key | Across all endpoints |
| Requests/day | {{n}} | Per account | Total daily |
| Concurrent | {{n}} | Per API key | Simultaneous requests |

### Rate Limit Headers

| Header | Meaning |
|--------|---------|
| `X-RateLimit-Limit` | Max requests in window |
| `X-RateLimit-Remaining` | Requests left |
| `X-RateLimit-Reset` | Unix timestamp when resets |
| `Retry-After` | Seconds to wait (on 429) |

### Rate Limit Handling

```typescript
// Pseudocode
if (response.status === 429) {
  const retryAfter = response.headers['retry-after'] || 60;
  await sleep(retryAfter * 1000);
  return retry(request);
}
```

---

## Global Error Handling

Errors common across all services:

| HTTP Status | Meaning | Our Code | Action |
|-------------|---------|----------|--------|
| 401 | Invalid API key | `{{PREFIX}}_AUTH_FAILED` | Check credentials |
| 403 | Insufficient permissions | `{{PREFIX}}_FORBIDDEN` | Check account tier |
| 429 | Rate limited | `{{PREFIX}}_RATE_LIMITED` | Wait and retry |
| 500 | Provider error | `{{PREFIX}}_PROVIDER_ERROR` | Retry with backoff |
| 503 | Service unavailable | `{{PREFIX}}_UNAVAILABLE` | Retry later |

### Error Response Format

```json
{
  "error": {
    "type": "{{error_type}}",
    "code": "{{error_code}}",
    "message": "{{human_readable_message}}",
    "param": "{{field_name}}",  // Optional
    "doc_url": "{{help_link}}"  // Optional
  }
}
```

---

## Webhooks

### Webhook Configuration

| Attribute | Value |
|-----------|-------|
| Endpoint URL | `https://{{our_domain}}/webhooks/{{provider}}` |
| Signing Secret | `{{PROVIDER}}_WEBHOOK_SECRET` |
| Signature Header | `{{Provider}}-Signature` |
| Timestamp Header | `{{Provider}}-Timestamp` |

### Webhook Verification

```typescript
// Pseudocode
function verifyWebhook(payload: string, signature: string, secret: string): boolean {
  const expectedSig = computeHmac('sha256', secret, payload);
  return timingSafeEqual(signature, expectedSig);
}
```

### Common Webhook Events

| Event | Description | Services |
|-------|-------------|----------|
| `{{event.type}}` | {{description}} | {{which services}} |
| `{{event.type}}` | {{description}} | {{which services}} |

---

## SDK / Client

### Official SDK

| Language | Package | Version |
|----------|---------|---------|
| Node.js | `{{package}}` | `^{{version}}` |
| Python | `{{package}}` | `^{{version}}` |

### Our Wrapper

We wrap the SDK with:
- Consistent error handling
- Type mapping to our types
- Logging and tracing
- Retry logic

Location: `integrations/{{provider}}/client.ts`

---

## Testing

### Sandbox/Test Mode

| Feature | Test Behavior |
|---------|---------------|
| API calls | No real {{actions}} |
| Test data | Use test {{credentials}} |
| Webhooks | Triggered manually in dashboard |

### Test Credentials

| Type | Test Value | Result |
|------|------------|--------|
| {{test_type}} | `{{test_value}}` | {{expected_result}} |
| {{test_type}} | `{{test_value}}` | {{expected_result}} |

---

## Monitoring

### Health Check

```typescript
async function checkProviderHealth(): Promise<boolean> {
  try {
    // Use a lightweight endpoint
    await client.{{healthEndpoint}}();
    return true;
  } catch {
    return false;
  }
}
```

### Metrics to Track

| Metric | Why |
|--------|-----|
| Request latency | Performance |
| Error rate | Reliability |
| Rate limit remaining | Capacity |
| Webhook delivery | Integration health |

---

## Changelog

| Date | Change | Impact |
|------|--------|--------|
| {{date}} | Initial setup | — |
| {{date}} | Added {{service}} | New capabilities |

---

## Notes

{{Any provider-specific notes, gotchas, or tips}}

---
Phase: 22 (External Integration Specs)
Type: Provider Configuration
Generated: {{timestamp}}
---
