# Section 0i: Complete Document Reference

---
**Section:** 0i Rollout
**Phases:** 65-68
**Location:** `0. Admin/0i. Rollout/` + Phase folders
---

## Admin Folder Documents

Location: `0. Admin/0i. Rollout/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-rollout.md** | Instructions for Claude. Defines deployment approach, monitoring rules, incident handling. | Claude reads this |
| **Master-Rollout-Guide 65to68.md** | Detailed guide for rollout phases. | Reference for both |
| **ROLLOUT-PROGRESS-TEMPLATE.md** | Template to track deployment progress. | You fill this in |
| **ROLLOUT-DECISION-NOTES-TEMPLATE.md** | Template to log decisions and incidents. | You fill this in |

---

## Phase-by-Phase Documents

### Phase 65: Rollout Planning

**Folder:** `65. Rollout-Planning/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-65-PROMPT.md** | Instructions for Claude. Tells Claude to finalize deployment plan and verify readiness. | Give to Claude |
| **PRE-DEPLOY-CHECKLIST-TEMPLATE.md** | Template for final pre-deployment checks. | Claude creates PRE-DEPLOY-CHECKLIST.md |
| **ROLLBACK-PLAN-TEMPLATE.md** | Template for rollback procedures. | Claude creates ROLLBACK-PLAN.md |

**Outputs:**
- `PRE-DEPLOY-CHECKLIST.md` — Final checks
- `ROLLBACK-PLAN.md` — How to undo if needed

---

### Phase 66: Deployment

**Folder:** `66. Deployment/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-66-PROMPT.md** | Instructions for Claude. Tells Claude to execute the deployment. | Give to Claude |
| **DEPLOYMENT-LOG-TEMPLATE.md** | Template to record deployment steps as they happen. | You/Claude fill DEPLOYMENT-LOG.md |

**Output:** `DEPLOYMENT-LOG.md` — Record of what happened during deployment

**This is the actual deployment.** Follow DEPLOYMENT-GUIDE.md from 0h step by step.

---

### Phase 67: Monitoring

**Folder:** `67. Monitoring/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-67-PROMPT.md** | Instructions for Claude. Tells Claude to monitor the first hour closely. | Give to Claude |
| **MONITORING-LOG-TEMPLATE.md** | Template to record metrics and observations. | You/Claude fill MONITORING-LOG.md |
| **INCIDENT-REPORT-TEMPLATE.md** | Template for documenting any incidents. | Claude creates if needed |

**Outputs:**
- `MONITORING-LOG.md` — First hour observations
- `INCIDENT-REPORT.md` — If something goes wrong

---

### Phase 68: Post-Launch

**Folder:** `68. Post-Launch/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-68-PROMPT.md** | Instructions for Claude. Tells Claude to verify everything is working and close out. | Give to Claude |
| **LAUNCH-VERIFICATION-TEMPLATE.md** | Template for verifying production is healthy. | Claude creates LAUNCH-VERIFICATION.md |
| **RELEASE-COMPLETE-TEMPLATE.md** | Template for final sign-off. | Claude creates RELEASE-COMPLETE.md |
| **LESSONS-LEARNED-TEMPLATE.md** | Template for retrospective notes. | Claude creates LESSONS-LEARNED.md |

**Outputs:**
- `LAUNCH-VERIFICATION.md` — Verification that it's working
- `RELEASE-COMPLETE.md` — Final sign-off
- `LESSONS-LEARNED.md` — What to improve next time

---

## Order to Run Section 0i

```
Step 1:  Read Claude-rollout.md (understand section)
Step 2:  Phase 65 - Complete PRE-DEPLOY-CHECKLIST.md and ROLLBACK-PLAN.md
Step 3:  Phase 66 - Execute deployment, record in DEPLOYMENT-LOG.md
         ⚡ Be available during this step
Step 4:  Phase 67 - Monitor first hour, record in MONITORING-LOG.md
         🚨 If issues → Follow ROLLBACK-PLAN.md or RUNBOOK.md
Step 5:  Phase 68 - Verify, complete LAUNCH-VERIFICATION.md
Step 6:  Phase 68 - Complete RELEASE-COMPLETE.md (final sign-off)
Step 7:  Phase 68 - Complete LESSONS-LEARNED.md
Step 8:  (Optional) Run 0i audit for retrospective
Step 9:  🎉 You're live!
```

---

## The Golden Hour

The first hour after deployment is critical. Watch for:

| Metric | Normal | Concerning | Action |
|--------|--------|------------|--------|
| Error rate | <0.1% | >0.5% | Investigate |
| Response time | <500ms | >1000ms | Investigate |
| Alerts | None | Any critical | Respond |
| User complaints | None | Any | Investigate |

**Be available.** Don't deploy and disappear.

---

## If Something Goes Wrong

### Minor Issue
1. Document in MONITORING-LOG.md
2. Fix if quick
3. Continue monitoring

### Major Issue
1. Consider rollback (when in doubt, roll back)
2. Follow RUNBOOK.md
3. Document everything
4. Create INCIDENT-REPORT.md

### Rollback
1. Follow ROLLBACK-PLAN.md step by step
2. Verify rollback worked
3. Document what happened
4. Plan for new deployment attempt

---

## What You Should Have When Done

| Document | Location |
|----------|----------|
| PRE-DEPLOY-CHECKLIST.md | Phase 65 |
| ROLLBACK-PLAN.md | Phase 65 |
| DEPLOYMENT-LOG.md | Phase 66 |
| MONITORING-LOG.md | Phase 67 |
| INCIDENT-REPORT.md | Phase 67 (if needed) |
| LAUNCH-VERIFICATION.md | Phase 68 |
| RELEASE-COMPLETE.md | Phase 68 |
| LESSONS-LEARNED.md | Phase 68 |

---

## Deployment Day Tips

| Do | Don't |
|----|-------|
| Deploy early in the week | Deploy on Friday |
| Have RUNBOOK ready | Hope nothing goes wrong |
| Be available | Disappear after deploy |
| Monitor actively | Assume it's fine |
| Roll back if unsure | Try to fix under pressure |
| Document everything | Trust your memory |

---

🎉 **Congratulations!** Once RELEASE-COMPLETE.md is signed, you've completed the full Fullspecs journey from concept to production!

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
