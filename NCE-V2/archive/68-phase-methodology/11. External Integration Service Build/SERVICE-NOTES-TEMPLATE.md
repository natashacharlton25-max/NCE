# {{Service Name}}

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD (Phase 11)
Type: EXTERNAL INTEGRATION SERVICE
Parent Provider: {{Provider Name}}
---

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Direction** | Inbound source / Outbound receiver / Downstream renderer / Combined |
| **Output Form (if outbound)** | Rendered HTML / PDF blob / DOCX blob / JSON / Other |
| **Asset Sink (if inbound binary)** | R2 via `assets/` system |
| **Library Sink (if inbound metadata)** | D1 binding `{{NAME}}` via `library/` |
| **Worker secret binding name** | `{{SECRET_NAME}}` |
| **resilience/ involvement** | Yes (RateLimiter, RetryPolicyEngine, FallbackStrategyResolver) / No |

---

## Service Identity

| Field | Value |
|-------|-------|
| **Provider** | {{e.g., Google}} |
| **Service Name** | {{e.g., Google Drive API}} |
| **API Version** | {{e.g., v3}} |
| **Documentation** | {{URL}} |

---

## Purpose
(Why this specific service is needed. One sentence.)

## Intent
(What capability this service provides to NCE-V2.)

## Justification
- Why this service is needed from this provider
- How it supports the consuming system(s)

---

## Consuming Systems

| System | How It Uses This Service |
|--------|--------------------------|
| {{system-name}} | … |

---

## In Scope
- …

## Out of Scope
- …

## Non-Goals
- …

---

## Data Boundary

### Data Sent to Service

| Data Type | Sensitivity | Form | Description |
|-----------|-------------|------|-------------|
| | None / Low / Medium / High | JSON / rendered HTML / binary | |

### Data Received from Service

| Data Type | Validation Required | Sink Target | Description |
|-----------|---------------------|-------------|-------------|
| | Yes / No | `assets/` (R2) / library D1 / metadata-only | |

---

## Authentication

| Field | Value |
|-------|-------|
| **Method** | API Key / OAuth 2.0 / Service Account / Signed request |
| **Scopes Required** | {{list specific scopes}} |
| **Token Refresh** | Yes / No / N/A |
| **Worker Secret Binding Name** | `{{SECRET_NAME}}` |

---

## Service Constraints (Worker context)

### Rate Limits

| Limit Type | Value | Notes |
|------------|-------|-------|
| Requests per second | {{or Unknown}} | `resilience/RateLimiter` involvement? |
| Requests per day | {{or Unknown}} | |
| Payload size limit | {{or Unknown}} | Worker 128 MB memory constraint |

### Quotas

| Quota Type | Value | Notes |
|------------|-------|-------|
| | | |

### Cost

| Usage | Cost | Notes |
|-------|------|-------|
| | | |

### Latency (Worker 5-min CPU limit)

| Metric | Value | Notes |
|--------|-------|-------|
| p99 latency | {{or Unknown}} | Multiple calls per Worker invocation? |

---

## API Stability

| Field | Value |
|-------|-------|
| API version used | {{version}} |
| Deprecation status | Active / Deprecated / Sunset date: {{date}} |
| Breaking change risk | Low / Medium / High |

---

## Failure Contract
(What failures must be tolerated. Not how to handle them.)

| Failure Type | Expected Behaviour | resilience/ Pattern |
|--------------|--------------------|---------------------| 
| Service unavailable | | RetryPolicyEngine / FallbackStrategyResolver |
| Auth failure / token expired | | |
| Rate limit exceeded | | RateLimiter |
| Timeout | | Worker CPU limit interaction |
| Invalid request | | |
| Unexpected response format | | |

---

## Dependencies

### Internal Dependencies

| Dependency | Binding | Reason |
|------------|---------|--------|
| | service / D1 / R2 | |

### Other Services from Same Provider

| Service | Reason |
|---------|--------|
| {{OAuth}} | Authentication |

### External Dependencies

| Dependency | Reason |
|------------|--------|
| | |

---

## Size Constraint
This service integration is intended to remain under **~2000 LOC** of runtime TypeScript (exclusions applied).

---

## Notes

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Phase 11 Build | COMPLETE | Claude | {{timestamp}} |
| Phase 13 Pass 0 | PENDING | — | — |
| Phase 14 Pass 1 | PENDING | — | — |
| Phase 15 Pass 2 | PENDING | — | — |
| Phase 16 Pass 3 | PENDING | — | — |
| Phase 17 Pass 4 | PENDING | — | — |

## Pass Notes
