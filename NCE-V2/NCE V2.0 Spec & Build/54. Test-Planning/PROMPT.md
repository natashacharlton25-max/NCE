# PHASE 54: TEST PLANNING

---
Phase: 54
Section: 0g. Testing
Name: Test Planning
Location: NCE-V2/NCE V2.0 Spec & Build/54. Test-Planning/
Project: NCE-V2 (TypeScript on Cloudflare Workers + Astro)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Define NCE-V2's testing strategy, set up infrastructure, and produce the test plan + traceability matrix before writing tests.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/TECH-STACK.md` (vitest + miniflare; biome for testing rules)
2. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
3. `NCE-V2/admin/IMPLEMENTATION-VERIFICATION-REPORT.md` (Phase 53)
4. `NCE-V2/specs/project-wide/ERROR-CODES.md` (Phase 26)
5. Per-component spec/ERRORS.md + spec/EXAMPLES.md (Phase 21–22)

---

## TASK

Produce `TEST-PLAN.md` + `TEST-TRACE.md`:

### TEST-PLAN.md

1. **Test categories** (with NCE-V2 specifics):
   - **Unit tests** (vitest): utility functions, validators, lib/svg utilities, pure functions
   - **Integration tests** (vitest + miniflare): per-Worker fetch handlers, D1 query layer (library/Librarian), service-binding interactions, R2/KV access
   - **Component tests** (Astro testing, if frontend in scope): UI components in isolation with mocked NCE-V2 fetch
   - **E2E tests** (Playwright or similar): Astro consumer flows hitting staging NCE-V2
   - **Accessibility tests**: WCAG audits (axe-core, etc.) — Astro side
   - **Worker-specific tests**: 5-min CPU + 128 MB memory limits validated

2. **Coverage targets** (per CODING-STANDARDS.md): 80% line coverage for runtime code (excluding `*.test.ts`, `*.debug.ts`, `__debug__/`, `*.types.ts`)

3. **Per-environment**: tests run in local (miniflare); staging (deployed); not in prod

### TEST-TRACE.md

Spec → test traceability matrix:
- Every public function in FUNCTIONS.md → at least one unit/integration test
- Every error code in ERRORS.md → at least one error test
- Every API endpoint → at least one integration test
- Every user journey → at least one E2E test
- Every component → at least one component test

---

## MANDATORY RULES

- Tests live in `tests/<system>/<subsystem>.test.ts` (parallel to `src/`)
- vitest is the test framework
- miniflare emulates Workers in tests
- D1 tests use test-only D1 databases (separate from dev)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] TEST-PLAN.md created
- [ ] TEST-TRACE.md created with full coverage matrix
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 55 (Unit Testing)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
