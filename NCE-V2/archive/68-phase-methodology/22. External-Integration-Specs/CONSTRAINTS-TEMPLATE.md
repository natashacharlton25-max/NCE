# {{Provider}} {{Service}} — Constraints

---
Component: integration-{{provider}}-{{service}}
Type: External Integration
Provider: {{provider}}
Service: {{service}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document captures all external constraints imposed by the provider: rate limits, quotas, authentication requirements, and costs.

---

## Constraints Summary

| Category | Constraint | Value | Impact |
|----------|------------|-------|--------|
| Rate Limit | Requests/min | {{n}} | Throttling |
| Quota | Daily requests | {{n}} | Hard block |
| Auth | Token expiry | {{duration}} | Re-auth needed |
| Cost | Per request | ${{n}} | Budget impact |
| Size | Max payload | {{n}} MB | Split requests |

---

## Rate Limits

### Request Rate Limits

| Limit Type | Value | Window | Scope |
|------------|-------|--------|-------|
| Requests | {{n}} | per minute | Per API key |
| Requests | {{n}} | per hour | Per API key |
| Requests | {{n}} | per day | Per account |
| Burst | {{n}} | per second | Per API key |

### Rate Limit Headers

| Header | Description | Example |
|--------|-------------|---------|
| `X-RateLimit-Limit` | Max requests in window | `100` |
| `X-RateLimit-Remaining` | Requests left | `45` |
| `X-RateLimit-Reset` | When limit resets | `1705312800` |
| `Retry-After` | Seconds to wait (on 429) | `60` |

### Rate Limit Behaviour

| Scenario | Provider Response | Our Handling |
|----------|-------------------|--------------|
| Approaching limit | Headers show low remaining | Slow down requests |
| At limit | 429 Too Many Requests | Wait for Retry-After |
| Burst exceeded | 429 immediate | Exponential backoff |

### Our Rate Limit Strategy

```
Requests remaining > 20%  → Normal operation
Requests remaining 10-20% → Slow to 50% rate
Requests remaining < 10%  → Slow to 25% rate
Requests remaining = 0    → Wait for reset
```

---

## Quotas

### Usage Quotas

| Quota Type | Limit | Period | Overage |
|------------|-------|--------|---------|
| API calls | {{n}} | per day | Blocked |
| API calls | {{n}} | per month | Blocked or ${{n}}/1000 |
| Storage | {{n}} GB | total | Extra ${{n}}/GB |
| Bandwidth | {{n}} GB | per month | Extra ${{n}}/GB |

### Quota Tracking

| Source | Update Frequency | How to Check |
|--------|------------------|--------------|
| Response headers | Every request | `X-Quota-Used` |
| Dashboard | Real-time | {{provider}} console |
| API endpoint | On demand | `GET /usage` |

### Our Quota Monitoring

- Track usage internally
- Alert at 80% of quota
- Block non-critical operations at 95%
- Critical operations only at 99%

---

## Authentication

### Auth Method

| Field | Value |
|-------|-------|
| **Type** | API Key / OAuth 2.0 / Service Account |
| **Location** | Header: `Authorization: Bearer {{token}}` |
| **Scopes** | {{list required scopes}} |

### Token Lifecycle

| Field | Value |
|-------|-------|
| **Access token lifetime** | {{duration}} |
| **Refresh token lifetime** | {{duration}} |
| **Auto-refresh** | Yes / No |
| **Refresh endpoint** | `POST /oauth/token` |

### Credential Requirements

| Credential | Required | How to Obtain |
|------------|----------|---------------|
| API Key | Yes/No | {{provider}} dashboard |
| Client ID | Yes/No | {{provider}} dashboard |
| Client Secret | Yes/No | {{provider}} dashboard |
| Webhook Secret | Yes/No | {{provider}} dashboard |

### Scopes Required

| Scope | Purpose | Required |
|-------|---------|----------|
| `{{scope}}` | {{what it allows}} | Yes/No |
| `{{scope}}` | {{what it allows}} | Yes/No |
| `{{scope}}` | {{what it allows}} | Yes/No |

### Auth Rotation

| Scenario | Action Required |
|----------|-----------------|
| Key compromised | Regenerate immediately |
| Scheduled rotation | Every {{period}} |
| Token expired | Auto-refresh or re-auth |

---

## Size Limits

### Request Limits

| Limit | Value | Notes |
|-------|-------|-------|
| Max request body | {{n}} MB | Split larger payloads |
| Max URL length | {{n}} chars | Use POST for long queries |
| Max headers size | {{n}} KB | Minimize custom headers |
| Max file upload | {{n}} MB | Chunk larger files |

### Response Limits

| Limit | Value | Notes |
|-------|-------|-------|
| Max response size | {{n}} MB | Paginate large results |
| Max items per page | {{n}} | Use pagination |
| Max nested depth | {{n}} levels | Flatten if needed |

### Batch Limits

| Limit | Value | Notes |
|-------|-------|-------|
| Max items per batch | {{n}} | Split larger batches |
| Max batch request size | {{n}} MB | Split by size |
| Max concurrent batches | {{n}} | Queue excess |

---

## Timeout Limits

| Operation | Provider Timeout | Our Timeout | Notes |
|-----------|------------------|-------------|-------|
| Standard request | {{n}}s | {{n}}s | Ours should be shorter |
| Long-running | {{n}}s | {{n}}s | Consider async |
| File upload | {{n}}s | {{n}}s | Chunk large files |
| Webhook delivery | {{n}}s | N/A | Must respond fast |

---

## Cost Structure

### Pricing Model

| Model | Description |
|-------|-------------|
| **Type** | Pay-per-use / Subscription / Freemium |
| **Billing cycle** | Monthly / Annual |
| **Currency** | USD / EUR / etc. |

### Per-Operation Costs

| Operation | Cost | Unit | Notes |
|-----------|------|------|-------|
| API call | ${{n}} | per 1,000 | Standard rate |
| {{operation}} | ${{n}} | per {{unit}} | {{notes}} |
| {{operation}} | ${{n}} | per {{unit}} | {{notes}} |
| Storage | ${{n}} | per GB/month | {{notes}} |
| Bandwidth | ${{n}} | per GB | Egress only |

### Tiers

| Tier | Price | Includes | Overage |
|------|-------|----------|---------|
| Free | $0 | {{limits}} | Blocked |
| Basic | ${{n}}/mo | {{limits}} | ${{n}}/1000 |
| Pro | ${{n}}/mo | {{limits}} | ${{n}}/1000 |
| Enterprise | Custom | Unlimited | N/A |

### Our Cost Controls

| Control | Threshold | Action |
|---------|-----------|--------|
| Daily spend alert | ${{n}} | Notify |
| Daily spend limit | ${{n}} | Block non-critical |
| Monthly budget | ${{n}} | Require approval |

### Cost Tracking

- Log cost per request
- Emit events to CostTracker
- Daily cost summary
- Monthly budget report

---

## Regional Constraints

### Data Residency

| Region | Endpoint | Notes |
|--------|----------|-------|
| US | `api.{{provider}}.com` | Default |
| EU | `api.eu.{{provider}}.com` | GDPR compliance |
| APAC | `api.ap.{{provider}}.com` | If available |

### Compliance

| Requirement | Status | Notes |
|-------------|--------|-------|
| GDPR | Compliant / Partial / N/A | {{notes}} |
| HIPAA | Compliant / Partial / N/A | {{notes}} |
| SOC 2 | Compliant / Partial / N/A | {{notes}} |
| PCI DSS | Compliant / Partial / N/A | {{notes}} |

---

## API Versioning Constraints

| Field | Value |
|-------|-------|
| **Current version** | {{version}} |
| **Minimum supported** | {{version}} |
| **Deprecation notice** | {{days}} days |
| **Sunset policy** | {{policy}} |

### Version Migration

| From Version | To Version | Breaking Changes | Deadline |
|--------------|------------|------------------|----------|
| v1 | v2 | {{changes}} | {{date}} |

---

## Webhook Constraints

### Delivery

| Constraint | Value |
|------------|-------|
| Max delivery attempts | {{n}} |
| Retry interval | {{schedule}} |
| Timeout per attempt | {{n}}s |
| Expected response | 2xx within {{n}}s |

### Payload

| Constraint | Value |
|------------|-------|
| Max payload size | {{n}} KB |
| Signature header | `X-{{Provider}}-Signature` |
| Signature algorithm | HMAC-SHA256 |

---

## Monitoring & Alerts

### Provider Status

| Resource | URL | Check Frequency |
|----------|-----|-----------------|
| Status page | {{url}} | Every 5 min |
| Health endpoint | `GET /health` | Every 1 min |

### Our Alerts

| Condition | Severity | Action |
|-----------|----------|--------|
| Rate limit > 80% | Warning | Slow down |
| Rate limit hit | Critical | Backoff |
| Quota > 90% | Warning | Alert team |
| Auth failure | Critical | Check credentials |
| Provider 5xx | Warning | Monitor |
| Provider down | Critical | Activate fallback |

---

## Notes

- Constraints may change — check provider docs regularly
- Test in sandbox before production
- Monitor for constraint changes in provider changelog

---
Source: PRE-SPEC-NOTES.md section 8
Phase: 22 (External Integration Specs)
Generated: {{timestamp}}
---
