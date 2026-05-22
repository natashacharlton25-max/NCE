# 0g → 0h Handoff Contract

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Binding contract between Testing (0g) and Prerelease (0h)
---

## What 0g Delivers

By the end of 0g Testing, these artifacts exist:

### Test Artifacts

| Artifact | Contains | Status |
|----------|----------|--------|
| Unit tests | Business logic, utilities tested | ☐ COMPLETE |
| Integration tests | All API endpoints tested | ☐ COMPLETE |
| Component tests | All components tested (mocked) | ☐ COMPLETE |
| E2E tests | Critical flows tested | ☐ COMPLETE |
| Accessibility tests | A11y requirements verified | ☐ COMPLETE |

### Documentation Artifacts

| Artifact | Contains | Status |
|----------|----------|--------|
| TEST-PLAN.md | Testing strategy, environment, tools | ☐ COMPLETE |
| TEST-TRACE.md | Every test mapped to spec | ☐ COMPLETE |
| COVERAGE-REPORT.md | Coverage metrics | ☐ COMPLETE |
| TEST-RESULTS.md | Final test execution results | ☐ COMPLETE |
| BUG-REPORTS/ | All bugs documented and resolved | ☐ COMPLETE |

---

## What 0g Guarantees

| Guarantee | Evidence | Verification |
|-----------|----------|--------------|
| All specs have tests | TEST-TRACE.md | Every spec row has test |
| All tests pass | TEST-RESULTS.md | Green status |
| No flaky tests | Flakiness check | 3x consecutive pass |
| Coverage meets targets | COVERAGE-REPORT.md | Targets met or deviation approved |
| Accessibility verified | A11y test results | All items passed |
| Bugs resolved | BUG-REPORTS/ | All critical/high closed |

---

## What 0g Does NOT Deliver

These are explicitly deferred to 0h Prerelease:

| Item | Why Deferred | Owner |
|------|--------------|-------|
| Performance tests | Production concern | 0h |
| Security tests | Production concern | 0h |
| Load tests | Production concern | 0h |
| CI/CD pipeline | Deployment concern | 0h |
| Deployment configuration | Deployment concern | 0h |
| Staging environment | Deployment concern | 0h |
| Production monitoring | Operations concern | 0h/0i |

---

## What 0h Must NOT Change

These are **locked** from 0g:

| Locked Item | Cannot Change | Escalation If Needed |
|-------------|---------------|----------------------|
| Test structure | No reorganizing tests | Return to 0g |
| Coverage targets | No lowering minimums | Return to 0g |
| Test-to-spec mapping | No breaking traceability | Return to 0g |
| Test environment config | No changing isolation | Return to 0g |
| Flakiness requirement | No accepting flaky tests | Return to 0g |

**If 0h discovers a need to change these:** STOP. Document. Escalate. Do not proceed without approval.

---

## What 0h MAY Do

Within the boundaries of 0g outputs:

| Allowed Action | Example | Constraint |
|----------------|---------|------------|
| Add performance tests | Load testing, benchmarks | Must not modify existing tests |
| Add security tests | Penetration testing | Must not modify existing tests |
| Configure CI to run tests | GitHub Actions, GitLab CI | Must run all tests |
| Add deployment-specific tests | Smoke tests for staging | Additive only |

---

## What 0h MAY Discover

Testing in different environments may reveal issues:

### Environment Issues (0h Fixes)

| Discovery | Action | Owner |
|-----------|--------|-------|
| Test fails in CI but passes locally | Fix environment parity | 0h |
| Performance below acceptable | Optimize or document | 0h |
| Security vulnerability | Fix and re-test | 0h/0f |

### Test Issues (Return to 0g)

| Discovery | Action | Owner |
|-----------|--------|-------|
| Test is actually flaky | Fix flakiness | 0g |
| Test coverage gap found | Add missing tests | 0g |
| Test-spec mismatch | Fix traceability | 0g |

### Implementation Issues (Return to 0f)

| Discovery | Action | Owner |
|-----------|--------|-------|
| Bug in different environment | Fix in implementation | 0f → 0g |
| Performance issue requiring code change | Fix implementation | 0f → 0g |

---

## Handoff Checklist

Before starting 0h Prerelease, verify:

### Tests Ready

- [ ] All tests pass locally
- [ ] Flakiness check passed (3x consecutive)
- [ ] Coverage meets all targets
- [ ] No critical/high bugs open

### Documentation Ready

- [ ] TEST-PLAN.md complete
- [ ] TEST-TRACE.md complete (no gaps)
- [ ] COVERAGE-REPORT.md generated
- [ ] TEST-RESULTS.md shows all pass
- [ ] All BUG-REPORTS resolved or documented

### Quality Gates

- [ ] Unit coverage ≥ 70%
- [ ] All endpoints have tests
- [ ] All components have tests
- [ ] All flows have E2E tests
- [ ] All negative flows covered
- [ ] A11y requirements verified

### Environment Ready

- [ ] Test environment documented
- [ ] Test data strategy documented
- [ ] All test dependencies listed
- [ ] `npm test` is CI-ready (exits 1 on failure)

---

## Test Execution Requirements for 0h

When 0h sets up CI/CD:

### Required Test Runs

| When | What | Failure Action |
|------|------|----------------|
| Every PR | All tests | Block merge |
| Merge to main | All tests | Block deploy |
| Pre-deploy | All tests + smoke | Block deploy |

### Test Command

```bash
# Must work in CI environment
npm test

# Must exit 1 on any failure
echo $?  # Should be 0 if pass, 1 if fail
```

### Coverage Reporting

```bash
# Generate coverage for CI
npm run test:coverage

# Coverage report location
./coverage/lcov-report/
```

---

## Rework Process

If 0h testing reveals issues that require changes:

### For Test Issues (0g Rework)

1. **0h documents** the issue
2. **Return to 0g** for fix
3. **0g fixes** test issue
4. **0g re-runs** Phase 60 verification
5. **Resume 0h** after handoff checklist passes

### For Implementation Issues (0f Rework)

1. **0h documents** the issue
2. **Return to 0f** for fix
3. **0f fixes** implementation
4. **0f re-passes** Phase 53
5. **0g re-tests** affected areas
6. **0g re-passes** Phase 60
7. **Resume 0h** after handoff checklist passes

**Do NOT:**
- Have 0h fix test issues
- Skip re-verification after fixes
- Lower coverage targets to pass

---

## Success Criteria

0g Testing is complete when:

- [ ] All items in Handoff Checklist are checked
- [ ] TEST-RESULTS.md shows COMPLETE
- [ ] No OPEN bug reports (critical/high)
- [ ] Sign-off below is complete

0h Prerelease can begin when:

- [ ] 0g Testing is complete
- [ ] Handoff checklist verified
- [ ] No blocking issues

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| 0g Tester | | | ☐ Testing Complete |
| 0h Release Engineer | | | ☐ Ready for Prerelease |

---

## Reference Documents

| Document | Location | Purpose |
|----------|----------|---------|
| TEST-PLAN.md | Project root | Testing strategy |
| TEST-TRACE.md | Project root | Test-to-spec mapping |
| TEST-RESULTS.md | Project root | Final results |
| COVERAGE-REPORT.md | Project root | Coverage metrics |
| BUG-REPORTS/ | Project root | Bug documentation |
| 0f-to-0g-HANDOFF.md | 0. Admin/0f/ | Entry contract |

---
Generated: {{timestamp}}
Handoff: 0g Testing → 0h Prerelease
---
