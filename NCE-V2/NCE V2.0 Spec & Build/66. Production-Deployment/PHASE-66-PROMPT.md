# PHASE 66: PRODUCTION DEPLOYMENT

---
Phase: 66
Section: 0i. Rollout
Name: Production Deployment
Location: NCE-V2/NCE V2.0 Spec & Build/66. Production-Deployment/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Execute NCE-V2 production deployment of the signed-off RC. **Non-collapsible.**

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/DEPLOYMENT-GUIDE.md` (Phase 64 — produced as part of doc bundle)
2. `NCE-V2/admin/PRE-DEPLOY-CHECKLIST.md` (Phase 65)
3. `NCE-V2/admin/PRE-DEPLOY-SNAPSHOT.md` (Phase 65)
4. `NCE-V2/admin/ROLLBACK-READY.md` (Phase 65)

---

## DEPLOYMENT STRATEGY

For NCE-V2 on Cloudflare Workers:
- **Rolling** is the default (Workers are stateless, zero-downtime). `wrangler deploy` per Worker rolls instances.
- Per-Worker deployments are independent; can deploy `platform` Worker first, then dependents.

Order (per IMPLEMENTATION-PLAN.md):
1. **D1 migrations applied to production** (`wrangler d1 migrations apply <library>-prod`) — done in Phase 65 readiness OR here, before Worker deploys
2. **Deploy `platform` Worker** first (services + system + state + library)
3. **Deploy remaining system Workers** (alphabetical, parallel-safe except for dependencies)
4. **Verify each Worker deployed** (`wrangler deployments list`)

---

## TASK

### Step 1: Pre-flight (re-verify)
- [ ] PRE-DEPLOY-CHECKLIST.md all green
- [ ] On-call engineer available
- [ ] Rollback target SHA confirmed

### Step 2: D1 Migrations (production)
For each library (in dependency order):
- `wrangler d1 migrations apply <library>-prod`
- Verify with `SELECT * FROM _index LIMIT 1;`

### Step 3: Deploy `platform` Worker
- `wrangler deploy --env production` for platform Worker
- Verify deployment via `wrangler deployments list`
- Smoke test: hit a known platform Worker endpoint, verify 200

### Step 4: Deploy remaining Workers
Per Worker (in parallel where dependencies allow):
- `wrangler deploy --env production`
- Smoke test each

### Step 5: Verify
- All Workers report current RC commit SHA
- All endpoints respond
- No spike in error rate vs PRE-DEPLOY-SNAPSHOT

### Step 6: Start Golden Hour
- Notify on-call: deployment complete, Golden Hour begins now
- Monitor metrics for 60 minutes minimum
- If any critical alert: roll back per ROLLBACK-READY.md (`wrangler rollback`)

---

## OUTPUT

```
NCE-V2/admin/DEPLOYMENT-LOG.md
```

Capture: timestamp per step, Worker SHAs deployed, smoke test results, Golden Hour start time.

---

## MANDATORY RULES

- Follow DEPLOYMENT-GUIDE.md exactly
- Don't skip smoke tests
- Don't proceed past Golden Hour without verification (Phase 67)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All Workers deployed and verified
- [ ] D1 migrations applied
- [ ] Smoke tests pass
- [ ] DEPLOYMENT-LOG.md complete
- [ ] Golden Hour started
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 67 (Post-Deploy Verification)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
