# Phase 66: Production Deployment

---
Phase: 66
Section: 0i Rollout
Name: Production Deployment
Status: TEMPLATE
Collapsible: **NEVER**
---

## Role

You are executing the production deployment of the signed-off Release Candidate. This phase is non-collapsible — it must always be executed explicitly.

---

## Task

Execute the deployment to production following DEPLOYMENT-GUIDE.md exactly, verify the deployment, and start the Golden Hour monitoring period.

---

## Inputs

| File | Location | Purpose |
|------|----------|---------|
| DEPLOYMENT-GUIDE.md | From 0h | Deployment procedure |
| PRE-DEPLOY-CHECKLIST.md | From Phase 65 | Pre-flight verification |
| PRE-DEPLOY-SNAPSHOT.md | From Phase 65 | Baseline metrics |
| ROLLBACK-READY.md | From Phase 65 | Rollback preparation |

---

## Deployment Strategy

Select based on DEPLOYMENT-GUIDE.md:

| Strategy | When to Use | Risk |
|----------|-------------|------|
| Rolling | Stateless, zero-downtime | Low |
| Blue-Green | Instant rollback needed | Low |
| Canary | High-risk changes | Medium |
| Shadow | Pre-verify with real traffic | Low |
| Big Bang | Small apps, downtime OK | Higher |

---

## Process

### Step 1: (Optional) Traffic Shadowing

If using shadow verification:

```bash
# Mirror 5% of production traffic
{{TRAFFIC_SHADOW_COMMAND}}
```

- [ ] Shadow traffic enabled
- [ ] Monitor for 15-30 minutes
- [ ] Abort thresholds verified
- [ ] Shadow results OK → proceed

### Step 2: Announce Deployment Start

```
🚀 DEPLOYMENT STARTING
RC: {{RC_TAG}}
Target: Production
Strategy: {{STRATEGY}}
Time: {{TIMESTAMP}}
Deployer: {{NAME}}
Golden Hour ends: {{TIMESTAMP + 60min}}
```

### Step 3: Execute Deployment

Follow DEPLOYMENT-GUIDE.md exactly:

```bash
{{DEPLOYMENT_COMMAND}}
```

### Step 4: Verify Deployment

| Check | Command | Expected |
|-------|---------|----------|
| Version | `{{VERSION_CHECK}}` | {{RC_TAG}} |
| Health | `{{HEALTH_CHECK}}` | 200 OK |
| Commit SHA | `{{SHA_CHECK}}` | {{COMMIT}} |

### Step 5: Canary Progression (if applicable)

| Stage | % | Duration | Exit Criteria | Status |
|-------|---|----------|---------------|--------|
| 1 | 1% | 30 min | All metrics OK | ☐ |
| 2 | 5% | 30 min | All metrics OK | ☐ |
| 3 | 25% | 30 min | All metrics OK | ☐ |
| 4 | 50% | 30 min | All metrics OK | ☐ |
| 5 | 100% | - | Full rollout | ☐ |

### Step 6: Start Golden Hour

```
⏱️ GOLDEN HOUR ACTIVE
Started: {{TIMESTAMP}}
Ends: {{TIMESTAMP + 60min}}
Release Owner: {{NAME}} (must remain reachable)
Stack freeze: Active
```

### Step 7: Announce Deployment Complete

```
✅ DEPLOYMENT COMPLETE
RC: {{RC_TAG}}
Status: Live on production
Time: {{TIMESTAMP}}
Golden Hour: Active until {{END_TIME}}
Verification: Proceeding to Phase 67
```

---

## Canary Abort Thresholds

**NON-NEGOTIABLE — No judgment calls:**

| Metric | Abort Threshold | Action |
|--------|-----------------|--------|
| Error rate | > 2× baseline | Immediate rollback |
| p95 latency | > 2× baseline | Pause + evaluate |
| New error type | Any | Pause + investigate |
| Cost spike | > 3× baseline (15 min) | Immediate rollback |
| Memory | > 95% | Immediate rollback |
| CPU | > 90% | Pause + evaluate |

**If abort threshold exceeded:**

```
⚠️ CANARY ABORT THRESHOLD EXCEEDED

Metric: {{METRIC}}
Threshold: {{THRESHOLD}}
Actual: {{ACTUAL}}

Action: IMMEDIATE ROLLBACK
This is NON-NEGOTIABLE.
```

---

## Cost Spike Guardrail

> **If cost anomaly > 3× baseline within 15 minutes, rollback or pause.**

Protects against:
- Runaway autoscaling
- Misconfigured caching
- Infinite loops

---

## Golden Hour Policy

| Rule | Requirement |
|------|-------------|
| Release Owner availability | 60 minutes post-deploy |
| Stack freeze | No other deployments |
| P1 alert response | Immediate |

---

## Rules

### DOs

- ✅ Follow DEPLOYMENT-GUIDE.md exactly
- ✅ Verify version after deployment
- ✅ Monitor abort thresholds
- ✅ Respect Golden Hour policy
- ✅ Document everything in DEPLOYMENT-LOG.md

### DON'Ts

- ❌ Deviate from DEPLOYMENT-GUIDE.md
- ❌ Skip version verification
- ❌ Ignore abort thresholds
- ❌ Allow Release Owner to leave during Golden Hour
- ❌ Deploy other changes during Golden Hour

---

## Rollback Trigger

If deployment fails or health checks fail:

1. **CAPTURE EVIDENCE SNAPSHOT** (< 5 min)
   - See EVIDENCE-SNAPSHOT-GUIDE.md
2. **EXECUTE ROLLBACK** per DEPLOYMENT-GUIDE.md
3. **Document** in DEPLOYMENT-LOG.md
4. **Create** INCIDENT-REPORT.md
5. **Return to 0h**

**On-call Engineer has AUTONOMOUS rollback authority.**

---

## Output Format

Create:

1. **DEPLOYMENT-LOG.md** (from template)
   - Location: `/docs/0i/logs/`
   - Timestamped steps
   - Commands executed
   - Verification results
   - Rollback markers

---

## Deployment Log Format Standard

```markdown
# Deployment Log

## Header
- RC Tag: {{RC_TAG}}
- Commit SHA: {{SHA}}
- Deployer: {{NAME}}
- Start Time: {{TIMESTAMP}}
- End Time: {{TIMESTAMP}}

## Timestamped Steps
| Time | Step | Command | Output | Status |
|------|------|---------|--------|--------|

## Verification Results
- [ ] Version matches
- [ ] Health OK
- [ ] Metrics OK

## Rollback Decision Markers
- Rollback triggered: ☐ Yes / ☐ No
```

---

## On Completion

### If Deployment Succeeds

- [ ] Version verified on production
- [ ] Health checks passing
- [ ] Golden Hour started
- [ ] DEPLOYMENT-LOG.md complete
- [ ] Proceed to Phase 67

### If Deployment Fails

```
CAPTURE EVIDENCE SNAPSHOT (< 5 min)
↓
EXECUTE ROLLBACK IMMEDIATELY
↓
Document in DEPLOYMENT-LOG.md
↓
Create INCIDENT-REPORT.md
↓
Return to 0h for investigation
```

---

## Template Reference

Use: `0. Admin/0i. Rollout/DEPLOYMENT-LOG-TEMPLATE.md`

---
Generated: {{timestamp}}
Phase: 66 - Production Deployment
Section: 0i Rollout
---
