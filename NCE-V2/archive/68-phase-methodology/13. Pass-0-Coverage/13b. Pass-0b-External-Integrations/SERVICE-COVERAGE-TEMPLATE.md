# Service Coverage Review — {{Provider}} / {{Service Name}}

## Parent Provider
{{Provider Name}}

## Service Identity
- **Service:** {{service name}}
- **API Version:** {{version or Unknown}}
- **Location:** `NCE-V2/specs/integrations/{{provider}}/{{service}}/` (Pass 1+)

## Service Role Summary
(1–2 sentences describing what this service provides.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Direction** | Outbound receiver / Inbound source / Downstream renderer |
| **Output Form (if outbound)** | Rendered HTML / PDF blob / DOCX blob / JSON / Other |
| **Asset Sink (if inbound binary)** | R2 via `assets/` system |
| **Library Sink (if inbound metadata)** | D1 binding {{NAME}} via `library/` |
| **Output-Boundary Side Correct?** | Yes / Flag |

## Overall Verdict
- Coverage: Yes / Partial / No
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
| Payload size (Worker 128MB memory) | {{or Unknown}} | Yes / No | |
| Quota | {{or Unknown}} | Yes / No | |
| Latency impact (Worker 5-min CPU) | {{or Unknown}} | Yes / No | |

## Failure Handling

| Failure Type | Documented? | Notes |
|--------------|-------------|-------|
| Service unavailable | Yes / No | resilience/ involvement? |
| Auth failure | Yes / No | |
| Rate limit exceeded | Yes / No | |
| Timeout | Yes / No | Worker CPU limit interaction |

## Size & Sufficiency Assessment

| Signal | Assessment | Notes |
|--------|------------|-------|
| Scope clarity | Clear / Partial / Unclear | |
| Estimated LOC | ~{{number}} | TS multiplier 1.3–1.5× |
| Size band | Low (<1500) / Medium (1500–2000) / High (>2000) | |

## Boundary Issues

- Output-boundary check: outbound rendered artefact going to the right kind of provider?
- Library bypass check: inbound metadata going via `library/` or direct D1 write?
- Asset bypass check: inbound binary going via `assets/` and R2 or direct write?
- Responsibility overlap with sibling services?

## Verdict
CAN / CANNOT fulfil its role within size and scope constraints.

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---
