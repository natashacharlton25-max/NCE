# PHASE 29: DATABASE SCHEMA CONSOLIDATION

---
Phase: 29
Name: Database Schema Consolidation
Section: 0d. PreCode
Location: NCE-V2/NCE V2.0 Spec & Build/29. Database-Schema-Consolidation/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You consolidate all database definitions from component STORAGE.md files and per-library spec docs into a unified master D1 schema for NCE-V2.

This is **extraction and consolidation** — NOT design. The specs already define what's needed.

---

## NCE-V2 DATABASE TYPE — ALREADY DECIDED

For NCE-V2, the database type is **fixed**: Cloudflare D1 (SQLite at edge), accessed by Worker binding.

This is locked in:
- [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
- [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md)

**Skip the "ask user for database type" step**. The decision is made.

Binary assets are in **R2** (not D1); cache/config in **KV**; stateful coordination in **Durable Objects**; embeddings in **Vectorize**. None of these need DB-schema-style consolidation here.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
3. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md)
4. All per-library spec docs from Phase 23 (`NCE-V2/specs/<system>/libraries/<library>/`)
5. All component STORAGE.md files from Phase 21 (under `spec/STORAGE.md`)
6. Phase 25 GAP-ANALYSIS.md (for any storage gaps surfaced)

---

## TASK

1. Gather all D1-touching docs:
   - Per-library `.library.md` files (Phase 23)
   - Per-component `STORAGE.md` (Phase 21 — for systems that own D1 tables outside the library pattern, if any)
2. Extract every D1 table definition into a unified `DATABASE-SCHEMA.md`:
   - Per-library schemas (standard `_index`, `{items}`, `{items}_latest` view, optional `{items}_fts`, `{items}_drafts`)
   - Any system-level tables that don't fit the library pattern
3. Check for conflicts:
   - Two libraries claiming the same D1 binding name → STOP, escalate
   - Same column name with different types across libraries (rare; cross-library not a real conflict but flag for awareness)
4. Produce `MIGRATION-STRATEGY.md`:
   - Migration order (which library DBs come first per dependency map)
   - Per-library migration directory: `migrations/{library}/`
   - Versioning scheme (timestamped migrations: `0001_init.sql`, `0002_add_field.sql`)
   - D1 migrations via `wrangler d1 migrations apply`

> **Logging rule:** Conflicts and decisions go in `NCE-V2/admin/PRECODE-DECISION-NOTES.md`.

---

## MANDATORY RULES

- Use D1 (no "what database" question)
- Apply LIBRARY-TEMPLATE v2.0.0 schema pattern uniformly
- JSON stored as TEXT (D1 has no native JSON column)
- Do **NOT** invent tables — pull from approved specs
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/database/
├── DATABASE-SCHEMA.md
└── MIGRATION-STRATEGY.md
```

Plus per-library migration directories (created in implementation):
```
migrations/{library}/
├── 0001_init.sql
└── ...
```

---

## END CONDITION

- [ ] `DATABASE-SCHEMA.md` consolidates all D1 schemas
- [ ] `MIGRATION-STRATEGY.md` defines migration order + versioning
- [ ] No unresolved conflicts
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 30 (API Surface Consolidation)

---

## TEMPLATES (enriched for NCE-V2)

- [DATABASE-SCHEMA-TEMPLATE.md](./DATABASE-SCHEMA-TEMPLATE.md) — D1-specific
- [MIGRATION-STRATEGY-TEMPLATE.md](./MIGRATION-STRATEGY-TEMPLATE.md) — `wrangler d1 migrations`-aware

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
