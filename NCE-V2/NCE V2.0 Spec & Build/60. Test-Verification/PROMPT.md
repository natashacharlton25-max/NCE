# Phase 60: Test Verification

---
Section: 0g Testing
Phase: 60 of 60
Previous: Phase 59 (Accessibility Testing)
Next: 0h Prerelease (Phase 61)
Collapsible: **NEVER** — Final verification required
---

## Purpose

Final verification that all testing is complete, coverage targets are met, and the system is ready for prerelease.

**This phase CANNOT be skipped or collapsed.**

---

## Inputs Required

| Document | Location | Purpose |
|----------|----------|---------|
| TEST-PLAN.md | Project root | Verify strategy followed |
| TEST-TRACE.md | Project root | Verify completeness |
| All test files | tests/ | Run and verify |
| BUG-REPORTS/ | Project root | Verify resolved |
| COVERAGE-TARGETS.md | Admin | Check targets |
| **0g-COMPLETION-CRITERIA.md** | `0. Admin/0g. Testing/` | **Required pass criteria** |

**IMPORTANT:** Before starting Phase 60, read `0g-COMPLETION-CRITERIA.md` for binding pass/fail requirements.

---

## Steps

### Step 1: Run All Tests

```bash
npm test
```

**Requirement:** All tests must pass.

| Result | Action |
|--------|--------|
| All pass | ✅ Proceed |
| Any fail | ❌ Fix before proceeding |

### Step 2: Flakiness Check (REQUIRED)

Run the full test suite **3 times consecutively**:

```bash
npm test && npm test && npm test
```

Or scripted:

```bash
#!/bin/bash
for i in 1 2 3; do
  echo "Run $i of 3"
  npm test || { echo "FAILED on run $i"; exit 1; }
done
echo "FLAKINESS CHECK PASSED"
```

**Evaluation:**

| Result | Status | Action |
|--------|--------|--------|
| 3 passes | ✅ Deterministic | Proceed |
| 2 passes, 1 fail | ❌ Flaky | Fix the flaky test |
| 1 pass, 2 fails | ❌ Flaky | Fix the flaky test |
| 3 fails | ❌ Broken | Fix the broken test |

**Phase 60 cannot pass if any test is flaky.**

### Step 3: Generate Coverage Report

```bash
npm run test:coverage
```

Record results:

```markdown
## Coverage Report

| Metric | Value | Target | Minimum | Status |
|--------|-------|--------|---------|--------|
| Line coverage | {{%}} | 80% | 70% | ☐ |
| Branch coverage | {{%}} | - | - | Info |
| Function coverage | {{%}} | - | - | Info |
```

**Requirement:** Line coverage ≥ 70%

### Step 3b: Verify Coverage Exclusions

Ensure the coverage exclusion configuration matches COVERAGE-TARGETS.md.

**Check configuration file:**

```typescript
// vitest.config.ts or jest.config.ts
coverage: {
  exclude: [
    '**/*.test.ts',
    '**/*.spec.ts',
    '**/*.d.ts',
    '**/generated/**',
    '**/*.config.*',
  ],
}
```

**Cross-reference with COVERAGE-TARGETS.md:**

| Exclusion in Config | Matches "What Counts" Table | Status |
|---------------------|----------------------------|--------|
| `**/*.test.ts` | Test files - correct | ☐ |
| `**/*.d.ts` | Type definitions - correct | ☐ |
| `**/generated/**` | Generated code - correct | ☐ |

**If mismatch found:**
- Update config to match COVERAGE-TARGETS.md
- Re-run coverage report
- Document any approved deviations

### Step 4: Verify TEST-TRACE.md Completeness

Open TEST-TRACE.md and verify all sections are complete:

#### API Endpoint Coverage

```markdown
| Metric | Count | Status |
|--------|-------|--------|
| Total endpoints | {{n}} | - |
| Endpoints with tests | {{n}} | - |
| Coverage | {{%}} | ☐ Target: 100% |
```

**All endpoints must have tests.**

#### Error Code Coverage

```markdown
| Metric | Count | Status |
|--------|-------|--------|
| Total error codes | {{n}} | - |
| Error codes tested | {{n}} | - |
| Coverage | {{%}} | ☐ Target: 100% |
```

**All error codes must be triggered by tests.**

#### Component Coverage

```markdown
| Metric | Count | Status |
|--------|-------|--------|
| Total components | {{n}} | - |
| Components with tests | {{n}} | - |
| Coverage | {{%}} | ☐ Target: 100% |
```

**All interactive components must have tests.**

#### Page/Flow Coverage

```markdown
| Metric | Count | Status |
|--------|-------|--------|
| Total flows | {{n}} | - |
| Happy paths tested | {{n}} | - |
| Negative flows tested | {{n}} | - |
| Coverage | {{%}} | ☐ Target: 100% |
```

**All flows must have tests. Each feature needs at least one negative flow.**

#### Accessibility Coverage

```markdown
| Metric | Count | Status |
|--------|-------|--------|
| A11y checklist items | {{n}} | - |
| Items verified | {{n}} | - |
| Coverage | {{%}} | ☐ Target: 100% |
```

**All A11Y-CHECKLIST.md items must be verified.**

### Step 5: Review Bug Reports

Check BUG-REPORTS/ directory:

```markdown
| Bug ID | Severity | Status | Resolution |
|--------|----------|--------|------------|
| BUG-001 | High | RESOLVED | Fixed in 0f |
| BUG-002 | Medium | RESOLVED | Fixed in 0f |
| BUG-003 | Low | WONT_FIX | Documented |
```

**Requirements:**
- All CRITICAL bugs: RESOLVED
- All HIGH bugs: RESOLVED
- MEDIUM bugs: RESOLVED or justified
- LOW bugs: RESOLVED, WONT_FIX, or documented

### Step 6: Create TEST-RESULTS.md

```markdown
# Test Results

## Summary

| Metric | Value |
|--------|-------|
| Date | {{date}} |
| Total tests | {{n}} |
| Passed | {{n}} |
| Failed | 0 |
| Skipped | 0 |
| Flakiness check | PASSED |

## Coverage

| Area | Target | Actual | Status |
|------|--------|--------|--------|
| Unit (LOC) | 70% | {{%}} | ✅ |
| API endpoints | 100% | 100% | ✅ |
| Components | 100% | 100% | ✅ |
| Page flows | 100% | 100% | ✅ |
| A11y items | 100% | 100% | ✅ |

## Test Distribution

| Type | Count |
|------|-------|
| Unit tests | {{n}} |
| Integration tests | {{n}} |
| Component tests | {{n}} |
| E2E tests | {{n}} |
| A11y tests | {{n}} |
| **Total** | **{{n}}** |

## Bugs

| Severity | Found | Resolved | Open |
|----------|-------|----------|------|
| Critical | {{n}} | {{n}} | 0 |
| High | {{n}} | {{n}} | 0 |
| Medium | {{n}} | {{n}} | {{n}} |
| Low | {{n}} | {{n}} | {{n}} |

## Verification

- [x] All tests pass
- [x] Flakiness check passed (3x)
- [x] Coverage targets met
- [x] TEST-TRACE.md complete
- [x] All critical/high bugs resolved
- [x] A11y verified

## Conclusion

**Status: READY FOR 0h PRERELEASE**
```

### Step 7: Create COVERAGE-REPORT.md

```markdown
# Coverage Report

## Generated

- Date: {{date}}
- Tool: {{coverage_tool}}
- Report location: ./coverage/lcov-report/index.html

## Summary

| Metric | Value |
|--------|-------|
| Statements | {{%}} |
| Branches | {{%}} |
| Functions | {{%}} |
| Lines | {{%}} |

## By Directory

| Directory | Lines | Branches | Status |
|-----------|-------|----------|--------|
| src/utils/ | {{%}} | {{%}} | ✅ |
| src/services/ | {{%}} | {{%}} | ✅ |
| src/components/ | {{%}} | {{%}} | ✅ |

## Files Below Threshold

| File | Coverage | Reason |
|------|----------|--------|
| (none) | - | - |

## Exclusions

| Pattern | Reason |
|---------|--------|
| **/*.d.ts | Type definitions |
| **/*.config.* | Configuration |
| **/generated/** | Generated code |
```

### Step 8: Complete Handoff Checklist

Review 0g-to-0h-HANDOFF.md and verify all items:

```markdown
## Handoff Checklist

### Tests Ready
- [x] All tests pass locally
- [x] Flakiness check passed (3x consecutive)
- [x] Coverage meets all targets
- [x] No critical/high bugs open

### Documentation Ready
- [x] TEST-PLAN.md complete
- [x] TEST-TRACE.md complete (no gaps)
- [x] COVERAGE-REPORT.md generated
- [x] TEST-RESULTS.md shows all pass
- [x] All BUG-REPORTS resolved or documented

### Quality Gates
- [x] Unit coverage ≥ 70%
- [x] All endpoints have tests
- [x] All components have tests
- [x] All flows have E2E tests
- [x] All negative flows covered
- [x] A11y requirements verified

### Environment Ready
- [x] Test environment documented
- [x] Test data strategy documented
- [x] All test dependencies listed
- [x] `npm test` is CI-ready (exits 1 on failure)
```

---

## Pass/Fail Criteria

### Automatic Pass

Phase 60 passes if ALL are true:

| Criterion | Verification |
|-----------|--------------|
| All tests pass | `npm test` exits 0 |
| Flakiness check passes | 3x consecutive pass |
| Unit coverage ≥ 70% | Coverage report |
| All endpoints have tests | TEST-TRACE.md |
| All components have tests | TEST-TRACE.md |
| All flows have E2E tests | TEST-TRACE.md |
| All negative flows covered | TEST-TRACE.md |
| A11y tests pass | Test results |
| TEST-TRACE.md complete | No gaps |
| No critical/high bugs open | BUG-REPORTS/ |

### Conditional Pass (Requires Approval)

If targets not fully met:

1. Document in DEVIATION-REPORT
2. Provide justification
3. Get explicit approval
4. Record approval in TEST-RESULTS.md

### Automatic Fail

Phase 60 fails if ANY are true:

| Failure Condition | Action |
|-------------------|--------|
| Any test fails | Fix test or implementation |
| Flaky test detected | Fix flakiness |
| Coverage < 70% | Add more tests |
| Critical bug unresolved | Return to 0f |
| TEST-TRACE.md incomplete | Complete traceability |

---

## Output

By end of Phase 60, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| TEST-RESULTS.md | Project root | ☐ Created |
| COVERAGE-REPORT.md | Project root | ☐ Created |
| TEST-TRACE.md | Project root | ☐ Complete |
| All tests | tests/ | ☐ Passing |
| Flakiness check | - | ☐ Passed |
| Handoff checklist | - | ☐ Complete |

---

## Final Checklist

**All items must be checked before proceeding to 0h:**

### Test Execution
- [ ] All tests pass
- [ ] Flakiness check passed (ran 3x, all passed)

### Coverage Verification
- [ ] Unit coverage ≥ 70%
- [ ] All API endpoints covered (TEST-TRACE.md)
- [ ] All components covered (TEST-TRACE.md)
- [ ] All page flows covered (TEST-TRACE.md)
- [ ] All negative flows covered (TEST-TRACE.md)
- [ ] All a11y items verified (TEST-TRACE.md)

### Documentation
- [ ] TEST-PLAN.md complete
- [ ] TEST-TRACE.md complete (no gaps)
- [ ] TEST-RESULTS.md created
- [ ] COVERAGE-REPORT.md created

### Bug Resolution
- [ ] All critical bugs resolved
- [ ] All high bugs resolved
- [ ] Medium/low bugs resolved or documented

### Handoff Ready
- [ ] 0g-to-0h-HANDOFF.md checklist complete
- [ ] All artifacts in place
- [ ] Ready for prerelease

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Tester | | | ☐ Testing Complete |
| Reviewer | | | ☐ Results Verified |
| 0h Lead | | | ☐ Ready for Prerelease |

---

## SECTION HANDOFF

**Create `0g-to-0h-HANDOFF.md`** in project admin folder:

```markdown
# 0g → 0h Section Handoff

---
Created: {{timestamp}}
Section Completed: 0g Testing (Phases 54-60)
Next Section: 0h Prerelease (Phases 61-64)
---

## Summary

0g Testing is COMPLETE. All tests pass, flakiness check passed, coverage targets met, and all bugs resolved.

## Key Artifacts Created

| Artifact | Location | Status |
|----------|----------|--------|
| TEST-PLAN.md | Project root | APPROVED |
| TEST-TRACE.md | Project root | Complete (no gaps) |
| TEST-RESULTS.md | Project root | All pass |
| COVERAGE-REPORT.md | Project root | Targets met |
| tests/ | /tests/ | All passing |
| BUG-REPORTS/ | Project root | All resolved |

## Test Summary

| Metric | Value |
|--------|-------|
| Total tests | {{n}} |
| All pass | ✅ |
| Flakiness check | PASSED (3x) |
| Line coverage | {{%}} (≥70%) |

## Coverage Summary

| Area | Target | Actual | Status |
|------|--------|--------|--------|
| Unit (LOC) | 70% | {{%}} | ✅ |
| API endpoints | 100% | 100% | ✅ |
| Components | 100% | 100% | ✅ |
| Page flows | 100% | 100% | ✅ |
| A11y items | 100% | 100% | ✅ |

## Bug Resolution

| Severity | Found | Resolved | Open |
|----------|-------|----------|------|
| Critical | {{n}} | {{n}} | 0 |
| High | {{n}} | {{n}} | 0 |
| Medium | {{n}} | {{n}} | {{n}} |
| Low | {{n}} | {{n}} | {{n}} |

## Handoff Verification

- [ ] All tests pass
- [ ] Flakiness check passed (3x consecutive)
- [ ] Coverage targets met
- [ ] TEST-TRACE.md complete (no gaps)
- [ ] All critical/high bugs resolved
- [ ] TEST-RESULTS.md created
- [ ] COVERAGE-REPORT.md created

**Ready to begin 0h Prerelease.**
```

---

## What Happens Next

After Phase 60 passes:

1. **0g Testing is COMPLETE**
2. **All artifacts are locked** — No changes without returning to 0g
3. **Create `0g-to-0h-HANDOFF.md`** (see above)
4. **Proceed to 0h Prerelease** (Phase 61)

If issues found in 0h:
- Test issues → Return to 0g
- Implementation issues → Return to 0f → Re-test in 0g
- Both must re-pass Phase 60

---
Generated: {{timestamp}}
Phase: 60 (Test Verification)
Section: 0g Testing
---
