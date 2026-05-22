# Implementation Checklist

---
Project: {{project_name}}
Date: {{date}}
Implementer: {{name}}
Version: {{commit_hash}}
Status: COMPLETE | INCOMPLETE
---

## Summary

| Section | Total Items | Verified | Status |
|---------|-------------|----------|--------|
| Backend | | | |
| Frontend | | | |
| Integration | | | |
| Quality | | | |
| Documentation | | | |

---

## Backend Verification

### Endpoints

| Endpoint | Method | Implemented | Tested | Status |
|----------|--------|-------------|--------|--------|
| /api/auth/login | POST | ☐ | ☐ | |
| /api/auth/register | POST | ☐ | ☐ | |
| /api/auth/logout | POST | ☐ | ☐ | |
| /api/users/me | GET | ☐ | ☐ | |
| /api/users/me | PUT | ☐ | ☐ | |
| {{add all from API-SURFACE.md}} | | | | |

### Backend Checklist

- [ ] All endpoints from API-SURFACE.md implemented
- [ ] All endpoints verified in Phase 47
- [ ] BACKEND-VERIFICATION-REPORT.md shows all pass
- [ ] Auth system works (login, logout, token refresh)
- [ ] Error responses match ERROR-CODES.md format
- [ ] Database schema matches DATABASE-SCHEMA.md
- [ ] All responses use shared-types/
- [ ] No `any` types in responses

---

## Frontend Verification

### Pages

| Page | Route | Implemented | Guard Works | States | Status |
|------|-------|-------------|-------------|--------|--------|
| Home | / | ☐ | N/A | ☐ | |
| Login | /auth/login | ☐ | Guest | ☐ | |
| Dashboard | /dashboard | ☐ | Auth | ☐ | |
| {{add all from PAGES.md}} | | | | | |

### Components

| Component | Implemented | Props | Events | A11y | Status |
|-----------|-------------|-------|--------|------|--------|
| Button | ☐ | ☐ | ☐ | ☐ | |
| Input | ☐ | ☐ | ☐ | ☐ | |
| {{add all from COMPONENTS.md}} | | | | | |

### Frontend Checklist

- [ ] All pages from PAGES.md implemented
- [ ] All components from COMPONENTS.md implemented
- [ ] All route guards work per spec
- [ ] All design tokens from DESIGN-SYSTEM.md used
- [ ] State management follows STATE-MANAGEMENT.md
- [ ] Loading states implemented
- [ ] Error states implemented
- [ ] Empty states implemented (where applicable)

---

## Integration Verification

### API Connections

| Component/Page | Endpoint | Connected | Works | Status |
|----------------|----------|-----------|-------|--------|
| LoginForm | /auth/login | ☐ | ☐ | |
| Dashboard | /projects | ☐ | ☐ | |
| {{add all from API-MAPPING.md}} | | | | |

### Integration Checklist

- [ ] All API-MAPPING.md connections wired
- [ ] Data displays correctly from API
- [ ] Mutations work (create, update, delete)
- [ ] State updates on data changes
- [ ] Auth flow complete (login → token → user)
- [ ] Error handling works end-to-end
- [ ] Type safety maintained (no `any` leaks)

---

## Quality Verification

### Performance

| Metric | Target | Actual | Meets Target |
|--------|--------|--------|--------------|
| Bundle size (JS) | {{from spec}} | {{measured}} | ☐ |
| Bundle size (CSS) | {{from spec}} | {{measured}} | ☐ |
| Largest Contentful Paint | {{from spec}} | {{measured}} | ☐ |
| First Input Delay | {{from spec}} | {{measured}} | ☐ |
| Cumulative Layout Shift | {{from spec}} | {{measured}} | ☐ |

**If any target not met:**
- Documented in DEVIATION-REPORT: {{DEV-XXX}}
- Reason: {{explanation}}

### Accessibility

| Check | Status | Notes |
|-------|--------|-------|
| Keyboard navigation works | ☐ | |
| Focus indicators visible | ☐ | |
| Color contrast passes (4.5:1 text, 3:1 UI) | ☐ | |
| Form inputs have labels | ☐ | |
| Errors announced to screen readers | ☐ | |
| No keyboard traps | ☐ | |
| Skip links present (if applicable) | ☐ | |

### Code Quality

- [ ] No TypeScript errors
- [ ] No console errors in dev
- [ ] No console warnings in dev
- [ ] Linting passes
- [ ] File size limits respected (~300 LOC target)

---

## Documentation Verification

### Required Documents

| Document | Complete | Notes |
|----------|----------|-------|
| IMPLEMENTATION-LOG.md | ☐ | All phases logged |
| BACKEND-VERIFICATION-REPORT.md | ☐ | From Phase 47 |
| README.md | ☐ | Setup instructions |
| .env.example | ☐ | All variables |

### README Must Include

- [ ] Project description
- [ ] Prerequisites (Node version, etc.)
- [ ] Installation steps
- [ ] Environment setup
- [ ] How to run dev server
- [ ] How to run seeds
- [ ] How to run backend
- [ ] How to run frontend

---

## Deviation Summary

| ID | Phase | Description | Status |
|----|-------|-------------|--------|
| {{DEV-XXX}} | {{phase}} | {{description}} | Approved/Rejected |

**All deviations must be APPROVED or REJECTED. No OPEN allowed.**

- [ ] All deviation reports resolved

---

## Commit History

| Check | Status |
|-------|--------|
| Commits are meaningful (not "WIP", "fix", etc.) | ☐ |
| Each phase has identifiable commit(s) | ☐ |
| No secrets in commit history | ☐ |
| .gitignore prevents sensitive files | ☐ |

**Total commits:** {{count}}
**Average commits per phase:** {{average}}

---

## Environment Verification

### Can Start Fresh

- [ ] Clone repo
- [ ] Install dependencies (single command)
- [ ] Set up environment variables (documented)
- [ ] Run database migrations
- [ ] Run seeds
- [ ] Start backend server
- [ ] Start frontend server
- [ ] Access application in browser

### Environment Variables

| Variable | Documented | Required |
|----------|------------|----------|
| DATABASE_URL | ☐ | Yes |
| JWT_SECRET | ☐ | Yes |
| API_PORT | ☐ | Yes |
| {{add all}} | | |

---

## Final Checklist

### Implementation Complete

- [ ] All backend endpoints match API-SURFACE.md
- [ ] All frontend pages match PAGES.md
- [ ] All components match COMPONENTS.md
- [ ] All state matches STATE-MANAGEMENT.md
- [ ] All integration matches API-MAPPING.md

### Quality Complete

- [ ] Performance constraints met (or documented)
- [ ] Accessibility basics pass
- [ ] No TypeScript errors
- [ ] Commit history clean

### Documentation Complete

- [ ] IMPLEMENTATION-LOG.md complete
- [ ] README.md complete
- [ ] All deviations resolved

### Handoff Ready

- [ ] Dev environment reproducible
- [ ] All tests from Phase 47 still pass
- [ ] Application works end-to-end

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Implementer | | | ☐ Implementation Complete |
| Reviewer (if applicable) | | | ☐ Verified |

---

## Handoff Statement

**Status:** ☐ READY FOR 0g TESTING

**Blocking issues:** {{none / list issues}}

**Notes for testing team:**
{{Any context, known issues, or areas needing attention}}

---
Template: IMPLEMENTATION-CHECKLIST-TEMPLATE.md
Phase: 53
Section: 0f. Implementation
---
