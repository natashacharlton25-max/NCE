# {{Service Name}}

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD
Type: EXTERNAL INTEGRATION SERVICE
Parent Provider: {{Provider Name}}
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
(What capability this service provides to the project.)

## Justification
- Why this service is needed from this provider
- How it supports the consuming system(s)

---

## Consuming Systems
(Which of YOUR internal systems use this service.)

| System | How It Uses This Service |
|--------|--------------------------|
| {{system-name}} | … |

---

## In Scope
(What we WILL use this service for. Be specific.)

- …
- …

## Out of Scope
(What we will NOT use this service for, even if it supports it.)

- …
- …

## Non-Goals
(Things this service integration will never do.)

- …
- …

---

## Data Boundary

### Data Sent to Service

| Data Type | Sensitivity | Description |
|-----------|-------------|-------------|
| … | None / Low / Medium / High | … |

### Data Received from Service

| Data Type | Validation Required | Description |
|-----------|---------------------|-------------|
| … | Yes / No | … |

---

## Authentication

| Field | Value |
|-------|-------|
| **Method** | API Key / OAuth 2.0 / Service Account / Other |
| **Scopes Required** | {{list specific scopes}} |
| **Token Refresh** | Yes / No / N/A |
| **Credentials Location** | {{reference only — never store actual credentials}} |

---

## Service Constraints

### Rate Limits

| Limit Type | Value | Notes |
|------------|-------|-------|
| Requests per second | {{or Unknown}} | |
| Requests per day | {{or Unknown}} | |
| Payload size limit | {{or Unknown}} | |

### Quotas

| Quota Type | Value | Notes |
|------------|-------|-------|
| {{e.g., Storage}} | {{or Unknown}} | |
| {{e.g., API calls}} | {{or Unknown}} | |

### Cost

| Usage | Cost | Notes |
|-------|------|-------|
| {{e.g., First 1000 calls}} | Free | |
| {{e.g., Additional calls}} | {{rate}} | |

---

## API Stability

| Field | Value |
|-------|-------|
| API version used | {{version}} |
| Version release date | {{or Unknown}} |
| Deprecation status | Active / Deprecated / Sunset date: {{date}} |
| Breaking change risk | Low / Medium / High |

---

## Failure Contract
(What failures must be tolerated. Not how to handle them.)

| Failure Type | Expected Behaviour |
|--------------|-------------------|
| Service unavailable | … |
| Auth failure / token expired | … |
| Rate limit exceeded | … |
| Timeout | … |
| Invalid request | … |
| Unexpected response format | … |

---

## Dependencies

### Internal Dependencies
(Your systems/subsystems this service depends on.)

| Dependency | Reason |
|------------|--------|
| … | … |

### Other Services from Same Provider
(Other services from this provider that this service depends on.)

| Service | Reason |
|---------|--------|
| {{e.g., OAuth}} | Authentication |

### External Dependencies
(Other providers this service depends on.)

| Dependency | Reason |
|------------|--------|
| … | … |

---

## Size Constraint
This service integration is intended to remain under **~1,500 lines** of implementation code.

---

## Notes
(Assumptions, edge cases, or implementation considerations. No actual implementation.)

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Build | COMPLETE | Claude | {{timestamp}} |
| Pass 0 | PENDING | — | — |
| Pass 1 | PENDING | — | — |
| Pass 2 | PENDING | — | — |
| Pass 3 | PENDING | — | — |
| Pass 4 | PENDING | — | — |

## Pass Notes