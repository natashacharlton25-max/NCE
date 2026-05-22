# Master Testing Guide — Phases 54-60

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Complete guide to 0g Testing section
---

## Overview

### What Is 0g?

**0g Testing** creates and executes test plans that verify the implementation from 0f matches the specifications from 0c-0e.

### Core Distinction

| Section | Question Answered |
|---------|-------------------|
| 0f Implementation | "Did we build it?" (implementation matches spec) |
| 0g Testing | "Does it work?" (behaviour matches expectations) |

### Key Principle

**Test against specs, not implementation.**

```
✅ RIGHT: "Login endpoint returns 401 for invalid credentials" (from API-SURFACE.md)
❌ WRONG: "authService.validatePassword returns false" (implementation detail)
```

Tests remain valid when code is refactored, as long as contracts hold.

---

## Phase Summary

| Phase | Name | Focus | Collapsible |
|-------|------|-------|-------------|
| 54 | Test Planning | Strategy, scope, tools, environment | **NEVER** |
| 55 | Unit Testing | Functions, utilities, business logic | Yes |
| 56 | Integration Testing | API endpoints, database | Yes |
| 57 | Component Testing | UI components (mocked) | Yes |
| 58 | E2E Testing | User flows | Yes |
| 59 | Accessibility Testing | A11y verification | Yes |
| 60 | Test Verification | Coverage, traceability, final report | **NEVER** |

### Collapsing Rules

```
ALLOWED:
├── Phases 55-57 → "Unit & Component Tests"
├── Phases 58-59 → "E2E & A11y Tests"
└── Any adjacent phases within a block

NEVER COLLAPSE:
├── Phase 54 (Test Planning) — Strategy must exist
└── Phase 60 (Test Verification) — Final check required
```

**Even when collapsing:** TEST-PLAN.md and TEST-TRACE.md are always required.

---

## Test Pyramid

```
        /\
       /  \  E2E Tests (few, critical paths)
      /----\  
     /      \  Integration Tests (all endpoints)
    /--------\
   /          \  Unit Tests (business logic)
  --------------
     Component Tests (all UI components)
```

| Layer | What | Quantity | Speed |
|-------|------|----------|-------|
| Unit | Functions, utilities, business logic | Many | Fast |
| Integration | API endpoints, database queries | All endpoints | Medium |
| Component | UI components (mocked data) | All components | Medium |
| E2E | Full user flows | Critical paths | Slow |

---

## Coverage Targets

| Type | Target | Minimum | Failure Rule |
|------|--------|---------|--------------|
| Unit tests | 80% | 70% | Below minimum = Phase 60 fails |
| Integration tests | All endpoints | All critical | Missing endpoint = Phase 60 fails |
| Component tests | All components | All interactive | Missing component = Phase 60 fails |
| E2E tests | All user flows | Happy paths + negatives | Missing flow = Phase 60 fails |

---

## Core Rules

### 1. Tests Must Be Deterministic

Tests must not rely on:
- Real time (use mocked/controlled time)
- Randomness (seed random generators or use fixtures)
- External services (mock all external dependencies)
- Execution order (each test must be independent)

**Execution Order Rule:** Tests must be executable in any order and in isolation.

**If a test fails, it must fail consistently.** A test that fails intermittently is a bug.

### 2. 0g Finds, 0f Fixes

| Role | Responsibility |
|------|----------------|
| 0g (Tester) | Discover bugs, document, report |
| 0f (Implementer) | Fix bugs, re-verify |

**0g never fixes code. 0g tests code.**

### 3. Bug Fixes Return to 0f

All fixes for bugs discovered in 0g must:
1. Return to 0f for implementation
2. Re-pass Phase 53 verification
3. Return to 0g for re-testing

No mid-phase patching. No "quick fixes" during testing.

### 4. Failure Ownership

| Failure Layer | Root Cause | Owner |
|---------------|------------|-------|
| Unit test | Logic or data rule wrong | 0f |
| Integration test | API or schema mismatch | 0f or 0c |
| Component test | Component contract issue | 0f or 0e |
| E2E test | System integration or state | 0f |

**Triage rule:** Start at the lowest failing layer.

---

## System Invariants

These are the foundational truths of the Fullspecs methodology. When something feels "wrong" but no rule is technically violated, check these invariants.

| Invariant | Meaning |
|-----------|---------|
| Specs are the single source of truth | Code is disposable; specs are not |
| Verification artifacts must be reproducible | Any artifact must be recreatable from scratch on a clean machine |
| shared-types define the contract boundary | Not convenience — the actual API contract |
| A green state must be achievable cleanly | Tests must pass on any machine with correct setup |
| No undocumented knowledge may unblock progress | If it's not written down, it doesn't exist |
| Tests verify specs, not implementation | If code diverges from spec, file a bug — don't "fix" the test |

**When in doubt:** If an action would violate an invariant, STOP and escalate.

---

## Global Stop Conditions

If any of these conditions occur, STOP and escalate. Do not continue.

| Condition | Action |
|-----------|--------|
| TEST-TRACE.md cannot reach 100% spec coverage | STOP — Missing specs or tests |
| More than one spec requires deviation | STOP — Systematic spec issue |
| IMPLEMENTATION-LOG.md cannot be completed honestly | STOP — Implementation gap |
| Tests require modifying specs to pass | STOP — Return to 0c/0d/0e |
| Flakiness cannot be eliminated | STOP — Infrastructure issue |
| Coverage target cannot be met without exclusions | STOP — Justify or add tests |

**"Progress theatre" is forbidden.** If progress requires dishonesty, the system has failed upstream.

---

## Traceability Annotation Standard

All tests must use JSDoc-style `@spec` annotations for traceability.

### Standard Format

```typescript
/**
 * @spec {{DOCUMENT}}#{{SECTION}}
 * @verifies {{WHAT_IT_VERIFIES}}
 */
it('test description', () => {
  // ...
});
```

### Spec Reference Format

| Document | Section Format | Example |
|----------|----------------|---------|
| API-SURFACE.md | `#METHOD-/path` | `#POST-/api/projects` |
| ERROR-CODES.md | `#ERROR_CODE` | `#VALIDATION_ERROR` |
| COMPONENTS.md | `#ComponentName` | `#Button` |
| PAGES.md | `#PageName` | `#Dashboard` |
| TYPES.md | `#TypeName` | `#User` |
| A11Y-CHECKLIST.md | `#Category` | `#Keyboard` |

This standard format enables automatic TEST-TRACE.md generation.

---

## Test Environment Contract

### Isolation Rules

| Rule | Requirement |
|------|-------------|
| Database | Isolated test database, not dev database |
| State reset | Database reset between test runs |
| Data independence | No test may rely on developer-local data |
| Environment variables | All test env vars documented |
| External services | All external services mocked |

### Reset Strategy Options

| Strategy | When to Use |
|----------|-------------|
| Transaction rollback | Fast, for unit/integration |
| Truncate + re-seed | Clean slate, for E2E |
| Snapshot restore | Complex data, for E2E |

---

## Test Data Strategy

### The Problem

Using development seeds for testing creates bias — tests pass because they match the "happy path" data.

### The Solution: Test Factories

| Approach | When |
|----------|------|
| Factory functions | Generate test data on demand |
| Test fixtures | Static, version-controlled test data |
| Snapshot isolation | Separate test database snapshots |

### Rules

1. Tests should NOT rely on specific seed data from 0f
2. Tests should generate or load their own test data
3. Test data factories must create valid data per TYPES.md
4. Edge case data must be explicitly constructed

---

## Inputs from Previous Sections

### From 0f Implementation

| Artifact | Purpose in 0g |
|----------|---------------|
| Working backend | Test target |
| Working frontend | Test target |
| shared-types/ | Test data typing |
| IMPLEMENTATION-LOG.md | Test scope reference |
| BACKEND-VERIFICATION-REPORT.md | Don't duplicate — extend |
| 0f-to-0g-HANDOFF.md | Entry contract |

### From Earlier Sections (Specs to Test Against)

| Artifact | Test Against |
|----------|--------------|
| API-SURFACE.md | API behaviour |
| DATABASE-SCHEMA.md | Data integrity |
| ERROR-CODES.md | Error handling |
| PAGES.md | Page behaviour |
| COMPONENTS.md | Component behaviour |
| STATE-MANAGEMENT.md | State behaviour |
| A11Y-CHECKLIST.md | Accessibility |

---

## Outputs

### Test Artifacts

| Artifact | Contains |
|----------|----------|
| Unit tests | Function/module level tests |
| Integration tests | API endpoint tests |
| Component tests | UI component tests (mocked) |
| E2E tests | Full user flow tests |
| Accessibility tests | A11y verification |

### Documentation Artifacts

| Artifact | Contains |
|----------|----------|
| TEST-PLAN.md | Strategy, scope, tools, environment |
| TEST-TRACE.md | Maps tests to specs |
| COVERAGE-REPORT.md | Coverage summary |
| TEST-RESULTS.md | Final results |
| BUG-REPORTS/ | Any bugs discovered |

---

## Bug Handling

### Bug vs Deviation

| Type | Definition | Owner |
|------|------------|-------|
| Bug | Implementation doesn't match spec | 0f fixes |
| Deviation | Spec needs to change | 0c/0d/0e decides |

### Bug Severity

| Severity | Definition | Action |
|----------|------------|--------|
| Critical | App crashes, data loss, security | Block release, fix immediately |
| High | Feature broken | Fix before release |
| Medium | Feature impaired | Fix or document |
| Low | Minor issue | Fix if time |

### Bug Fix Flow

```
0g discovers bug
    ↓
Create BUG-REPORT
    ↓
Classify severity
    ↓
Continue testing (don't stop)
    ↓
After 0g complete: Return to 0f
    ↓
0f fixes bug + re-passes Phase 53
    ↓
Return to 0g for re-testing
    ↓
Update bug report: RESOLVED
```

---

## Phase 60 Pass Criteria

Phase 60 may only pass if:

- [ ] All tests pass (green)
- [ ] Flakiness check passes (3x consecutive)
- [ ] Unit test coverage ≥ minimum (70%)
- [ ] All endpoints have integration tests
- [ ] All interactive components have tests
- [ ] All critical user flows have E2E tests
- [ ] All negative flows have E2E tests
- [ ] A11y tests pass
- [ ] TEST-TRACE.md is complete
- [ ] All critical/high bugs are resolved

**No exceptions without documented deviation approval.**

---

## Flakiness Check

Run the full test suite 3 times consecutively:

```bash
npm test && npm test && npm test
```

| Result | Status |
|--------|--------|
| 3 passes | ✅ Deterministic — proceed |
| 2 passes, 1 fail | ❌ Flaky — fix before proceeding |
| 1 pass, 2 fails | ❌ Flaky — fix before proceeding |
| 3 fails | ❌ Broken — fix before proceeding |

**Phase 60 cannot pass if any test is flaky.**

---

## What 0g Does NOT Do

| Item | Why | Owner |
|------|-----|-------|
| Fix bugs | 0g finds, 0f fixes | 0f |
| Change specs | 0g tests against specs | 0c/0d/0e |
| Performance testing | Separate concern | 0h |
| Security testing | Separate concern | 0h |
| Load testing | Separate concern | 0h |
| CI/CD setup | Deployment concern | 0h |

---

## Quick Reference

| Rule | Summary |
|------|---------|
| Test against specs | Not implementation details |
| Deterministic | No randomness, time, external deps |
| Isolated environment | Test DB, reset between runs |
| Factory data | Don't rely on dev seeds |
| Component mocks | Isolated from live backend |
| E2E DB verification | Check database, not just UI |
| Flakiness check | 3x consecutive pass |
| Traceability | Every test → spec |
| 0g finds, 0f fixes | Clear ownership |
| Phase 54/60 | Never collapse |

---

## Files in 0g Section

### Admin (`0. Admin/0g. Testing/`)

| File | Purpose |
|------|---------|
| Master-Testing-Guide 54to60.md | This document |
| Claude-testing.md | Instructions for Claude Code |
| TEST-PLAN-TEMPLATE.md | Test planning template |
| TEST-TRACE-TEMPLATE.md | Traceability matrix |
| BUG-REPORT-TEMPLATE.md | Bug documentation |
| COVERAGE-TARGETS.md | Coverage requirements |
| 0g-to-0h-HANDOFF.md | Handoff contract |

### Phase Folders

| Phase | Folder | Files |
|-------|--------|-------|
| 54 | 54. Test-Planning/ | PHASE-54-PROMPT.md |
| 55 | 55. Unit-Testing/ | PHASE-55-PROMPT.md |
| 56 | 56. Integration-Testing/ | PHASE-56-PROMPT.md |
| 57 | 57. Component-Testing/ | PHASE-57-PROMPT.md |
| 58 | 58. E2E-Testing/ | PHASE-58-PROMPT.md |
| 59 | 59. Accessibility-Testing/ | PHASE-59-PROMPT.md |
| 60 | 60. Test-Verification/ | PHASE-60-PROMPT.md |

---
Generated: {{timestamp}}
Section: 0g Testing
Phases: 54-60
---
