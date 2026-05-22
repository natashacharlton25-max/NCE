# PHASE 31: LIBRARY STRUCTURE

---
Phase: 31
Name: Library Structure
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/31. Library-Structure/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You document the structure of NCE-V2's shared `lib/` utilities (and any additional library code paths). This consolidates Phase 23's lib/ specs into a structural overview.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [FileTree-v2.md](../../FileTree-v2.md) — `lib/svg/` lives here
2. All Phase 23 `lib/` utility specs (`NCE-V2/specs/lib/<utility>/spec/`)
3. Phase 25 PATTERN-ANALYSIS.md (for any new utilities surfaced)

---

## TASK

Produce `LIBRARY-STRUCTURE.md` covering:

1. **lib/ layout**
   - Top-level: `src/lib/`
   - Subdirectories per utility group:
     - `src/lib/svg/` — SVG drawing/manipulation (from FileTree-v2)
     - Any additional utility groups identified in Phase 25 PATTERN-ANALYSIS (e.g. `src/lib/d1-query/`, `src/lib/types/`)
   - Each utility group is a TypeScript module collection

2. **Naming convention**
   - Utility groups: lowercase, hyphens (`d1-query`, `svg`, `types`, `errors`)
   - File names per utility: camelCase (`drawPath.ts`, `bindParameters.ts`)
   - Type-only files: `*.types.ts` (excluded from runtime LOC)

3. **Import pattern**
   - From a system Worker: `import { drawPath } from "../lib/svg";`
   - lib/ utilities are bundled into each Worker that uses them (no service binding)

4. **lib/ rules** (recap from Phase 23)
   - No business logic
   - No system dependencies
   - No state (or minimal — caching only)
   - No D1/R2/KV access (libraries are pure; storage access is system territory)

---

## OUTPUT LOCATION

```
NCE-V2/specs/LIBRARY-STRUCTURE.md
```

(Project-wide spec, not per-library.)

---

## MANDATORY RULES

- Do **NOT** invent libraries not produced by Phase 23
- Apply lib/ rules from Phase 23
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `LIBRARY-STRUCTURE.md` documents lib/ layout
- [ ] All identified utility groups represented
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 32 (Repo Setup)

---

## TEMPLATES (enriched for NCE-V2)

- [LIBRARY-STRUCTURE-TEMPLATE.md](./LIBRARY-STRUCTURE-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
