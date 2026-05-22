# PHASE 65: PRE-DEPLOY VERIFICATION

---
Phase: 65
Section: 0i. Rollout
Name: Pre-Deploy Verification
Location: NCE-V2/NCE V2.0 Spec & Build/65. Pre-Deploy-Verification/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Final verification that NCE-V2 is ready for production deployment. Last checkpoint before deploy.

---

## STEP 0: 0i READINESS REVIEW

15-min cross-functional review:

| Role | Present | Confirmed Ready |
|------|---------|-----------------|
| Release Owner | ☐ | ☐ |
| Deployer | ☐ | ☐ |
| On-call Engineer | ☐ | ☐ |
| SRE/DevOps | ☐ | ☐ |
| Product (optional) | ☐ | ☐ |

**Outcome:** Signed "GO to begin 0i" (separate from "GO to deploy").

If NO-GO: blocker list with owner + ETA; resolve, then restart Phase 65.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/0h-to-0i-HANDOFF.md` (Phase 64)
2. All Phase 63 audits
3. RC tag + commit SHA
4. D1 production database state

---

## TASK

### Pre-Flight Verification
- [ ] RC tag is the verified commit from Phases 61–64
- [ ] CI green on RC commit (re-verify)
- [ ] Staging deployment matches RC commit
- [ ] All Phase 64 sign-offs collected
- [ ] On-call engineer briefed and available
- [ ] Rollback plan reviewed and ready

### Pre-Deploy Snapshot
Capture baseline metrics from staging + production to compare post-deploy:
- Current production error rate
- Current Worker CPU + memory p99
- Current D1 query latency p99
- Current request throughput
- Current cost (Workers, D1, R2, KV usage)

Write to `PRE-DEPLOY-SNAPSHOT.md`.

### Rollback Readiness
- [ ] Previous Worker version available for rollback (`wrangler deployments list` shows prior version)
- [ ] D1 migrations are backward-compatible (older Worker reads new schema OK)
- [ ] Rollback runbook clear: `wrangler rollback --message "rollback to <SHA>"`
- [ ] Rollback target SHA documented

### NCE-V2-Specific Checks
- [ ] D1 production has latest migrations applied (and tested on staging)
- [ ] R2 production buckets exist and accessible
- [ ] KV production namespaces populated
- [ ] All Worker secrets set in production (re-verify `wrangler secret list --env production`)

---

## OUTPUT

```
NCE-V2/admin/PRE-DEPLOY-CHECKLIST.md
NCE-V2/admin/PRE-DEPLOY-SNAPSHOT.md
NCE-V2/admin/ROLLBACK-READY.md
```

---

## MANDATORY RULES

- Don't deploy without GO from readiness review
- Don't deploy without rollback ready
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Readiness review: GO
- [ ] Pre-deploy snapshot captured
- [ ] Rollback ready
- [ ] Human approves "GO to deploy"
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 66 (Production Deployment)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
