# NCE-V2 Deploy Checklist

---
Status: DRAFT | PRE-DEPLOY READY | DEPLOYED | ROLLED BACK
Version: v0.1.0
Last Updated: {{timestamp}}
Methodology Stage: 5d/5e
RC tag: `v{{X.Y.Z}}-rc{{N}}`
---

## 0i Readiness Review (15-min cross-functional)

| Role | Present | Confirmed Ready |
|---|---|---|
| Release Owner | ☐ | ☐ |
| Deployer | ☐ | ☐ |
| On-call Engineer | ☐ | ☐ |
| SRE/DevOps | ☐ | ☐ |
| Product owner (optional) | ☐ | ☐ |

**Outcome:** ☐ GO to begin 0i / ☐ NO-GO (resolve blockers)

| Blocker | Owner | ETA |
|---|---|---|
| | | |

---

## Pre-Flight Verification

- [ ] RC tag is the verified commit from Stages 4 + 5a-c
- [ ] CI green on RC commit (re-verify)
- [ ] Staging deployment matches RC
- [ ] All audit reports (Stage 5b) PASS or risks accepted in writing
- [ ] On-call available
- [ ] Rollback plan reviewed

---

## Pre-Deploy Snapshot

| Metric | Production current | Staging current | Notes |
|---|---|---|---|
| Error rate (per Worker) | | | |
| Worker CPU p99 | | | |
| Worker memory p99 | | | |
| D1 query latency p99 | | | |
| Request throughput | | | |
| Cost (per hour est.) | | | |

---

## Rollback Readiness

- [ ] Previous Worker version available (`wrangler deployments list`)
- [ ] D1 migrations backward-compatible verified on staging
- [ ] Rollback target SHA documented: `{{...}}`
- [ ] Rollback runbook clear: `wrangler rollback --message "rollback to <SHA>"`

---

## NCE-V2-Specific Pre-Deploy Checks

- [ ] D1 production databases ready (or migrations applied this gate)
- [ ] R2 production buckets exist + accessible
- [ ] KV production namespaces populated
- [ ] All Worker secrets set in production
- [ ] Astro consumer staging endpoint pointed at NCE-V2 production-ready URL (if in scope)

---

## Deployment Sequence (5e)

| # | Step | Started | Completed | Verified |
|---|---|---|---|---|
| 1 | Apply D1 migrations to production | | | ☐ |
| 2 | Deploy `platform` Worker | | | ☐ |
| 3 | Smoke test platform | | | ☐ |
| 4 | Deploy `resilience`, `observability`, `audit`, `versioning` | | | ☐ |
| 5 | Deploy `access`, `brand`, `ai`, library-owning systems | | | ☐ |
| 6 | Deploy `assets`, `document-templates`, etc. | | | ☐ |
| 7 | Deploy `integrations`, `renderers`, etc. | | | ☐ |
| 8 | Deploy `website`, `publishing`, etc. | | | ☐ |
| 9 | Final per-Worker smoke test | | | ☐ |
| 10 | Start Golden Hour | | | ☐ |

---

## Golden Hour Observation (60 min minimum)

| Time | Worker error rate | CPU p99 | D1 latency | Status |
|---|---|---|---|---|
| T+0 | | | | |
| T+15 | | | | |
| T+30 | | | | |
| T+45 | | | | |
| T+60 | | | | |

If any critical alert → roll back per ROLLBACK-READY.md.

---

## Verdict

**Status:** ☐ DEPLOYED CLEAN / ☐ DEPLOYED WITH WARNINGS / ☐ ROLLED BACK

---

## Sign-off

| Role | Name | Date | Time |
|---|---|---|---|
| Release Owner — GO to deploy | | | |
| Deployer — deploy executed | | | |
| On-call — Golden Hour clean | | | |

---
