# 0f Audit Handoff Prompt

---
Section: 0f Implementation (Phases 43-53)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 53, before external audit
---

## Instructions for Claude

You have completed Section 0f (Implementation). Prepare a handoff package for external audit review.

---

## Create: 0f-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- What was implemented in 0f
- How closely implementation matches specs
- Any significant challenges or deviations
- Overall confidence that code is correct

### 2. Self-Assessment Scores

Score your own work honestly (1-5 scale):

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | Is all specified functionality implemented? |
| Consistency | /5 | Does code match specs? |
| Accuracy | /5 | Does code work correctly? |
| Methodology | /5 | Did you follow implementation guidelines? |
| Quality | /5 | Is code clean and maintainable? |
| Readiness | /5 | Ready for testing? |

### 3. Concerns & Uncertainties

List anything you're not 100% confident about:
- Areas where specs were ambiguous
- Complex logic that might have bugs
- Performance concerns
- Security considerations
- Integration points that weren't fully tested

### 4. Spec Compliance Summary

| Component | Spec Location | Implementation Status | Deviations |
|-----------|---------------|----------------------|------------|
| {{name}} | {{spec_path}} | Complete/Partial | None / See DEVIATION-REPORT |

### 5. Deviations from Specs

For each deviation from specs:

| Deviation | Spec Said | Implementation Did | Reason | Approved |
|-----------|-----------|-------------------|--------|----------|
| | | | | Yes/Pending |

Link to DEVIATION-REPORT.md files if they exist.

### 6. Key Implementation Decisions

| Decision | Context | Choice Made | Impact |
|----------|---------|-------------|--------|
| | | | |

### 7. File Manifest

**Implementation Files Created:**

| Category | Location | File Count |
|----------|----------|------------|
| Backend code | /src/backend/ | |
| Frontend code | /src/frontend/ | |
| Shared types | /src/shared/ | |
| Database migrations | /migrations/ | |
| Configuration | /config/ | |
| Tests (written in 0f) | /tests/ | |

**Documentation Files Created:**

| File | Location | Purpose |
|------|----------|---------|
| IMPLEMENTATION-LOG.md | | Phase-by-phase progress |
| IMPLEMENTATION-CHECKLIST.md | | Final verification |
| BACKEND-VERIFICATION-REPORT.md | | Backend test results |
| 0f-to-0g-HANDOFF.md | | Handoff to testing |
| DEVIATION-REPORT.md | | Spec deviations (if any) |

### 8. Dependency Bundles

| Bundle Name | Primary File | Related Files |
|-------------|--------------|---------------|
| Backend Core | /src/backend/index.ts | Controllers, services, routes |
| Database | /migrations/ | Schema, seeds, models |
| API Layer | /src/api/ | Routes, middleware, validators |
| Frontend Core | /src/frontend/App.tsx | Pages, components, state |
| Shared Types | /src/shared/types.ts | Interfaces, enums, constants |
| Verification | BACKEND-VERIFICATION-REPORT.md | Test results, coverage |

### 9. Condensed Context

```
PROJECT: {{name}}
PURPOSE: {{one sentence}}

IMPLEMENTED COMPONENTS:
- {{component}} - {{status}}
- {{component}} - {{status}}

TECH STACK:
- Backend: {{framework}}
- Frontend: {{framework}}
- Database: {{database}}

KEY CONSTRAINTS:
- {{constraint}}
```

### 10. Recommended Files for Review

1. **IMPLEMENTATION-LOG.md** - Phase-by-phase progress
2. **IMPLEMENTATION-CHECKLIST.md** - All requirements met?
3. **BACKEND-VERIFICATION-REPORT.md** - Do tests pass?
4. **0f-to-0g-HANDOFF.md** - Ready for testing?
5. **DEVIATION-REPORT.md** - Any spec changes?
6. **{{critical_file}}.ts** - Core business logic
7. **{{critical_file}}.ts** - Critical data handling

---

## Pre-Handoff Verification

Before creating this handoff, verify:

- [ ] All implementation phases complete (43-53)
- [ ] IMPLEMENTATION-LOG.md is up to date
- [ ] IMPLEMENTATION-CHECKLIST.md all items checked
- [ ] BACKEND-VERIFICATION-REPORT.md shows passing
- [ ] All DEVIATION-REPORTs are documented
- [ ] 0f-to-0g-HANDOFF.md is complete
- [ ] Code compiles without errors
- [ ] Basic tests pass

If any check fails, fix the issue first, then create the handoff.

---

## Output Location

Save as: `0f-AUDIT-HANDOFF.md` in the project's audit folder or 0f admin folder.

---

*End of Handoff Prompt*
