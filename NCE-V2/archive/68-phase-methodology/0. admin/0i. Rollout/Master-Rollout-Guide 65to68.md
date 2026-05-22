# Master Rollout Guide — Phases 65 to 68

---
Status: COMPLETE
Version: v1.4.0
Last Updated: January 2025
Owner: Release Engineering
Section: 0i Rollout
Phases: 65-68
---

## Overview

**0i Rollout** executes the production deployment and establishes operational monitoring. It bridges the gap between "signed-off Release Candidate" and "live, monitored production system."

**Core Question:** "Is it live and healthy?"

**Philosophy:** 0i deploys exactly what 0h approved and monitors it. 0i executes the plan created in 0h — it does not create new plans.

---

## v1.4 Additions

| Addition | Location | Purpose |
|----------|----------|---------|
| 0i Readiness Review | Phase 65 (Step 0) | Cross-functional GO before 0i |
| Abort 0i Criteria | Phase 65 | When to abort before deployment |
| Human Factors Check | Phase 65 | Fatigue/availability safety |
| Rollback Cost Classification | Phase 65 | Smarter rollback decisions |
| Noisy Signal Classification | Phase 67 | Prevent rollback whiplash |
| Canary Confidence Score | Phase 67 | Quantify canary confidence |
| Authority Matrix | Claude-rollout.md | Explicit decision authority |
| Release Fatigue Guardrail | Claude-rollout.md | Max deploys per day |

---

## 0i Workflow

```
0h Handoff
    ↓
Phase 65: Pre-Deploy Verification
    ↓
Phase 66: Production Deployment
    ↓
Phase 67: Post-Deploy Verification
    ↓
Phase 68: Stabilization & Handoff
    ↓
Operations
```

---

## What 0i Receives (from 0h)

| Artifact | Purpose in 0i |
|----------|---------------|
| 0h-to-0i-HANDOFF.md | Pre-deploy verification |
| Signed-off RC | Exact commit to deploy |
| DEPLOYMENT-GUIDE.md | Deployment execution |
| RUNBOOK.md | Incident handling |
| ALERTING-THRESHOLDS.md | Alert configuration |
| COST-THRESHOLDS.md | Cost monitoring |
| PERFORMANCE-BASELINE.md | Health verification |
| PRERELEASE-CHECKLIST.md | Authority verification |

---

## What 0i Produces

| Artifact | Phase | Purpose |
|----------|-------|---------|
| PRE-DEPLOY-CHECKLIST.md | 65 | Pre-flight verification |
| PRE-DEPLOY-SNAPSHOT.md | 65 | Observability baseline |
| ROLLBACK-READY.md | 65 | Rollback preparation |
| DEPLOYMENT-LOG.md | 66 | Execution record |
| SMOKE-TEST-RESULTS.md | 67 | Production verification |
| LAUNCH-VERIFICATION.md | 67 | Health check results |
| PRODUCTION-ALERTS.md | 68 | Alert configuration |
| MONITORING-DASHBOARD.md | 68 | Dashboard links |
| RELEASE-COMPLETE.md | 68 | Final summary |
| INCIDENT-REPORT.md | Any | If rollback occurs |

---

## Phase Summary

| Phase | Name | Purpose | Collapsible |
|-------|------|---------|-------------|
| 65 | Pre-Deploy Verification | Final checks, rollback ready | Yes |
| 66 | Production Deployment | Execute deployment | **NEVER** |
| 67 | Post-Deploy Verification | Smoke tests, health checks | Yes |
| 68 | Stabilization & Handoff | Monitoring, close release | **NEVER** |

---

## Phase 65: Pre-Deploy Verification

**Purpose:** Final verification that everything is ready for production deployment.

### 0i Readiness Review (v1.4)

Before starting Phase 65, conduct 15-minute cross-functional review with Release Owner, Deployer, On-call, and SRE. Outcome: Signed "GO to begin 0i".

### Abort 0i Criteria (v1.4)

| Condition | Action |
|-----------|--------|
| Upstream dependency degraded | Abort → Wait |
| Monitoring drift unresolved | Abort → Fix |
| Secrets/DB mismatch | Abort → 0h |
| Deployment freeze | Abort → Reschedule |
| Missing sign-offs | Abort → 0h |
| On-call/Release Owner unavailable | Abort → Reschedule |

### Key Steps

1. Verify handoff completeness (0h-to-0i-HANDOFF.md)
2. Verify documentation ready
3. Database compatibility verification
4. Production config drift check
5. Production secrets drift check
6. **Production observability snapshot** (BEFORE deploy)
7. Verify rollback readiness (including dry run)
8. Deployment freeze compliance
9. Notify stakeholders
10. Pre-flight checklist
11. **Upstream status verification** (P1 dependencies)
12. Monitoring baseline drift check

### Locked Items Reminder

> **If any stakeholder requests a change to a locked item during Phase 65, STOP and return to 0h.**

### Success Criteria

All pre-flight checks pass. If any fail, STOP and resolve.

### Outputs

- PRE-DEPLOY-CHECKLIST.md
- PRE-DEPLOY-SNAPSHOT.md
- ROLLBACK-READY.md

---

## Phase 66: Production Deployment

**Purpose:** Execute the deployment of the signed-off RC to production.

### Deployment Strategies

| Strategy | When to Use | Risk Level |
|----------|-------------|------------|
| Rolling | Stateless services | Low |
| Blue-Green | Instant rollback needed | Low |
| Canary | High-risk changes | Medium |
| Shadow | Pre-verify with real traffic | Low |
| Big Bang | Small apps, downtime OK | Higher |

### Canary Exit Criteria

| Criterion | Target | Duration |
|-----------|--------|----------|
| Error rate | ≤ baseline | 30-60 min |
| p95 latency | ≤ baseline | 30-60 min |
| No new error types | Zero | 30-60 min |

### Canary Abort Thresholds (Non-Negotiable)

| Metric | Abort Threshold | Action |
|--------|-----------------|--------|
| Error rate | > 2× baseline | Immediate rollback |
| p95 latency | > 2× baseline | Pause + evaluate |
| New error type | Any | Pause + investigate |
| Cost spike | > 3× baseline (15 min) | Immediate rollback |

### Key Steps

1. Announce deployment start
2. (Optional) Traffic shadowing
3. Execute deployment
4. Verify deployment
5. Start Golden Hour timer
6. Announce deployment complete

### Golden Hour Policy

> Release Owner must remain active for 60 minutes post-deployment. No other deployments to the same stack permitted.

### Outputs

- DEPLOYMENT-LOG.md

---

## Phase 67: Post-Deploy Verification

**Purpose:** Verify production is healthy and functioning as expected.

### Grace Period

5-minute P1 alert suppression for cold-start warming. Metrics still collected.

### Key Steps

1. Smoke tests (< 30 min)
2. Controlled feature toggle (internal users)
3. Metrics verification (compare to Phase 65 snapshot)
4. Error monitoring
5. Traffic verification

### Metrics Comparison

Compare against BOTH:
- 0h baseline (target)
- Phase 65 pre-deploy snapshot (actual state before deployment)

### Outputs

- SMOKE-TEST-RESULTS.md
- HEALTH-CHECK-RESULTS.md
- LAUNCH-VERIFICATION.md

---

## Phase 68: Stabilization & Handoff

**Purpose:** Establish ongoing monitoring, confirm operational readiness, and formally close the release.

### Hotfix Boundary Rule

> Any code change during 0i stabilization requires exiting 0i and following the hotfix path or returning to 0h.

### Key Steps

1. Implement monitoring alerts
2. Implement cost monitoring
3. Golden Hour completion
4. Post-Golden-Hour review
5. Feature flag rollout plan (optional)
6. Stabilization period (24-48 hours)
7. Stakeholder communication
8. Release closure checklist
9. Capture Deployment SLO actuals
10. Create release summary

### Stabilization Period

Minimum 24-48 hours of healthy operation before closing release.

### Outputs

- PRODUCTION-ALERTS.md
- MONITORING-DASHBOARD.md
- RELEASE-COMPLETE.md
- LESSONS-LEARNED.md (optional)

---

## Rollback Procedures

### When to Rollback

| Condition | Action |
|-----------|--------|
| Deployment fails | Rollback immediately |
| Health checks fail | Rollback immediately |
| Error rate > 1% for 5+ min | Evaluate rollback |
| Abort threshold exceeded | Rollback immediately |
| Security vulnerability | Rollback immediately |

### Rollback Execution

1. Announce rollback
2. **CAPTURE EVIDENCE SNAPSHOT** (< 5 min)
3. Execute rollback per DEPLOYMENT-GUIDE.md
4. Verify rollback
5. Document in INCIDENT-REPORT.md
6. Return to 0h for fix and new RC

### Evidence Preservation

Before rollback, capture:
- Application logs (last 30 min)
- Heap dump (if applicable)
- Metrics snapshot
- Error samples

---

## 0i Authority Table

| Decision | Authority |
|----------|-----------|
| Start 0i (Readiness Review) | Release Owner |
| Pause canary | Deployer or On-call |
| Resume canary | Deployer + On-call consensus |
| Rollback | On-call Engineer (autonomous) |
| Abort rollout entirely | Release Owner |
| Extend Golden Hour | Release Owner |
| Exit 0i for hotfix | Release Owner |
| Close release | Release Owner |

**Rule:** On-call has autonomous rollback authority. Do NOT wait for approval.

---

## 0i Safety Pins

### Safety Pin #1: Rollback Authority
On-call Engineer has autonomous rollback authority. Do NOT wait for approval.

### Safety Pin #2: Abort Thresholds
> 2× baseline = immediate rollback. Non-negotiable.

### Safety Pin #3: Hotfix Boundary
No code changes during 0i stabilization. Exit to hotfix path or return to 0h.

### Safety Pin #4: Deployment Freeze
Organizational freeze rules enforced. Block deployment if in freeze window.

---

## Deployment SLO

| Metric | Target |
|--------|--------|
| Deployment duration | < X minutes |
| Smoke test completion | < 30 minutes |
| Golden Hour issues | 0 critical |
| Rollback frequency | < 10% of deploys |

---

## Artifact Storage

| Type | Location |
|------|----------|
| Documentation | `/docs/0i/` |
| Deployment logs | `/docs/0i/logs/` |
| Incident reports | `/docs/0i/incidents/` |
| Evidence snapshots | `s3://{{BUCKET}}/incidents/` |

---

## 0i Glossary

| Term | Definition |
|------|------------|
| Golden Hour | 60-min post-deploy window, Release Owner available, stack frozen |
| Grace Period | 5-min P1 alert suppression for cold-start warming |
| Canary Exit Criteria | Thresholds met before promoting canary |
| Canary Abort Threshold | Thresholds triggering immediate rollback |
| Observability Snapshot | Pre-deploy capture of production metrics |
| Traffic Shadowing | Mirror traffic without customer impact |
| Evidence Snapshot | Logs/dumps captured before rollback |
| Hotfix Boundary | No code changes during stabilization |

---

## File Inventory

### Admin Files (4)

| File | Purpose |
|------|---------|
| Master-Rollout-Guide 65to68.md | This document |
| Claude-rollout.md | AI instructions with Safety Pins |
| 0i-COMPLETION-CRITERIA.md | Definition of done |
| HOTFIX-PATH.md | Emergency fix process (placeholder) |

### Templates (7)

| File | Phase | Template-Version |
|------|-------|------------------|
| PRE-DEPLOY-CHECKLIST-TEMPLATE.md | 65 | 1.0 |
| DEPLOYMENT-LOG-TEMPLATE.md | 66 | 1.0 |
| LAUNCH-VERIFICATION-TEMPLATE.md | 67 | 1.0 |
| PRODUCTION-ALERTS-TEMPLATE.md | 68 | 1.0 |
| RELEASE-COMPLETE-TEMPLATE.md | 68 | 1.0 |
| INCIDENT-REPORT-TEMPLATE.md | Any | 1.0 |
| EVIDENCE-SNAPSHOT-GUIDE.md | Rollback | 1.0 |

### Phase Prompts (4)

| File | Location |
|------|----------|
| PHASE-65-PROMPT.md | `65. Pre-Deploy-Verification/` |
| PHASE-66-PROMPT.md | `66. Production-Deployment/` |
| PHASE-67-PROMPT.md | `67. Post-Deploy-Verification/` |
| PHASE-68-PROMPT.md | `68. Stabilization-Handoff/` |

---

## 0i-to-Operations Handoff

### What 0i Delivers

| Artifact | Contains |
|----------|----------|
| RELEASE-COMPLETE.md | Final release summary |
| PRODUCTION-ALERTS.md | Configured alert inventory |
| RUNBOOK.md | Operational procedures (from 0h) |

### What 0i Guarantees

| Guarantee | Evidence |
|-----------|----------|
| RC deployed and healthy | RELEASE-COMPLETE.md |
| Alerts configured and tested | PRODUCTION-ALERTS.md |
| Stabilization period passed | 24-48h healthy metrics |
| Runbook accessible | Linked from alerts |

---

## Quick Reference

| Rule | Summary |
|------|---------|
| Non-collapsible | Phase 66 (Deployment), Phase 68 (Handoff) |
| 0i Readiness Review | 15-min cross-functional GO (v1.4) |
| Abort 0i Criteria | 8 conditions that block deployment (v1.4) |
| Golden Hour | 60 min, Release Owner available |
| Grace Period | 5 min P1 suppression |
| Abort Threshold | > 2× baseline = rollback |
| Cost Spike | > 3× baseline in 15 min = rollback |
| Rollback Authority | On-call autonomous |
| Evidence | Capture BEFORE rollback |
| Hotfix Boundary | No changes during stabilization |
| Stabilization | 24-48 hours minimum |
| Noisy Signals | Known patterns, observe before rollback (v1.4) |
| Canary Confidence | Score 4/4 = safe to promote (v1.4) |
| Release Fatigue | Max 2 deploys/day without Director approval (v1.4) |

---
Generated: January 2025
Section: 0i Rollout
Template: Master-Rollout-Guide 65to68.md v1.3
---
