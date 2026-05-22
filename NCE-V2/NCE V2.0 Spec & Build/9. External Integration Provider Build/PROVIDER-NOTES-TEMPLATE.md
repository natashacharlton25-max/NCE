# {{Provider Name}}

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD (Phase 9)
Type: EXTERNAL INTEGRATION PROVIDER
---

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Direction** | Outbound receiver / Inbound source / Downstream renderer / Mixed |
| **FileTree-v2 subsystem** | `integrations/{{Provider}}Integration.ts` |
| **Worker secret binding name** | `{{SECRET_NAME}}` |
| **Worker fetch() pattern** | from owning system Worker / via `integrations/` Worker |

---

## Provider Identity

| Field | Value |
|-------|-------|
| **Provider** | {{e.g., Google, Stripe, Canva}} |
| **Provider Website** | {{URL}} |
| **Developer Portal** | {{URL or N/A}} |
| **API Documentation** | {{URL}} |

---

## Purpose
(Why this provider is needed for NCE-V2. One sentence.)

## Intent
(What business capability this provider enables.)

## Justification
- Why an external provider is needed (vs in-house)
- Why this specific provider was chosen

---

## Services Used
(Filled in during Phase 10 Service Scope.)

| Service | Purpose | Direction | Consuming System(s) |
|---------|---------|-----------|---------------------|
| {{Drive API}} | {{brief}} | Outbound | {{system-name}} |

---

## Services NOT Used
- {{service}} — reason
- …

---

## Consuming Systems

| System | How It Uses This Provider |
|--------|---------------------------|
| {{system-name}} | … |

---

## Account & Access

| Field | Value |
|-------|-------|
| **Account Type** | Free / Paid / Enterprise / TBD |
| **Account Owner** | {{role}} |
| **Billing** | {{who pays}} |

---

## Authentication Overview

| Field | Value |
|-------|-------|
| **Primary Auth Method** | API Key / OAuth 2.0 / Service Account / Signed request |
| **Worker Secret Binding Name** | `{{SECRET_NAME}}` (referenced, never stored in code) |
| **Shared Across Services?** | Yes / No |

---

## Provider Constraints

### Pricing Model

| Tier | Limits | Cost |
|------|--------|------|
| | | |

### Global Rate Limits (Worker context)

| Limit Type | Value | Worker Impact |
|------------|-------|---------------|
| {{e.g., API calls per day}} | {{or Unknown}} | Per-Worker request count |
| Burst limit | {{or Unknown}} | Need `resilience/RateLimiter` involvement? |
| Latency p99 | {{or Unknown}} | Worker 5-min CPU limit constraint |

### Provider Stability

| Field | Value |
|-------|-------|
| Provider maturity | Established / Growing / New |
| API stability | Stable / Evolving / Unstable |
| Deprecation policy | {{or Unknown}} |

---

## Compliance & Risk

| Concern | Assessment |
|---------|------------|
| Data residency | {{or Unknown}} |
| Compliance certifications | {{e.g., SOC2, GDPR}} |
| Vendor lock-in risk | Low / Medium / High |
| Exit strategy | {{how hard to switch}} |

---

## Non-Goals
- …

---

## Size Constraint
The integration wrapper for this provider should target **~2000 LOC** or less (runtime TypeScript, exclusions applied).

---

## Notes

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Phase 9 Build | COMPLETE | Claude | {{timestamp}} |
| Phase 10 Service Scope | PENDING | — | — |
| Phase 11 Service Build | PENDING | — | — |
| Phase 13 Pass 0 | PENDING | — | — |
| Phase 14 Pass 1 | PENDING | — | — |
| Phase 15 Pass 2 | PENDING | — | — |
| Phase 16 Pass 3 | PENDING | — | — |
| Phase 17 Pass 4 | PENDING | — | — |

## Pass Notes
