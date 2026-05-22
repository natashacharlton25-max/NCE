# NCE-V2 Release Summary — v{{X.Y.Z}}

---
Status: DRAFT | RELEASED | STABILISED
Release Date: {{date}}
Version: v{{X.Y.Z}}
RC tag: v{{X.Y.Z}}-rc{{N}}
Commit SHA: `{{...}}`
Methodology Stage: 5f (final)
---

## Release Overview

| Field | Value |
|---|---|
| Version | v{{X.Y.Z}} |
| Type | Major / Minor / Patch |
| Release date (production) | {{date}} |
| Stabilisation period end | {{date}} |
| Released by | {{name}} |

---

## What Shipped

### Workers Deployed

| Worker | Version SHA | Status |
|---|---|---|
| platform | | Deployed |
| access | | Deployed |
| ai | | Deployed |
| ... (all 24) | | |

### D1 Migrations Applied

| Library | Latest Migration | Applied to Prod |
|---|---|---|
| brands | `0001_init.sql` | ✓ |
| audiences | `0001_init.sql` | ✓ |
| ... | | |

### Feature Flags Rolled Out

| Flag | Default | Rollout State |
|---|---|---|
| | enabled / disabled | Internal / 10% / 50% / 100% |

### Astro Consumer

| Aspect | Status |
|---|---|
| Astro pointing at NCE-V2 production | ☐ |
| E2E smoke pass | ☐ |
| Out of scope this release | ☐ |

---

## Verification Summary

| Gate | Status | Notes |
|---|---|---|
| Stage 5a CI/CD | ✓ Operational | |
| Stage 5b Performance audit | ✓ PASS | |
| Stage 5b Security audit | ✓ PASS | |
| Stage 5b License | ✓ PASS | |
| Stage 5b Cost thresholds | ✓ Configured | |
| Stage 5b Alerting | ✓ Configured | |
| Stage 5c Final review | ✓ Signed off | |
| Stage 5d Pre-deploy | ✓ GO | |
| Stage 5e Production deployment | ✓ Clean | |
| Stage 5f Post-deploy + stabilisation | ✓ Clean | |

---

## Golden Hour + Stabilisation Observations

| Metric | Baseline (pre-deploy) | T+1 hour | T+24h | T+48h |
|---|---|---|---|---|
| Worker error rate | | | | |
| CPU p99 | | | | |
| Memory p99 | | | | |
| D1 latency p99 | | | | |
| Throughput | | | | |
| Cost | | | | |

Any anomalies: (list, or "None")

---

## Risks Accepted

| Risk | Mitigation | Accepted by | Date |
|---|---|---|---|
| | | | |

---

## Sign-offs

| Role | Name | Date |
|---|---|---|
| Technical lead | | |
| Security audit | | |
| Performance audit | | |
| Product / business owner | | |
| Release Owner | | |
| On-call (post-Golden Hour) | | |

---

## Operations Handoff

| Item | Status |
|---|---|
| On-call team briefed | ☐ |
| Runbook walkthrough completed | ☐ |
| Dashboards bookmarked | ☐ |
| Alert routing verified | ☐ |
| Incident response procedure documented | ☐ |
| Hotfix path procedure understood | ☐ |

**On-call rotation:** {{name(s)}}
**Primary dashboard:** {{URL}}
**Runbook location:** {{path}}

---

## Lessons Learned

(Captured during release; carry forward to next cycle.)

| Lesson | Category | Action for next release |
|---|---|---|
| | Process / Tooling / Spec / Code | |

---

## Closure

NCE-V2 v{{X.Y.Z}} is **RELEASED** and operationally owned. `PASS-PROGRESS.md` marked PRODUCTION.

Next: hotfix path for issues; new feature cycle returns to Stage 2.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Release closer | | |

---
