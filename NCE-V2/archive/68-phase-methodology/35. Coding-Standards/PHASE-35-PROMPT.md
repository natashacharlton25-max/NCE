# PHASE 35: CODING STANDARDS

---
Phase: 35
Name: Coding Standards
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/35. Coding-Standards/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You produce `CODING-STANDARDS.md` — concrete, LLM-friendly TypeScript-on-Workers coding standards for NCE-V2.

This makes implementation in Phase 43+ unambiguous.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. `NCE-V2/specs/TECH-STACK.md` (Phase 33)
3. `NCE-V2/specs/project-wide/CONVENTIONS.md` (Phase 26)
4. `NCE-V2/specs/project-wide/SECURITY-STANDARDS.md` (Phase 26)

---

## TASK

Produce `CODING-STANDARDS.md` covering:

### 1. TypeScript Standards
- `strict: true` in tsconfig; no `any` without justified comment
- Prefer `unknown` over `any` when type unknown at parse time
- Result types over exceptions for expected failures (`Result<T, E>` pattern)
- `interface` for object shapes, `type` for unions/aliases
- Type-only imports: `import type { X } from "./y";`

### 2. Naming
- Files: PascalCase for classes (`BrandManager.ts`), camelCase for utilities (`bindParameters.ts`)
- Types: PascalCase (`BrandEntry`, `LibraryQueryOptions`)
- Functions: camelCase (`createBrand`, `queryLibrary`)
- Constants: SCREAMING_SNAKE_CASE for module-level constants (`MAX_RETRY_COUNT`)
- Worker binding names in `wrangler.toml`: SCREAMING_SNAKE_CASE (`BRANDS_DB`, `ASSETS_BUCKET`)

### 3. File Organization
- One primary export per file (matches filename)
- Types-only: `*.types.ts` (excluded from runtime LOC)
- Tests: `*.test.ts` (excluded from runtime LOC)
- Debug: `*.debug.ts` or under `__debug__/` (excluded from runtime LOC)

### 4. LOC Constraints
- ~2000 LOC per runtime file (excluding comments, blank lines, and excluded patterns above)
- ~300 LOC soft limit per function — split if larger
- File >2000 LOC requires explicit exception logged in `PRECODE-DECISION-NOTES.md`

### 5. Async / Worker Patterns
- Always `await` D1 calls; never floating promises
- Use `ctx.waitUntil(promise)` in fetch handler for fire-and-forget work (e.g. analytics)
- 5-min CPU limit: split long work via Queue or DO
- 128 MB memory: stream large R2 reads (don't buffer)

### 6. Error Handling
- Result types at module boundaries (so callers handle failures explicitly)
- Throw only for programmer errors (invariant violations)
- All errors map to project-wide error codes (per Phase 26 ERROR-CODES.md)
- Log via `system/Logger`; user-facing errors via response envelope `{ ok: false, error: { code, message } }`

### 7. Library Access
- All reads go through `library/Librarian` (no direct D1 from outside `library/`)
- All writes through the library's write-owner module (e.g. only `BrandManager` writes brands)
- Use TypeScript types from each library's exports for type safety

### 8. Comments
- Comments explain *why*, not *what*
- One-line max for routine comments
- Block comments / JSDoc for public APIs only
- Never explain things obvious from the code

### 9. Imports
- ESM only (Workers don't support CommonJS)
- Sort imports: third-party first, then internal modules
- No deep imports across system boundaries — use the system's main entry point

### 10. Linting & Formatting
- Biome configured to enforce above (or ESLint + Prettier if user override at Phase 33)
- CI runs `biome check .` (Phase 61)
- Pre-commit hook optional

### 11. Testing Style (preview — full standards in Phase 54)
- Tests live in `tests/<system>/<subsystem>.test.ts`
- Use vitest + miniflare for Worker tests
- Cover: happy path, validation errors, dependency failures, edge cases (per Phase 27 hardening pass 4)

---

## OUTPUT LOCATION

```
NCE-V2/specs/CODING-STANDARDS.md
```

---

## MANDATORY RULES

- Apply CONVENTIONS.md from Phase 26
- TypeScript strict everywhere
- LOC band non-negotiable without logged exception
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `CODING-STANDARDS.md` covers all 11 sections
- [ ] Standards are concrete (rules, not principles)
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 36 (PreCode Ready Check)

---

## TEMPLATES (enriched for NCE-V2)

- [CODING-STANDARDS-TEMPLATE.md](./CODING-STANDARDS-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
