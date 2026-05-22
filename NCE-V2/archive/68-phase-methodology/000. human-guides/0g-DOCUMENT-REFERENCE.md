# Section 0g: Complete Document Reference

---
**Section:** 0g Testing
**Phases:** 54-60
**Location:** `0. Admin/0g. Testing/` + Phase folders
---

## Admin Folder Documents

Location: `0. Admin/0g. Testing/`

| Document | What It Does | Who Uses It |
|----------|--------------|-------------|
| **Claude-testing.md** | Instructions for Claude. Defines testing approach, traceability rules. | Claude reads this |
| **Master-Testing-Guide 54to60.md** | Detailed guide for testing phases. | Reference for both |
| **TESTING-PROGRESS-TEMPLATE.md** | Template to track testing progress. | You fill this in |
| **TESTING-DECISION-NOTES-TEMPLATE.md** | Template to log decisions. | You fill this in |

---

## Phase-by-Phase Documents

### Phase 54: Test Planning

**Folder:** `54. Test-Planning/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-54-PROMPT.md** | Instructions for Claude. Tells Claude to create the overall test strategy. | Give to Claude |
| **TEST-PLAN-TEMPLATE.md** | Template for test strategy - what to test, how, coverage targets. | Claude creates TEST-PLAN.md |
| **COVERAGE-TARGETS-TEMPLATE.md** | Template for coverage goals per component. | Claude creates COVERAGE-TARGETS.md |

**Outputs:**
- `TEST-PLAN.md` — Testing strategy
- `COVERAGE-TARGETS.md` — Coverage goals

---

### Phases 55-56: Unit Tests

**Folders:** `55. Unit-Test-Specs/`, `56. Unit-Test-Implementation/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-55-PROMPT.md** | Instructions to spec unit tests for each function. | Give to Claude |
| **UNIT-TEST-SPEC-TEMPLATE.md** | Template for unit test definitions. | Claude creates specs |
| **PHASE-56-PROMPT.md** | Instructions to implement unit tests. | After Phase 55 |

**Outputs:**
- Unit test specifications
- Working unit tests

---

### Phases 57-58: Integration Tests

**Folders:** `57. Integration-Test-Specs/`, `58. Integration-Test-Implementation/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-57-PROMPT.md** | Instructions to spec integration tests. | Give to Claude |
| **INTEGRATION-TEST-SPEC-TEMPLATE.md** | Template for integration test definitions. | Claude creates specs |
| **PHASE-58-PROMPT.md** | Instructions to implement integration tests. | After Phase 57 |

**Outputs:**
- Integration test specifications
- Working integration tests

---

### Phase 59: E2E Tests

**Folder:** `59. E2E-Tests/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-59-PROMPT.md** | Instructions to create end-to-end tests for user journeys. | Give to Claude |
| **E2E-TEST-SPEC-TEMPLATE.md** | Template for E2E test scenarios. | Claude creates specs |

**Output:** E2E tests covering critical user paths

---

### Phase 60: Test Verification

**Folder:** `60. Test-Verification/`

| Document | What It Does | Order |
|----------|--------------|-------|
| **PHASE-60-PROMPT.md** | Instructions to run all tests and verify coverage. | Give to Claude |
| **TEST-TRACE-TEMPLATE.md** | Template mapping tests back to specs. | Claude creates TEST-TRACE.md |
| **TEST-VERIFICATION-TEMPLATE.md** | Template for test results summary. | Claude creates TEST-VERIFICATION.md |

**Outputs:**
- `TEST-TRACE.md` — Which tests verify which specs
- `TEST-VERIFICATION.md` — Results summary
- Coverage report

---

## Order to Run Section 0g

```
Step 1:  Read Claude-testing.md (understand section)
Step 2:  Phase 54 - Create TEST-PLAN.md + COVERAGE-TARGETS.md
Step 3:  Phases 55-56 - Spec and implement unit tests
Step 4:  Phases 57-58 - Spec and implement integration tests
Step 5:  Phase 59 - Create E2E tests
Step 6:  Phase 60 - Run tests, create TEST-TRACE.md and TEST-VERIFICATION.md
Step 7:  (Recommended) Run 0g audit
Step 8:  Fix any issues
Step 9:  Proceed to Section 0h
```

---

## Critical Document: TEST-TRACE.md

This document maps every test back to the spec it verifies:

| Test | Spec File | Section | What It Verifies |
|------|-----------|---------|------------------|
| test_user_login | User/spec/FUNCTIONS.md | login() | User can authenticate |
| test_payment_fail | Payment/spec/ERRORS.md | E401 | Invalid card handled |

**Every spec requirement should have at least one test.**

---

## Test Types Explained

| Type | What It Tests | Example |
|------|---------------|---------|
| **Unit** | Single function | "Does calculateTotal() return correct value?" |
| **Integration** | Components together | "Can UserService save to database?" |
| **E2E** | Complete user flow | "Can user sign up, log in, buy item?" |

---

## What You Should Have When Done

| Document | Location |
|----------|----------|
| TEST-PLAN.md | Phase 54 |
| COVERAGE-TARGETS.md | Phase 54 |
| Unit test code | /tests/unit/ |
| Integration test code | /tests/integration/ |
| E2E test code | /tests/e2e/ |
| TEST-TRACE.md | Phase 60 |
| TEST-VERIFICATION.md | Phase 60 |
| Coverage report | Generated |

---

*Give PHASE-XX-PROMPT.md to Claude for each phase. Claude uses the TEMPLATE files to create outputs.*
