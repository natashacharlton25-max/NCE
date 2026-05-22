# Coverage Targets

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Define and enforce test coverage requirements
Reference: Used in Phase 60 (Test Verification)
---

## Overview

This document defines the minimum coverage requirements for 0g Testing to pass.

**Phase 60 cannot pass unless all targets are met or explicitly approved deviations are documented.**

---

## Code Coverage Targets

### Unit Test Coverage (Line Coverage)

| Target | Minimum | Failure Rule |
|--------|---------|--------------|
| 80% | 70% | Below 70% = Phase 60 fails |

### What Counts

| Counts Toward Coverage | Does Not Count |
|------------------------|----------------|
| Business logic | Test files |
| Utility functions | Type definitions |
| Validators | Configuration files |
| Data transformations | Generated code |
| Service functions | Third-party libraries |

### Exclusions

These may be excluded from coverage calculation:
- Test files (`*.test.ts`, `*.spec.ts`)
- Type definition files (`*.d.ts`)
- Configuration files (`*.config.ts`)
- Generated files (ORM clients, etc.)

---

## Spec Coverage Targets

### API Endpoints

| Metric | Target | Minimum |
|--------|--------|---------|
| Endpoint coverage | 100% | All critical paths |
| Happy path tests | 100% | 100% |
| Error path tests | 100% | All documented errors |

**Every endpoint in API-SURFACE.md must have:**
- At least one happy path test
- Tests for documented error codes
- Auth tests (if protected)

### Components

| Metric | Target | Minimum |
|--------|--------|---------|
| Component coverage | 100% | All interactive |
| Prop testing | All props | Required props |
| Event testing | All events | Critical events |
| State testing | All states | Interactive states |

**Every component in COMPONENTS.md must have:**
- Render test
- Props handling tests
- Event emission tests (if events defined)
- State variant tests (if stateful)

### Pages and Flows

| Metric | Target | Minimum |
|--------|--------|---------|
| Page coverage | 100% | Critical pages |
| Happy path flows | 100% | 100% |
| Negative flows | 1+ per feature | 1+ per feature |

**Every page in PAGES.md must have:**
- At least one E2E flow
- Database verification for mutations
- At least one negative/error flow

### Error Codes

| Metric | Target | Minimum |
|--------|--------|---------|
| Error code coverage | 100% | All critical |

**Every error code in ERROR-CODES.md must be triggered by at least one test.**

### Accessibility

| Metric | Target | Minimum |
|--------|--------|---------|
| A11y checklist | 100% | 100% |
| Automated a11y | No critical violations | No critical violations |
| Manual a11y | All checks complete | All checks complete |

**Every item in A11Y-CHECKLIST.md must be verified.**

---

## Traceability Requirements

### TEST-TRACE.md Completeness

| Requirement | Status |
|-------------|--------|
| Every API endpoint mapped | Required |
| Every error code mapped | Required |
| Every component mapped | Required |
| Every page flow mapped | Required |
| Every a11y item mapped | Required |
| No orphan tests | Required |

**"Orphan test"** = A test that doesn't map to any spec. These must be either:
- Mapped to a spec
- Justified and documented
- Removed

---

## Pass/Fail Criteria

### Automatic Pass

Phase 60 passes automatically if:
- [ ] All tests pass (green)
- [ ] Flakiness check passes (3x consecutive)
- [ ] Unit coverage ≥ 70%
- [ ] All endpoints have tests
- [ ] All components have tests
- [ ] All flows have E2E tests
- [ ] All negative flows covered
- [ ] All a11y items verified
- [ ] TEST-TRACE.md complete
- [ ] No orphan tests
- [ ] All critical/high bugs resolved

### Conditional Pass (Requires Approval)

If targets are not met but close:
1. Document deviation in DEVIATION-REPORT
2. Provide justification
3. Get explicit approval
4. Proceed with approval documented

### Automatic Fail

Phase 60 fails automatically if:
- Any test is flaky (fails on any of 3 runs)
- Unit coverage < 70%
- Critical bug unresolved
- TEST-TRACE.md incomplete

---

## Measurement

### Coverage Report Generation

```bash
# Generate coverage report
npm run test:coverage

# Expected output location
./coverage/lcov-report/index.html
```

### Coverage Report Contents

The coverage report must show:
- Line coverage percentage
- Branch coverage percentage
- Function coverage percentage
- Files below threshold

### Spec Coverage Calculation

```
Spec Coverage = (Specs with tests / Total specs) × 100%

Where "Specs with tests" is counted from TEST-TRACE.md
```

---

## Coverage Exclusion Rules

### Legitimate Exclusions

These may be excluded from coverage requirements:

| Exclusion | Justification |
|-----------|---------------|
| Third-party integrations | Tested by provider |
| Framework boilerplate | Not custom code |
| Generated code | Regenerated on change |
| Dead code flagged for removal | Will be deleted |

### Illegitimate Exclusions

These may NOT be excluded:

| Cannot Exclude | Reason |
|----------------|--------|
| "Too complex" | Complexity = higher risk |
| "No time" | Coverage is non-negotiable |
| "Will add later" | Later never comes |
| "It obviously works" | Obvious bugs happen |

---

## Examples

### Good: Meeting Targets

```
Unit Coverage: 82% (target: 80%, min: 70%) ✅
API Coverage: 100% (15/15 endpoints) ✅
Component Coverage: 100% (23/23 components) ✅
E2E Coverage: 100% (8/8 flows) ✅
A11y Coverage: 100% (12/12 items) ✅

Status: PASS
```

### Acceptable: Deviation Approved

```
Unit Coverage: 68% (target: 80%, min: 70%) ⚠️
API Coverage: 100% (15/15 endpoints) ✅
Component Coverage: 100% (23/23 components) ✅
E2E Coverage: 100% (8/8 flows) ✅
A11y Coverage: 100% (12/12 items) ✅

Deviation: DEVIATION-REPORT-007.md
Approval: Product Owner, 2025-01-28
Reason: Generated ORM code inflating denominator
Action: Excluded generated files from calculation
Recalculated: 74% ✅

Status: PASS (with approved deviation)
```

### Fail: Below Minimum

```
Unit Coverage: 58% (target: 80%, min: 70%) ❌
API Coverage: 80% (12/15 endpoints) ❌
Component Coverage: 100% (23/23 components) ✅
E2E Coverage: 75% (6/8 flows) ❌
A11y Coverage: 100% (12/12 items) ✅

Status: FAIL
Action: Add missing tests before Phase 60 can pass
```

---

## Quick Reference

| Area | Target | Minimum | Hard Fail |
|------|--------|---------|-----------|
| Unit LOC | 80% | 70% | < 70% |
| Endpoints | 100% | Critical | Missing critical |
| Components | 100% | Interactive | Missing interactive |
| Flows | 100% | Happy + 1 negative | No E2E |
| Errors | 100% | Critical | Missing critical |
| A11y | 100% | 100% | Missing items |
| Flakiness | 0 | 0 | Any flaky test |

---
Generated: {{timestamp}}
Section: 0g Testing
Reference: Phase 60 verification
---
