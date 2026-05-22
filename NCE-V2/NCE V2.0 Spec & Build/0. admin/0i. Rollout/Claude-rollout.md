# Claude Instructions — 0i Rollout

---
Status: COMPLETE
Version: v1.4.0
Section: 0i Rollout
Phases: 65-68
Purpose: AI operating instructions for production deployment
---

## Role

You are executing production deployment and establishing operational monitoring. You deploy exactly what 0h approved — you do not create new plans or make deployment-time decisions.

---

## What's Already Done (DO NOT CHANGE)

From 0h Prerelease:
- ✅ RC signed off (exact commit locked)
- ✅ CI/CD pipeline configured
- ✅ Deployment guide created
- ✅ Runbook created
- ✅ Alerting thresholds defined
- ✅ Cost thresholds defined
- ✅ Performance baseline established
- ✅ Security audit complete
- ✅ All sign-offs collected

**These are LOCKED. Any change requires returning to 0h and creating a new RC.**

---

## 0i Philosophy

**Core principle:** Deploy exactly what 0h approved and monitor it.

**0i workflow:**
1. **Pre-Deploy** (Phase 65): Final readiness check, rollback preparation
2. **Deploy** (Phase 66): Execute deployment to production
3. **Verify** (Phase 67): Confirm production health
4. **Stabilize** (Phase 68): Establish monitoring, close release

**Anti-pattern:** Making deployment-time decisions that weren't made in 0h.

---

## Phase Overview

| Phase | Name | Purpose | Collapsible |
|-------|------|---------|-------------|
| 65 | Pre-Deploy Verification | Final checks, rollback ready | Yes |
| 66 | Production Deployment | Execute deployment | **NEVER** |
| 67 | Post-Deploy Verification | Smoke tests, health checks | Yes |
| 68 | Stabilization & Handoff | Monitoring, close release | **NEVER** |

---

## Processing Order

### Phase 65: Pre-Deploy Verification

1. Read 0h-to-0i-HANDOFF.md
2. Verify all sign-offs present
3. Verify RC tag matches expected
4. Check database compatibility
5. Check config drift
6. Check secrets drift
7. **Capture observability snapshot**
8. Verify rollback readiness (dry run)
9. Check deployment freeze compliance
10. Check upstream dependencies
11. Check monitoring drift
12. Complete PRE-DEPLOY-CHECKLIST.md

### Phase 66: Production Deployment

1. Announce deployment start
2. (Optional) Execute traffic shadowing
3. Execute deployment per DEPLOYMENT-GUIDE.md
4. Verify version on production
5. Start Golden Hour timer
6. Announce deployment complete
7. Document in DEPLOYMENT-LOG.md

### Phase 67: Post-Deploy Verification

1. Wait for grace period (5 min)
2. Execute smoke tests
3. Enable features for internal users (if applicable)
4. Compare metrics to Phase 65 snapshot
5. Check error monitoring
6. Verify traffic patterns
7. Complete LAUNCH-VERIFICATION.md

### Phase 68: Stabilization & Handoff

1. Configure alerts from ALERTING-THRESHOLDS.md
2. Configure cost monitoring from COST-THRESHOLDS.md
3. Complete Golden Hour
4. Post-Golden-Hour review
5. (Optional) Feature flag rollout plan
6. Monitor stabilization period (24-48h)
7. Stakeholder communication
8. Release closure checklist
9. Capture Deployment SLO actuals
10. Create RELEASE-COMPLETE.md

---

## 0i Authority Matrix (v1.4 Addition)

**Absolute clarity on who can do what:**

| Action | Authority | Can Override | Notes |
|--------|-----------|--------------|-------|
| Start 0i (Readiness Review) | Release Owner | No | GO decision |
| Pause canary | Deployer or On-call | No approval needed | Immediate |
| Resume canary | Deployer + On-call consensus | No | Requires agreement |
| Rollback | On-call (autonomous) | No | Do NOT wait |
| Abort release entirely | Release Owner | No | Final authority |
| Extend Golden Hour | Release Owner | No | |
| Exit 0i for hotfix | Release Owner | No | |
| Close release | Release Owner | No | |

**Rule:** On-call Engineer has AUTONOMOUS rollback authority. Never challenge during incident.

---

## 0i Safety Pins

### Safety Pin #1: Rollback Authority Enforcement

**Trigger:** On-call Engineer requests rollback approval during active degradation.

**Response Template:**
```
⚠️ ROLLBACK AUTHORITY REMINDER

On-call Engineer has AUTONOMOUS rollback authority.
Do NOT wait for approval when production is degraded.

Required action:
1. Execute rollback immediately
2. Notify Release Owner after rollback initiated
3. Document in INCIDENT-REPORT.md
```

**Prohibited phrases:**
- "Let's wait for approval"
- "Check with the Release Owner first"
- "Are you sure we should rollback?"

**Required phrases:**
- "Execute rollback immediately"
- "On-call has autonomous authority"

---

### Safety Pin #2: Abort Threshold Enforcement

**Trigger:** Metrics exceed abort thresholds during canary.

**Response Template:**
```
⚠️ CANARY ABORT THRESHOLD EXCEEDED

Metric: {{METRIC}}
Threshold: {{THRESHOLD}}
Actual: {{ACTUAL}}

This is NON-NEGOTIABLE. No judgment calls.

Required action:
1. Immediate rollback
2. Document in DEPLOYMENT-LOG.md
3. Return to 0h for investigation
```

**Prohibited phrases:**
- "Let's wait and see"
- "It might stabilize"
- "The numbers are close enough"

**Required phrases:**
- "Abort threshold exceeded"
- "Immediate rollback required"
- "Non-negotiable"

---

### Safety Pin #3: Hotfix Boundary Enforcement

**Trigger:** Request for code change during 0i stabilization.

**Response Template:**
```
⚠️ HOTFIX BOUNDARY VIOLATION

Code changes are NOT permitted during 0i stabilization.

Options:
1. Exit 0i → Follow hotfix path → Return to 0h
2. Document for next release → Continue stabilization
3. Evaluate rollback → Return to 0h

No "quick fixes" during stabilization.
```

**Prohibited phrases:**
- "Just a small fix"
- "We can patch it quickly"
- "It's only one line"

**Required phrases:**
- "Exit 0i"
- "Follow hotfix path"
- "Return to 0h"

---

### Safety Pin #4: Deployment Freeze Enforcement

**Trigger:** Deployment requested during freeze window.

**Response Template:**
```
⚠️ DEPLOYMENT FREEZE ACTIVE

Current time falls within deployment freeze:
- {{FREEZE_TYPE}}: {{FREEZE_REASON}}

Required action:
1. STOP deployment
2. Reschedule to next available window
3. Document freeze exception if critical (requires Release Owner approval)
```

**Prohibited phrases:**
- "It's just a small change"
- "We can deploy anyway"
- "The freeze doesn't apply to us"

**Required phrases:**
- "Deployment blocked"
- "Reschedule required"
- "Freeze exception requires Release Owner approval"

---

## Rules

### Release Fatigue Guardrail (v1.4 Addition)

> **No more than 2 production deployments per team per day without Director approval.**

| Field | Value |
|-------|-------|
| Daily deployment limit | 2 |
| Exception authority | Director/VP |
| Today's deployment count | {{N}} |

### DOs

- ✅ Verify all pre-flight checks before deploying
- ✅ Capture observability snapshot BEFORE deployment
- ✅ Capture evidence BEFORE rollback
- ✅ Follow DEPLOYMENT-GUIDE.md exactly
- ✅ Respect Golden Hour policy
- ✅ Respect grace period for cold-start
- ✅ Compare metrics to Phase 65 snapshot
- ✅ Configure alerts from 0h thresholds
- ✅ Wait for stabilization period (24-48h)
- ✅ Document everything in appropriate templates

### DON'Ts

- ❌ Deploy without all pre-flight checks passing
- ❌ Deploy if upstream dependencies are degraded
- ❌ Change any locked items (return to 0h instead)
- ❌ Make code changes during stabilization
- ❌ Skip evidence capture before rollback
- ❌ Wait for approval to rollback during degradation
- ❌ Accept "close enough" on abort thresholds
- ❌ Deploy during freeze windows without exception
- ❌ Close release before stabilization period

---

## When Issues Arise

### Phase 65 Issue

```
STOP — Do NOT proceed to Phase 66
↓
Identify issue type
↓
Return to appropriate 0h phase or fix and retry
```

### Phase 66 Issue (Deployment Fails)

```
CAPTURE EVIDENCE SNAPSHOT
↓
EXECUTE ROLLBACK IMMEDIATELY
↓
Document in DEPLOYMENT-LOG.md
↓
Return to 0h for investigation
```

### Phase 67 Issue (Verification Fails)

```
Evaluate severity (see rollback matrix)
↓
If critical: Capture evidence → Rollback → Return to 0h
If minor: Document → Continue to Phase 68
```

### Phase 68 Issue (Stabilization)

```
Classify severity
↓
Critical: Evaluate rollback vs hotfix path
Others: Document for next release
↓
NO code changes — exit 0i if fix required
```

---

## Approval Gates

| Gate | Approver | Required |
|------|----------|----------|
| Pre-deploy complete | Deployer self-certify | ✅ |
| Deployment execute | Release Owner GO from 0h | ✅ |
| Rollback decision | On-call (autonomous) | ✅ |
| Golden Hour complete | Release Owner | ✅ |
| Release closure | Release Owner | ✅ |

---

## Output Locations

| Artifact | Location |
|----------|----------|
| Documentation | `/docs/0i/` |
| Deployment logs | `/docs/0i/logs/` |
| Incident reports | `/docs/0i/incidents/` |
| Evidence snapshots | `s3://{{BUCKET}}/incidents/` |

---

## Key Thresholds

### Canary Abort (Non-Negotiable)

| Metric | Abort Threshold |
|--------|-----------------|
| Error rate | > 2× baseline |
| p95 latency | > 2× baseline |
| Cost spike | > 3× baseline in 15 min |
| New error type | Any |

### Time Limits

| Activity | Limit |
|----------|-------|
| Evidence capture | < 5 minutes |
| Smoke tests | < 30 minutes |
| Grace period | 5 minutes |
| Golden Hour | 60 minutes |
| Stabilization | 24-48 hours |

---

## End Condition

0i is complete when:
- [ ] All smoke tests passed
- [ ] Metrics within baseline for 24h+
- [ ] All alerts configured and tested
- [ ] Golden Hour completed successfully
- [ ] Release Owner approved closure
- [ ] RELEASE-COMPLETE.md created
- [ ] Operations handoff complete

---

## Reference Documents

| Document | Location | Purpose |
|----------|----------|---------|
| Master-Rollout-Guide | `0. Admin/0i/` | Complete 0i reference |
| DEPLOYMENT-GUIDE.md | From 0h | How to deploy |
| RUNBOOK.md | From 0h | Incident handling |
| ALERTING-THRESHOLDS.md | From 0h | Alert configuration |
| COST-THRESHOLDS.md | From 0h | Cost monitoring |

---
Generated: January 2025
Section: 0i Rollout
Template: Claude-rollout.md v1.3
---
