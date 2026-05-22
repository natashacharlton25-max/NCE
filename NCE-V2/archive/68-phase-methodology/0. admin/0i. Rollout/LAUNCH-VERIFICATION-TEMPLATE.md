# Launch Verification

---
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
Version: v1.0.0
Template-Version: 1.0
Last Updated: {{DATE}}
Owner: {{NAME}}
RC Tag: {{RC_TAG}}
---

## Verification Summary

| Field | Value |
|-------|-------|
| RC Tag | {{RC_TAG}} |
| Deployment Time | {{TIMESTAMP}} |
| Verification Started | {{TIMESTAMP}} |
| Verification Completed | {{TIMESTAMP}} |
| Overall Status | ☐ PASS / ☐ FAIL |

---

## Grace Period

| Field | Value |
|-------|-------|
| Grace period started | {{TIMESTAMP}} |
| Grace period ended | {{TIMESTAMP}} |
| Duration | 5 minutes |
| P1 alerts suppressed | ☐ Yes |
| Metrics collected | ☐ Yes |
| Human monitoring active | ☐ Yes |

---

## Noisy Signal Classification (v1.4)

**Known noisy conditions — observe before rollback:**

| Metric | Noisy Conditions | Duration | Current Status |
|--------|------------------|----------|----------------|
| JVM memory | First 5-10 min | Transient | ☐ Normal / ☐ Noisy |
| Cache miss rate | Post-deploy | 5-15 min | ☐ Normal / ☐ Noisy |
| CPU spike | Traffic rebalancing | 2-5 min | ☐ Normal / ☐ Noisy |
| DB connections | Pool warm-up | 2-5 min | ☐ Normal / ☐ Noisy |
| Cold start latency | Serverless | First requests | ☐ Normal / ☐ Noisy |
| GC pause | JVM init | First 5 min | ☐ Normal / ☐ Noisy |

**Rule:** Noisy signals require sustained duration before rollback evaluation.

---

## Smoke Tests

**Time Limit:** Complete within 30 minutes of deployment.

| Test | Status | Duration | Notes |
|------|--------|----------|-------|
| User login | ☐ Pass / ☐ Fail | | |
| Core feature 1: {{NAME}} | ☐ Pass / ☐ Fail | | |
| Core feature 2: {{NAME}} | ☐ Pass / ☐ Fail | | |
| Core feature 3: {{NAME}} | ☐ Pass / ☐ Fail | | |
| API health endpoint | ☐ Pass / ☐ Fail | | |
| Database connectivity | ☐ Pass / ☐ Fail | | |
| External service integration | ☐ Pass / ☐ Fail | | |

**Smoke Test Summary:**
- Total tests: {{N}}
- Passed: {{N}}
- Failed: {{N}}
- Completion time: {{DURATION}}

---

## Feature Toggle Verification (if applicable)

| Feature Flag | Default State | Enabled For | Status | Metrics OK |
|--------------|---------------|-------------|--------|------------|
| {{FLAG_1}} | OFF | Internal (1%) | ☐ | ☐ |
| {{FLAG_2}} | OFF | Internal (1%) | ☐ | ☐ |
| {{FLAG_3}} | OFF | Internal (1%) | ☐ | ☐ |

**Feature Toggle Checklist:**
- [ ] Flags verified OFF by default
- [ ] Enabled for internal/QA users only
- [ ] Feature works for enabled users
- [ ] Metrics normal for enabled segment
- [ ] No errors from feature code paths

---

## Canary Confidence Score (v1.4)

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
| 2/4 | Low confidence | Extend canary |
| ≤1/4 | Insufficient | Evaluate rollback |

**Current confidence level:** {{SCORE}}/4 — {{INTERPRETATION}}

---

## Metrics Verification

### Comparison to Baselines

| Metric | 0h Baseline | Pre-Deploy (Phase 65) | Current | Delta from Pre-Deploy | Status |
|--------|-------------|----------------------|---------|----------------------|--------|
| Error rate | < {{X}}% | {{VALUE}} | | | ☐ Pass |
| p95 latency | < {{X}}ms | {{VALUE}} | | | ☐ Pass |
| p99 latency | < {{X}}ms | {{VALUE}} | | | ☐ Pass |
| CPU utilization | < {{X}}% | {{VALUE}} | | | ☐ Pass |
| Memory utilization | < {{X}}% | {{VALUE}} | | | ☐ Pass |
| DB connections | {{VALUE}} | {{VALUE}} | | | ☐ Pass |
| Queue depth | {{VALUE}} | {{VALUE}} | | | ☐ Pass |
| Request rate (RPS) | {{VALUE}} | {{VALUE}} | | | ☐ Pass |

**Metric Verification Window:** {{DURATION}} (minimum 15 minutes after grace period)

---

## Anomaly Detection

| Check | Tool | Status | Notes |
|-------|------|--------|-------|
| p95 vs 0h baseline | {{Datadog/Prometheus}} | ☐ Normal | |
| Error rate anomaly | {{Tool}} | ☐ Normal | |
| Traffic pattern anomaly | {{Tool}} | ☐ Normal | |
| Cost anomaly | {{Tool}} | ☐ Normal | |

---

## Error Monitoring

| Check | Status | Notes |
|-------|--------|-------|
| Error tracking dashboard reviewed | ☐ | |
| No new error types | ☐ | |
| Error rate within baseline | ☐ | |
| No critical errors | ☐ | |
| No uncaught exceptions | ☐ | |

**New Errors Detected:**

| Error Type | Count | Severity | Action |
|------------|-------|----------|--------|
| None | - | - | - |

---

## Traffic Verification

| Check | Status | Notes |
|-------|--------|-------|
| Traffic flowing to new version | ☐ | |
| Geographic distribution normal | ☐ | |
| No traffic anomalies | ☐ | |
| Load balancer healthy | ☐ | |
| CDN cache warming | ☐ | |

**Traffic Distribution:**

| Region/Zone | Expected % | Actual % | Status |
|-------------|------------|----------|--------|
| {{REGION_1}} | {{X}}% | | ☐ Normal |
| {{REGION_2}} | {{X}}% | | ☐ Normal |

---

## Resource Verification

| Resource | Expected | Actual | Status |
|----------|----------|--------|--------|
| Pod/Instance count | {{N}} | | ☐ Normal |
| CPU allocation | {{X}} | | ☐ Normal |
| Memory allocation | {{X}} | | ☐ Normal |
| Disk usage | {{X}}% | | ☐ Normal |

---

## Verification Decision

| Outcome | Selected |
|---------|----------|
| **PASS** - All verifications successful, proceed to Phase 68 | ☐ |
| **PASS WITH ISSUES** - Minor issues documented, proceed to Phase 68 | ☐ |
| **FAIL** - Critical issues, evaluate rollback | ☐ |

---

## Issues Found (if any)

| Issue | Severity | Action | Status |
|-------|----------|--------|--------|
| | | | |

---

## Rollback Evaluation (if FAIL)

| Question | Answer |
|----------|--------|
| Is production degraded? | ☐ Yes / ☐ No |
| Are users impacted? | ☐ Yes / ☐ No |
| Is error rate > 1% for 5+ min? | ☐ Yes / ☐ No |
| Is p95 > 2× baseline? | ☐ Yes / ☐ No |

**Rollback Decision:** ☐ Rollback / ☐ Continue with monitoring

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Deployer | | | ☐ Verification complete |
| On-call Engineer | | | ☐ Metrics verified |

---
Generated: {{timestamp}}
Phase: 67 - Post-Deploy Verification
Template: LAUNCH-VERIFICATION-TEMPLATE.md v1.0
---
