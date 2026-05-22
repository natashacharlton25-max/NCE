# PHASE 24: REPO SPECS

---
Phase: 24
Name: Repo Specs (repository structure spec)
Location: NCE-V2/NCE V2.0 Spec & Build/24. Repo-Specs/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You produce a Repo Spec — a single document defining the NCE-V2 repository structure: top-level directories, naming conventions, build/deploy layout, file organization rules.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker topology + deployment
3. [FileTree-v2.md](../../FileTree-v2.md) — 27 systems + lib/
4. `NCE-V2/specs/IMPLEMENTATION-PLAN.md` (Pass 4)

---

## RUNTIME CONTEXT

- One Worker per system (except `services`/`system`/`state`/`library` grouped into `platform`)
- `wrangler.toml` per Worker; D1 migrations in `migrations/{library}/`
- Shared utilities in `lib/`
- TypeScript with `tsconfig.json`; types in `*.types.ts` (excluded from runtime LOC)

---

## TASK

Produce `REPO-SPEC.md` covering:

1. **Top-level layout**
   - `src/<system>/` per system (matches FileTree-v2.md)
   - `src/lib/` for shared utilities
   - `migrations/` for D1 migrations per library
   - `tests/` for `*.test.ts` files
   - `__debug__/` for debug utilities (excluded from runtime)
   - `wrangler/` for per-Worker wrangler configs
   - `package.json`, `tsconfig.json`, etc.

2. **Naming conventions**
   - System folders: lowercase, hyphens (match FileTree-v2.md)
   - TypeScript files: PascalCase for classes (BrandManager.ts), camelCase for utility modules
   - Types-only files: `*.types.ts`
   - Test files: `*.test.ts`
   - Debug files: `*.debug.ts` or under `__debug__/`

3. **File organization rules**
   - Per-system runtime files in `src/<system>/`
   - Per-subsystem TS files at `src/<system>/<SubsystemName>.ts`
   - Type definitions per subsystem at `src/<system>/<SubsystemName>.types.ts`
   - Tests parallel structure under `tests/<system>/`

4. **Build/deploy**
   - Per-Worker `wrangler.toml` with bindings (D1, R2, KV, DO, secrets)
   - Build pipeline: TypeScript compile → bundle per Worker → wrangler deploy
   - Environment separation: dev / staging / prod

5. **LOC enforcement**
   - 2000 LOC band per runtime `.ts` (with file exclusions)
   - Lint rule or CI check optional (Phase 35 territory)

---

## OUTPUT LOCATION

```
NCE-V2/specs/REPO-SPEC.md
```

---

## MANDATORY RULES

- Use FileTree-v2.md verbatim for system names
- Honor 2000 LOC band
- Do **NOT** invent new top-level systems
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] REPO-SPEC.md exists with all sections
- [ ] Folder layout matches FileTree-v2.md
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 25 (Post-Spec Analysis)

---

## TEMPLATES

- [REPO-SPEC-TEMPLATE.md](./REPO-SPEC-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
