# PHASE 23: LIBRARY SPECS

---
Phase: 23
Name: Library Specs (D1 content libraries + lib/ utilities)
Location: NCE-V2/NCE V2.0 Spec & Build/23. Library-Specs/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are writing implementation-ready specs for NCE-V2 libraries.

**Two kinds of "library" exist in NCE-V2 — they get specced differently:**

1. **D1 Content Libraries** — versioned content stores (brands, audiences, themes, prompts, outlines, etc.). Owned by various systems (BrandManager owns brands DB; ContentManager owns themes DB; etc.). Spec'd via [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) (per-library doc with D1 schema, entry schema, staging, validation, write policy, migration rules).

2. **`lib/` Utilities** — shared TypeScript code (e.g. `lib/svg/`). Stateless, no decisions, no D1. Spec'd with a subset of the 10 spec files (typically INDEX, OVERVIEW, FUNCTIONS, TYPES, ERRORS, EXAMPLES — 6 files).

This phase handles both, with different spec sets.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md) — `library/` system (5 subsystems); `lib/svg/` utilities (8 modules)
5. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) — D1 content library template (REQUIRED for kind #1)
6. PRE-SPEC-NOTES.md for each library

---

## RUNTIME CONTEXT

- **D1**: SQLite at edge, accessed by Worker binding (no file paths)
- **R2**: object store for binaries (asset libraries link to R2 IDs)
- **`library/` system** mediates all D1 content library access: Librarian (query), FileManager (read-by-id), Writer (write), Archivist (validation/archive), CacheHandler (cache)
- **`lib/` utilities** are imported in-Worker; no service binding, no async (where possible)

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

- D1 content libraries hold **text and JSON only** (no binary data)
- Library entry references to binaries (images, PDFs) are asset IDs pointing to R2, never the bytes
- Library write access is owned by ONE module per library (e.g. only BrandManager writes brands library)
- Library reads happen through `library/Librarian`

---

## TASK

### Step A — D1 Content Libraries

For each D1 content library identified in Phase 25 (Post-Spec Analysis) and per `_LIBRARIES.md` v2.0.0:

1. Create folder at `NCE-V2/specs/<owning-system>/libraries/<library-name>/`
2. Produce a per-library spec using [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) verbatim structure:
   - D1 binding name + tables (`_index`, `{items}`, `{items}_latest` view, optional FTS5)
   - Entry schema (JSON in `data` TEXT column)
   - Required + optional fields with token priority
   - Preview fields (for `_index`)
   - Staging (drafts in `{items}_drafts` table)
   - Dependencies (other libraries this depends on)
   - Queryable fields (FTS-indexed / filterable / sortable)
   - Performance & scale
   - Example entries (minimal + complete)
   - Validation rules
   - Write policy (which module owns writes)
   - Migration rules

Expected libraries (per _LIBRARIES.md v2.0.0 — verify in Phase 25):
- `brands` (BrandManager), `audiences` (AudienceManager), `voices` (BrandVoiceManager), `tones` (BrandVoiceManager)
- `themes`, `outlines`, `templates` (ContentManager)
- `prompts` (AISystemManager), `model-configs` (ModelManager)
- `section-specs`, `content-types`, `cognitive-types` (TemplateEngine)
- `social-templates` (SocialMediaManager), `email-templates` (EmailManager)
- `visual-brands` (VisualManager) — (note: visual is absorbed into `brand/` in v2; check)

### Step B — `lib/` Utilities

For `lib/svg/` (the only `lib/` utility folder per FileTree-v2.md) and any additional utilities identified in Phase 25:

1. Create folder at `NCE-V2/specs/lib/<utility-name>/`
2. Produce 6 spec files:
   - INDEX.md
   - OVERVIEW.md (emphasize "no decisions, just utilities")
   - FUNCTIONS.md (primary focus — comprehensive function signatures)
   - TYPES.md
   - ERRORS.md
   - EXAMPLES.md (more than typical — libraries need them)

> **Logging rule:** Library identifications added in Phase 25 logged in PASS-DECISION-NOTES.md.

---

## LIBRARY DEPENDENCY RULES

| Allowed | Not Allowed |
|---------|-------------|
| `lib/` → `lib/` | `lib/` → System |
| System → `lib/` | `lib/` → Subsystem |
| Subsystem → `lib/` | `lib/` → External Integration |
| D1 library → other D1 library (soft refs only via library/Librarian) | D1 library → direct D1 of another library |

---

## MANDATORY RULES

- Do **NOT** put business logic in `lib/` utilities (decisions belong to systems)
- Do **NOT** make `lib/` utilities depend on systems
- Do **NOT** create circular dependencies
- D1 content libraries always go through `library/Librarian` for reads; only their write-owner module writes
- TypeScript signatures everywhere
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{owning-system}}/libraries/{{library-name}}/
└── {{library-name}}.library.md      # D1 content library spec (per LIBRARY-TEMPLATE v2.0.0)

NCE-V2/specs/lib/{{utility-name}}/
└── spec/
    ├── INDEX.md
    ├── OVERVIEW.md
    ├── FUNCTIONS.md
    ├── TYPES.md
    ├── ERRORS.md
    └── EXAMPLES.md
```

---

## APPROVAL GATE

Per D1 library:
- [ ] D1 binding name set
- [ ] Schema declared (tables + view + optional FTS5)
- [ ] Entry schema with required/optional + validation
- [ ] Write owner module declared
- [ ] No business logic mixed into the library spec itself

Per `lib/` utility:
- [ ] 6 spec files created
- [ ] FUNCTIONS.md comprehensive
- [ ] No business logic
- [ ] Pure functions where possible

---

## END CONDITION

Phase 23 is COMPLETE when:
- [ ] All D1 content libraries have per-library spec docs
- [ ] `lib/svg/` (and any other identified utilities) have spec/ folders with 6 files
- [ ] All approved
- [ ] PASS-PROGRESS.md updated

**Next:** Phase 24 (Repo Specs)

---

## TEMPLATES (enriched for NCE-V2)

- [INDEX-LIBRARY-TEMPLATE.md](./INDEX-LIBRARY-TEMPLATE.md)
- [OVERVIEW-LIBRARY-TEMPLATE.md](./OVERVIEW-LIBRARY-TEMPLATE.md)
- [FUNCTIONS-LIBRARY-TEMPLATE.md](./FUNCTIONS-LIBRARY-TEMPLATE.md)
- [TYPES-TEMPLATE.md](./TYPES-TEMPLATE.md)
- [ERRORS-TEMPLATE.md](./ERRORS-TEMPLATE.md)
- [EXAMPLES-TEMPLATE.md](./EXAMPLES-TEMPLATE.md)

Plus the master [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) for D1 content libraries.

---

## INPUTS

- PRE-SPEC-NOTES.md per library/utility (Phase 20)
- Phase 25 (Post-Spec Analysis) library identifications
- LIBRARY-TEMPLATE.md v2.0.0
- Library write-owner system's CONTRACT.md (which module writes)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- The two-kinds-of-library split (D1 content libraries vs `lib/` utilities) — confirm the right framing?
- May this draft be promoted to "Approved"?
