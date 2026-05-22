# Hotfix Path

---
Status: OPERATIONAL
Version: v2.0.0
Template-Version: 2.0
Section: 0i Rollout
Purpose: Emergency fix process for critical production issues
---

## Overview

This document defines the abbreviated process for emergency fixes that cannot follow the full 0h → 0i pipeline. Hotfixes bypass normal release gates but maintain critical safety checks.

**Key Principle:** Hotfixes are exceptions, not the norm. Every hotfix creates technical debt that must be reconciled.

---

## When to Use Hotfix Path

### Qualifies for Hotfix

| Condition | Severity | Hotfix Appropriate |
|-----------|----------|-------------------|
| Production down (service unavailable) | P0 | ✅ Yes |
| Security vulnerability (active exploit) | P0 | ✅ Yes |
| Data corruption in progress | P0 | ✅ Yes |
| P1 bug affecting >50% users | P1 | ✅ Yes |
| P1 bug affecting <50% users | P1 | ⚠️ Evaluate (consider rollback first) |

### Does NOT Qualify for Hotfix

| Condition | Action |
|-----------|--------|
| P2 bug | Schedule for next release |
| P3/P4 bug | Backlog |
| Feature request | Never hotfix |
| Performance optimization | Never hotfix |
| "Nice to have" fix | Never hotfix |

**Decision Rule:** If you can wait 24 hours without significant business/user impact, it's not a hotfix.

---

## Hotfix Authority Matrix

| Decision | Authority | Backup |
|----------|-----------|--------|
| Declare hotfix needed | On-call + Release Owner | On-call alone if P0 |
| Approve hotfix path | Release Owner | Director |
| Approve hotfix code | Senior Engineer | Tech Lead |
| Deploy hotfix | Deployer + On-call | On-call alone if P0 |
| Close hotfix | Release Owner | Director |

---

## Hotfix Phases

### Phase H0: Detection & Triage (Target: 15 min)

**Trigger:** Critical issue detected in production.

| Step | Owner | Verification |
|------|-------|--------------|
| 1. Confirm issue is real | On-call | Metrics/logs confirm |
| 2. Assess severity | On-call | P0 or P1 confirmed |
| 3. Evaluate rollback option | On-call + Deployer | Can rollback solve this? |
| 4. Decide: Rollback vs Hotfix | Release Owner | Document decision |

**Decision Tree:**

```
Issue detected
     │
     ▼
Is rollback an option?
     │
   Yes ──────────────────────► ROLLBACK (preferred)
     │                              │
    No                              │
     │                              │
     ▼                              │
Is it P0 or P1 (>50% users)?        │
     │                              │
    Yes                             │
     │                              │
     ▼                              │
HOTFIX PATH ◄───────────────────────┘
     │                        (if rollback fails)
     ▼
Document justification
```

**Output:** HOTFIX-DECISION.md

```markdown
# Hotfix Decision

Date: {{timestamp}}
Issue: {{ISSUE_ID}}
Severity: {{P0 | P1}}

## Issue Summary
{{Brief description}}

## Why Rollback Not Viable
{{Reason rollback won't work}}

## Hotfix Justification
{{Why this needs immediate fix}}

## Approved By
- On-call: {{NAME}} at {{TIME}}
- Release Owner: {{NAME}} at {{TIME}}
```

---

### Phase H1: Isolate & Fix (Target: 30-60 min)

**Goal:** Create minimal fix, nothing else.

| Step | Owner | Verification |
|------|-------|--------------|
| 1. Create hotfix branch | Developer | `hotfix/ISSUE-ID` from production tag |
| 2. Identify root cause | Developer | Document in INCIDENT-REPORT |
| 3. Write minimal fix | Developer | Only change what's broken |
| 4. Write/update tests | Developer | Must cover the fix |
| 5. Run critical tests | Developer | Unit + affected integration |
| 6. Expedited code review | Senior Engineer | Focus on safety, not style |

**Hotfix Branch Rules:**

```bash
# Branch from the production tag, NOT main
git checkout -b hotfix/ISSUE-123 v1.0.0-rc.2

# Fix must be minimal
# NO refactoring
# NO "while we're here" changes
# NO unrelated fixes
```

**Code Review Focus (Expedited):**

- [ ] Does the fix address the root cause?
- [ ] Is the fix minimal (no extra changes)?
- [ ] Are there any new security risks?
- [ ] Do critical tests pass?
- [ ] Is there a test for the specific bug?

**Output:** Approved hotfix branch with passing tests

---

### Phase H2: Pre-Deploy Verification (Target: 15 min)

**Goal:** Ensure hotfix is safe to deploy.

| Check | Status | Blocker |
|-------|--------|---------|
| Critical tests pass | ☐ | Yes |
| Code review approved | ☐ | Yes |
| Rollback plan documented | ☐ | Yes |
| On-call available | ☐ | Yes |
| Monitoring ready | ☐ | Yes |

**Rollback Plan:**

```markdown
## Hotfix Rollback Plan

If hotfix fails:
1. Revert to: {{PREVIOUS_TAG}}
2. Command: {{rollback command}}
3. Verification: {{how to verify rollback worked}}
4. Escalation: {{who to call if rollback fails}}
```

---

### Phase H3: Deploy (Target: 15 min)

**Goal:** Deploy hotfix with maximum visibility.

| Step | Owner | Verification |
|------|-------|--------------|
| 1. Announce deployment | Deployer | Slack/comms channel |
| 2. Deploy to production | Deployer | Deploy succeeds |
| 3. Verify fix works | On-call | Issue resolved |
| 4. Monitor for 30 min | On-call | No new issues |

**Deploy Checklist:**

- [ ] Deployment announced
- [ ] Hotfix deployed
- [ ] Fix verified (issue resolved)
- [ ] Error rates stable
- [ ] No new alerts
- [ ] 30-minute monitoring complete

**If Hotfix Fails:**

1. Execute rollback immediately
2. Document failure in INCIDENT-REPORT
3. Reassess: Can we fix the fix? Or need different approach?
4. If second attempt: Requires Release Owner re-approval

---

### Phase H4: Sync & Close (Target: Same day)

**Goal:** Reconcile hotfix with main branch and close out.

| Step | Owner | Verification |
|------|-------|--------------|
| 1. Merge hotfix to main | Developer | PR merged |
| 2. Update version/changelog | Developer | Documented |
| 3. Schedule sync release | Release Owner | Release planned |
| 4. Complete incident report | On-call | INCIDENT-REPORT.md |
| 5. Schedule retrospective | Release Owner | Meeting booked |

**Critical:** The hotfix must be merged to main within 24 hours to prevent drift.

**Sync Release Requirement:**

- A full 0h → 0i release must follow within 1 week
- This release syncs the RC with the hotfix
- Hotfix changes get full test coverage in this release

---

## Hotfix Checklist (Complete)

### H0: Triage
- [ ] Issue severity confirmed (P0/P1)
- [ ] Rollback evaluated (not viable or failed)
- [ ] Release Owner approved hotfix path
- [ ] HOTFIX-DECISION.md created

### H1: Fix
- [ ] Hotfix branch created from production tag
- [ ] Root cause identified
- [ ] Minimal fix implemented
- [ ] Tests added/updated
- [ ] Critical tests pass
- [ ] Code review approved

### H2: Pre-Deploy
- [ ] Rollback plan documented
- [ ] On-call confirmed available
- [ ] Monitoring confirmed ready

### H3: Deploy
- [ ] Deployment announced
- [ ] Hotfix deployed
- [ ] Fix verified (issue resolved)
- [ ] 30-minute monitoring complete
- [ ] No new issues

### H4: Sync & Close
- [ ] Hotfix merged to main
- [ ] Changelog updated
- [ ] Sync release scheduled (within 1 week)
- [ ] INCIDENT-REPORT.md complete
- [ ] Retrospective scheduled

---

## What Hotfix Path Does NOT Skip

Even in emergency mode, these are **mandatory**:

| Requirement | Why |
|-------------|-----|
| Rollback plan | Must have escape hatch |
| Critical tests (unit + smoke) | Prevent making it worse |
| Code review (expedited) | Second pair of eyes on safety |
| Post-deploy monitoring (30 min) | Confirm fix worked |
| Documentation | Audit trail + learning |
| Merge to main | Prevent branch drift |
| Sync release | Reconcile RC |

---

## What Hotfix Path DOES Skip

| Normal Requirement | Skipped Because |
|--------------------|-----------------|
| Full test suite | Time-critical (critical tests only) |
| Performance testing | Time-critical |
| Full 0h prerelease phases | Time-critical |
| Feature flag rollout | Direct deploy |
| Extended stabilization | 30 min instead of 24h |

---

## Hotfix Metrics

Track these to ensure hotfixes remain exceptions:

| Metric | Target | Alert If |
|--------|--------|----------|
| Hotfixes per quarter | < 2 | > 4 |
| Hotfix success rate | 100% | Any failure |
| Time to fix (H0-H3) | < 2 hours | > 4 hours |
| Sync release within 1 week | 100% | Any miss |
| Retrospective completion | 100% | Any skip |

---

## Post-Hotfix Retrospective

**Required within 5 business days.**

### Questions to Answer

1. **Detection:** How did we find the issue? Could we have found it earlier?
2. **Root Cause:** What was the actual root cause? Why did it slip through?
3. **Fix:** Was the fix correct? Any side effects?
4. **Process:** What slowed us down? What worked well?
5. **Prevention:** How do we prevent this class of issue?

### Retrospective Output

Create `HOTFIX-RETRO-{{ISSUE_ID}}.md`:

```markdown
# Hotfix Retrospective: {{ISSUE_ID}}

## Timeline
- Detected: {{time}}
- Triage complete: {{time}}
- Fix ready: {{time}}
- Deployed: {{time}}
- Confirmed fixed: {{time}}
- Total duration: {{time}}

## What Happened
{{Description of the issue}}

## Root Cause
{{Technical root cause}}

## Why It Wasn't Caught
{{Gap in testing/process}}

## The Fix
{{What was changed}}

## Prevention Actions
| Action | Owner | Due Date |
|--------|-------|----------|
| {{action}} | {{name}} | {{date}} |

## Lessons Learned
{{Key takeaways}}
```

---

## Communication Templates

### Hotfix Start Announcement

```
🚨 HOTFIX IN PROGRESS

Issue: {{ISSUE_ID}} - {{Brief description}}
Severity: {{P0/P1}}
Status: Hotfix approved, deploying shortly

On-call: {{NAME}}
ETA: {{estimated time}}

Updates will follow.
```

### Hotfix Complete Announcement

```
✅ HOTFIX COMPLETE

Issue: {{ISSUE_ID}} - {{Brief description}}
Status: Fixed and verified

Deployed: {{time}}
Verified: {{time}}
Monitoring: Stable

Sync release scheduled for: {{date}}
Retrospective scheduled for: {{date}}
```

---

## References

- [Claude-rollout.md](Claude-rollout.md) - Hotfix Boundary Safety Pin
- [INCIDENT-REPORT-TEMPLATE.md](INCIDENT-REPORT-TEMPLATE.md) - Incident documentation
- [DEVIATION-REPORT-TEMPLATE.md](../DEVIATION-REPORT-TEMPLATE.md) - Document deviations
- [Master-Rollout-Guide 65to68.md](Master-Rollout-Guide%2065to68.md) - Full rollout process

---
Generated: {{timestamp}}
Section: 0i Rollout
Document: HOTFIX-PATH.md v2.0 (operational)
---
