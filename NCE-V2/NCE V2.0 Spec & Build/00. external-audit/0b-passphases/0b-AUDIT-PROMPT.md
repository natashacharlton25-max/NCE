# External Audit Prompt: Section 0b - PassPhases 0-4

---
Section: 0b PassPhases 0-4 (Phases 13-19)
Audit Type: RECOMMENDED (Light Audit)
Priority: High
Estimated Time: 30-45 minutes
---

## Your Role

You are an independent auditor reviewing software architecture decisions.

**Your job is to verify** that the architecture is sound before detailed specification work begins. Architecture mistakes here are expensive to fix later.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0b using the "Fullspecs" methodology. This section refines the project structure through 5 passes.

**0b produces:**
- Pass 0: Coverage review (all components accounted for)
- Pass 1: Grounding (SYSTEM.md, ADMIN.md per component)
- Pass 2: Contracts (CONTRACT.md defining interfaces)
- Pass 3: Dependencies (DEPENDENCY-MAP.md, build order)
- Pass 4: Implementation planning (files, LOC, tasks)
- PASS-NOTES.md distributed to each component

**Why 0b matters:**
- Contracts define how components communicate
- Dependency map determines build order
- Wrong architecture = rework in implementation
- This is where circular dependencies get caught

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package
2. Check for architectural issues
3. Request **up to 5 files**

**Priority Files:**
- DEPENDENCY-MAP.md (architecture)
- PASS-DECISION-NOTES.md (key decisions)
- POST-PASS-CHECKLIST.md (completion)
- Critical CONTRACT.md files

### Round 2: Follow-up

Request **up to 3 additional files** or ask questions about specific contracts.

---

## What to Look For

### Critical Issues (🔴)

- [ ] Circular dependencies in DEPENDENCY-MAP
- [ ] Contract missing for component interaction
- [ ] Contract contradicts another contract
- [ ] Component responsibility unclear
- [ ] Missing component (gap in coverage)

### Important Issues (🟠)

- [ ] Contract interface is vague
- [ ] Dependency seems unnecessary
- [ ] Build order doesn't make sense
- [ ] LOC estimate seems very wrong
- [ ] Component boundary unclear

### Minor Issues (🟡)

- [ ] Documentation could be clearer
- [ ] Minor inconsistencies in naming
- [ ] Examples would help

---

## Specific Checks for 0b

### Coverage (Pass 0)
- Are all systems from 0a accounted for?
- Are all subsystems accounted for?
- Are all integrations accounted for?

### Contracts (Pass 2)
- Does every component that talks to another have a contract?
- Are contract interfaces clearly defined?
- Do contracts specify data types?
- Do contracts specify error conditions?

### Dependencies (Pass 3)
- Is DEPENDENCY-MAP.md complete?
- Are there circular dependencies? (Critical!)
- Does build order make sense?
- Can components be built in the specified order?

### Implementation Planning (Pass 4)
- Are LOC estimates reasonable?
- Are file counts reasonable?
- Is task breakdown logical?

### PASS-NOTES Distribution (Phase 19)
- Does every component have PASS-NOTES.md?
- Do PASS-NOTES contain relevant context?

---

## 0b CHECKLIST

```
## Pass Completion
- [ ] Pass 0 (Coverage) complete
- [ ] Pass 1 (Grounding) complete
- [ ] Pass 2 (Contracts) complete
- [ ] Pass 3 (Dependencies) complete
- [ ] Pass 4 (Implementation Planning) complete

## Contracts
- [ ] CONTRACT.md exists for each component
- [ ] Interfaces are clearly defined
- [ ] Data types specified
- [ ] Error conditions specified
- [ ] No contradicting contracts

## Dependencies
- [ ] DEPENDENCY-MAP.md exists
- [ ] No circular dependencies
- [ ] Build order is valid
- [ ] All dependencies documented

## Distribution
- [ ] PASS-NOTES.md in each component folder
- [ ] POST-PASS-CHECKLIST.md complete

## Readiness
- [ ] Ready for Full Specs (0c)
- [ ] No blocking issues
```

---

## SCORING RUBRIC

**Categories:**
- Completeness (25%)
- Consistency (20%)
- Accuracy (20%)
- Methodology (15%)
- Quality (10%)
- Readiness (10%)

**Verdicts:**
- ✅ PASS: Score ≥ 4.0, no Critical findings
- ⚠️ CONDITIONAL: Score ≥ 3.0, ≤2 Critical findings
- ❌ FAIL: Score < 3.0 OR >2 Critical findings

---

## HANDOFF PACKAGE

[Human: Paste Claude's 0b-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
