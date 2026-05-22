# PHASE 60: TEST VERIFICATION

---
Phase: 60
Section: 0g. Testing
Name: Test Verification (final gate)
Location: NCE-V2/NCE V2.0 Spec & Build/60. Test-Verification/
Project: NCE-V2 (TypeScript on Cloudflare Workers + Astro)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Final verification that NCE-V2 testing (Phases 55–59) is complete, coverage targets met, and the system is ready for prerelease.

**This phase is MANDATORY. Cannot be skipped or collapsed.**

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/TEST-PLAN.md`, `TEST-TRACE.md` (Phase 54)
2. All test files in `tests/`
3. Bug reports (if any) in `NCE-V2/admin/BUG-REPORTS/`
4. CODING-STANDARDS.md coverage target (80%)

---

## TASK

### Run All Tests

```
pnpm vitest run                       # unit + integration
pnpm playwright test                  # E2E (against staging)
pnpm test:a11y                        # accessibility
```

### Verify Coverage

- Line coverage ≥80% on runtime code
- Branch coverage ≥70%
- File exclusions: `*.test.ts`, `*.debug.ts`, `__debug__/`, `*.types.ts`

### Verify TEST-TRACE Completeness

- Every TEST-TRACE row has a corresponding test
- Every spec'd error code has at least one test
- Every API endpoint has at least one integration test
- Every component has at least one component test
- Every user journey has at least one E2E test

### Bug Reports

- All bug reports either resolved (with closing test) or accepted-risk-logged

### NCE-V2-Specific Verification

- Output-boundary tests pass (website/ emits JSON, renderers/ emits final)
- Library access tests pass (no direct D1 from outside library/)
- resilience/ pattern tests pass (RateLimiter, Retry, Fallback fire correctly)
- Worker CPU/memory limit tests pass

---

## OUTPUT

```
NCE-V2/admin/TEST-VERIFICATION-REPORT.md
```

---

## MANDATORY RULES

- Don't fix issues — only verify and report
- Don't accept low coverage silently
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All tests pass
- [ ] Coverage targets met
- [ ] TEST-TRACE 100% covered
- [ ] All bug reports resolved or risk-accepted
- [ ] TEST-VERIFICATION-REPORT.md complete
- [ ] Human approves handoff to prerelease (Phase 61+)
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 61 (CI/CD Pipeline) — 0h Prerelease begins

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
