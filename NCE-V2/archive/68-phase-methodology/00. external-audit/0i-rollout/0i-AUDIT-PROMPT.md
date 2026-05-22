# External Audit Prompt: Section 0i - Rollout

---
Section: 0i Rollout (Phases 65-68)
Audit Type: OPTIONAL (Post-Deploy Review)
Priority: Low
Estimated Time: 20-30 minutes
---

## Your Role

You are an independent auditor reviewing a completed deployment.

**Your job is to verify** that the deployment was executed correctly and identify lessons learned.

**Note:** This is a POST-DEPLOYMENT review. The code is already in production.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0i. This section deploys the RC to production.

**0i produces:**
- Pre-deploy verification
- Deployment execution log
- Launch verification
- Incident reports (if any)
- Release completion sign-off

**Why 0i audit matters:**
- Learn from deployment issues
- Verify production is healthy
- Document for future releases
- Close out the release properly

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package
2. Check deployment outcome
3. Request **up to 5 files**

**Priority Files:**
- DEPLOYMENT-LOG.md
- LAUNCH-VERIFICATION.md
- INCIDENT-REPORT.md (if any)
- RELEASE-COMPLETE.md

### Round 2: Follow-up

Request **up to 3 additional files** if needed.

---

## What to Look For

### Critical Issues (🔴)

- [ ] Deployment failed and wasn't recovered
- [ ] Production is unhealthy
- [ ] Incident unresolved
- [ ] Wrong RC deployed

### Important Issues (🟠)

- [ ] Deployment guide not followed
- [ ] Verification steps skipped
- [ ] Incidents poorly documented
- [ ] Monitoring gaps

### Minor Issues (🟡)

- [ ] Documentation could be better
- [ ] Minor process deviations
- [ ] Lessons learned not captured

---

## Specific Checks for 0i

### Deployment Execution
- Was the correct RC deployed?
- Was the deployment guide followed?
- Was rollback ready?

### Verification
- Were all verification steps completed?
- Is production healthy?
- Are alerts working?

### Incidents
- Were incidents handled correctly?
- Were incidents documented?
- Is root cause identified?

### Closure
- Is RELEASE-COMPLETE.md signed?
- Are all deliverables complete?
- Are lessons learned documented?

---

## 0i CHECKLIST

```
## Pre-Deploy
- [ ] PRE-DEPLOY-CHECKLIST.md complete
- [ ] Correct RC identified
- [ ] Rollback ready

## Deployment
- [ ] DEPLOYMENT-LOG.md exists
- [ ] Deployment guide followed
- [ ] Deployment successful

## Verification
- [ ] LAUNCH-VERIFICATION.md exists
- [ ] All checks passed
- [ ] Production healthy

## Incidents (if any)
- [ ] INCIDENT-REPORT.md exists
- [ ] Incidents resolved
- [ ] Root cause documented

## Closure
- [ ] RELEASE-COMPLETE.md signed
- [ ] 0i-COMPLETION-CRITERIA.md satisfied
- [ ] Handoff to operations complete
```

---

## HANDOFF PACKAGE

[Human: Paste Claude's 0i-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
