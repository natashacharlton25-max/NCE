# Section 0i: Rollout

## Human Guide

---
**What this section does:** Deploys to production and monitors the launch
**Phases:** 65-68
**Time:** Hours to a day
**Audit:** Optional (post-launch review)
---

## What Happens in This Section?

Everything is ready. Now you go live:

1. **Pre-deploy checks** - Final verification
2. **Deploy** - Push the code to production
3. **Monitor** - Watch for problems
4. **Post-launch** - Verify everything works

**Think of it like:** Opening day.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Approve deployment | Give the final GO |
| Watch the launch | Be available during deployment |
| Monitor | Watch for alerts and issues |
| Verify | Confirm everything is working |
| Celebrate | You launched! 🎉 |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 65 | Rollout Planning | Final pre-deploy checks |
| 66 | Deployment | Actually deploy to production |
| 67 | Monitoring | Watch the first hour closely |
| 68 | Post-Launch | Verify and close out |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| PRE-DEPLOY-CHECKLIST.md | Final checks before deploy |
| DEPLOYMENT-LOG.md | Record of what happened |
| LAUNCH-VERIFICATION.md | Verification that it's working |
| INCIDENT-REPORT.md | If something goes wrong |
| RELEASE-COMPLETE.md | Final sign-off |

---

## The Deployment Process

### Before You Deploy

1. **Verify you're ready**
   - All sign-offs from 0h obtained?
   - External audit passed?
   - Rollback procedure tested?
   - On-call team aware?

2. **Communicate**
   - Let stakeholders know
   - Let the support team know
   - Schedule a time when you can watch it

### During Deployment

3. **Execute the deployment**
   - Follow DEPLOYMENT-GUIDE.md step by step
   - Document each step in DEPLOYMENT-LOG.md
   - Don't skip steps, even if they seem unnecessary

4. **Monitor closely**
   - Watch error rates
   - Watch response times
   - Watch alerts

### After Deployment

5. **Verify**
   - Run smoke tests
   - Check critical user paths
   - Verify data looks correct

6. **Celebrate (or rollback)**
   - If it works: 🎉
   - If it doesn't: Follow rollback procedure

---

## The Golden Hour

The first hour after deployment is critical. Watch for:

| Metric | Normal | Concerning | Act |
|--------|--------|------------|-----|
| Error rate | <0.1% | >0.5% | Investigate |
| Response time | <500ms | >1000ms | Investigate |
| Alerts | None | Any critical | Respond |
| User complaints | None | Any | Investigate |

**Be available.** Don't deploy and then go to lunch.

---

## If Something Goes Wrong

### Minor Issues
- Document in DEPLOYMENT-LOG.md
- Fix if quick
- Continue monitoring

### Major Issues
1. **Consider rollback** - When in doubt, roll back
2. **Follow the RUNBOOK** - It has procedures for common issues
3. **Document everything** - What happened, when, what you did
4. **Create INCIDENT-REPORT.md** - Full post-mortem

### Rollback
If you need to rollback:
1. Don't panic
2. Follow the rollback procedure in DEPLOYMENT-GUIDE.md
3. Verify the rollback worked
4. Document what happened
5. Figure out what went wrong
6. Plan for a new deployment attempt

---

## Common Questions

### "When should I rollback?"
When users are significantly affected and you can't fix it quickly. If in doubt, rollback.

### "How long should I monitor?"
At minimum: 1 hour actively, 24 hours watching.
Ideally: A week of monitoring metrics.

### "What if it's partially working?"
Document which parts work and which don't. Decide if it's acceptable to stay live or if you should rollback.

### "Can I deploy on Friday?"
Generally not recommended. If something goes wrong, you're working the weekend. Monday or Tuesday deployments are safer.

---

## Troubleshooting

### "Deployment failed"
Check the error message. Consult DEPLOYMENT-GUIDE.md troubleshooting section. Don't retry until you understand why.

### "It's slow after deployment"
Check resource usage (CPU, memory, database). You might need to scale up.

### "Users are seeing errors"
Check logs. Identify the pattern. Either fix forward (if quick) or rollback.

### "The monitoring isn't showing anything"
Monitoring might be broken! Manually check the application is working.

---

## Post-Launch (Phase 68)

After things stabilize:

1. **Create LAUNCH-VERIFICATION.md**
   - Document that everything is working
   - Record key metrics

2. **Create RELEASE-COMPLETE.md**
   - Final sign-off
   - Mark the project as launched

3. **Lessons learned**
   - What went well?
   - What could improve?
   - Any action items for next time?

---

## When You're Done

You'll have:
- ✅ Application deployed and running
- ✅ Monitoring active
- ✅ Any incidents documented
- ✅ LAUNCH-VERIFICATION.md complete
- ✅ RELEASE-COMPLETE.md signed

**Congratulations! You've completed the Fullspecs process!** 🎉

---

## Audit (Optional)

The 0i audit is a **post-launch review**. Consider running it if:
- There were incidents during deployment
- You want to document lessons learned
- You want a second opinion on production health

**Audit files:** `00. external-audit/0i-rollout/`

**What the audit reviews:**
- Was deployment executed correctly?
- Were incidents handled well?
- Is production healthy?
- What lessons were learned?

---

*You made it! From concept to production. Well done.*
