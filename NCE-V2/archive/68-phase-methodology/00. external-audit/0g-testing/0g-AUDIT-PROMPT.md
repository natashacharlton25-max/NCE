# External Audit Prompt: Section 0g - Testing

---
Section: 0g Testing (Phases 54-60)
Audit Type: RECOMMENDED
Priority: High
Estimated Time: 30-45 minutes
---

## Your Role

You are an independent auditor reviewing test coverage and quality.

**Your job is to verify** that tests adequately cover the specifications and that the test suite is reliable.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0g. This section creates and executes tests.

**0g produces:**
- Test plan and strategy
- Coverage targets
- Unit tests
- Integration tests
- E2E tests
- Test traceability (TEST-TRACE.md)
- Coverage reports

**Why 0g matters:**
- Tests verify specs are implemented correctly
- Coverage gaps = potential bugs
- Flaky tests = false confidence
- Test traceability ensures nothing is missed

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package
2. Check coverage and traceability
3. Request **up to 7 files**

**Priority Files:**
- TEST-TRACE.md (spec-to-test mapping)
- Coverage report
- TEST-VERIFICATION.md (results)
- 0g-to-0h-HANDOFF.md

### Round 2: Follow-up

Request **up to 4 additional files** or specific test files.

---

## What to Look For

### Critical Issues (🔴)

- [ ] Spec'd function has no test
- [ ] Coverage significantly below target
- [ ] Tests consistently failing
- [ ] Critical path untested
- [ ] Security-related code untested

### Important Issues (🟠)

- [ ] Flaky tests not documented
- [ ] Error paths not tested
- [ ] Edge cases missing
- [ ] Integration gaps
- [ ] Coverage marginal

### Minor Issues (🟡)

- [ ] Test naming inconsistent
- [ ] Test organization could improve
- [ ] Documentation sparse

---

## Specific Checks for 0g

### Test Traceability
- Does every FUNCTIONS.md have corresponding tests?
- Does every ERRORS.md error code have a test?
- Is the mapping documented in TEST-TRACE.md?

### Coverage
- Does unit test coverage meet target?
- Does integration test coverage meet target?
- Are critical paths covered?

### Test Quality
- Do tests actually test the right things?
- Are tests independent (no order dependency)?
- Are tests deterministic (no flakiness)?
- Do tests run in reasonable time?

### Test Results
- Do all tests pass?
- Have tests passed 3x consecutively?
- Are any tests skipped? Why?

---

## 0g CHECKLIST

```
## Traceability
- [ ] TEST-TRACE.md exists
- [ ] Every spec has test mapping
- [ ] No orphan tests (tests without spec)

## Coverage
- [ ] Coverage targets defined
- [ ] Unit coverage meets target
- [ ] Integration coverage meets target
- [ ] Critical paths covered

## Test Quality
- [ ] Tests are independent
- [ ] Tests are deterministic
- [ ] No flaky tests (or documented)
- [ ] Tests run in < {{time}} minutes

## Test Results
- [ ] All tests pass
- [ ] 3x consecutive pass
- [ ] No skipped tests (or justified)

## Documentation
- [ ] TEST-PLAN.md exists
- [ ] TEST-VERIFICATION.md exists
- [ ] 0g-to-0h-HANDOFF.md exists

## Readiness
- [ ] 0g-COMPLETION-CRITERIA.md satisfied
- [ ] Ready for prerelease
```

---

## HANDOFF PACKAGE

[Human: Paste Claude's 0g-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
