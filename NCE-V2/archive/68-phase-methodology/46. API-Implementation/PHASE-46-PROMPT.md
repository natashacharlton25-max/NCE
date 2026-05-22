# PHASE 46: API IMPLEMENTATION

---
Phase: 46
Name: API Implementation (all system Workers + endpoints)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/46. API-Implementation/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Implement all NCE-V2 system Workers and their endpoints per Phase 30 API-SURFACE.md. Each of the 23 non-platform systems becomes its own Worker with fetch handlers + internal logic.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
2. `NCE-V2/specs/project-wide/ERROR-CODES.md` (Phase 26)
3. Per-system specs (Phase 21): FUNCTIONS.md, TYPES.md, BEHAVIOUR.md, ERRORS.md, CONFIG.md, STORAGE.md, EXAMPLES.md
4. Per-integration specs (Phase 22)
5. `NCE-V2/specs/CODING-STANDARDS.md` (Phase 35)
6. Platform Worker from Phase 45

---

## TASK

For each of the 23 non-platform system Workers (alphabetical: access, ai, assets, audit, brand, checks, content, document-templates, documents, email, integrations, marks, observability, orchestration, publishing, renderers, resilience, review, social, template, verification, versioning, website):

1. Implement Worker entry point `src/<system>/index.ts` with fetch handler
2. Implement all subsystems per their `spec/FUNCTIONS.md` + `TYPES.md`
3. Wire up bindings (D1 via library/Librarian, R2 for assets/, KV, DO as needed)
4. Wire up service bindings for inter-Worker calls
5. Handle errors per project-wide ERROR-CODES.md
6. Apply output-boundary rule (website/ emits JSON; renderers/ produces final artefacts; email/ composes rendered HTML)
7. resilience/ patterns for external calls (RateLimiter, RetryPolicyEngine, FallbackStrategyResolver)
8. Tests alongside implementation

For each external integration (Phase 22 services):
- Wrapper implementation per `OUR-WRAPPER.md`
- Field mapping per `FIELD-MAPPING.md`
- Worker secrets via `env.<SECRET_NAME>`

---

## MANDATORY RULES

- All D1 reads through `library/Librarian` (no direct D1 from outside library/)
- Inbound binaries → `assets/` (R2); inbound metadata → relevant library D1 via library/
- TypeScript strict
- Tests cover happy path + each error category per ERRORS.md
- Apply output-boundary rule
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All 23 system Workers implemented
- [ ] All endpoints per API-SURFACE.md exist
- [ ] All TS types match Phase 21 TYPES.md
- [ ] Tests pass
- [ ] `wrangler dev` boots each Worker
- [ ] IMPLEMENTATION-LOG.md updated
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 47 (Backend Verification)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
