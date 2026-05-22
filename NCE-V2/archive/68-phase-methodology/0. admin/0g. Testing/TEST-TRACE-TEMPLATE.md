# TEST-TRACE.md Template

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Map every test to its specification source
Fill out: During Phases 55-60, complete in Phase 60
---

# Test Traceability Matrix

## Overview

| Metric | Value |
|--------|-------|
| Total Specs | {{count}} |
| Specs with Tests | {{count}} |
| Coverage | {{percent}}% |
| Last Updated | {{date}} |

---

## API Endpoint Coverage

**Source:** API-SURFACE.md

| Endpoint | Method | Test File | Test Name | Status |
|----------|--------|-----------|-----------|--------|
| `/api/auth/login` | POST | `integration/auth.api.test.ts` | `logs in with valid credentials` | ✅ |
| `/api/auth/login` | POST | `integration/auth.api.test.ts` | `returns 401 for invalid password` | ✅ |
| `/api/auth/login` | POST | `integration/auth.api.test.ts` | `returns 400 for missing email` | ✅ |
| `/api/auth/logout` | POST | `integration/auth.api.test.ts` | `logs out authenticated user` | ✅ |
| `/api/projects` | GET | `integration/projects.api.test.ts` | `returns list of projects` | ✅ |
| `/api/projects` | POST | `integration/projects.api.test.ts` | `creates project with valid data` | ✅ |
| `/api/projects/:id` | GET | `integration/projects.api.test.ts` | `returns project by id` | ✅ |
| `/api/projects/:id` | PUT | `integration/projects.api.test.ts` | `updates project` | ✅ |
| `/api/projects/:id` | DELETE | `integration/projects.api.test.ts` | `deletes project` | ✅ |
| {{endpoint}} | {{method}} | {{file}} | {{test}} | ☐ |

### Endpoint Coverage Summary

| Resource | Endpoints | Tested | Coverage |
|----------|-----------|--------|----------|
| Auth | {{n}} | {{n}} | {{%}} |
| Projects | {{n}} | {{n}} | {{%}} |
| {{resource}} | {{n}} | {{n}} | {{%}} |
| **Total** | **{{n}}** | **{{n}}** | **{{%}}** |

---

## Error Code Coverage

**Source:** ERROR-CODES.md

| Error Code | Description | Test File | Test Name | Status |
|------------|-------------|-----------|-----------|--------|
| `AUTH_001` | Invalid credentials | `integration/auth.api.test.ts` | `returns 401 for invalid password` | ✅ |
| `AUTH_002` | Token expired | `integration/auth.api.test.ts` | `returns 401 for expired token` | ✅ |
| `VALIDATION_001` | Missing required field | `integration/projects.api.test.ts` | `returns 400 for missing name` | ✅ |
| `NOT_FOUND_001` | Resource not found | `integration/projects.api.test.ts` | `returns 404 for invalid id` | ✅ |
| {{code}} | {{description}} | {{file}} | {{test}} | ☐ |

### Error Coverage Summary

| Category | Codes | Tested | Coverage |
|----------|-------|--------|----------|
| Auth | {{n}} | {{n}} | {{%}} |
| Validation | {{n}} | {{n}} | {{%}} |
| Not Found | {{n}} | {{n}} | {{%}} |
| **Total** | **{{n}}** | **{{n}}** | **{{%}}** |

---

## Component Coverage

**Source:** COMPONENTS.md

| Component | Prop/Behaviour | Test File | Test Name | Status |
|-----------|----------------|-----------|-----------|--------|
| Button | renders children | `component/Button.test.tsx` | `renders with children` | ✅ |
| Button | onClick event | `component/Button.test.tsx` | `calls onClick when clicked` | ✅ |
| Button | disabled state | `component/Button.test.tsx` | `is disabled when prop is true` | ✅ |
| Button | variants | `component/Button.test.tsx` | `applies variant classes` | ✅ |
| Input | value binding | `component/Input.test.tsx` | `displays value` | ✅ |
| Input | onChange event | `component/Input.test.tsx` | `calls onChange on input` | ✅ |
| Input | error state | `component/Input.test.tsx` | `shows error message` | ✅ |
| {{component}} | {{behaviour}} | {{file}} | {{test}} | ☐ |

### Component Coverage Summary

| Component | Behaviours | Tested | Coverage |
|-----------|------------|--------|----------|
| Button | {{n}} | {{n}} | {{%}} |
| Input | {{n}} | {{n}} | {{%}} |
| {{component}} | {{n}} | {{n}} | {{%}} |
| **Total** | **{{n}}** | **{{n}}** | **{{%}}** |

---

## Page/Flow Coverage

**Source:** PAGES.md

| Page | Flow | Test File | Test Name | Status |
|------|------|-----------|-----------|--------|
| Login | Happy path | `e2e/auth.e2e.ts` | `user can log in` | ✅ |
| Login | Invalid credentials | `e2e/auth.e2e.ts` | `shows error for wrong password` | ✅ |
| Dashboard | View stats | `e2e/dashboard.e2e.ts` | `displays user stats` | ✅ |
| Projects | Create | `e2e/projects.e2e.ts` | `user can create project` | ✅ |
| Projects | Edit | `e2e/projects.e2e.ts` | `user can edit project` | ✅ |
| Projects | Delete | `e2e/projects.e2e.ts` | `user can delete project` | ✅ |
| {{page}} | {{flow}} | {{file}} | {{test}} | ☐ |

### Page Coverage Summary

| Page | Flows | Tested | Coverage |
|------|-------|--------|----------|
| Login | {{n}} | {{n}} | {{%}} |
| Dashboard | {{n}} | {{n}} | {{%}} |
| Projects | {{n}} | {{n}} | {{%}} |
| **Total** | **{{n}}** | **{{n}}** | **{{%}}** |

---

## Accessibility Coverage

**Source:** A11Y-CHECKLIST.md

| Requirement | Test Type | Test File | Test Name | Status |
|-------------|-----------|-----------|-----------|--------|
| Keyboard navigation | Manual | - | Tab through app | ✅ |
| Focus indicators | Manual | - | Visible focus | ✅ |
| Screen reader | Manual | - | VoiceOver test | ✅ |
| No critical violations | Automated | `a11y/pages.a11y.test.ts` | `home page passes axe` | ✅ |
| Form labels | Automated | `a11y/forms.a11y.test.ts` | `all inputs have labels` | ✅ |
| {{requirement}} | {{type}} | {{file}} | {{test}} | ☐ |

### A11y Coverage Summary

| Type | Requirements | Tested | Coverage |
|------|--------------|--------|----------|
| Automated | {{n}} | {{n}} | {{%}} |
| Manual | {{n}} | {{n}} | {{%}} |
| **Total** | **{{n}}** | **{{n}}** | **{{%}}** |

---

## Unit Test Coverage

**Source:** Business logic from implementation

| Module | Function | Test File | Test Name | Status |
|--------|----------|-----------|-----------|--------|
| utils/date | formatDate | `unit/date.test.ts` | `formats ISO to readable` | ✅ |
| utils/date | parseDate | `unit/date.test.ts` | `parses valid date string` | ✅ |
| utils/validation | validateEmail | `unit/validation.test.ts` | `validates correct email` | ✅ |
| utils/validation | validateEmail | `unit/validation.test.ts` | `rejects invalid email` | ✅ |
| {{module}} | {{function}} | {{file}} | {{test}} | ☐ |

---

## Database Operation Coverage

**Source:** DATABASE-SCHEMA.md

| Table | Operation | Test File | Test Name | Status |
|-------|-----------|-----------|-----------|--------|
| users | INSERT | `integration/users.db.test.ts` | `creates user record` | ✅ |
| users | SELECT | `integration/users.db.test.ts` | `finds user by email` | ✅ |
| users | UPDATE | `integration/users.db.test.ts` | `updates user profile` | ✅ |
| users | DELETE | `integration/users.db.test.ts` | `deletes user` | ✅ |
| projects | INSERT | `integration/projects.db.test.ts` | `creates project` | ✅ |
| {{table}} | {{operation}} | {{file}} | {{test}} | ☐ |

---

## Negative Flow Coverage

**Required:** At least one negative flow per feature

| Feature | Negative Flow | Test File | Test Name | Status |
|---------|---------------|-----------|-----------|--------|
| Auth | Invalid login | `e2e/auth.e2e.ts` | `shows error for wrong password` | ✅ |
| Auth | Expired token | `integration/auth.api.test.ts` | `returns 401 for expired token` | ✅ |
| Projects | Forbidden access | `e2e/projects.e2e.ts` | `cannot access others project` | ✅ |
| Projects | Invalid data | `integration/projects.api.test.ts` | `returns 400 for missing name` | ✅ |
| {{feature}} | {{flow}} | {{file}} | {{test}} | ☐ |

---

## Coverage Gaps

### Specs Without Tests

| Spec | Location | Reason | Action |
|------|----------|--------|--------|
| {{spec}} | {{file}} | {{why}} | {{what}} |

### Tests Without Spec Mapping

| Test File | Test Name | Action |
|-----------|-----------|--------|
| {{file}} | {{test}} | {{what}} |

---

## Summary

### Overall Coverage

| Area | Specs | Tested | Coverage | Target | Status |
|------|-------|--------|----------|--------|--------|
| API Endpoints | {{n}} | {{n}} | {{%}} | 100% | ☐ |
| Error Codes | {{n}} | {{n}} | {{%}} | 100% | ☐ |
| Components | {{n}} | {{n}} | {{%}} | 100% | ☐ |
| Pages/Flows | {{n}} | {{n}} | {{%}} | 100% | ☐ |
| Accessibility | {{n}} | {{n}} | {{%}} | 100% | ☐ |
| Unit (LOC) | - | - | {{%}} | 70% min | ☐ |

### Verification

- [ ] All API endpoints have tests
- [ ] All error codes are tested
- [ ] All components have tests
- [ ] All page flows have E2E tests
- [ ] All negative flows covered
- [ ] All a11y requirements verified
- [ ] Unit coverage ≥ 70%
- [ ] No unmapped tests

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Tester | | | ☐ Traceability Complete |
| Reviewer | | | ☐ Coverage Approved |

---
Generated: {{timestamp}}
Phase: 60 (Test Verification)
---
