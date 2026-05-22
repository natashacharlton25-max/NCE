# Service Coverage Review — {{Provider}} / {{Service Name}}

## Parent Provider
{{Provider Name}}

## Service Identity
- **Service:** {{service name}}
- **API Version:** {{version or Unknown}}
- **Location:** `{{project}}/integration-{{provider}}/{{service}}/`

## Service Role Summary
(1–2 sentences describing what this service provides.)

## Overall Verdict
- Coverage: ✅ / ⚠️ / ❌
- Risk level: Low / Medium / High
- Est. total LOC: ~{{number}}
- Largest expected file: ~{{number}} LOC
- Review date: {{timestamp}}

## Responsibility Coverage

| Area | Coverage | Notes |
|------|----------|-------|
| | Covered / Partial / Missing | |

## Consuming Systems

| Internal System | Usage | Clear? |
|-----------------|-------|--------|
| | | Yes / Partial / No |

## Data Boundary

| Direction | Data Type | Sensitivity | Documented? |
|-----------|-----------|-------------|-------------|
| Outbound (we send) | | Low / Medium / High | Yes / No |
| Inbound (we receive) | | Low / Medium / High | Yes / No |

## Constraints Assessment

| Constraint | Value | Documented? | Notes |
|------------|-------|-------------|-------|
| Rate limit | {{or Unknown}} | Yes / No | |
| Payload size | {{or Unknown}} | Yes / No | |
| Quota | {{or Unknown}} | Yes / No | |

## Failure Handling

| Failure Type | Documented? | Notes |
|--------------|-------------|-------|
| Service unavailable | Yes / No | |
| Auth failure | Yes / No | |
| Rate limit exceeded | Yes / No | |
| Timeout | Yes / No | |

## Size & Sufficiency Assessment

| Signal | Assessment | Notes |
|--------|------------|-------|
| Scope clarity | Clear / Partial / Unclear | |
| Estimated LOC | ~{{number}} | |
| Size risk | Low / Medium / High | |

## Boundary Issues
(List any responsibility overlap with sibling services or consuming systems.)

- …

## Verdict
CAN / CANNOT fulfil its role within size and scope constraints.

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
---
