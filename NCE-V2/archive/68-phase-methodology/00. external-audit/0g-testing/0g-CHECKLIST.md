# 0g Testing Audit Checklist

---
Section: 0g Testing (Phases 54-60)
Purpose: Verification checklist for external auditor
---

## Test Planning (Phase 54)

### TEST-PLAN.md
- [ ] File exists
- [ ] Testing strategy defined
- [ ] Test types identified (unit, integration, E2E)
- [ ] Tools and frameworks specified
- [ ] Test environment defined

### COVERAGE-TARGETS.md
- [ ] File exists
- [ ] Unit test coverage target defined
- [ ] Integration test coverage target defined
- [ ] Critical path coverage identified

---

## Unit Tests (Phase 55-56)

### Coverage
- [ ] All FUNCTIONS.md functions have unit tests
- [ ] All ERRORS.md error paths have tests
- [ ] Edge cases covered
- [ ] Coverage meets target (typically ≥80%)

### Quality
- [ ] Tests are focused (one assertion concept per test)
- [ ] Tests are independent (no shared state)
- [ ] Tests are deterministic (same result every run)
- [ ] Tests use appropriate mocks/stubs

---

## Integration Tests (Phase 57-58)

### Coverage
- [ ] Component interactions tested
- [ ] Database operations tested
- [ ] API endpoints tested
- [ ] External service integrations tested (mocked)

### Quality
- [ ] Tests clean up after themselves
- [ ] Tests can run in isolation
- [ ] Tests don't depend on external services

---

## E2E Tests (Phase 59)

### Coverage
- [ ] Critical user journeys covered
- [ ] Happy paths tested
- [ ] Error scenarios tested
- [ ] Authentication flows tested

### Quality
- [ ] Tests are stable (not flaky)
- [ ] Tests run in reasonable time
- [ ] Tests work in CI environment

---

## Test Traceability (Critical)

### TEST-TRACE.md
- [ ] File exists
- [ ] Every spec file has test mapping
- [ ] Format: Spec → Test File → Test Cases

### Traceability Quality
- [ ] No spec'd functions without tests
- [ ] No orphan tests (tests not tied to specs)
- [ ] Error codes all have tests
- [ ] Validation rules all have tests

---

## Test Results (Phase 60)

### TEST-VERIFICATION.md
- [ ] File exists
- [ ] All test results documented
- [ ] Pass/fail counts accurate

### Results Quality
- [ ] All tests pass
- [ ] Tests passed 3x consecutively (no flakiness)
- [ ] No skipped tests (or justification provided)
- [ ] Test execution time reasonable

### Coverage Report
- [ ] Coverage report generated
- [ ] Coverage meets targets
- [ ] No critical gaps

---

## Bug Status

- [ ] All critical bugs fixed
- [ ] All high bugs fixed
- [ ] Medium/low bugs documented for future
- [ ] No open blockers

---

## Flaky Tests

- [ ] Flaky tests identified (if any)
- [ ] Flaky tests documented
- [ ] Flaky tests either fixed or quarantined
- [ ] No flaky tests in critical paths

---

## Handoff Readiness

### 0g-COMPLETION-CRITERIA.md
- [ ] All criteria satisfied
- [ ] Sign-off recorded

### 0g-to-0h-HANDOFF.md
- [ ] File exists
- [ ] All deliverables listed
- [ ] Test artifacts included
- [ ] Known issues documented

---

## Consistency Delta (vs Section 0f)

- [ ] Tests cover all implemented code
- [ ] No implementation without tests
- [ ] Test expectations match implementation

---

## Red Flags (Automatic Critical)

- [ ] Spec'd function has no test
- [ ] Coverage significantly below target (>10% gap)
- [ ] Critical path untested
- [ ] Tests consistently failing
- [ ] Flaky tests in critical paths

---

*End of 0g Checklist*
