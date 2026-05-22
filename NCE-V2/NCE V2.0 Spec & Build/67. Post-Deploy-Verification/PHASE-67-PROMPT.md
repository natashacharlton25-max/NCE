# Phase 67: Post-Deploy Verification

---
Phase: 67
Section: 0i Rollout
Name: Post-Deploy Verification
Status: TEMPLATE
---

## Role

You are verifying that production is healthy and functioning as expected after deployment. This phase confirms the deployment succeeded and the application is ready for stabilization.

---

## Task

Execute smoke tests, verify metrics against baselines, enable feature flags for internal users, and confirm the deployment is healthy.

---

## Inputs

| File | Location | Purpose |
|------|----------|---------|
| DEPLOYMENT-LOG.md | From Phase 66 | Deployment record |
| PRE-DEPLOY-SNAPSHOT.md | From Phase 65 | Baseline metrics |
| PERFORMANCE-BASELINE.md | From 0h | Target metrics |
| ALERTING-THRESHOLDS.md | From 0h | Alert thresholds |

---

## Process

### Step 1: Grace Period

**For cold-start sensitive infrastructure (serverless, JVM):**

| Setting | Value |
|---------|-------|
| Duration | 5 minutes |
| P1 alerts | Suppressed |
| Metrics | Still collecting |
| Human monitoring | Required |

```
⏸️ GRACE PERIOD ACTIVE (5 min)
Metrics: Collecting
P1 Alerts: Suppressed (false positive prevention)
Human monitoring: Required
```

- [ ] Grace period started
- [ ] Human monitoring active
- [ ] Grace period completed

### Noisy Signal Classification (v1.4 Addition)

**Known noisy conditions — do NOT rollback immediately:**

| Metric | Known Noisy Conditions | Duration | Action |
|--------|------------------------|----------|--------|
| JVM memory | First 5-10 min post-deploy | Transient | Observe only |
| Cache miss rate | Post-deploy, post-restart | 5-15 min | Ignore unless sustained |
| CPU spike | Traffic rebalancing | 2-5 min | Correlate before rollback |
| DB connections | Connection pool warm-up | 2-5 min | Observe only |
| Cold start latency | Serverless/Lambda | First requests | Expected, observe |
| GC pause | JVM initialization | First 5 min | Expected, observe |

**Rule:** Noisy signals require sustained duration before triggering rollback evaluation.

### Step 2: Smoke Tests

**Time Limit:** Complete within 30 minutes of deployment.

| Test | Status | Notes |
|------|--------|-------|
| User login | ☐ Pass | |
| Core feature 1 | ☐ Pass | |
| Core feature 2 | ☐ Pass | |
| Core feature 3 | ☐ Pass | |
| API health | ☐ Pass | |
| Database connectivity | ☐ Pass | |
| External integrations | ☐ Pass | |

Document results in SMOKE-TEST-RESULTS.md

### Step 3: Controlled Feature Toggle

**For features with flags (from 0h audit):**

| Step | Action | Status |
|------|--------|--------|
| 1 | Verify flags are OFF (default) | ☐ |
| 2 | Enable for internal/QA (1%) | ☐ |
| 3 | Verify feature works | ☐ |
| 4 | Monitor metrics for segment | ☐ |
| 5 | Document results | ☐ |

| Flag | Default | Enabled For | Status | Metrics OK |
|------|---------|-------------|--------|------------|
| {{FLAG_1}} | OFF | Internal | ☐ | ☐ |
| {{FLAG_2}} | OFF | Internal | ☐ | ☐ |

**Note:** Full rollout happens in Phase 68, not here.

### Canary Confidence Score (v1.4 Addition)

**Quantify confidence for stakeholder communication:**

| Factor | Weight | Status | Score |
|--------|--------|--------|-------|
| Error rate stable | 1 | ☐ | /1 |
| Latency trending flat | 1 | ☐ | /1 |
| No new error types | 1 | ☐ | /1 |
| Cost stable | 1 | ☐ | /1 |
| **Total** | | | **/4** |

| Score | Interpretation | Action |
|-------|----------------|--------|
| 4/4 | High confidence | Safe to promote |
| 3/4 | Moderate confidence | Promote with monitoring |
| 2/4 | Low confidence | Extend canary duration |
| ≤1/4 | Insufficient confidence | Evaluate rollback |

### Step 4: Metrics Verification

**Compare to BOTH baselines:**

| Metric | 0h Target | Pre-Deploy (P65) | Current | Delta | Status |
|--------|-----------|------------------|---------|-------|--------|
| Error rate | < {{X}}% | {{VALUE}} | | | ☐ |
| p95 latency | < {{X}}ms | {{VALUE}} | | | ☐ |
| p99 latency | < {{X}}ms | {{VALUE}} | | | ☐ |
| CPU | < {{X}}% | {{VALUE}} | | | ☐ |
| Memory | < {{X}}% | {{VALUE}} | | | ☐ |
| DB connections | {{VALUE}} | {{VALUE}} | | | ☐ |
| Queue depth | {{VALUE}} | {{VALUE}} | | | ☐ |

**Why compare to both:**
- 0h baseline = target (what we want)
- Phase 65 snapshot = pre-deploy state (what we had)
- Distinguishes "deployment caused it" vs "prod was already trending badly"

**Verification window:** Minimum 15 minutes after grace period.

### Step 5: Anomaly Detection

| Check | Tool | Status |
|-------|------|--------|
| p95 vs baseline | {{Tool}} | ☐ Normal |
| Error rate anomaly | {{Tool}} | ☐ Normal |
| Traffic pattern | {{Tool}} | ☐ Normal |
| Cost anomaly | {{Tool}} | ☐ Normal |

### Step 6: Error Monitoring

| Check | Status |
|-------|--------|
| Error tracking dashboard reviewed | ☐ |
| No new error types | ☐ |
| Error rate within baseline | ☐ |
| No critical errors | ☐ |

**If new errors found:**

| Error Type | Count | Severity | Action |
|------------|-------|----------|--------|
| | | | |

### Step 7: Traffic Verification

| Check | Status |
|-------|--------|
| Traffic flowing to new version | ☐ |
| Geographic distribution normal | ☐ |
| No traffic anomalies | ☐ |
| Load balancer healthy | ☐ |

---

## Rules

### DOs

- ✅ Wait for grace period before alerting
- ✅ Complete smoke tests within 30 minutes
- ✅ Compare to Phase 65 snapshot
- ✅ Document all verification results
- ✅ Enable feature flags only for internal users

### DON'Ts

- ❌ Skip grace period
- ❌ Rush smoke tests
- ❌ Ignore metric anomalies
- ❌ Enable feature flags for all users
- ❌ Proceed if smoke tests fail

---

## Rollback Decision Point

If verification fails, evaluate using this matrix:

| Condition | Action |
|-----------|--------|
| Production degraded | Rollback |
| Error rate > 1% for 5+ min | Evaluate rollback |
| p95 > 2× baseline | Evaluate rollback |
| Smoke tests fail | Evaluate rollback |
| Minor issues only | Continue with documentation |

**On-call Engineer has AUTONOMOUS rollback authority.**

If rollback needed:
1. **CAPTURE EVIDENCE** (see EVIDENCE-SNAPSHOT-GUIDE.md)
2. **EXECUTE ROLLBACK**
3. **Document** in INCIDENT-REPORT.md
4. **Return to 0h**

---

## Output Format

Create these files:

1. **SMOKE-TEST-RESULTS.md**
   - Location: `/docs/0i/`
   - All smoke test results

2. **HEALTH-CHECK-RESULTS.md**
   - Location: `/docs/0i/`
   - Metrics verification

3. **LAUNCH-VERIFICATION.md** (from template)
   - Location: `/docs/0i/`
   - Complete verification summary

---

## On Completion

### If Verification Passes

- [ ] All smoke tests passed
- [ ] Metrics within baseline
- [ ] No new error types
- [ ] Feature toggles working (internal)
- [ ] LAUNCH-VERIFICATION.md complete
- [ ] Proceed to Phase 68

### If Verification Fails

```
Evaluate severity
↓
If critical:
├── CAPTURE EVIDENCE SNAPSHOT
├── Execute rollback
├── Document in INCIDENT-REPORT.md
├── Return to 0h
↓
If minor:
├── Document in LAUNCH-VERIFICATION.md
├── Continue to Phase 68 with documented issues
```

---

## Template Reference

Use: `0. Admin/0i. Rollout/LAUNCH-VERIFICATION-TEMPLATE.md`

---
Generated: {{timestamp}}
Phase: 67 - Post-Deploy Verification
Section: 0i Rollout
---
