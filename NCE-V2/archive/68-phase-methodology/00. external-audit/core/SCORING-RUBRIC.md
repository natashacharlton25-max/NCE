# External Audit Scoring Rubric

---
Version: 1.0.0
Purpose: Universal scoring system for all external AI audits
Applies To: All sections (0a-0i)
---

## How Scoring Works

External auditors score Claude's work in **6 categories**, then give an **overall verdict**.

---

## Scoring Categories

### 1. Completeness (Weight: 25%)

*Are all required items present?*

| Score | Meaning | Signs |
|-------|---------|-------|
| 5 | Everything present, plus helpful extras | All checklist items done, additional useful documentation |
| 4 | All required items present | Nothing missing from checklist |
| 3 | Most items present, minor gaps | 1-2 non-critical items missing |
| 2 | Significant gaps | Multiple items missing, some critical |
| 1 | Major items missing | Cannot proceed without significant additions |

---

### 2. Consistency (Weight: 20%)

*Do items match across files? No contradictions?*

| Score | Meaning | Signs |
|-------|---------|-------|
| 5 | Perfect alignment | Terms, types, names identical everywhere |
| 4 | Consistent | No contradictions, minor terminology variations |
| 3 | Mostly consistent | Few inconsistencies, not blocking |
| 2 | Problematic inconsistencies | Contradictions that will cause confusion |
| 1 | Major contradictions | Files directly conflict, cannot proceed |

**Common Consistency Issues:**
- Same thing called different names in different files
- Type definitions don't match between files
- Error codes defined differently
- Function signatures don't match contracts

---

### 3. Accuracy (Weight: 20%)

*Is the content correct? Does it make sense?*

| Score | Meaning | Signs |
|-------|---------|-------|
| 5 | Completely accurate | No errors found, logic is sound |
| 4 | Accurate | Minor issues that don't affect correctness |
| 3 | Mostly accurate | Some errors, but core is correct |
| 2 | Accuracy problems | Errors that will cause implementation issues |
| 1 | Fundamentally flawed | Major logical errors, incorrect assumptions |

**Common Accuracy Issues:**
- Impossible requirements (can't be built as specified)
- Logical contradictions
- Wrong data types for the use case
- Security vulnerabilities in design

---

### 4. Methodology Compliance (Weight: 15%)

*Does it follow Fullspecs rules and templates?*

| Score | Meaning | Signs |
|-------|---------|-------|
| 5 | Exemplary compliance | Follows all rules, good use of templates |
| 4 | Compliant | All major rules followed |
| 3 | Minor deviations | Small departures from templates, documented |
| 2 | Significant deviations | Major departures, not documented |
| 1 | Non-compliant | Ignores methodology, wrong templates |

**Key Rules to Check:**
- Correct templates used
- File naming conventions followed
- Required sections present in each file
- Cross-references are valid
- Decision notes documented

---

### 5. Quality (Weight: 10%)

*Is it well-written and clear?*

| Score | Meaning | Signs |
|-------|---------|-------|
| 5 | Excellent quality | Clear, professional, easy to understand |
| 4 | Good quality | Well-written, minor improvements possible |
| 3 | Acceptable | Understandable but could be clearer |
| 2 | Quality issues | Confusing, ambiguous, hard to follow |
| 1 | Poor quality | Cannot understand intent, needs rewrite |

**Quality Indicators:**
- Clear language (not jargon-heavy)
- Good formatting and structure
- Examples where helpful
- No placeholder text left behind
- Consistent style

---

### 6. Readiness (Weight: 10%)

*Is this ready for the next section?*

| Score | Meaning | Signs |
|-------|---------|-------|
| 5 | Fully ready | Next section can start immediately |
| 4 | Ready | Minor items won't block progress |
| 3 | Mostly ready | Few items need attention first |
| 2 | Not quite ready | Significant work needed |
| 1 | Not ready | Cannot proceed, blocking issues |

**Readiness Checks:**
- All outputs needed by next section exist
- No unresolved decisions blocking progress
- Handoff document complete
- No critical issues outstanding

---

## Consistency Delta (Special Score)

*How well does this section align with the previous section?*

This score measures **drift** - whether this section's work matches what the previous section established.

| Score | Meaning |
|-------|---------|
| 5 | Perfect alignment with previous section |
| 4 | Minor terminology differences, no conflicts |
| 3 | Some items changed without explicit updates |
| 2 | Significant drift from previous section |
| 1 | Breaking changes without documentation |

**Critical Rule:** If Section N changes something that Section N-1 established, it must be documented. Undocumented changes = Critical finding, even if the change is "correct."

---

## Calculating Overall Score

```
Overall = (Completeness × 0.25) + (Consistency × 0.20) + (Accuracy × 0.20) 
        + (Methodology × 0.15) + (Quality × 0.10) + (Readiness × 0.10)
```

**Example:**
- Completeness: 4
- Consistency: 5
- Accuracy: 4
- Methodology: 3
- Quality: 4
- Readiness: 4

Overall = (4×0.25) + (5×0.20) + (4×0.20) + (3×0.15) + (4×0.10) + (4×0.10)
        = 1.0 + 1.0 + 0.8 + 0.45 + 0.4 + 0.4 = **4.05**

---

## Finding Severity Levels

When the auditor finds problems, they classify by severity:

| Level | Icon | Meaning | Action Required |
|-------|------|---------|-----------------|
| **Critical** | 🔴 | Blocks progress, will cause failure | Must fix before proceeding |
| **Important** | 🟠 | Will cause problems later | Should fix before proceeding |
| **Minor** | 🟡 | Small issues, low risk | Fix if time permits |
| **Suggestion** | 🔵 | Optional improvement | Consider for future |

### What Makes Something Critical?

- Cannot proceed to next section without fixing
- Will definitely cause bugs or failures
- Security vulnerability
- Data loss potential
- Completely missing required component

### What Makes Something Important?

- Will likely cause problems, but not immediately
- Technical debt that will compound
- Inconsistencies that will confuse implementers
- Missing error handling for likely scenarios

### What Makes Something Minor?

- Small quality issues
- Style inconsistencies
- Missing but non-essential documentation
- Edge cases that are unlikely

### What Makes Something a Suggestion?

- "Nice to have" improvements
- Alternative approaches
- Optimizations
- Future enhancements

---

## Verdicts

Based on score and findings, the auditor gives a verdict:

| Verdict | Criteria | What Happens Next |
|---------|----------|-------------------|
| ✅ **PASS** | Score ≥ 4.0 AND no Critical findings | Proceed to next section |
| ⚠️ **CONDITIONAL PASS** | Score ≥ 3.0 AND ≤2 Critical findings | Fix critical/important, then proceed |
| ❌ **FAIL** | Score < 3.0 OR >2 Critical findings | Fix issues, re-audit recommended |

---

## Light Scoring Mode

For faster audits or less critical sections, use 3 categories:

| Category | Weight |
|----------|--------|
| Completeness | 40% |
| Consistency | 30% |
| Accuracy | 30% |

No Methodology, Quality, or Readiness scores. No Consistency Delta.

---

## Common False Positives

Things auditors sometimes flag that aren't real problems:

| False Positive | Why It Happens | How to Recognize |
|----------------|----------------|------------------|
| "Over-engineering" | Auditor lacks project context | Compare to stated requirements |
| "Missing tests" | Tests are in section 0g | Tests come later in pipeline |
| "Should use different tech" | Auditor preference | Tech stack was decided in 0d |
| "Too detailed" | Auditor opinion | Methodology requires detail |
| "Naming is wrong" | Style preference | Check if it follows conventions |

---

## Score Calibration Examples

### Score 5 - Completeness
✅ All required files present
✅ All sections filled out
✅ Extra helpful documentation added
✅ Examples provided throughout
✅ Edge cases documented

### Score 3 - Consistency
⚠️ Most terms match
⚠️ One function called "getUser" in one file, "fetchUser" in another
⚠️ No major contradictions
⚠️ Types mostly align

### Score 1 - Accuracy
❌ Database schema doesn't support required operations
❌ API endpoints don't match function signatures
❌ Logic has circular dependency
❌ Security model has obvious hole

---

## Remember

**Audits are advisory, not authoritative.**

The human makes the final decision. These scores guide the decision, they don't make it.

If you disagree with the auditor's findings:
1. Review their reasoning
2. Check if they misunderstood something
3. Make your own decision
4. Document why you overrode the finding

---

*End of Scoring Rubric*
