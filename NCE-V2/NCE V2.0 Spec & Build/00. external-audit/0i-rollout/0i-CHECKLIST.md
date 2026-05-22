# 0i Rollout Audit Checklist

---
Section: 0i Rollout (Phases 65-68)
Purpose: Verification checklist for external auditor (post-deploy review)
---

## Phase 65: Rollout Planning

### PRE-DEPLOY-CHECKLIST.md
- [ ] File exists
- [ ] All items checked
- [ ] 0h-to-0i-HANDOFF.md verified
- [ ] RC confirmed

### Communication
- [ ] Stakeholders notified
- [ ] On-call team aware
- [ ] Rollback plan communicated

---

## Phase 66: Deployment

### DEPLOYMENT-LOG.md
- [ ] File exists
- [ ] Timestamp recorded
- [ ] Each step documented
- [ ] Outcome recorded

### Deployment Execution
- [ ] Correct RC deployed
- [ ] Deployment guide followed
- [ ] No manual deviations (or documented)
- [ ] Deployment successful

### If Rollback Occurred
- [ ] Rollback documented
- [ ] Reason recorded
- [ ] Time to rollback recorded
- [ ] New RC created (if needed)

---

## Phase 67: Monitoring

### Golden Hour
- [ ] First hour monitored closely
- [ ] Metrics within targets
- [ ] No critical alerts fired

### Production Health
| Metric | Target | Status |
|--------|--------|--------|
| Error rate | ≤{{%}} | ✅/❌ |
| Response time | ≤{{ms}} | ✅/❌ |
| Availability | ≥{{%}} | ✅/❌ |

### Alerts
- [ ] Alerting working
- [ ] On-call received test alert
- [ ] Dashboards accessible

---

## Phase 68: Post-Launch

### LAUNCH-VERIFICATION.md
- [ ] File exists
- [ ] All verification steps completed
- [ ] Results documented

### Verification Checks
- [ ] Smoke tests passed
- [ ] Critical paths verified
- [ ] User authentication working
- [ ] Data integrity verified

### RELEASE-COMPLETE.md
- [ ] File exists
- [ ] All sign-offs obtained
- [ ] Project closure documented

---

## Incident Handling (if applicable)

### INCIDENT-REPORT.md
- [ ] File exists (for each incident)
- [ ] Timeline documented
- [ ] Impact assessed
- [ ] Resolution documented
- [ ] Root cause identified
- [ ] Lessons learned captured

### Incident Status
- [ ] All incidents resolved
- [ ] No open critical issues
- [ ] Follow-up items tracked

---

## Closure

### 0i-COMPLETION-CRITERIA.md
- [ ] All criteria satisfied
- [ ] Human sign-off recorded

### Handoff to Operations
- [ ] Operations team informed
- [ ] Runbook accessible
- [ ] Support documentation complete

### Documentation
- [ ] Release notes published
- [ ] User documentation updated (if applicable)
- [ ] API documentation updated (if applicable)

---

## Lessons Learned

- [ ] What went well documented
- [ ] What could improve documented
- [ ] Action items for next release identified

---

## Consistency Delta (vs Section 0h)

- [ ] Deployed RC matches 0h RC
- [ ] No changes between 0h approval and deployment
- [ ] All 0h sign-offs still valid

---

## Red Flags (Automatic Critical)

- [ ] Wrong RC deployed
- [ ] Production unhealthy
- [ ] Unresolved critical incident
- [ ] Rollback not documented
- [ ] No deployment log

---

*End of 0i Checklist*
