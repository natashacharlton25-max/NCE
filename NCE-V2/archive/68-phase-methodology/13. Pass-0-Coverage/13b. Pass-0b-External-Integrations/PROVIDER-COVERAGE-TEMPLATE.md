# Provider Coverage Review — {{Provider Name}}

## Provider Identity
- **Provider:** {{provider name}}
- **Location:** `NCE-V2/specs/integrations/{{provider}}/` (Pass 1+) / `NCE-V2/pass-0/integrations/{{provider}}/` (Pass 0 reviews)

## Provider Role Summary
(1–2 sentences describing why this provider is needed.)

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Direction** | Outbound receiver / Inbound source / Downstream renderer (Astro) / Mixed |
| **Auth Method** | API key / OAuth / Signed request / Other |
| **Worker Communication** | `fetch()` from owning system Worker / via `integrations/` system |
| **Output-Boundary Side Correct?** | Yes / Flag |

## Overall Verdict
- Coverage: Yes / Partial / No
- Risk level: Low / Medium / High
- Vendor lock-in: Low / Medium / High
- Review date: {{timestamp}}

## Services Coverage

| Service | Purpose | Coverage | Direction | Est. LOC | Size Band | Notes |
|---------|---------|----------|-----------|----------|-----------|-------|
| | | Covered / Partial / Missing | Inbound / Outbound / Renderer | ~{{number}} | Low (<1500) / Medium (1500–2000) / High (>2000) | |

## Consuming Systems

| Internal System | Usage | Clear? |
|-----------------|-------|--------|
| | | Yes / Partial / No |

## Constraints Assessment

| Constraint | Documented? | Risk | Notes |
|------------|-------------|------|-------|
| Rate limits | Yes / Partial / No | Low / Medium / High | |
| Authentication | Yes / Partial / No | Low / Medium / High | Worker secret binding name? |
| Pricing/Quotas | Yes / Partial / No | Low / Medium / High | |
| CPU-time impact (Worker 5-min limit) | Yes / Partial / No | Low / Medium / High | |

## Vendor Risk

| Factor | Assessment | Notes |
|--------|------------|-------|
| Lock-in | Low / Medium / High | |
| Migration difficulty | Easy / Moderate / Hard | |
| Alternatives exist | Yes / Limited / No | |

## Missing Capability Areas

| Capability | Impact | Notes |
|------------|--------|-------|
| | Low / Medium / High | |

## Verdict
CAN / CANNOT fulfil role as currently structured.

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---
