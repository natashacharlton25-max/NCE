# Cross-AI Validation Guide

---
**Purpose:** Handle disagreements between Claude and external auditors
**Authority:** Human owner makes final decisions
**Principle:** Advisory, not authoritative — AI opinions inform, humans decide
---

## When AIs Disagree

Disagreements between Claude (primary AI) and external auditors are normal and valuable. They surface:
- Ambiguities in specs
- Alternative interpretations
- Edge cases neither considered
- Genuine errors on either side

**Disagreement is a feature, not a bug.**

---

## Resolution Process

### Step 1: Isolate the Disagreement

Document clearly:
```
Topic: [What they disagree about]
Claude says: [Position A]
External auditor says: [Position B]
```

### Step 2: Get Both Sides to Defend

**Ask the external auditor:**
```
Explain specifically why you disagree with this approach:
[paste Claude's position]
What concrete problems would this cause?
```

**Ask Claude:**
```
The external auditor disagrees with your approach.
Their argument: [paste auditor's reasoning]
Defend your position or acknowledge if they're correct.
```

### Step 3: Evaluate the Arguments

Consider:
- Which argument is more specific?
- Which cites concrete risks vs theoretical concerns?
- Which aligns better with project constraints?
- Does either side concede any points?

### Step 4: Make Your Decision

| If... | Then... |
|-------|---------|
| Claude concedes | Accept auditor's finding |
| Auditor's argument is stronger | Accept finding, update specs |
| Claude's defense is convincing | Reject finding, document why |
| Both have valid points | Choose safer/more conservative option |
| Truly unclear | Default to auditor (external perspective valuable) |

### Step 5: Document the Resolution

**If accepting the finding:**
- Update relevant specs/code
- Note in DECISION-NOTES: "External audit finding accepted over Claude's initial approach"

**If rejecting the finding:**
- Use `RISK-ACCEPTANCE-TEMPLATE.md`
- Document rationale clearly
- File in `0. Admin/risk-acceptances/`

---

## Common Disagreement Patterns

### Pattern 1: Style vs Substance
- **Auditor:** "This naming convention is wrong"
- **Claude:** "It follows project conventions"
- **Resolution:** Check CODING-STANDARDS.md. If consistent with project, reject finding.

### Pattern 2: Theoretical vs Practical
- **Auditor:** "This could fail under extreme load"
- **Claude:** "Load will never reach that level for this use case"
- **Resolution:** Check actual requirements. Accept if risk is real, reject if purely theoretical.

### Pattern 3: Different Interpretations
- **Auditor:** "Spec says X, code does Y"
- **Claude:** "Spec is ambiguous, Y is valid interpretation"
- **Resolution:** Clarify spec. Both might be right about ambiguity.

### Pattern 4: Missing Context
- **Auditor:** "This violates best practice Z"
- **Claude:** "We intentionally chose differently because [reason in DECISIONS.md]"
- **Resolution:** If decision was documented, reject finding. If not, evaluate fresh.

---

## Escalation Triggers

Consider deeper review if:
- Same disagreement pattern repeats 3+ times
- Disagreement is about security or data integrity
- Neither AI can articulate concrete consequences
- You genuinely can't evaluate the technical merits

**Escalation options:**
- Consult a human expert
- Run a third AI audit for tiebreaker
- Default to most conservative option
- Document uncertainty and proceed with monitoring

---

## What NOT to Do

❌ Always side with Claude (bias toward familiar)
❌ Always side with auditor (bias toward external authority)
❌ Ignore disagreements and hope they don't matter
❌ Let AIs argue indefinitely without deciding
❌ Make decisions without documenting rationale

---

## Documenting Disagreement Resolution

When you resolve a disagreement:

1. **If accepting risk:** Document in `RISK-ACCEPTANCE-TEMPLATE.md`
2. **File in:** `0. Admin/risk-acceptances/` for audit trail
3. **Note in:** Relevant DECISION-NOTES file
4. **Format:**

```markdown
## AI Disagreement Resolution: [Date]

**Topic:** [Brief description]
**Claude position:** [Summary]
**Auditor position:** [Summary]
**Decision:** [Accepted/Rejected] auditor finding
**Rationale:** [Why]
**Action taken:** [What changed, or "None - risk accepted"]
```

---

## Quick Reference

| Situation | Default Action |
|-----------|----------------|
| Clear auditor win | Accept, update |
| Clear Claude win | Reject, document |
| Unclear | Default to auditor |
| Security-related | Default to more cautious |
| Style/preference | Default to existing approach |
| Both partially right | Synthesize or pick safer |

---

*Two perspectives are better than one. Your judgment makes three.*
