# Section 0g Completion Criteria

---
Project: {{project_name}}
Section: 0g Testing (Phases 54-60)
Status: TEMPLATE
---

## Purpose

This document defines explicit completion criteria for Section 0g (Testing). All criteria must be met before proceeding to Section 0h (Prerelease).

---

## Phase Completion

### Phase 54: Test Planning
- [ ] TEST-PLAN.md complete
- [ ] Test strategy defined (unit, integration, E2E, accessibility)
- [ ] COVERAGE-TARGETS.md defined
- [ ] Test environment requirements documented
- [ ] Human approved

### Phase 55: Unit Testing
- [ ] Unit tests written for all components
- [ ] Coverage meets target: {{target}}%
- [ ] All tests passing
- [ ] No flaky tests (3x consecutive pass)
- [ ] Human approved

### Phase 56: Integration Testing
- [ ] Integration tests written for all interfaces
- [ ] API contracts verified
- [ ] Database integration tested
- [ ] External service mocks/stubs working
- [ ] All tests passing
- [ ] Human approved

### Phase 57: Component Testing
- [ ] Component tests written for all UI components
- [ ] Component isolation verified
- [ ] State management tested
- [ ] All tests passing
- [ ] Human approved

### Phase 58: E2E Testing
- [ ] E2E tests written for critical user journeys
- [ ] Happy paths covered
- [ ] Error paths covered
- [ ] All tests passing (3x consecutive)
- [ ] Human approved

### Phase 59: Accessibility Testing
- [ ] A11Y-CHECKLIST.md verified
- [ ] Automated accessibility tests passing
- [ ] Manual accessibility review complete
- [ ] WCAG compliance level met: {{level}}
- [ ] Human approved

### Phase 60: Test Verification
- [ ] TEST-TRACE.md complete (spec-to-test mapping)
- [ ] All specs have corresponding tests
- [ ] Coverage report generated
- [ ] TEST-VERIFICATION.md complete
- [ ] No flaky tests remaining
- [ ] All BUG-REPORTs resolved or documented
- [ ] Human approved

---

## Coverage Requirements

| Type | Target | Actual | Met |
|------|--------|--------|-----|
| Unit test coverage | {{target}}% | | [ ] |
| Integration test coverage | {{target}}% | | [ ] |
| E2E critical paths | 100% | | [ ] |
| Accessibility compliance | {{level}} | | [ ] |

---

## Test Quality Criteria

| Criterion | Requirement | Status |
|-----------|-------------|--------|
| Flakiness | 0 flaky tests (3x pass required) | [ ] |
| Determinism | All tests produce consistent results | [ ] |
| Independence | Tests can run in any order | [ ] |
| Speed | Test suite runs in < {{time}} | [ ] |
| Isolation | Tests don't affect each other | [ ] |

---

## Traceability Requirements

| Requirement | Status |
|-------------|--------|
| Every spec in 0c has at least one test | [ ] |
| Every error code has error path test | [ ] |
| Every API endpoint has integration test | [ ] |
| Every user journey has E2E test | [ ] |
| TEST-TRACE.md is complete and accurate | [ ] |

---

## Bug Status

| Category | Count | Status |
|----------|-------|--------|
| Critical bugs | | Must be 0 |
| High bugs | | Must be 0 |
| Medium bugs | | Documented |
| Low bugs | | Documented |

All critical and high severity bugs must be resolved before proceeding.

---

## Required Files

| File | Status | Location |
|------|--------|----------|
| TEST-PLAN.md | [ ] | 54. Test Planning/ |
| COVERAGE-TARGETS.md | [ ] | 0. Admin/0g. Testing/ |
| TEST-TRACE.md | [ ] | 60. Test Verification/ |
| TEST-VERIFICATION.md | [ ] | 60. Test Verification/ |
| Coverage report | [ ] | {{location}} |
| BUG-REPORT(s) | [ ] | If any |
| 0g-to-0h-HANDOFF.md | [ ] | 0. Admin/0g. Testing/ |

---

## Handoff Checklist

Before proceeding to 0h:

### Code Artifacts
- [ ] All test files committed
- [ ] Test configuration committed
- [ ] CI test pipeline working
- [ ] Coverage reports accessible

### Documentation
- [ ] TEST-PLAN.md finalized
- [ ] TEST-TRACE.md complete
- [ ] TEST-VERIFICATION.md complete
- [ ] All BUG-REPORTs closed or documented

### Quality Gates
- [ ] Coverage targets met
- [ ] No flaky tests
- [ ] No critical/high bugs open
- [ ] 3x consecutive test suite pass

### Sign-Offs
- [ ] Developer sign-off
- [ ] QA sign-off (if applicable)
- [ ] Human approval

---

## External Audit Checkpoint

If external audit is performed:

- [ ] Audit requested
- [ ] Handoff package prepared
- [ ] Audit completed
- [ ] Findings reviewed
- [ ] Critical findings resolved
- [ ] Audit report filed

**Audit Status:** NOT STARTED | IN PROGRESS | COMPLETE | SKIPPED

---

## Final Approval

| Criterion | Met |
|-----------|-----|
| All phases complete | [ ] |
| All coverage targets met | [ ] |
| All tests passing | [ ] |
| No flaky tests | [ ] |
| No critical/high bugs | [ ] |
| TEST-TRACE complete | [ ] |
| Handoff document ready | [ ] |

### Sign-Off

| Role | Name | Date | Approved |
|------|------|------|----------|
| Developer | | | [ ] |
| QA | | | [ ] |
| Human | | | [ ] |

---

## Section Status

**0g Testing Status:** NOT STARTED | IN PROGRESS | COMPLETE

**Completion Date:** _______________

**Ready for 0h:** YES / NO

---
Template Version: 1.0.0
---
