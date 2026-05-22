# Section 0h: Prerelease

## Human Guide

---
**What this section does:** Final preparations before going live
**Phases:** 61-64
**Time:** A day or two
**Audit:** ⭐ MANDATORY
---

## What Happens in This Section?

Your code is written and tested. Now you prepare for launch:

1. **Set up CI/CD** - Automated building and deploying
2. **Create Release Candidate** - Tag the final version
3. **Production hardening** - Final security and performance checks
4. **Get sign-offs** - Everyone agrees it's ready

**Think of it like:** Final inspection before opening the building to the public.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Review CI/CD setup | Does the automation work? |
| Approve the Release Candidate | Is this the version to launch? |
| Review performance | Does it meet speed requirements? |
| Review security | Are there vulnerabilities? |
| Sign off | Give the final GO |
| Run the audit | **Required** before deployment |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 61 | CI/CD Pipeline | Set up automated build/deploy |
| 62 | Release Candidate | Create the final version |
| 63 | Production Hardening | Performance and security checks |
| 64 | Release Approval | Get sign-offs, make GO decision |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| CI-PIPELINE.md | How automated deployment works |
| ENVIRONMENT-CONFIG.md | Server configurations |
| PERFORMANCE-IMPACT-ANALYSIS.md | Speed and load testing results |
| Security audit results | Vulnerability scan results |
| ALERTING-THRESHOLDS.md | When to alert on problems |
| COST-THRESHOLDS.md | Budget limits |
| DEPLOYMENT-GUIDE.md | How to deploy step by step |
| RUNBOOK.md | What to do when things go wrong |
| PRERELEASE-CHECKLIST.md | Final verification |

---

## Understanding Release Candidate (RC)

The "Release Candidate" is the exact version you're going to deploy.

**Key rule: Once tagged, it cannot change.**

```
RC Tag: v1.0.0-rc.1
Commit: abc123...
Created: 2024-01-15 10:30:00
Status: LOCKED 🔒
```

If you need to make changes, you create a NEW RC (v1.0.0-rc.2), not modify the existing one.

---

## Understanding Locks

Once certain things are set, they're "locked" and shouldn't change:

| Item | Locked After | Why |
|------|--------------|-----|
| RC Commit | Phase 61 | Can't change what you're deploying |
| CI/CD Pipeline | Phase 61 | Can't change how it's deployed |
| Performance Baselines | Phase 63 | Can't lower the targets |
| Security Requirements | Phase 63 | Can't relax security |
| Alerting Thresholds | Phase 63 | Can't make alerts less sensitive |

**If you need to change a locked item:** Create a new RC and restart 0h.

---

## Understanding Performance Targets

| Metric | What It Means | Typical Target |
|--------|---------------|----------------|
| p50 Response | Middle speed (50% faster, 50% slower) | <200ms |
| p95 Response | 95% of requests are this fast | <500ms |
| p99 Response | 99% of requests are this fast | <1000ms |
| Error Rate | How often things fail | <0.1% |
| Throughput | Requests per second | Depends on needs |

---

## Common Questions

### "What is CI/CD?"
- **CI** (Continuous Integration): Automatically builds and tests code
- **CD** (Continuous Deployment): Automatically deploys to servers

It means you don't manually upload files - it happens automatically when code is approved.

### "What if performance doesn't meet targets?"
Options:
1. Optimize the code (go back to 0f)
2. Lower the targets (must be approved and justified)
3. Accept the risk (document why)

### "What if there are security vulnerabilities?"
- **Critical vulnerabilities**: Must be fixed. No launch.
- **High vulnerabilities**: Should be fixed or explicitly accepted with documentation.
- **Medium/Low**: Document and plan to fix later.

### "What's a runbook?"
A guide for handling problems in production. When something goes wrong at 3 AM, the on-call person follows the runbook.

---

## Troubleshooting

### "CI/CD pipeline is failing"
Ask Claude to investigate. Say: "The pipeline is failing at [stage]. Can you diagnose?"

### "Performance is below targets"
Ask: "What's causing slow performance? How can we optimize?"

### "There are security vulnerabilities"
Review each one. Ask: "Explain vulnerability [X]. How critical is it? How do we fix it?"

### "Someone won't sign off"
Find out why. Address their concerns. Document if they sign off with reservations.

---

## The Sign-Off Process

Before launch, you need sign-offs:

| Role | Required? | What They're Confirming |
|------|-----------|------------------------|
| Developer | Yes | Code is ready |
| QA/Tester | Yes | Testing is adequate |
| Security | Yes | Security is acceptable |
| Product Owner | Often | Features are correct |
| Operations | Often | Ready to support it |

Each person signs and dates. Document any concerns.

---

## When You're Done

You'll have:
- ✅ CI/CD pipeline working
- ✅ Release Candidate tagged (and locked)
- ✅ Performance meets targets
- ✅ Security audit passed
- ✅ DEPLOYMENT-GUIDE.md complete
- ✅ RUNBOOK.md complete
- ✅ All sign-offs obtained
- ✅ GO decision made
- ✅ **External audit passed** ⭐

**Next:** Move to Section 0i (Rollout)

---

## Audit (MANDATORY) ⭐

**You must run the 0h audit before deploying.**

This is the FINAL safety check before real users are affected.

**Audit files:** `00. external-audit/0h-prerelease/`

**What the audit checks:**
- Is the RC immutable (hasn't changed)?
- Are locked items still locked?
- Does performance meet targets?
- Are security requirements satisfied?
- Is the deployment guide complete?
- Is the runbook usable?
- Are all sign-offs obtained?

---

*This is the last gate. After this, real users are affected.*
