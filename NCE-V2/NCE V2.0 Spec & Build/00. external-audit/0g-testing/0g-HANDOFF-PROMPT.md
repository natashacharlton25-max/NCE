# 0g Audit Handoff Prompt

---
Section: 0g Testing (Phases 54-60)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 60, before external audit (RECOMMENDED)
---

## Instructions for Claude

You have completed Section 0g (Testing). Prepare a handoff package for external audit review.

**Note:** This audit is RECOMMENDED. Test quality directly affects production confidence.

---

## Create: 0g-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- Testing approach taken
- Coverage achieved
- Overall confidence in test suite

### 2. Self-Assessment Scores

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | All specs have tests? |
| Consistency | /5 | Tests follow patterns? |
| Accuracy | /5 | Tests are correct? |
| Methodology | /5 | Testing rules followed? |
| Quality | /5 | Tests are maintainable? |
| Readiness | /5 | Ready for prerelease? |

### 3. Concerns & Uncertainties

- Areas with lower coverage
- Tests that might be flaky
- Edge cases not fully covered

### 4. Coverage Summary

| Type | Target | Actual | Status |
|------|--------|--------|--------|
| Unit Tests | {{%}} | {{%}} | ✅/⚠️/❌ |
| Integration Tests | {{%}} | {{%}} | ✅/⚠️/❌ |
| E2E Tests | {{count}} | {{count}} | ✅/⚠️/❌ |

### 5. Test Traceability Summary

| Spec File | Tests | Coverage |
|-----------|-------|----------|
| {{component}}/FUNCTIONS.md | {{count}} | {{%}} |
| {{component}}/ERRORS.md | {{count}} | {{%}} |

### 6. Test Results

| Category | Passed | Failed | Skipped |
|----------|--------|--------|---------|
| Unit | | | |
| Integration | | | |
| E2E | | | |
| **Total** | | | |

### 7. Key Decisions Made

| Decision | Rationale |
|----------|-----------|
| | |

### 8. File Manifest

| File | Location | Status |
|------|----------|--------|
| TEST-PLAN.md | | |
| COVERAGE-TARGETS.md | | |
| TEST-TRACE.md | | |
| Unit test files | /tests/unit/ | |
| Integration test files | /tests/integration/ | |
| E2E test files | /tests/e2e/ | |
| TEST-VERIFICATION.md | | |
| 0g-to-0h-HANDOFF.md | | |

### 9. Dependency Bundles

| Bundle | Primary File | Related Files |
|--------|--------------|---------------|
| Strategy | TEST-PLAN.md | COVERAGE-TARGETS.md |
| Traceability | TEST-TRACE.md | All spec files |
| Results | Coverage report | TEST-VERIFICATION.md |
| Gate | TEST-VERIFICATION.md | 0g-to-0h-HANDOFF.md |

### 10. Recommended Files for Review

1. **TEST-TRACE.md** - Spec-to-test mapping
2. **Coverage report** - Actual coverage
3. **TEST-VERIFICATION.md** - Test results
4. **0g-to-0h-HANDOFF.md** - Ready for prerelease?
5. **Flaky test list** (if any)

---

## Pre-Handoff Verification

- [ ] All spec'd functions have tests
- [ ] Coverage meets targets
- [ ] All tests pass (3x consecutive)
- [ ] No flaky tests (or documented)
- [ ] TEST-TRACE.md complete
- [ ] 0g-COMPLETION-CRITERIA.md satisfied

---

## Output Location

Save as: `0g-AUDIT-HANDOFF.md` in `00. external-audit/0g-testing/`

---

*End of Handoff Prompt*
