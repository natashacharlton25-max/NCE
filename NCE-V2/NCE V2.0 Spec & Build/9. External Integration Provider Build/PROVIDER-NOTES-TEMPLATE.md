# {{Provider Name}}

---
Status: DRAFT | APPROVED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD
Type: EXTERNAL INTEGRATION PROVIDER
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
(Why this provider is needed for the project. One sentence.)

## Intent
(What business/project capability this provider enables.)

## Justification
- Why an external provider is needed (vs building in-house)
- Why this specific provider was chosen over alternatives

---

## Services Used
(List of specific services/APIs from this provider that the project will use. Details in each service's SERVICE-NOTES.md.)

| Service | Purpose | Consuming System(s) |
|---------|---------|---------------------|
| {{e.g., Drive API}} | {{brief purpose}} | {{system-name}} |
| {{e.g., OAuth 2.0}} | {{brief purpose}} | {{system-name}} |

---

## Services NOT Used
(Explicit exclusions — services from this provider we will NOT use, even if available.)

- {{e.g., Google Sheets API}} — not needed for this project
- {{e.g., Google Calendar API}} — out of scope

---

## Consuming Systems
(Which of YOUR internal systems depend on this provider.)

| System | How It Uses This Provider |
|--------|---------------------------|
| {{system-name}} | … |

---

## Account & Access

| Field | Value |
|-------|-------|
| **Account Type** | Free / Paid / Enterprise / TBD |
| **Account Owner** | {{role or person, not credentials}} |
| **Billing** | {{who pays, or N/A for free tier}} |

---

## Authentication Overview
(High-level auth approach for this provider. Service-specific details in SERVICE-NOTES.md.)

| Field | Value |
|-------|-------|
| **Primary Auth Method** | API Key / OAuth 2.0 / Service Account / Other |
| **Credentials Storage** | {{reference only — never store actual credentials}} |
| **Shared Across Services?** | Yes / No |

---

## Provider Constraints

### Pricing Model

| Tier | Limits | Cost |
|------|--------|------|
| {{e.g., Free}} | {{e.g., 15GB storage}} | Free |
| {{e.g., Paid}} | {{e.g., 2TB storage}} | {{cost}} |

### Global Rate Limits
(Provider-wide limits. Service-specific limits in SERVICE-NOTES.md.)

| Limit Type | Value | Notes |
|------------|-------|-------|
| {{e.g., API calls per day}} | {{or Unknown}} | |

### Provider Stability

| Field | Value |
|-------|-------|
| Provider maturity | Established / Growing / New / Unknown |
| API stability | Stable / Evolving / Unstable |
| Deprecation policy | {{or Unknown}} |

---

## Compliance & Risk

| Concern | Assessment |
|---------|------------|
| Data residency | {{where data is stored, or Unknown}} |
| Compliance certifications | {{e.g., SOC2, GDPR, HIPAA, or Unknown}} |
| Vendor lock-in risk | Low / Medium / High |
| Exit strategy | {{how hard to switch providers}} |

---

## Non-Goals
(Things we will NOT do with this provider, even if possible.)

- …
- …

---

## Size Constraint
All services from this provider combined should remain under reasonable implementation size.
Individual services should target **~1,500 lines** or less each.

---

## Notes
(Assumptions, considerations, or future concerns.)

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