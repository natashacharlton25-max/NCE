# Phase 68: Stabilization & Handoff

---
Phase: 68
Section: 0i Rollout
Name: Stabilization & Handoff
Status: TEMPLATE
Collapsible: **NEVER**
---

## Role

You are establishing ongoing monitoring, confirming operational readiness, and formally closing the release. This phase is non-collapsible — it must always be executed explicitly.

---

## Task

Configure production alerts and monitoring, complete the Golden Hour, manage the stabilization period, and create the final release summary for operations handoff.

---

## Inputs

| File | Location | Purpose |
|------|----------|---------|
| ALERTING-THRESHOLDS.md | From 0h | Alert configuration |
| COST-THRESHOLDS.md | From 0h | Cost monitoring |
| LAUNCH-VERIFICATION.md | From Phase 67 | Verification results |
| DEPLOYMENT-LOG.md | From Phase 66 | Deployment record |
| **0i-COMPLETION-CRITERIA.md** | `0. Admin/0i. Rollout/` | **Required pass criteria** |

**IMPORTANT:** Before finalizing Phase 68, verify against `0i-COMPLETION-CRITERIA.md` for binding completion requirements.

---

## Hotfix Boundary Rule

> **Any code change during 0i stabilization requires exiting 0i and following the hotfix path or returning to 0h.**

No "quick fixes" during stabilization.

| Severity | Action |
|----------|--------|
| Critical (P1) | Evaluate rollback vs hotfix path |
| High (P2) | Document, schedule for next release |
| Medium (P3) | Document for next release |
| Low (P4) | Backlog |

---

## Process

### Step 1: Implement Monitoring Alerts

Configure from ALERTING-THRESHOLDS.md:

| Alert | Configured | Tested | Routing |
|-------|------------|--------|---------|
| Service Down | ☐ | ☐ | ☐ |
| High Error Rate (P1) | ☐ | ☐ | ☐ |
| High Error Rate (P2) | ☐ | ☐ | ☐ |
| Slow Response (P1) | ☐ | ☐ | ☐ |
| Slow Response (P2) | ☐ | ☐ | ☐ |
| High CPU | ☐ | ☐ | ☐ |
| High Memory | ☐ | ☐ | ☐ |

**Test alert routing:** Send test alert to verify notifications work.

### Step 2: Monitoring Ownership

| Category | Owner | Backup | Contact |
|----------|-------|--------|---------|
| Application | {{NAME}} | {{NAME}} | |
| Infrastructure | {{NAME}} | {{NAME}} | |
| Database | {{NAME}} | {{NAME}} | |
| Cost | {{NAME}} | {{NAME}} | |

**Rule:** Each alert category must have owner + backup before release closure.

### Step 3: Cost Monitoring

Configure from COST-THRESHOLDS.md:

| Alert | Configured | Status |
|-------|------------|--------|
| Daily burn rate | ☐ | |
| 50% budget warning | ☐ | |
| 80% budget critical | ☐ | |
| Cost anomaly detection | ☐ | |

### Step 4: Golden Hour Completion

| Check | Status |
|-------|--------|
| 60 minutes elapsed | ☐ |
| No critical issues | ☐ |
| Release Owner sign-off | ☐ |
| Stack freeze lifted | ☐ |

```
✅ GOLDEN HOUR COMPLETE
Duration: 60 minutes
Issues: {{NONE or LIST}}
Status: Stabilization continues
```

### Step 5: Post-Golden-Hour Review

**Quick review immediately after Golden Hour:**

| Check | Status | Notes |
|-------|--------|-------|
| Any anomalies during Golden Hour? | ☐ No | |
| Any alerts triggered? | ☐ No | |
| Any unexpected log patterns? | ☐ No | |
| Any user complaints? | ☐ No | |
| Metrics still within baseline? | ☐ Yes | |
| Cost trending as expected? | ☐ Yes | |

If issues identified → Document and escalate if needed.

### Step 6: Feature Flag Rollout Plan (Optional)

If features were enabled for internal users in Phase 67:

**Rollout Progression:**
```
Internal (1%) → 5% → 25% → 50% → 100%
```

**Each step requires:**
- [ ] No new errors
- [ ] p95 within baseline
- [ ] No cost anomalies
- [ ] Minimum duration met

| Flag | Current % | Target % | Criteria Met | Status |
|------|-----------|----------|--------------|--------|
| {{FLAG_1}} | 1% | 5% | ☐ | |
| {{FLAG_2}} | 1% | 5% | ☐ | |

| Step | % Users | Duration | Gate |
|------|---------|----------|------|
| 1 | Internal | Phase 67 | Smoke tests |
| 2 | 5% | 4 hours | Metrics OK |
| 3 | 25% | 8 hours | Metrics OK |
| 4 | 50% | 12 hours | Metrics OK |
| 5 | 100% | - | Full rollout |

**Note:** May extend beyond stabilization — document handoff.

### Step 7: Stabilization Period

**Minimum:** 24-48 hours of healthy operation.

During stabilization:
- [ ] Monitor error rates
- [ ] Monitor performance metrics
- [ ] Monitor costs
- [ ] Address alerts (following hotfix boundary)
- [ ] Document any issues
- [ ] NO code changes

### Step 8: Stakeholder Communication

- [ ] Announce successful deployment
- [ ] Update status page (if applicable)
- [ ] Update release notes/changelog

### Step 9: Release Closure Checklist

| Check | Status |
|-------|--------|
| All smoke tests passed | ☐ |
| Metrics within baseline 24h+ | ☐ |
| All alerts configured and tested | ☐ |
| All alert owners assigned | ☐ |
| No critical issues in stabilization | ☐ |
| Golden Hour completed | ☐ |
| On-call handoff complete | ☐ |
| Feature flags documented | ☐ |
| Feature flag rollout handed off | ☐ / N/A |

### Step 10: Capture Deployment SLO Actuals

| Metric | Target | Actual |
|--------|--------|--------|
| Deployment duration | < {{X}} min | |
| Smoke test completion | < 30 min | |
| Time to full traffic | < {{X}} min | |
| Golden Hour issues | 0 | |
| Rollback used | No | |

### Step 11: Create Release Summary

Complete RELEASE-COMPLETE.md with:
- RC deployed
- Deployment date
- Duration
- Strategy used
- Issues encountered
- Rollback used?
- Feature flags enabled
- Final status

---

## Operations Handoff

### What to Deliver

| Artifact | Location |
|----------|----------|
| RELEASE-COMPLETE.md | `/docs/0i/` |
| PRODUCTION-ALERTS.md | `/docs/0i/` |
| RUNBOOK.md | From 0h |

### Handoff Checklist

| Item | Status |
|------|--------|
| RELEASE-COMPLETE.md finalized | ☐ |
| Operations team notified | ☐ |
| On-call rotation updated | ☐ |
| Alert ownership transferred | ☐ |
| Runbook ownership confirmed | ☐ |
| Feature flag rollout handed off | ☐ / N/A |
| Open issues documented | ☐ |

---

## Rules

### DOs

- ✅ Configure ALL alerts from 0h
- ✅ Test alert routing
- ✅ Assign ownership for each alert category
- ✅ Wait for full stabilization period
- ✅ Document everything in RELEASE-COMPLETE.md
- ✅ Complete operations handoff

### DON'Ts

- ❌ Make code changes during stabilization
- ❌ Close release before 24h stable
- ❌ Skip alert testing
- ❌ Leave alert ownership unassigned
- ❌ Skip operations handoff

---

## Output Format

Create these files:

1. **PRODUCTION-ALERTS.md** (from template)
   - Location: `/docs/0i/`
   - All alert configuration

2. **MONITORING-DASHBOARD.md**
   - Location: `/docs/0i/`
   - Dashboard links

3. **RELEASE-COMPLETE.md** (from template)
   - Location: `/docs/0i/`
   - Final release summary

4. **LESSONS-LEARNED.md** (optional)
   - Location: `/docs/0i/`
   - Retrospective notes

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Deployer | | | ☐ |
| On-call Engineer | | | ☐ |
| Operations | | | ☐ Handoff accepted |
| **Release Owner** | | | ☐ **RELEASE CLOSED** |

---

## On Completion

### If Release Closure Succeeds

- [ ] All stabilization checks passed
- [ ] All alerts configured
- [ ] Operations handoff complete
- [ ] RELEASE-COMPLETE.md created
- [ ] Release Owner signed off
- [ ] **0i COMPLETE** → Operations

### If Issues During Stabilization

```
Classify severity
↓
Critical: Evaluate rollback vs hotfix path
Others: Document for next release
↓
NO code changes — exit 0i if fix required
```

---

## Template References

Use:
- `0. Admin/0i. Rollout/PRODUCTION-ALERTS-TEMPLATE.md`
- `0. Admin/0i. Rollout/RELEASE-COMPLETE-TEMPLATE.md`

---
Generated: {{timestamp}}
Phase: 68 - Stabilization & Handoff
Section: 0i Rollout
---
