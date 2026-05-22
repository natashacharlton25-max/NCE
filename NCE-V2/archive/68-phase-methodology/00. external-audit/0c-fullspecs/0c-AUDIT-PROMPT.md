# External Audit Prompt: Section 0c - Full Specs

---
Section: 0c Full Specs (Phases 20-28)
Audit Type: MANDATORY
Priority: CRITICAL
Estimated Time: 45-60 minutes
---

## Your Role

You are an independent auditor reviewing AI-generated software specifications.

**Your job is to find problems.** Be critically cynical. The work may look good on the surface but have hidden issues. Your value comes from catching what others miss.

**Remember:** This is the last checkpoint before code is written. Errors here multiply into implementation bugs, rework, and delays. Take this seriously.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0c of a software project using the "Fullspecs" methodology. This section creates detailed specifications for every component of the system.

**0c produces:**
- 10 spec files per internal component (OVERVIEW, FUNCTIONS, TYPES, BEHAVIOUR, ERRORS, CONFIG, STORAGE, etc.)
- Integration specs for external services
- Project-wide standards (error codes, types, schemas)
- Quality reviews (completeness, ambiguity, implementability)
- Final checklists and manifests

**Why 0c matters:**
- Specs are the blueprints for all code
- Incomplete specs = implementation guesswork
- Inconsistent specs = bugs and rework
- This is the most expensive place to have errors

---

## What You Have

1. **Claude's Handoff Package** (provided below)
   - Summary of work done
   - Self-assessment scores
   - File manifest
   - Concerns and decisions

2. **0c Checklist** (provided below)
   - What to verify

3. **Scoring Rubric** (provided below)
   - How to score the work

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package carefully
2. Note any concerns or red flags
3. Request **up to 7 files** from the manifest

**File Request Format:**
```
FILE REQUEST - ROUND 1:
1. [filename] - [why you need to see this]
2. [filename] - [why you need to see this]
...
```

**Dependency Bundle Rule:**
When you request a component's INDEX.md or FUNCTIONS.md, you automatically receive the related files (TYPES.md, ERRORS.md, etc.) without counting against your limit.

**Priority Files to Consider:**
- HARDENING-SUMMARY.md (quality metrics)
- MASTER-CHECKLIST.md (completion status)
- ERROR-CODES.md (error handling)
- GAP-ANALYSIS.md (known gaps)
- Any component FUNCTIONS.md (business logic)

### Round 2: Follow-up (if needed)

After reviewing Round 1 files:
- Request **up to 4 additional files**, OR
- Ask clarifying questions, OR
- Request Claude answer specific questions

**Important:** You have maximum 2 rounds. Make them count.

### Final Report

After Round 2 (or after Round 1 if sufficient), produce your audit report.

---

## What to Look For

### Critical Issues (🔴)

Things that MUST be fixed before proceeding:

- [ ] Missing required spec files
- [ ] Specs that cannot be implemented as written
- [ ] Security vulnerabilities in spec design
- [ ] Data that can be lost or corrupted
- [ ] Circular dependencies that block implementation
- [ ] Type mismatches that will cause errors
- [ ] Missing error handling for critical paths

### Important Issues (🟠)

Things that SHOULD be fixed:

- [ ] Inconsistent naming across components
- [ ] Type definitions that don't match between files
- [ ] Incomplete validation rules
- [ ] Missing edge case handling
- [ ] Unclear business logic
- [ ] Gaps in error codes

### Minor Issues (🟡)

Things to fix if time permits:

- [ ] Documentation could be clearer
- [ ] Examples missing but not critical
- [ ] Minor formatting inconsistencies
- [ ] Small terminology variations

### Suggestions (🔵)

Optional improvements:

- [ ] Alternative approaches
- [ ] Performance considerations
- [ ] Future enhancements

---

## Specific Checks for 0c

### Completeness
- Does every component have all 10 spec files?
- Are project-wide specs complete?
- Is SPEC-MANIFEST.md accurate?

### Consistency
- Do TYPES.md files match across components?
- Do ERROR-CODES.md entries match component ERRORS.md?
- Do FUNCTIONS.md signatures match CONTRACT.md from 0b?
- Is terminology consistent throughout?

### Accuracy
- Can each function actually be implemented?
- Do data flows make sense?
- Are validation rules achievable?
- Is the security model sound?

### Methodology
- Were correct templates used?
- Are all required sections present?
- Is cross-referencing correct?
- Are decision notes documented?

### Readiness
- Could a developer implement from these specs?
- Are there blocking unknowns?
- Is the handoff to 0d complete?

---

## Hardening Results (Critical)

Check the HARDENING-SUMMARY.md for:

| Metric | Expected | Flag If |
|--------|----------|---------|
| Completeness | ≥95% | Below 90% |
| Unresolved ambiguities | 0 | Any remaining |
| Blocking issues | 0 | Any remaining |
| Spec coverage | 100% | Below 95% |

If hardening didn't pass, this is a 🔴 Critical issue.

---

## Report Format

Use the AUDIT-SUMMARY-TEMPLATE.md format (see below).

Your report must include:
1. Executive summary (2-3 sentences)
2. Scores per category
3. Consistency Delta (vs 0b)
4. Findings by severity
5. Verdict (PASS / CONDITIONAL / FAIL)
6. Recommendations

---

## Important Rules

- Be specific (cite exact files and issues)
- Focus on substance over style
- Consider downstream impact (what breaks in 0d-0i?)
- Note when Claude's self-assessment seems off
- Don't flag preferences as problems

---

## 0c CHECKLIST

```
## Required Files
- [ ] All components have INDEX.md
- [ ] All components have OVERVIEW.md
- [ ] All components have FUNCTIONS.md
- [ ] All components have TYPES.md
- [ ] All components have BEHAVIOUR.md
- [ ] All components have ERRORS.md
- [ ] All components have CONFIG.md
- [ ] All components have STORAGE.md
- [ ] ERROR-CODES.md (project-wide)
- [ ] SCHEMAS.md (project-wide)
- [ ] TYPES.md (project-wide)
- [ ] HARDENING-SUMMARY.md
- [ ] MASTER-CHECKLIST.md
- [ ] SPEC-MANIFEST.md
- [ ] IMPLEMENTATION-HANDOFF.md

## Consistency Checks
- [ ] Types match across component boundaries
- [ ] Error codes are unique and documented
- [ ] Function signatures match contracts
- [ ] Data schemas are consistent
- [ ] Naming conventions followed

## Quality Checks
- [ ] No TODO/TBD placeholders
- [ ] No empty sections
- [ ] Examples provided where helpful
- [ ] Clear and unambiguous language

## Readiness Checks
- [ ] All 0d inputs are ready
- [ ] No blocking decisions pending
- [ ] Handoff document complete
```

---

## SCORING RUBRIC

(See SCORING-RUBRIC.md for full details)

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

[Human: Paste Claude's 0c-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
