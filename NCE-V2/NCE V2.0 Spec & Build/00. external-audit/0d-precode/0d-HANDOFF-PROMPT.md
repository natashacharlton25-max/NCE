# 0d Audit Handoff Prompt

---
Section: 0d PreCode (Phases 29-36)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 36, before external audit (OPTIONAL)
---

## Instructions for Claude

You have completed Section 0d (PreCode). Prepare a handoff package for external audit review.

**Note:** This audit is OPTIONAL. If 0c audit was thorough, 0d audit may be skipped.

---

## Create: 0d-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- What was consolidated in 0d
- Any conflicts found and resolved
- Overall readiness for implementation

### 2. Self-Assessment Scores

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | All consolidation done? |
| Consistency | /5 | Consolidated files match specs? |
| Accuracy | /5 | Schemas and APIs correct? |
| Methodology | /5 | PreCode rules followed? |
| Quality | /5 | Documentation clear? |
| Readiness | /5 | Ready for Frontend/Implementation? |

### 3. Concerns & Uncertainties

- Schema decisions that might need adjustment
- API design concerns
- Tech stack questions

### 4. Consolidation Summary

**Database Schema:**
- Tables: {{count}}
- Relationships: {{count}}
- Conflicts found: {{count}}
- Conflicts resolved: {{count}}

**API Surface:**
- Endpoints: {{count}}
- Methods: GET {{n}}, POST {{n}}, PUT {{n}}, DELETE {{n}}
- Conflicts found: {{count}}
- Conflicts resolved: {{count}}

### 5. Key Decisions Made

| Decision | Phase | Rationale |
|----------|-------|-----------|
| | | |

### 6. Conflict Resolution

| Conflict | Source Files | Resolution | Approved |
|----------|--------------|------------|----------|
| | | | |

Write "None" if no conflicts.

### 7. File Manifest

| File | Location | Status |
|------|----------|--------|
| DATABASE-SCHEMA.md | /29. Database-Schema-Consolidation/ | |
| MIGRATION-STRATEGY.md | /29. Database-Schema-Consolidation/ | |
| API-SURFACE.md | /30. API-Surface-Consolidation/ | |
| LIBRARY-STRUCTURE.md | /31. Library-Structure/ | |
| REPO-STRUCTURE.md | /32. Repo-Setup/ | |
| TECH-STACK.md | /33. Tech-Stack/ | |
| ENVIRONMENT.md | /34. Environment/ | |
| CODING-STANDARDS.md | /35. Coding-Standards/ | |
| PRECODE-CHECKLIST.md | /36. PreCode-Ready-Check/ | |

### 8. Dependency Bundles

| Bundle | Primary File | Related Files |
|--------|--------------|---------------|
| Database | DATABASE-SCHEMA.md | MIGRATION-STRATEGY.md, all STORAGE.md from 0c |
| API | API-SURFACE.md | All FUNCTIONS.md from 0c |
| Structure | REPO-STRUCTURE.md | LIBRARY-STRUCTURE.md |
| Standards | CODING-STANDARDS.md | TECH-STACK.md |
| Gate | PRECODE-CHECKLIST.md | All above |

### 9. Recommended Files for Review

1. **DATABASE-SCHEMA.md** - Consolidated schema
2. **API-SURFACE.md** - Consolidated API
3. **TECH-STACK.md** - Technology choices
4. **PRECODE-CHECKLIST.md** - Gate status
5. **PRECODE-DECISION-NOTES.md** - Decisions made

---

## Pre-Handoff Verification

- [ ] DATABASE-SCHEMA.md matches all 0c STORAGE specs
- [ ] API-SURFACE.md matches all 0c FUNCTIONS specs
- [ ] No unresolved conflicts
- [ ] PRECODE-CHECKLIST.md complete
- [ ] No rollback required (Rule 1)

---

## Output Location

Save as: `0d-AUDIT-HANDOFF.md` in `00. external-audit/0d-precode/`

---

*End of Handoff Prompt*
