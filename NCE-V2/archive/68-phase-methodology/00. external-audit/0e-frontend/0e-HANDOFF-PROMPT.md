# 0e Audit Handoff Prompt

---
Section: 0e Frontend (Phases 37-42)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 42, before external audit (OPTIONAL)
---

## Instructions for Claude

You have completed Section 0e (Frontend). Prepare a handoff package for external audit review.

**Note:** This audit is OPTIONAL but recommended if frontend is complex.

---

## Create: 0e-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- What frontend specs were created
- How they map to the API surface
- Overall readiness for frontend implementation

### 2. Self-Assessment Scores

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | All pages/components specified? |
| Consistency | /5 | Specs match API contract? |
| Accuracy | /5 | Specs are implementable? |
| Methodology | /5 | Templates followed? |
| Quality | /5 | Documentation clear? |
| Readiness | /5 | Ready for implementation? |

### 3. Concerns & Uncertainties

- Complex interactions that might need refinement
- State management concerns
- Accessibility gaps

### 4. Frontend Summary

**Pages:** {{count}}
**Components:** {{count}}
**API Mappings:** {{count}}

### 5. Contract Verification

| Check | Status |
|-------|--------|
| FRONTEND-BACKEND-CONTRACT.md created | |
| All API endpoints mapped | |
| All types imported | |
| All error codes handled | |

### 6. Key Decisions Made

| Decision | Rationale |
|----------|-----------|
| | |

### 7. File Manifest

| File | Location | Status |
|------|----------|--------|
| FRONTEND-BACKEND-CONTRACT.md | | |
| DESIGN-INPUT.md | /37. Frontend Design Input/ | |
| PAGES.md | /38. Page Specs/ | |
| PAGE-{{name}}.md | /38. Page Specs/ | |
| COMPONENTS.md | /39. Component Specs/ | |
| COMPONENT-{{name}}.md | /39. Component Specs/ | |
| STATE-MANAGEMENT.md | /40. State Management/ | |
| API-MAPPING.md | /41. API Integration Mapping/ | |
| A11Y-CHECKLIST.md | | |
| FRONTEND-CHECKLIST.md | /42. Frontend Ready Check/ | |

### 8. Dependency Bundles

| Bundle | Primary File | Related Files |
|--------|--------------|---------------|
| Contract | FRONTEND-BACKEND-CONTRACT.md | API-SURFACE.md |
| Pages | PAGES.md | All PAGE-*.md files |
| Components | COMPONENTS.md | All COMPONENT-*.md files |
| State | STATE-MANAGEMENT.md | API-MAPPING.md |
| Accessibility | A11Y-CHECKLIST.md | COMPONENTS.md, PAGES.md |
| Gate | FRONTEND-CHECKLIST.md | 0e-to-0f-HANDOFF.md |

### 9. Recommended Files for Review

1. **FRONTEND-BACKEND-CONTRACT.md** - API binding
2. **PAGES.md** - Page overview
3. **STATE-MANAGEMENT.md** - State approach
4. **API-MAPPING.md** - Frontend-API connection
5. **A11Y-CHECKLIST.md** - Accessibility
6. **FRONTEND-CHECKLIST.md** - Gate status

---

## Pre-Handoff Verification

- [ ] FRONTEND-BACKEND-CONTRACT.md complete
- [ ] All pages specified
- [ ] All components specified
- [ ] API mappings complete
- [ ] Accessibility requirements documented
- [ ] FRONTEND-CHECKLIST.md complete

---

## Output Location

Save as: `0e-AUDIT-HANDOFF.md` in `00. external-audit/0e-frontend/`

---

*End of Handoff Prompt*
