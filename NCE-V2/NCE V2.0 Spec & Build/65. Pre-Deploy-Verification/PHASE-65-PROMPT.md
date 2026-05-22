# Phase 65: Pre-Deploy Verification

---
Phase: 65
Section: 0i Rollout
Name: Pre-Deploy Verification
Status: TEMPLATE
---

## Role

You are performing final verification that everything is ready for production deployment. This is the last checkpoint before deployment begins.

---

## Step 0: 0i Readiness Review (v1.4 Addition)

**BEFORE starting Phase 65 verification, conduct a 15-minute cross-functional readiness review.**

| Role | Present | Confirmed Ready |
|------|---------|-----------------|
| Release Owner | ☐ | ☐ |
| Deployer | ☐ | ☐ |
| On-call Engineer | ☐ | ☐ |
| SRE/DevOps | ☐ | ☐ |
| QA (optional) | ☐ | ☐ |
| Product (optional) | ☐ | ☐ |

**Outcome:** Signed "GO to begin 0i" (separate from "GO to deploy")

| Decision | Selected |
|----------|----------|
| **GO** — Proceed with Phase 65 | ☐ |
| **NO-GO** — Resolve issues first | ☐ |

**If NO-GO:**

| Blocker | Owner | ETA |
|---------|-------|-----|
| | | |

---

## Abort 0i Criteria (v1.4 Addition)

**If ANY of these conditions are true, ABORT 0i before deployment:**

| Condition | Action | Return To |
|-----------|--------|-----------|
| Upstream dependency degraded | Abort 0i | Wait + retry |
| Monitoring drift unresolved | Abort 0i | Fix + retry |
| Secrets mismatch | Abort 0i | 0h Phase 62 |
| DB schema mismatch | Abort 0i | 0h Phase 62 |
| Deployment freeze violation | Abort 0i | Reschedule |
| Missing sign-offs | Abort 0i | 0h Phase 64 |
| On-call unavailable | Abort 0i | Reschedule |
| Release Owner unavailable | Abort 0i | Reschedule |

**Rule:** Do NOT proceed to Phase 66 if any abort condition is true.

---

## Task

Verify all pre-deployment requirements are met, capture the production observability snapshot, confirm rollback readiness, and ensure no deployment blockers exist.

---

## Inputs

Read these files before starting:

| File | Location | Purpose |
|------|----------|---------|
| 0h-to-0i-HANDOFF.md | From 0h | Handoff verification |
| PRERELEASE-CHECKLIST.md | From 0h | Sign-off verification |
| DEPLOYMENT-GUIDE.md | From 0h | Deployment procedure |
| ALERTING-THRESHOLDS.md | From 0h | Alert baseline |
| RUNBOOK.md | From 0h | Incident procedures |

---

## Process

### Step 1: Verify Handoff Completeness

- [ ] 0h-to-0i-HANDOFF.md exists and is complete
- [ ] All required sign-offs present
- [ ] Release Owner GO decision recorded
- [ ] RC tag verified: `git show {{RC_TAG}}`

### Step 2: Verify Documentation Ready

- [ ] DEPLOYMENT-GUIDE.md accessible
- [ ] RUNBOOK.md accessible
- [ ] ALERTING-THRESHOLDS.md ready
- [ ] COST-THRESHOLDS.md ready

### Step 3: Database Compatibility Verification

```bash
# Verify production DB schema version
{{DB_VERSION_CHECK_COMMAND}}
```

- [ ] Production schema version matches expected
- [ ] No pending migrations
- [ ] Backward compatibility confirmed

### Step 4: Config Drift Check

- [ ] Environment variables match expected
- [ ] Feature flags match 0h audit
- [ ] Infrastructure matches IaC

### Step 5: Secrets Drift Check

```bash
# Compare secrets inventory hash
{{SECRETS_HASH_COMMAND}}
```

- [ ] Secrets hash matches 0h snapshot
- [ ] No new secrets added since 0h
- [ ] No secrets removed since 0h

### Step 6: Production Observability Snapshot

**CRITICAL:** Capture BEFORE deployment for before/after comparison.

| Metric | Current Value | Timestamp |
|--------|---------------|-----------|
| p95 latency | | |
| Error rate | | |
| CPU utilization | | |
| Memory utilization | | |
| DB connections | | |
| Queue depth | | |
| Request rate | | |

Save to: PRE-DEPLOY-SNAPSHOT.md

### Step 7: Rollback Readiness

- [ ] Previous version identified: `{{PREVIOUS_VERSION}}`
- [ ] Rollback procedure reviewed
- [ ] Rollback tested in 0h (evidence exists)
- [ ] Database rollback ready

**Rollback Dry Run:**
```bash
{{ROLLBACK_DRY_RUN_COMMAND}}
```
- [ ] Dry run passed

### Step 8: Deployment Freeze Check

- [ ] Not in Friday freeze
- [ ] Not in holiday freeze
- [ ] No active incidents
- [ ] Exception approved (if needed)

### Step 9: Upstream Status Verification

Check P1 dependencies:

| Dependency | Status Page | Status |
|------------|-------------|--------|
| {{Cloud Provider}} | {{URL}} | ☐ GREEN |
| {{Auth Provider}} | {{URL}} | ☐ GREEN |
| {{Payment Provider}} | {{URL}} | ☐ GREEN |
| {{CDN}} | {{URL}} | ☐ GREEN |

**Rule:** If ANY P1 dependency is DEGRADED, STOP deployment.

### Step 10: Monitoring Drift Check

- [ ] All alerts from ALERTING-THRESHOLDS.md configured
- [ ] No alerts disabled since 0h
- [ ] Dashboards functional
- [ ] Alert routing unchanged

### Step 11: Notify Stakeholders

- [ ] Deployment window confirmed
- [ ] On-call engineer notified
- [ ] Team notified in channel
- [ ] Release Owner confirms Golden Hour availability

### Step 12: Pre-Flight Checklist

Complete all items in PRE-DEPLOY-CHECKLIST-TEMPLATE.md

### Step 13: Human Factors Check (v1.4 Addition)

| Check | Status |
|-------|--------|
| Deployer has not done >2 deploys today | ☐ |
| On-call is not in fatigue window (>12h shift) | ☐ |
| Backup Release Owner identified for Golden Hour | ☐ |
| Two-person rule for canary promotion (if applicable) | ☐ / N/A |
| Long rollout? Break/hydration reminder set | ☐ / N/A |

**Backup Release Owner:** {{NAME}}

### Step 14: Rollback Cost Classification (v1.4 Addition)

| Rollback Type | Cost | Notes | Exec Awareness |
|---------------|------|-------|----------------|
| App-only | Low | Safe, standard | No |
| Config-only | Low | Safe, standard | No |
| Schema-affected | Medium | Requires DB compatibility check | Recommend |
| Cache invalidation | Medium | Performance hit expected | No |
| Data rewrite | High | Potential data impact | Required |
| Multi-region | High | Coordination required | Required |

**This deployment's rollback cost is:** ☐ Low / ☐ Medium / ☐ High

---

## Rules

### DOs

- ✅ Verify EVERY item on the checklist
- ✅ Capture observability snapshot BEFORE proceeding
- ✅ Test rollback dry run
- ✅ Check upstream dependencies
- ✅ Document any exceptions

### DON'Ts

- ❌ Skip any verification step
- ❌ Proceed if upstream dependencies degraded
- ❌ Proceed without rollback dry run
- ❌ Deploy during freeze without exception
- ❌ Proceed if any sign-off missing

---

## Locked Items Reminder

> **If any stakeholder requests a change to a locked item during Phase 65, STOP and return to 0h.**

Locked items:
- RC commit
- CI/CD pipeline
- Deployment configuration
- Performance baselines
- Security requirements
- Alerting thresholds

---

## Output Format

Create these files:

1. **PRE-DEPLOY-CHECKLIST.md** (from template)
   - Location: `/docs/0i/`
   - All verification items checked

2. **PRE-DEPLOY-SNAPSHOT.md**
   - Location: `/docs/0i/`
   - Production observability baseline

3. **ROLLBACK-READY.md**
   - Location: `/docs/0i/`
   - Rollback preparation confirmation

---

## On Completion

### If All Checks Pass

- [ ] PRE-DEPLOY-CHECKLIST.md complete
- [ ] PRE-DEPLOY-SNAPSHOT.md captured
- [ ] ROLLBACK-READY.md confirmed
- [ ] Proceed to Phase 66

### If Any Check Fails

```
STOP — Do NOT proceed to Phase 66
↓
Identify issue type:
├── Documentation missing → Return to 0h Phase 64
├── Sign-off missing → Return to 0h Phase 64
├── RC issue → Return to 0h Phase 61
├── DB compatibility → Return to 0h Phase 62
├── Secrets drift → Return to 0h Phase 62
├── Config drift → Fix config, retry Phase 65
├── Upstream degraded → Wait or escalate
├── Monitoring drift → Fix monitoring, retry
├── Freeze active → Reschedule
```

---

## Template Reference

Use: `0. Admin/0i. Rollout/PRE-DEPLOY-CHECKLIST-TEMPLATE.md`

---
Generated: {{timestamp}}
Phase: 65 - Pre-Deploy Verification
Section: 0i Rollout
---
