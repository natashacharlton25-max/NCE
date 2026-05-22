# External Audit Prompt: Section 0a - Concept to PassPhase

---
Section: 0a Concept to PassPhase (Phases 1-12)
Audit Type: OPTIONAL (Self-Check)
Priority: Medium
Estimated Time: 30 minutes
---

## Your Role

You are an independent auditor reviewing project foundation documents.

**Your job is to verify** that the project concept has been properly captured and structured before detailed specification work begins.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0a of a software project using the "Fullspecs" methodology. This section captures the initial project concept and structures it.

**0a produces:**
- Project overview and intention
- System identification and documentation
- Subsystem breakdown
- External integration identification
- Pre-pass verification checklist

**Why 0a matters:**
- Foundation for everything that follows
- Wrong structure here = wrong architecture later
- Missing systems/integrations cause rework
- Scope clarity prevents creep

---

## What You Have

1. **Claude's Handoff Package** (provided below)
2. **0a Checklist** (provided below)
3. **Scoring Rubric** (provided below)

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package
2. Check project structure makes sense
3. Request **up to 5 files** from the manifest

**Priority Files:**
- PROJECT-OVERVIEW.md (scope)
- PROJECT-INTENTION.md (success criteria)
- SYSTEM-NOTES.md (system breakdown)
- PREPASS-CHECKLIST.md (gate status)

### Round 2: Follow-up (if needed)

Request **up to 3 additional files** or ask clarifying questions.

---

## What to Look For

### Critical Issues (🔴)

- [ ] Project scope is unclear or contradictory
- [ ] Major system obviously missing
- [ ] Systems overlap significantly (unclear boundaries)
- [ ] Critical integration not identified

### Important Issues (🟠)

- [ ] System responsibilities unclear
- [ ] Subsystem breakdown doesn't make sense
- [ ] Integration scope too vague
- [ ] Success criteria not measurable

### Minor Issues (🟡)

- [ ] Documentation could be clearer
- [ ] Minor naming inconsistencies
- [ ] Examples would help

### Suggestions (🔵)

- [ ] Alternative structuring approaches
- [ ] Additional considerations

---

## Specific Checks for 0a

### Project Definition
- Is the project purpose clear?
- Are success criteria defined and measurable?
- Is scope explicitly bounded (what's NOT included)?

### System Structure
- Are all necessary systems identified?
- Are system boundaries clear (no overlap)?
- Does each system have a distinct responsibility?

### Subsystem Breakdown
- Do subsystems logically belong to their parent system?
- Is the breakdown at the right granularity?
- Are there subsystems that should be systems (or vice versa)?

### Integrations
- Are all external dependencies identified?
- Is the scope of each integration clear?
- Are there hidden integrations not listed?

### Readiness
- Is PREPASS-CHECKLIST.md complete?
- Are there blocking issues for 0b?

---

## 0a CHECKLIST

```
## Project Definition
- [ ] PROJECT-OVERVIEW.md exists and is complete
- [ ] PROJECT-INTENTION.md exists and is complete
- [ ] Project purpose is clear
- [ ] Success criteria are measurable
- [ ] Scope boundaries defined

## Systems
- [ ] All systems identified
- [ ] Each system has clear responsibility
- [ ] System boundaries don't overlap
- [ ] SYSTEM-*.md exists for each system

## Subsystems
- [ ] Subsystems identified for each system
- [ ] Subsystem breakdown is logical
- [ ] SUBSYSTEM-*.md exists for each subsystem

## Integrations
- [ ] All external integrations identified
- [ ] Provider and services documented
- [ ] Integration scope is clear

## Gate
- [ ] PREPASS-CHECKLIST.md complete
- [ ] No blocking issues
- [ ] Ready for 0b (Pass phases)
```

---

## SCORING RUBRIC

**Categories:**
- Completeness (25%) - All components identified?
- Consistency (20%) - Definitions align?
- Accuracy (20%) - Structure makes sense?
- Methodology (15%) - Templates followed?
- Quality (10%) - Documentation clear?
- Readiness (10%) - Ready for 0b?

**Verdicts:**
- ✅ PASS: Score ≥ 4.0, no Critical findings
- ⚠️ CONDITIONAL: Score ≥ 3.0, ≤2 Critical findings
- ❌ FAIL: Score < 3.0 OR >2 Critical findings

---

## HANDOFF PACKAGE

[Human: Paste Claude's 0a-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
