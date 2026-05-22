# 0g Testing File Manifest

---
Section: 0g Testing (Phases 54-60)
Purpose: Complete list of files available for audit review
---

## Dependency Bundles

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **Strategy** | "Strategy bundle" | TEST-PLAN.md, COVERAGE-TARGETS.md |
| **Traceability** | "Traceability bundle" | TEST-TRACE.md |
| **Unit Tests** | "Unit tests bundle" | All files in /tests/unit/ |
| **Integration Tests** | "Integration bundle" | All files in /tests/integration/ |
| **E2E Tests** | "E2E bundle" | All files in /tests/e2e/ |
| **Results** | "Results bundle" | Coverage report, TEST-VERIFICATION.md |
| **Gate** | "Gate bundle" | 0g-COMPLETION-CRITERIA.md, 0g-to-0h-HANDOFF.md |

---

## Documentation Files

| File | Location | Purpose |
|------|----------|---------|
| TEST-PLAN.md | /docs/0g/ | Testing strategy |
| COVERAGE-TARGETS.md | /docs/0g/ | Coverage requirements |
| TEST-TRACE.md | /docs/0g/ | Spec-to-test mapping |
| TEST-VERIFICATION.md | /docs/0g/ | Test results summary |
| 0g-COMPLETION-CRITERIA.md | /0. Admin/0g. Testing/ | Completion checklist |
| 0g-to-0h-HANDOFF.md | /docs/0g/ | Handoff to prerelease |

---

## Test Files

### Unit Tests
| File | Location | Tests |
|------|----------|-------|
| {{component}}.test.ts | /tests/unit/ | {{description}} |

### Integration Tests
| File | Location | Tests |
|------|----------|-------|
| {{component}}.spec.ts | /tests/integration/ | {{description}} |

### E2E Tests
| File | Location | Tests |
|------|----------|-------|
| {{journey}}.e2e.ts | /tests/e2e/ | {{description}} |

---

## Test Artifacts

| File | Location | Purpose |
|------|----------|---------|
| Coverage report | /coverage/ | Code coverage data |
| Test results | /test-results/ | JUnit/other format results |
| Screenshots (E2E) | /test-results/screenshots/ | Failure screenshots |

---

## Source Files (for reference)

| File | Section | Maps To |
|------|---------|---------|
| FUNCTIONS.md (per component) | 0c | Unit tests |
| ERRORS.md (per component) | 0c | Error path tests |
| API-SURFACE.md | 0d | Integration tests |
| PAGES.md | 0e | E2E tests |

---

## Priority Files for Audit

1. **TEST-TRACE.md** - Spec-to-test mapping
2. **Coverage report** - Actual coverage numbers
3. **TEST-VERIFICATION.md** - Test results
4. **0g-to-0h-HANDOFF.md** - Ready for prerelease?
5. **TEST-PLAN.md** - Strategy used

---

## File Count Summary

| Category | Count |
|----------|-------|
| Documentation | 6 |
| Unit test files | ~{{n}} |
| Integration test files | ~{{n}} |
| E2E test files | ~{{n}} |
| Artifacts | ~3 |
| **TOTAL** | **~{{total}}** |

---

*End of File Manifest*
