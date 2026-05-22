# PHASE 45: BACKEND CORE

---
Phase: 45
Name: Backend Core (Worker foundations)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/45. Backend-Core/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Build NCE-V2's Worker foundations: the `platform` Worker (services + system + state + library), plus shared middleware patterns (auth, error handling, request logging, response envelopes).

This is the foundation that all other system Workers depend on.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/TECH-STACK.md` (Phase 33)
2. `NCE-V2/specs/project-wide/API-STANDARDS.md`, `CONVENTIONS.md`, `ERROR-CODES.md`, `SECURITY-STANDARDS.md` (Phase 26)
3. `NCE-V2/specs/api/API-SURFACE.md` (Phase 30)
4. `NCE-V2/specs/CODING-STANDARDS.md` (Phase 35)
5. Per-system specs for `services/`, `system/`, `state/`, `library/` (Phase 21)

---

## TASK

Implement the `platform` Worker:

1. **`services/`** subsystems: APIKeyManager, AuthHandler, ContentMetadataManager, DatabaseHandler, DataSanitizer, GeneratedContentCatalog, IngestionEngine, PromptBuilder, PromptCondenser, ResearchTools, VectorStore (PythonRunner remains flagged OQ-PY-1)
2. **`system/`** subsystems: FailureHandler, Logger, Parsers, Builders, GarbageCollector
3. **`state/`** subsystems: ContextAssembler, StateManager, StateTransitionValidator
4. **`library/`** subsystems: Librarian, FileManager, Writer, Archivist, CacheHandler

For each subsystem:
- TypeScript implementation per the subsystem's `spec/FUNCTIONS.md` + `TYPES.md`
- Tests per `spec/EXAMPLES.md` and `spec/ERRORS.md`
- Conform to CODING-STANDARDS.md (strict types, Result patterns, async/await on D1, etc.)
- Library access via `library/Librarian` (so library/ itself defines this gateway)

Worker fetch handler scaffolding:
- Response envelope `{ ok: true, data: T } | { ok: false, error: { code, message } }`
- Error handling middleware
- Auth middleware (via `access/` system once it's built, or placeholder for now)
- Request logging via `system/Logger`

---

## MANDATORY RULES

- Follow CODING-STANDARDS.md exactly
- All D1 access through `library/`
- TypeScript strict; no `any` without justification
- Tests alongside implementation (vitest + miniflare)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `platform` Worker implemented (all 4 grouped systems)
- [ ] Tests pass via `vitest`
- [ ] `wrangler dev` boots platform Worker locally
- [ ] IMPLEMENTATION-LOG.md updated
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 46 (API Implementation)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
