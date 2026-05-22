# PHASE 67: POST-DEPLOY VERIFICATION

---
Phase: 67
Section: 0i. Rollout
Name: Post-Deploy Verification
Location: NCE-V2/NCE V2.0 Spec & Build/67. Post-Deploy-Verification/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Verify NCE-V2 production is healthy after deployment. Confirm deployment succeeded and is ready for stabilization.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/DEPLOYMENT-LOG.md` (Phase 66)
2. `NCE-V2/admin/PRE-DEPLOY-SNAPSHOT.md` (Phase 65)
3. `NCE-V2/admin/PERFORMANCE-BASELINE.md` (Phase 63)
4. `NCE-V2/admin/ALERTING-THRESHOLDS.md` (Phase 63)

---

## TASK

### Step 1: Grace Period

For Cloudflare Workers cold-start considerations:
- Wait 2 minutes after deploy for Worker initialization
- Workers ramp up quickly; less concerning than JVM/serverless cold starts

### Step 2: Smoke Tests (production)
- Hit one endpoint per Worker; verify 200
- Verify response envelope shape
- Verify TS type conformance (status, data shape)
- D1 query: `SELECT * FROM _index LIMIT 1;` per library

### Step 3: Metrics vs Baseline

| Metric | Baseline (PRE-DEPLOY-SNAPSHOT) | Current | Status |
|--------|---------------------------------|---------|--------|
| Error rate | | | OK / Alert |
| Worker CPU p99 | | | OK / Alert |
| Worker memory p99 | | | OK / Alert |
| D1 query latency p99 | | | OK / Alert |
| Request throughput | | | OK / Alert |
| Cost (per hour est.) | | | OK / Alert |

### Step 4: NCE-V2-Specific Checks
- [ ] Output-boundary intact: `website/` returns JSON, `renderers/` returns binary blobs as expected
- [ ] Library access works: a known D1 query through `library/Librarian` returns data
- [ ] External integrations work: a known outbound call succeeds (via mock or test trigger)
- [ ] No error spike in any system Worker

### Step 5: Enable Feature Flags (internal)
For features behind flags, enable for internal users only first. Verify behaviour. Then broaden.

### Step 6: Astro Consumer Verification (if applicable)
- Astro deployment pulls from production NCE-V2
- E2E smoke test from Astro
- No 5xx from NCE-V2 endpoints

---

## OUTPUT

```
NCE-V2/admin/LAUNCH-VERIFICATION.md
```

Captures: smoke test results, metrics-vs-baseline, feature flag rollout state, any anomalies.

---

## MANDATORY RULES

- Don't declare success until baseline comparison clean
- Any alert → roll back per ROLLBACK-READY.md
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All smoke tests PASS
- [ ] All metrics within baseline thresholds
- [ ] Feature flags rolled out correctly
- [ ] LAUNCH-VERIFICATION.md complete
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 68 (Stabilization & Handoff)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
