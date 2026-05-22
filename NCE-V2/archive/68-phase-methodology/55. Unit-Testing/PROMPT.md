# PHASE 55: UNIT TESTING

---
Phase: 55
Section: 0g. Testing
Name: Unit Testing
Location: NCE-V2/NCE V2.0 Spec & Build/55. Unit-Testing/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Write unit tests (vitest) for NCE-V2 business logic, utilities, validators, schemas, data transformations.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/TEST-PLAN.md`, `TEST-TRACE.md` (Phase 54)
2. Per-component spec/TYPES.md + spec/FUNCTIONS.md + spec/ERRORS.md (Phase 21)
3. `lib/svg/` spec (Phase 23)
4. Implementation code (Phases 45–46)

---

## TASK

For each component (system + subsystem + library):

### Test these (high priority)
- Utility functions in `lib/` (svg, etc.)
- Validators + schema validators (TS types validated at boundaries)
- Pure functions (no side effects)
- Business logic per spec/BEHAVIOUR.md
- Data transformations
- Error formatters

### Test categories
- Happy path (per spec/EXAMPLES.md)
- Validation errors (each rule from spec/TYPES.md)
- Error codes (each from spec/ERRORS.md)
- Edge cases (empty, null, boundary values)

### NCE-V2 specifics
- D1 query layer mocked (don't hit D1 in unit tests; use integration tests for that)
- Worker secret bindings mocked
- Output Form correctness verified (JSON-emitter never returns HTML)

---

## MANDATORY RULES

- Tests in `tests/<system>/<subsystem>.test.ts`
- vitest only (not jest)
- No D1/R2/KV/DO access (mock these — that's integration test territory)
- 80% line coverage minimum (excluding excluded patterns)
- TypeScript strict in tests too
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All TEST-TRACE.md unit-test rows covered
- [ ] 80% line coverage reached
- [ ] `pnpm vitest run` passes
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 56 (Integration Testing)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
