# PHASE 27: HARDENING

---
Phase: 27
Name: Hardening
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/27.Hardening/
---

## ROLE

You are reviewing all completed specs for implementation readiness, ensuring they are robust enough for Claude Code to implement without ambiguity.

---

## PURPOSE

Hardening ensures specs are:
- **Complete** — No missing information
- **Unambiguous** — One interpretation only
- **Consistent** — No contradictions
- **Implementable** — Claude Code can build from them
- **Testable** — Clear success criteria

---

## TASK

Perform 4 hardening passes:

| Pass | Focus | Output | Template |
|------|-------|--------|----------|
| 1 | Completeness | COMPLETENESS-REVIEW.md | **Create using COMPLETENESS-REVIEW-TEMPLATE.md** |
| 2 | Ambiguity | AMBIGUITY-REVIEW.md | **Create using AMBIGUITY-REVIEW-TEMPLATE.md** |
| 3 | Implementability | IMPLEMENTABILITY-REVIEW.md | **Create using IMPLEMENTABILITY-REVIEW-TEMPLATE.md** |
| 4 | Test Coverage | TEST-COVERAGE-REVIEW.md | **Create using TEST-COVERAGE-REVIEW-TEMPLATE.md** |

Then create:
- **HARDENING-SUMMARY.md** — Create using HARDENING-SUMMARY-TEMPLATE.md
- **IMPLEMENTATION-READY.md** — Create using IMPLEMENTATION-READY-TEMPLATE.md

---

## PASS 1: COMPLETENESS REVIEW

Check every spec file has all required sections filled.

### Checklist per Component

**INDEX.md:**
- [ ] All spec files listed with status
- [ ] Dependencies documented
- [ ] Build notes captured

**OVERVIEW.md:**
- [ ] Purpose clearly stated
- [ ] Responsibilities listed (does/doesn't)
- [ ] Boundaries defined
- [ ] Guarantees stated

**FUNCTIONS.md:**
- [ ] All public functions documented
- [ ] Parameters with types and descriptions
- [ ] Return types specified
- [ ] Errors listed per function

**TYPES.md:**
- [ ] All types defined
- [ ] Field constraints documented
- [ ] Validation rules specified

**BEHAVIOUR.md:**
- [ ] Process flows documented
- [ ] State machines defined (if applicable)
- [ ] Event emissions listed

**ERRORS.md:**
- [ ] All error codes defined
- [ ] Error messages provided
- [ ] Recovery actions specified

**CONFIG.md:**
- [ ] All config options listed
- [ ] Defaults specified
- [ ] Environment variables mapped

**STORAGE.md:** (if applicable)
- [ ] Schema defined
- [ ] Indexes specified
- [ ] Queries documented

**EXAMPLES.md:**
- [ ] Happy path examples
- [ ] Error case examples
- [ ] Edge case examples

---

## PASS 2: AMBIGUITY REVIEW

Find and resolve ambiguous specifications.

### Ambiguity Patterns

| Pattern | Example | Resolution |
|---------|---------|------------|
| Vague language | "should", "might", "usually" | Replace with "must", "will", specific conditions |
| Missing edge cases | "validate email" | Specify: empty, too long, invalid format |
| Undefined behavior | "returns error" | Specify which error code |
| Implicit assumptions | "user must exist" | State explicitly, specify error if not |
| Optional without default | "timeout (optional)" | Specify default value |

### Review Questions

For each function:
- What happens with null/undefined input?
- What happens with empty input?
- What happens at boundary values?
- What happens on timeout?
- What happens on partial failure?

---

## PASS 3: IMPLEMENTABILITY REVIEW

Ensure specs can be directly implemented.

### Checklist

**Data Flow:**
- [ ] Input sources clear
- [ ] Output destinations clear
- [ ] Transformation steps explicit

**Dependencies:**
- [ ] All dependencies exist
- [ ] Dependency interfaces documented
- [ ] Circular dependencies resolved

**External Services:**
- [ ] API endpoints specified
- [ ] Authentication method clear
- [ ] Rate limits documented
- [ ] Error mapping complete

**Storage:**
- [ ] Schema matches types
- [ ] Queries match access patterns
- [ ] Indexes support queries

**Configuration:**
- [ ] All config has defaults
- [ ] Environment variables named
- [ ] Validation rules specified

---

## PASS 4: TEST COVERAGE REVIEW

Ensure specs define what to test.

### Required Test Categories

| Category | Coverage |
|----------|----------|
| Unit tests | Every public function |
| Validation tests | Every input field |
| Error tests | Every error code |
| Integration tests | Every dependency call |
| Edge case tests | Every documented edge case |

### Test Specification Checklist

For each function:
- [ ] Happy path test defined
- [ ] Validation error tests defined
- [ ] Business error tests defined
- [ ] Dependency failure tests defined
- [ ] Edge cases identified

---

## OUTPUT FILES

### COMPLETENESS-REVIEW.md

```markdown
# Completeness Review

## Summary
- Components reviewed: X
- Fully complete: X
- Partially complete: X
- Incomplete: X

## By Component

### {{component}}
| File | Status | Missing |
|------|--------|---------|
| INDEX.md | ✅ Complete | — |
| FUNCTIONS.md | ⚠️ Partial | Error codes for updateX() |

## Action Items
1. [ ] Add missing error codes to {{component}}
```

### AMBIGUITY-REVIEW.md

```markdown
# Ambiguity Review

## Summary
- Ambiguities found: X
- Resolved: X
- Pending: X

## Findings

### AMB-001: Vague validation in {{component}}
**Location:** FUNCTIONS.md → createUser()
**Issue:** "validate email" doesn't specify rules
**Resolution:** Add: "RFC 5322 format, max 254 chars, lowercase normalized"
**Status:** RESOLVED / PENDING
```

### IMPLEMENTABILITY-REVIEW.md

```markdown
# Implementability Review

## Summary
- Blocking issues: X
- Warnings: X
- Ready to implement: X components

## Blocking Issues

### IMP-001: Missing dependency spec
**Component:** billing-subsystem
**Issue:** Depends on payment-system which doesn't exist
**Resolution:** Create payment-system specs OR use int-stripe directly
**Status:** BLOCKING
```

### TEST-COVERAGE-REVIEW.md

```markdown
# Test Coverage Review

## Summary
- Functions specified: X
- Test cases defined: X
- Coverage gaps: X

## By Component

### {{component}}
| Function | Happy | Validation | Error | Edge | Total |
|----------|-------|------------|-------|------|-------|
| create() | ✅ | ✅ | ✅ | ⚠️ | 4/5 |
| update() | ✅ | ✅ | ❌ | ❌ | 2/5 |
```

### HARDENING-SUMMARY.md

```markdown
# Hardening Summary

## Overall Assessment
**Status:** READY / NOT READY

## Metrics
| Metric | Value | Target | Status |
|--------|-------|--------|--------|
| Completeness | 95% | 100% | ⚠️ |
| Ambiguities resolved | 12/12 | 100% | ✅ |
| Implementability issues | 0 | 0 | ✅ |
| Test coverage | 90% | 80% | ✅ |

## Blocking Issues
(List any issues that prevent implementation)

## Recommendations
(List improvements for future)
```

### IMPLEMENTATION-READY.md

```markdown
# Implementation Ready Checklist

## Pre-Implementation Gate

### Documentation Complete
- [ ] All systems have complete specs
- [ ] All subsystems have complete specs
- [ ] All integrations have complete specs
- [ ] All libraries have complete specs
- [ ] Project-wide docs complete

### Quality Checks
- [ ] No ambiguities remaining
- [ ] No blocking implementability issues
- [ ] Test cases defined for all functions
- [ ] Error codes registered

### Dependencies Ready
- [ ] All internal dependencies specified
- [ ] All external services documented
- [ ] All libraries identified

### Sign-off
- [ ] Human reviewed hardening results
- [ ] Human approved for implementation

## Go / No-Go Decision

**Decision:** GO / NO-GO
**Date:** {{date}}
**Approved by:** {{name}}
**Notes:** {{any conditions or notes}}
```

---

## RULES

### DO:
- Be thorough — check everything
- Be specific — cite exact locations
- Be actionable — provide resolutions
- Prioritize — blocking vs. nice-to-have

### DO NOT:
- Skip components
- Accept "good enough"
- Fix issues inline (document for fixing)
- Proceed with blocking issues

---

## APPROVAL GATE

Phase 27 is COMPLETE when:

- [ ] All 4 review passes complete
- [ ] HARDENING-SUMMARY.md created
- [ ] IMPLEMENTATION-READY.md created
- [ ] All blocking issues resolved
- [ ] Human approves go/no-go decision

---

## NEXT PHASE

After Phase 27 approval → Phase 28 (Full Spec Checklist)

---
Generated: {{timestamp}}
---
