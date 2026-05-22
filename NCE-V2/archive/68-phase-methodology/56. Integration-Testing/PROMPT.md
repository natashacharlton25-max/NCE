# PHASE 56: INTEGRATION TESTING

---
Phase: 56
Section: 0g. Testing
Name: Integration Testing
Location: NCE-V2/NCE V2.0 Spec & Build/56. Integration-Testing/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Write integration tests (vitest + miniflare) for NCE-V2 API endpoints, D1 query layer, and inter-Worker service bindings. Automate the manual smoke tests from Phase 47 + extend with edge/negative cases.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
2. `NCE-V2/admin/BACKEND-VERIFICATION-REPORT.md` (Phase 47)
3. `NCE-V2/specs/project-wide/ERROR-CODES.md` (Phase 26)
4. Per-library `.library.md` (Phase 23)
5. Backend code (Phases 45–46)

---

## RELATIONSHIP TO PHASE 47

Phase 47 = manual verification.
Phase 56 = automate + extend with edge + negative cases. Don't duplicate; build on.

---

## TASK

### Per Endpoint
For each endpoint in API-SURFACE.md:
- Auto-test happy path (matches Phase 47 manual test)
- Auto-test each declared error code (validation, dependency, state, external, Worker, unknown)
- Auto-test auth enforcement
- Auto-test response envelope shape
- Auto-test TS type conformance

### Per D1 Library
- Test `library/Librarian` queries against test-D1 databases
- Test `library/Writer` (versioning, ref_status cascade, archive)
- Test FTS queries (where applicable)
- Test draft promotion (where applicable)

### Per Service Binding
- Test inter-Worker calls (mock the remote binding in miniflare)

### Per External Integration
- Test wrapper functions with mocked provider responses
- Test resilience/ patterns (RateLimiter, RetryPolicyEngine, FallbackStrategyResolver) trigger correctly
- Test inbound binary → R2 / metadata → D1 sinks

---

## MANDATORY RULES

- Use miniflare for Worker emulation
- Use dedicated test-D1 databases (separate from dev)
- Tests are deterministic (no flaky external calls — mock providers)
- TS strict
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All endpoints have integration tests
- [ ] All libraries have integration tests
- [ ] All external integrations have wrapper tests
- [ ] `pnpm vitest run` passes
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 57 (Component Testing)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
