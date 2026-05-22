# Pre-Deploy Checklist

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
RC Commit SHA: {{COMMIT_SHA}}
---

## Release Summary

| Field | Value |
|-------|-------|
| RC Tag | {{RC_TAG}} |
| Commit SHA | {{COMMIT_SHA}} |
| Target Environment | Production |
| Deployment Strategy | {{Rolling | Blue-Green | Canary | Shadow}} |
| Deployer | {{NAME}} |
| Release Owner | {{NAME}} |

---

## Step 0: 0i Readiness Review (v1.4)

**15-minute cross-functional review before starting verification.**

| Role | Present | Confirmed Ready |
|------|---------|-----------------|
| Release Owner | ☐ | ☐ |
| Deployer | ☐ | ☐ |
| On-call Engineer | ☐ | ☐ |
| SRE/DevOps | ☐ | ☐ |
| QA (optional) | ☐ | ☐ |
| Product (optional) | ☐ | ☐ |

**Outcome:**
| Decision | Selected |
|----------|----------|
| **GO** — Proceed with Phase 65 | ☐ |
| **NO-GO** — Resolve issues first | ☐ |

**If NO-GO:**
| Blocker | Owner | ETA |
|---------|-------|-----|
| | | |

---

## Abort 0i Criteria (v1.4)

**If ANY condition is true, ABORT 0i:**

| Condition | Status | Action if True |
|-----------|--------|----------------|
| Upstream dependency degraded | ☐ Clear | Abort → Wait |
| Monitoring drift unresolved | ☐ Clear | Abort → Fix |
| Secrets mismatch | ☐ Clear | Abort → 0h |
| DB schema mismatch | ☐ Clear | Abort → 0h |
| Deployment freeze violation | ☐ Clear | Abort → Reschedule |
| Missing sign-offs | ☐ Clear | Abort → 0h |
| On-call unavailable | ☐ Clear | Abort → Reschedule |
| Release Owner unavailable | ☐ Clear | Abort → Reschedule |

**All abort conditions clear:** ☐ Yes — Safe to proceed

---

## Step 1: Handoff Verification

| Check | Status | Notes |
|-------|--------|-------|
| 0h-to-0i-HANDOFF.md exists | ☐ | |
| All sign-offs collected | ☐ | |
| Release Owner GO decision recorded | ☐ | |
| RC tag verified (`git show {{RC_TAG}}`) | ☐ | |

---

## Step 2: Documentation Verification

| Document | Accessible | Location |
|----------|------------|----------|
| DEPLOYMENT-GUIDE.md | ☐ | |
| RUNBOOK.md | ☐ | |
| ALERTING-THRESHOLDS.md | ☐ | |
| COST-THRESHOLDS.md | ☐ | |

---

## Step 3: Database Compatibility

| Check | Status | Notes |
|-------|--------|-------|
| Production DB schema version matches expected | ☐ | |
| Migrations verified against prod DB | ☐ | |
| No pending migrations in production | ☐ | |
| Backward compatibility confirmed | ☐ | |

**Current Production Schema Version:** {{VERSION}}
**Expected Schema Version for RC:** {{VERSION}}

---

## Step 4: Config Drift Check

| Check | Status | Notes |
|-------|--------|-------|
| Environment variables match expected | ☐ | |
| Feature flag configuration matches 0h audit | ☐ | |
| Infrastructure matches IaC definitions | ☐ | |

---

## Step 5: Secrets Drift Check

| Check | Status | Notes |
|-------|--------|-------|
| Secrets metadata hash matches 0h snapshot | ☐ | |
| No new secrets added since 0h sign-off | ☐ | |
| No secrets removed since 0h sign-off | ☐ | |
| Secret rotation dates within policy | ☐ | |

**0h Secrets Hash:** {{HASH}}
**Current Secrets Hash:** {{HASH}}

---

## Step 6: Production Observability Snapshot

**Capture BEFORE deployment:**

| Metric | Current Value | Timestamp |
|--------|---------------|-----------|
| p95 latency | | |
| Error rate | | |
| CPU utilization | | |
| Memory utilization | | |
| DB connections (active) | | |
| DB connections (pool) | | |
| Queue depth | | N/A |
| Request rate (RPS) | | |

**Snapshot saved to:** PRE-DEPLOY-SNAPSHOT.md

---

## Step 7: Rollback Readiness

| Check | Status | Notes |
|-------|--------|-------|
| Previous production version identified | ☐ | Version: |
| Rollback procedure reviewed | ☐ | |
| Rollback tested in 0h (evidence verified) | ☐ | |
| Database rollback ready (if applicable) | ☐ | |
| Rollback dry run passed | ☐ | |

**Rollback Dry Run Command:**
```bash
{{COMMAND}}
```

**Dry Run Result:** {{PASS | FAIL}}

---

## Step 8: Deployment Freeze Compliance

| Check | Status |
|-------|--------|
| Not in Friday freeze window | ☐ |
| Not in holiday freeze window | ☐ |
| Not during active incident | ☐ |
| Freeze exception approved (if applicable) | ☐ / N/A |

**If Exception:**

| Field | Value |
|-------|-------|
| Freeze type | |
| Exception reason | |
| Approved by | |
| Approval date | |

---

## Step 9: Stakeholder Notification

| Check | Status |
|-------|--------|
| Deployment window confirmed | ☐ |
| On-call engineer notified | ☐ |
| Team notified in deployment channel | ☐ |
| Status page prepared (if applicable) | ☐ |
| Release Owner confirms Golden Hour availability | ☐ |

**On-call Engineer:** {{NAME}}
**Deployment Channel:** {{CHANNEL}}

---

## Step 10: Upstream Status Verification

| Dependency | Status Page | Current Status | Last Checked |
|------------|-------------|----------------|--------------|
| {{Cloud Provider}} | {{URL}} | ☐ GREEN | |
| {{Auth Provider}} | {{URL}} | ☐ GREEN | |
| {{Payment Provider}} | {{URL}} | ☐ GREEN | |
| {{CDN}} | {{URL}} | ☐ GREEN | |
| {{Other}} | {{URL}} | ☐ GREEN | |

**Rule:** If ANY P1 dependency is DEGRADED or DOWN, STOP deployment.

---

## Step 11: Monitoring Drift Check

| Check | Status |
|-------|--------|
| All alerts from ALERTING-THRESHOLDS.md configured | ☐ |
| No alerts disabled since 0h sign-off | ☐ |
| Dashboards still functional | ☐ |
| All required metrics being collected | ☐ |
| PagerDuty/Opsgenie routing unchanged | ☐ |

---

## Step 12: Pre-Flight Summary

| Check | Status |
|-------|--------|
| RC commit SHA verified | ☐ |
| CI/CD pipeline green | ☐ |
| Staging still healthy | ☐ |
| Production DB compatible | ☐ |
| Production config verified | ☐ |
| Production secrets verified | ☐ |
| Observability snapshot captured | ☐ |
| Upstream dependencies healthy | ☐ |
| Monitoring baseline verified | ☐ |
| No blocking incidents | ☐ |
| Rollback procedure confirmed | ☐ |
| Rollback dry run passed | ☐ |
| On-call coverage confirmed | ☐ |
| Golden Hour availability confirmed | ☐ |
| Deployment freeze compliance verified | ☐ |

---

## Step 13: Human Factors Check (v1.4)

| Check | Status |
|-------|--------|
| Deployer has not done >2 deploys today | ☐ |
| On-call is not in fatigue window (>12h shift) | ☐ |
| Backup Release Owner identified | ☐ |
| Two-person rule for canary (if applicable) | ☐ / N/A |
| Long rollout? Break reminder set | ☐ / N/A |

**Backup Release Owner:** {{NAME}}
**Today's deployment count:** {{N}} / 2 max

---

## Step 14: Rollback Cost Classification (v1.4)

| Rollback Type | Cost | Notes | Exec Awareness |
|---------------|------|-------|----------------|
| App-only | Low | Safe | No |
| Config-only | Low | Safe | No |
| Schema-affected | Medium | DB check | Recommend |
| Cache invalidation | Medium | Perf hit | No |
| Data rewrite | High | Data impact | Required |
| Multi-region | High | Coordination | Required |

**This deployment's rollback cost:** ☐ Low / ☐ Medium / ☐ High

**If High, exec notified:** ☐ Yes / ☐ N/A

---

## Pre-Deploy Decision

| Decision | Selected |
|----------|----------|
| **PROCEED** - All checks pass, ready to deploy | ☐ |
| **BLOCKED** - Issues must be resolved | ☐ |

**If BLOCKED:**

| Blocker | Required Action |
|---------|-----------------|
| | |

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Deployer | | | ☐ Pre-deploy verified |

---
Generated: {{timestamp}}
Phase: 65 - Pre-Deploy Verification
Template: PRE-DEPLOY-CHECKLIST-TEMPLATE.md v1.0
---
