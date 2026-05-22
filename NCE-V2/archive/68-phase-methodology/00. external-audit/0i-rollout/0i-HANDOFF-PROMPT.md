# 0i Audit Handoff Prompt

---
Section: 0i Rollout (Phases 65-68)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing deployment, for post-deploy review (OPTIONAL)
---

## Instructions for Claude

You have completed Section 0i (Rollout). Prepare a handoff package for post-deployment audit review.

**Note:** This audit is OPTIONAL and happens AFTER deployment. It's a retrospective review.

---

## Create: 0i-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- Deployment outcome
- Any incidents during rollout
- Current production status

### 2. Self-Assessment Scores

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | All deployment steps done? |
| Consistency | /5 | Followed deployment guide? |
| Accuracy | /5 | Deployment successful? |
| Methodology | /5 | Rollout rules followed? |
| Quality | /5 | Documentation complete? |
| Readiness | /5 | Production stable? |

### 3. Deployment Summary

| Field | Value |
|-------|-------|
| RC Deployed | {{tag}} |
| Deployment Time | {{timestamp}} |
| Deployment Duration | {{minutes}} |
| Rollback Required | Yes/No |
| Incidents | {{count}} |

### 4. Concerns & Lessons Learned

- What went well
- What could improve
- Surprises encountered

### 5. Phase Completion

| Phase | Status | Notes |
|-------|--------|-------|
| 65: Rollout Planning | ✅/⚠️/❌ | |
| 66: Deployment | ✅/⚠️/❌ | |
| 67: Monitoring | ✅/⚠️/❌ | |
| 68: Post-Launch | ✅/⚠️/❌ | |

### 6. Incident Summary (if any)

| Incident | Severity | Resolution | Time to Resolve |
|----------|----------|------------|-----------------|
| | | | |

Write "None" if no incidents.

### 7. Production Health

| Metric | Target | Current | Status |
|--------|--------|---------|--------|
| Error Rate | ≤{{%}} | {{%}} | ✅/⚠️/❌ |
| Response Time (p95) | ≤{{ms}} | {{ms}} | ✅/⚠️/❌ |
| Uptime | ≥{{%}} | {{%}} | ✅/⚠️/❌ |

### 8. File Manifest

| File | Location | Status |
|------|----------|--------|
| PRE-DEPLOY-CHECKLIST.md | | |
| DEPLOYMENT-LOG.md | | |
| LAUNCH-VERIFICATION.md | | |
| INCIDENT-REPORT.md (if any) | | |
| RELEASE-COMPLETE.md | | |
| 0i-COMPLETION-CRITERIA.md | | |

### 9. Recommended Files for Review

1. **DEPLOYMENT-LOG.md** - What happened
2. **LAUNCH-VERIFICATION.md** - Verification results
3. **INCIDENT-REPORT.md** - Incidents (if any)
4. **RELEASE-COMPLETE.md** - Final status
5. **Production metrics** - Current health

---

## Pre-Handoff Verification

- [ ] Deployment complete
- [ ] Production verified healthy
- [ ] Monitoring active
- [ ] All incidents resolved (or tracked)
- [ ] RELEASE-COMPLETE.md signed

---

## Output Location

Save as: `0i-AUDIT-HANDOFF.md` in `00. external-audit/0i-rollout/`

---

*End of Handoff Prompt*
