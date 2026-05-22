# Frontend Checklist — {{Project Name}}

---
Status: DRAFT | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
---

## Document Verification

| Document | Exists | Approved | Version | Date |
|----------|--------|----------|---------|------|
| FRONTEND-BACKEND-CONTRACT.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |
| DESIGN-SYSTEM.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |
| PAGES.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |
| COMPONENTS.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |
| STATE-MANAGEMENT.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |
| API-MAPPING.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |
| A11Y-CHECKLIST.md | ✅ / ❌ | ✅ / ❌ | {{version}} | {{date}} |

**Status:** All documents exist and approved / Missing documents

---

## Contract Verification

| Check | Status | Notes |
|-------|--------|-------|
| All public endpoints listed | ✅ / ❌ | |
| All endpoints have mock data | ✅ / ❌ | |
| All error codes documented | ✅ / ❌ | |
| No unresolved gaps | ✅ / ❌ | |

**Gaps Found:** None / {{list gaps}}

---

## Page Verification

| Check | Status | Notes |
|-------|--------|-------|
| All routes specified | ✅ / ❌ | {{n}} pages |
| All route guards defined | ✅ / ❌ | |
| All data requirements listed | ✅ / ❌ | |
| All states defined (loading/error/empty) | ✅ / ❌ | |
| All components listed | ✅ / ❌ | |

**Pages Count:** {{n}}
**Missing Pages:** None / {{list}}

---

## Component Verification

| Check | Status | Notes |
|-------|--------|-------|
| All page components defined | ✅ / ❌ | |
| All props documented | ✅ / ❌ | |
| All events documented | ✅ / ❌ | |
| All API calls mapped | ✅ / ❌ | |
| No orphaned components | ✅ / ❌ | |

**Components Count:** {{n}}
**Orphaned Components:** None / {{list}}

---

## State Verification

| Check | Status | Notes |
|-------|--------|-------|
| Server state defined | ✅ / ❌ | |
| Client state defined | ✅ / ❌ | |
| Persisted state documented | ✅ / ❌ | |
| Anti-patterns noted | ✅ / ❌ | |

---

## API Mapping Verification

| Check | Status | Notes |
|-------|--------|-------|
| All screens mapped to endpoints | ✅ / ❌ | |
| All components mapped to endpoints | ✅ / ❌ | |
| No missing endpoints | ✅ / ❌ | |
| No orphaned endpoints (or documented reason) | ✅ / ❌ | |
| Error handling defined for all calls | ✅ / ❌ | |
| Auth requirements match route guards | ✅ / ❌ | |

**Missing Endpoints:** None / {{list}}
**Orphaned Endpoints:** None / {{list with reasons}}

---

## Accessibility Verification

| Category | Pass | Fail | Notes |
|----------|------|------|-------|
| Keyboard Navigation | /{{ }} | /{{ }} | |
| Screen Reader | /{{ }} | /{{ }} | |
| Colour & Contrast | /{{ }} | /{{ }} | |
| Focus Management | /{{ }} | /{{ }} | |
| Forms & Errors | /{{ }} | /{{ }} | |
| Motion & Animation | /{{ }} | /{{ }} | |

**A11Y Checklist:** `frontend/A11Y-CHECKLIST.md` — ✅ Complete / ❌ Incomplete

**Testing Done:**
- [ ] axe DevTools (automated)
- [ ] Keyboard-only navigation
- [ ] Screen reader (VoiceOver/NVDA)
- [ ] Zoom 200%
- [ ] Reduced motion

---

## Cross-Reference Checks

### Pages → Components

| Page | Components Referenced | All Exist? |
|------|----------------------|------------|
| {{page}} | {{components}} | ✅ / ❌ |
| {{page}} | {{components}} | ✅ / ❌ |

**Missing Components:** None / {{list}}

### Components → Contract

| Component | API Calls | All in Contract? |
|-----------|-----------|------------------|
| {{component}} | {{endpoints}} | ✅ / ❌ |
| {{component}} | {{endpoints}} | ✅ / ❌ |

**Missing Endpoints:** None / {{list}}

### Contract → Error Handling

| Error Code | UI Handling Defined? |
|------------|---------------------|
| {{code}} | ✅ / ❌ |
| {{code}} | ✅ / ❌ |

**Unhandled Errors:** None / {{list}}

---

## Consistency Checks

| Check | Status | Notes |
|-------|--------|-------|
| Naming consistent | ✅ / ❌ | |
| Types match | ✅ / ❌ | |
| Route guards match auth | ✅ / ❌ | |
| State matches component needs | ✅ / ❌ | |

---

## Critical Question

**Is there anything required to build the frontend that is not documented?**

### Common Items Checklist

| Item | Needed? | Documented? |
|------|---------|-------------|
| Third-party integrations (analytics) | Yes / No | ✅ / ❌ / N/A |
| Authentication provider setup | Yes / No | ✅ / ❌ / N/A |
| Asset hosting / CDN | Yes / No | ✅ / ❌ / N/A |
| Environment configuration | Yes / No | ✅ / ❌ / N/A |
| Browser support requirements | Yes / No | ✅ / ❌ / N/A |
| Performance requirements | Yes / No | ✅ / ❌ / N/A |
| SEO requirements | Yes / No | ✅ / ❌ / N/A |
| i18n / localization | Yes / No | ✅ / ❌ / N/A |
| Accessibility requirements | Yes / No | ✅ / ❌ / N/A |

**Missing Items:** None / {{list with where to document}}

---

## The "Different AI" Test

**Could a new AI with no conversation history build this frontend using only the 0e documents?**

| Check | Status | Gap If Failed |
|-------|--------|---------------|
| No assumed context | ✅ / ❌ | |
| No undocumented decisions | ✅ / ❌ | |
| All behaviours explicit | ✅ / ❌ | |
| All state flows clear | ✅ / ❌ | |
| All API integrations mapped | ✅ / ❌ | |

**Test Result:** ✅ Pass / ❌ Fail — {{reason}}

---

## Summary

| Category | Status |
|----------|--------|
| Documents | ✅ All approved / ❌ Issues |
| Contract | ✅ Complete / ❌ Gaps |
| Pages | ✅ Complete / ❌ Missing |
| Components | ✅ Complete / ❌ Orphans/Missing |
| State | ✅ Complete / ❌ Issues |
| API Mapping | ✅ Complete / ❌ Gaps |
| Accessibility | ✅ Pass / ❌ Issues |
| Cross-References | ✅ Pass / ❌ Mismatches |
| Consistency | ✅ Pass / ❌ Issues |
| Critical Question | ✅ Nothing missing / ❌ Items missing |
| Different AI Test | ✅ Pass / ❌ Fail |

---

## Final Verdict

**0e Frontend Status:** ✅ READY FOR IMPLEMENTATION / ❌ NEEDS WORK

**If NEEDS WORK, fix:**
1. {{issue 1}} — Return to Phase {{n}}
2. {{issue 2}} — Return to Phase {{n}}

---

## Sign-Off

| Role | Name | Date | Approval |
|------|------|------|----------|
| Human Lead | | | ☐ APPROVE |
| Claude | | | ☐ Verification Complete |

---

## Next Steps

On approval:
1. Archive 0e Frontend as complete
2. Begin 0f Implementation (Phase 43+)
3. Frontend implementation can start from these specs

---
Generated: {{timestamp}}
Phase: 42 (Frontend Ready Check)
Section: 0e Frontend
---
