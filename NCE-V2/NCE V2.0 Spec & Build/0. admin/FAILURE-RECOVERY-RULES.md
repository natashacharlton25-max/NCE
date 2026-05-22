# Failure Recovery Rules

---
**Purpose:** Canonical rules for handling failures, rollbacks, and re-work
**Authority:** Human owner makes final decision; these rules provide guidance
---

## Core Principle

**No Fix-Forward.** When problems are found, go back to where the problem originated and fix it there. Do not patch over issues in later phases.

---

## Rollback Trigger Matrix

| Problem Found In | Problem Type | Roll Back To | Re-Audit Required? |
|------------------|--------------|--------------|-------------------|
| 0b PassPhases | Missing system/subsystem | 0a Phase 3-8 | No |
| 0b PassPhases | Missing integration | 0a Phase 9-11 | No |
| 0c Full Specs | Spec contradiction | 0b (re-run affected pass) | No |
| 0c Full Specs | Missing component | 0a or 0b | No |
| **0c Audit** | Critical finding | 0c (fix affected specs) | **Yes** |
| 0d PreCode | Schema conflict | 0c (fix STORAGE.md files) | Yes (0c) |
| 0d PreCode | API conflict | 0c (fix FUNCTIONS.md files) | Yes (0c) |
| 0e Frontend | Missing API endpoint | 0c + 0d | Yes (0c) |
| 0f Implementation | Spec ambiguity | 0c (clarify spec) | No |
| 0f Implementation | Missing functionality | 0c (add to spec) | Yes (0c) |
| **0f Audit** | Code doesn't match spec | 0f (fix code) or 0c (fix spec) | **Yes** |
| 0g Testing | Untestable spec | 0c (clarify spec) | No |
| 0h Prerelease | Security issue | 0f (fix code) | Yes (0f) |
| 0h Prerelease | Performance issue | 0f (fix code) | No |
| **0h Audit** | Critical finding | Depends on finding | **Yes** |
| 0i Rollout | Deployment failure | 0h (fix config) | No |

---

## Rollback Depth Rules

### Rule 1: Fix at Source
Always fix the problem where it originated, not where it was discovered.

**Example:** If 0f implementation reveals a spec gap → fix the spec in 0c, don't just code around it.

### Rule 2: Minimal Rollback
Roll back only as far as necessary. Don't restart entire sections if only one component is affected.

**Example:** If one subsystem's STORAGE.md has a conflict → fix that one file, not all specs.

### Rule 3: Cascade Forward
After fixing, re-run all phases between the fix and where you were.

**Example:** Fix in 0c Phase 21 → re-run Phases 22-28 for affected component → re-audit 0c → continue to 0d.

### Rule 4: Re-Audit After Critical Fixes
If you roll back past an audit point, you must re-audit.

| If You Roll Back Past | You Must Re-Run |
|----------------------|-----------------|
| 0c (Phase 28) | 0c Audit |
| 0f (Phase 53) | 0f Audit |
| 0h (Phase 64) | 0h Audit |

---

## Who Decides?

| Decision | Authority |
|----------|-----------|
| Whether to roll back | Human owner |
| How far to roll back | Human owner (guided by this doc) |
| Whether fix is complete | Human owner (advised by Claude) |
| Whether to re-audit | Mandatory per rules above |
| Whether to accept risk and proceed | Human owner (document in RISK-ACCEPTANCE) |

---

## What Gets Rewritten vs Amended

### Rewrite (Replace Entire File)
- Spec file with fundamental errors
- Contract that contradicts implementation
- Schema with structural flaws

### Amend (Update Specific Sections)
- Adding missing fields
- Clarifying ambiguous language
- Fixing typos or formatting
- Adding examples

### Append (Add to Existing)
- New error codes
- New edge cases
- Additional test scenarios

---

## Documentation Requirements

When rolling back, document in the section's DECISION-NOTES file:

```markdown
## Rollback: [Date]

**Discovered in:** Phase XX (Section 0X)
**Problem:** [Brief description]
**Root cause:** Phase YY (Section 0Y)
**Fix applied:** [What was changed]
**Files modified:** [List]
**Re-audit required:** Yes/No
**Approved by:** [Human owner]
```

---

## Emergency Procedures

### If Stuck in Rollback Loop
If you've rolled back the same issue 3+ times:
1. STOP
2. Document the pattern
3. Escalate to architectural review
4. Consider whether system design is flawed

### If Audit Keeps Failing
If same audit fails 3+ times:
1. Review findings for pattern
2. Check if methodology is being followed
3. Consider scope reduction
4. Document lessons learned

### If Deadline Pressure
If you must ship despite issues:
1. Document all known issues in RISK-ACCEPTANCE
2. Create post-launch fix plan
3. Never skip 0h audit for production deployments
4. Accept that shortcuts create debt

---

## AI Tooling Note

**Primary AI** = the system-following AI that generates artifacts (currently Claude).
If you switch to a different AI in the future, replace "Claude" references with your chosen tool. The methodology is AI-agnostic; only the execution changes.

---

## Quick Reference

**Minor issue:** Amend file, continue
**Spec gap:** Roll back to 0c, fix, cascade forward
**Audit failure:** Fix findings, re-audit
**Cross-section conflict:** Roll back to source section, fix, re-audit if past checkpoint
**Fundamental flaw:** Consider project pause and architectural review

---

*When in doubt, roll back further than you think necessary. It's cheaper than fixing forward.*
