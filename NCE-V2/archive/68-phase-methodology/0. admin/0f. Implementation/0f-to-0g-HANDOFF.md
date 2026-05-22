# 0f → 0g Handoff Contract

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Binding contract between Implementation (0f) and Testing (0g)
---

## What 0f Delivers

By the end of 0f Implementation, these artifacts exist:

### Code Artifacts

| Artifact | Contains | Status |
|----------|----------|--------|
| Working backend | All endpoints from API-SURFACE.md | ☐ COMPLETE |
| Working frontend | All pages from PAGES.md | ☐ COMPLETE |
| Working integration | Frontend connected to backend | ☐ COMPLETE |
| Database | Schema matching DATABASE-SCHEMA.md | ☐ COMPLETE |
| shared-types/ | Single source of truth for types | ☐ COMPLETE |
| Migrations | All database migrations | ☐ COMPLETE |
| Seeds | Development seed data | ☐ COMPLETE |

### Documentation Artifacts

| Artifact | Contains | Status |
|----------|----------|--------|
| IMPLEMENTATION-LOG.md | Phase-by-phase proof of work | ☐ COMPLETE |
| IMPLEMENTATION-CHECKLIST.md | Final verification | ☐ COMPLETE |
| BACKEND-VERIFICATION-REPORT.md | Backend test results | ☐ COMPLETE |
| ARCHITECTURE-PATTERNS.md | Coding standards used | ☐ COMPLETE |
| DEVIATION-REPORTS/ | Any spec deviations | ☐ COMPLETE (or N/A) |

---

## What 0f Does NOT Deliver

These are explicitly deferred to 0g Testing or later:

| Item | Why Deferred | Owner |
|------|--------------|-------|
| Unit tests | 0g Testing | 0g |
| Integration tests | 0g Testing | 0g |
| End-to-end tests | 0g Testing | 0g |
| Test coverage reports | 0g Testing | 0g |
| CI/CD pipeline | 0h Prerelease | 0h |
| Deployment configuration | 0h Prerelease | 0h |
| Production environment | 0h Prerelease | 0h |
| User documentation | 0h Prerelease | 0h |

---

## What 0g Must NOT Change

These are **locked** from 0f:

| Locked Item | Cannot Change | Escalation If Needed |
|-------------|---------------|----------------------|
| API contracts | No new endpoints, no changed signatures | Return to 0c/0d |
| Component APIs | No new required props, no changed events | Return to 0e |
| Route structure | No new routes, no changed paths | Return to 0e |
| Database schema | No new tables, no changed columns | Return to 0c/0d |
| Type definitions | No changes to shared-types/ | Return to 0c |
| Architecture patterns | No changes to code structure | Return to 0f |

**If 0g discovers a need to change these:** STOP. Document. Escalate. Do not "fix forward."

---

## What 0g MAY Do

Within the boundaries of 0f outputs:

| Allowed Action | Example | Constraint |
|----------------|---------|------------|
| Add test files | `*.test.ts`, `*.spec.ts` | Must not modify source code |
| Add test utilities | Test helpers, mocks | Must be in test folders |
| Add test fixtures | Test data, snapshots | Must not affect production |
| Report bugs | File bug reports | Does not fix, only reports |
| Report spec issues | Spec doesn't match behavior | Escalate, don't modify |

---

## What 0g MAY Discover

Testing may reveal issues that require action:

### Bugs (0g Fixes)

Bugs are implementation errors where code doesn't match spec:

| Discovery | Action | Owner |
|-----------|--------|-------|
| Code doesn't match spec | Fix the code | 0f (rework) |
| Missing edge case handling | Add handling per spec | 0f (rework) |
| Error handling incomplete | Complete per ERROR-CODES.md | 0f (rework) |

### Spec Issues (Escalate)

Spec issues are problems with the specification itself:

| Discovery | Action | Owner |
|-----------|--------|-------|
| Spec is ambiguous | Clarify spec, then implement | 0c/0d/0e |
| Spec is impossible | File deviation, redesign | 0c/0d/0e |
| Spec is incomplete | Add to spec, then implement | 0c/0d/0e |
| Specs conflict | Resolve conflict, then implement | 0c/0d/0e |

### Performance Issues (Document)

| Discovery | Action | Owner |
|-----------|--------|-------|
| Performance below target | Document, may optimize | 0f (if time) |
| Performance way below target | Escalate for redesign | 0c/0d/0e |

---

## Handoff Checklist

Before starting 0g Testing, verify:

### Code Ready

- [ ] All backend endpoints implemented and working
- [ ] All frontend pages implemented and working
- [ ] Frontend connects to backend successfully
- [ ] Database migrations run without errors
- [ ] Seed data loads successfully
- [ ] Dev environment starts with single command

### Documentation Ready

- [ ] IMPLEMENTATION-LOG.md complete for all phases
- [ ] IMPLEMENTATION-CHECKLIST.md all items checked
- [ ] BACKEND-VERIFICATION-REPORT.md shows all pass
- [ ] All deviation reports resolved or documented
- [ ] README.md has setup instructions

### Quality Gates

- [ ] Backend Verification (Phase 47) passed
- [ ] Implementation Verification (Phase 53) passed
- [ ] No blocking issues documented
- [ ] Performance constraints met (or documented why not)

### Environment Ready

- [ ] Can clone repo and run locally
- [ ] Environment variables documented
- [ ] Dependencies install cleanly
- [ ] Dev server starts without errors

---

## Testing Scope Guide

For 0g, prioritize testing based on:

### High Priority (Test First)

| Area | Why |
|------|-----|
| Authentication flows | Security critical |
| Data mutations (create, update, delete) | Data integrity |
| Error handling paths | User experience |
| Business logic | Core functionality |

### Medium Priority

| Area | Why |
|------|-----|
| List/display operations | User-facing |
| Form validation | Data quality |
| Navigation flows | Usability |
| Loading/error states | Edge cases |

### Lower Priority (If Time)

| Area | Why |
|------|-----|
| Edge cases | Less common scenarios |
| Performance edge cases | Optimization |
| Accessibility deep testing | Covered in A11Y-CHECKLIST |

---

## Rework Process

If 0g testing reveals bugs that require code changes:

1. **0g documents** the bug in a bug report
2. **0g completes** test pass (don't stop testing)
3. **0f reviews** bug reports
4. **0f fixes** bugs in implementation
5. **0f updates** IMPLEMENTATION-LOG.md with fixes
6. **0g re-tests** affected areas

**Do NOT:**
- Have 0g fix implementation code
- Skip documentation of bugs
- Fix forward without updating specs (if spec issue)

---

## Success Criteria

0g Testing can begin when:

- [ ] All items in Handoff Checklist are checked
- [ ] IMPLEMENTATION-CHECKLIST.md shows COMPLETE
- [ ] No OPEN deviation reports
- [ ] Sign-off below is complete

0g Testing is complete when:

- [ ] Test plan covers all specs
- [ ] All tests pass (or bugs documented)
- [ ] Coverage meets targets
- [ ] TEST-TRACE.md maps tests to specs
- [ ] Ready for 0h Prerelease

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| 0f Implementer | | | ☐ Implementation Complete |
| 0g Tester | | | ☐ Ready to Begin Testing |

---

## Reference Documents

| Document | Location | Purpose |
|----------|----------|---------|
| IMPLEMENTATION-LOG.md | Project root | What was built |
| IMPLEMENTATION-CHECKLIST.md | Project root | Verification |
| BACKEND-VERIFICATION-REPORT.md | Project root | Backend testing |
| ARCHITECTURE-PATTERNS.md | 0. Admin/0f/ | Coding standards |
| API-SURFACE.md | 0c/0d outputs | API specification |
| COMPONENTS.md | 0e outputs | Component specification |
| PAGES.md | 0e outputs | Page specification |

---
Generated: {{timestamp}}
Handoff: 0f Implementation → 0g Testing
---
