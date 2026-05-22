# 0c Audit Handoff Prompt

---
Section: 0c Full Specs (Phases 20-28)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 28, before external audit
---

## Instructions for Claude

You have completed Section 0c (Full Specs). Prepare a handoff package for external audit review.

---

## Create: 0c-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- What was accomplished in 0c
- How many components were specified
- Any notable challenges or decisions
- Overall confidence level

### 2. Self-Assessment Scores

Score your own work honestly (1-5 scale):

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | Are all specs complete? |
| Consistency | /5 | Do specs align across components? |
| Accuracy | /5 | Are specs technically sound? |
| Methodology | /5 | Did you follow templates correctly? |
| Quality | /5 | Are specs clear and well-written? |
| Readiness | /5 | Ready for implementation? |

### 3. Concerns & Uncertainties

List anything you're not 100% confident about:
- Areas where requirements were ambiguous
- Decisions made with incomplete information
- Specs that might need revision
- Potential gaps you're aware of

Be honest. This helps the auditor focus on the right areas.

### 4. Key Decisions Made

| Decision | Rationale | Phase | Impact |
|----------|-----------|-------|--------|
| | | | |

List significant decisions made during 0c, especially:
- Trade-offs
- Scope decisions
- Technical choices
- Deviations from templates

### 5. Deviations from Templates

If you deviated from standard templates, explain:

| File | Deviation | Reason |
|------|-----------|--------|
| | | |

Write "None" if no deviations.

### 6. Hardening Results Summary

From HARDENING-SUMMARY.md, include:
- Overall completeness percentage
- Number of components reviewed
- Number of issues found and resolved
- Any remaining known issues

### 7. File Manifest

List every file created or modified in 0c:

**Per-Component Specs (10 files each):**

| Component | Type | Files Complete | Location |
|-----------|------|----------------|----------|
| {{name}} | System | Yes/Partial | {{path}} |
| {{name}} | Subsystem | Yes/Partial | {{path}} |
| {{name}} | Integration | Yes/Partial | {{path}} |

**Project-Wide Specs (Phase 26):**

| File | Status | Location |
|------|--------|----------|
| ERROR-CODES.md | Complete/Partial | |
| SCHEMAS.md | Complete/Partial | |
| TYPES.md | Complete/Partial | |
| API-STANDARDS.md | Complete/Partial | |
| SECURITY-STANDARDS.md | Complete/Partial | |
| CONVENTIONS.md | Complete/Partial | |
| GLOSSARY.md | Complete/Partial | |

**Analysis Files (Phase 25):**

| File | Status |
|------|--------|
| GAP-ANALYSIS.md | |
| PATTERN-ANALYSIS.md | |
| DEPENDENCY-ANALYSIS.md | |
| TYPE-ANALYSIS.md | |
| ERROR-CODE-ANALYSIS.md | |

**Hardening Files (Phase 27):**

| File | Status |
|------|--------|
| COMPLETENESS-REVIEW.md | |
| AMBIGUITY-REVIEW.md | |
| IMPLEMENTABILITY-REVIEW.md | |
| TEST-COVERAGE-REVIEW.md | |
| HARDENING-SUMMARY.md | |

**Gate Files (Phase 28):**

| File | Status |
|------|--------|
| MASTER-CHECKLIST.md | |
| SPEC-MANIFEST.md | |
| IMPLEMENTATION-HANDOFF.md | |

### 8. Dependency Bundles

Group related files for the auditor:

| Bundle Name | Primary File | Related Files |
|-------------|--------------|---------------|
| {{Component}} Spec | {{component}}/INDEX.md | OVERVIEW.md, FUNCTIONS.md, TYPES.md, BEHAVIOUR.md, ERRORS.md, CONFIG.md, STORAGE.md |
| Project Standards | ERROR-CODES.md | SCHEMAS.md, TYPES.md, API-STANDARDS.md |
| Quality Gate | HARDENING-SUMMARY.md | COMPLETENESS-REVIEW.md, AMBIGUITY-REVIEW.md, IMPLEMENTABILITY-REVIEW.md |
| Final Gate | MASTER-CHECKLIST.md | SPEC-MANIFEST.md, IMPLEMENTATION-HANDOFF.md |

### 9. Condensed Context

One-page project summary (copy from previous sections or create):

```
PROJECT: {{name}}
PURPOSE: {{one sentence}}
TYPE: {{web app / API / tool / etc.}}

CORE SYSTEMS:
1. {{system}} - {{purpose}}
2. {{system}} - {{purpose}}

KEY CONSTRAINTS:
- {{constraint}}
- {{constraint}}

OUT OF SCOPE:
- {{item}}
```

### 10. Recommended Files for Review

The 5-7 most important files an auditor should examine first:

1. **HARDENING-SUMMARY.md** - Overall quality metrics
2. **MASTER-CHECKLIST.md** - Completion status
3. **ERROR-CODES.md** - Error handling completeness
4. **GAP-ANALYSIS.md** - Known gaps
5. **{{critical component}}/FUNCTIONS.md** - Core business logic
6. **{{critical component}}/FUNCTIONS.md** - Another key component
7. **IMPLEMENTATION-HANDOFF.md** - Ready for code?

---

## Pre-Handoff Verification

Before creating this handoff, verify:

- [ ] All required files exist
- [ ] No broken cross-references
- [ ] No TODO/TBD placeholders remaining
- [ ] HARDENING-SUMMARY.md is complete
- [ ] MASTER-CHECKLIST.md all items checked
- [ ] SPEC-MANIFEST.md lists all specs

If any check fails, fix the issue first, then create the handoff.

---

## Output Location

Save as: `0c-AUDIT-HANDOFF.md` in the project's audit folder or 0c admin folder.

---

*End of Handoff Prompt*
