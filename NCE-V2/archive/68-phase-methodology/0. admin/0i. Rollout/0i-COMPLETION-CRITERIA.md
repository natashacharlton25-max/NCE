# 0i Completion Criteria

---
Status: TEMPLATE
Version: v1.0.0
Template-Version: 1.0
Section: 0i Rollout
Purpose: Definition of done for production rollout
---

## Overview

0i Rollout is complete when all criteria below are satisfied. This document serves as the final gate before handing off to operations.

---

## Phase Completion Checklist

### Phase 65: Pre-Deploy Verification

| Criterion | Status | Evidence |
|-----------|--------|----------|
| 0h-to-0i-HANDOFF.md verified | ☐ | |
| All 0h sign-offs confirmed | ☐ | |
| RC tag verified | ☐ | |
| Database compatibility verified | ☐ | |
| Config drift check passed | ☐ | |
| Secrets drift check passed | ☐ | |
| Observability snapshot captured | ☐ | PRE-DEPLOY-SNAPSHOT.md |
| Rollback dry run passed | ☐ | |
| Deployment freeze compliance verified | ☐ | |
| Upstream dependencies healthy | ☐ | |
| Monitoring baseline verified | ☐ | |
| PRE-DEPLOY-CHECKLIST.md complete | ☐ | |

**Phase 65 Complete:** ☐ Date: ___

---

### Phase 66: Production Deployment

| Criterion | Status | Evidence |
|-----------|--------|----------|
| Deployment announced | ☐ | |
| Deployment executed | ☐ | |
| Version verified on production | ☐ | |
| Health checks passing | ☐ | |
| Golden Hour started | ☐ | |
| DEPLOYMENT-LOG.md complete | ☐ | |

**Phase 66 Complete:** ☐ Date: ___

---

### Phase 67: Post-Deploy Verification

| Criterion | Status | Evidence |
|-----------|--------|----------|
| Grace period observed | ☐ | |
| Smoke tests passed | ☐ | SMOKE-TEST-RESULTS.md |
| Feature toggles enabled (internal) | ☐ / N/A | |
| Metrics within baseline | ☐ | |
| No new error types | ☐ | |
| Traffic patterns normal | ☐ | |
| LAUNCH-VERIFICATION.md complete | ☐ | |

**Phase 67 Complete:** ☐ Date: ___

---

### Phase 68: Stabilization & Handoff

| Criterion | Status | Evidence |
|-----------|--------|----------|
| All alerts configured | ☐ | PRODUCTION-ALERTS.md |
| All alerts tested | ☐ | |
| Cost monitoring configured | ☐ | |
| Golden Hour completed | ☐ | |
| Post-Golden-Hour review done | ☐ | |
| Stabilization period passed (24-48h) | ☐ | |
| No critical issues during stabilization | ☐ | |
| Stakeholders notified | ☐ | |
| Deployment SLO actuals captured | ☐ | |
| RELEASE-COMPLETE.md created | ☐ | |

**Phase 68 Complete:** ☐ Date: ___

---

## Final Release Criteria

### Deployment Success

| Criterion | Status |
|-----------|--------|
| RC deployed to production | ☐ |
| Version matches expected RC tag | ☐ |
| No rollback required | ☐ / Rollback documented |

### Monitoring Success

| Criterion | Status |
|-----------|--------|
| All P1 alerts configured | ☐ |
| All P2 alerts configured | ☐ |
| Alert routing verified | ☐ |
| Cost alerts configured | ☐ |
| Dashboards accessible | ☐ |

### Stability Success

| Criterion | Status |
|-----------|--------|
| Error rate within baseline | ☐ |
| p95 latency within baseline | ☐ |
| Resource utilization within baseline | ☐ |
| No critical incidents | ☐ |
| 24+ hours stable | ☐ |

### Documentation Success

| Criterion | Status |
|-----------|--------|
| DEPLOYMENT-LOG.md complete | ☐ |
| LAUNCH-VERIFICATION.md complete | ☐ |
| PRODUCTION-ALERTS.md complete | ☐ |
| RELEASE-COMPLETE.md complete | ☐ |
| Runbook accessible from alerts | ☐ |

### Handoff Success

| Criterion | Status |
|-----------|--------|
| Operations team notified | ☐ |
| On-call rotation updated | ☐ |
| Alert ownership assigned | ☐ |
| Feature flag rollout handed off (if applicable) | ☐ / N/A |

---

## Deployment SLO Actuals

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Deployment duration | < {{X}} min | | ☐ Met |
| Smoke test completion | < 30 min | | ☐ Met |
| Time to full traffic | < {{X}} min | | ☐ Met |
| Golden Hour issues | 0 | | ☐ Met |
| Rollback used | No | | ☐ Met |

---

## Sign-Off

### Release Closure

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Deployer | | | ☐ |
| On-call Engineer | | | ☐ |
| **Release Owner** | | | ☐ **RELEASE CLOSED** |

---

## Final Status

| Status | Selected |
|--------|----------|
| **SUCCESS** - Release complete, operations handoff done | ☐ |
| **SUCCESS WITH ISSUES** - Release complete, issues documented | ☐ |
| **ROLLED BACK** - Returned to 0h for investigation | ☐ |

---

## If Rolled Back

| Field | Value |
|-------|-------|
| Rollback timestamp | |
| Rollback reason | |
| Evidence captured | ☐ Yes |
| INCIDENT-REPORT.md created | ☐ Yes |
| Return path | 0h Phase ___ |

---
Generated: {{timestamp}}
Section: 0i Rollout
Template: 0i-COMPLETION-CRITERIA.md v1.0
---
