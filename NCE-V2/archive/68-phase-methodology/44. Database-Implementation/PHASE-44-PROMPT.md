# PHASE 44: DATABASE IMPLEMENTATION

---
Phase: 44
Name: Database Implementation (D1 schemas + migrations)
Section: 0f. Implementation
Location: NCE-V2/NCE V2.0 Spec & Build/44. Database-Implementation/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Implement NCE-V2's D1 schemas per Phase 29 DATABASE-SCHEMA.md: write SQL migrations per library, apply to all environments, seed reference data.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/specs/database/DATABASE-SCHEMA.md` (Phase 29)
2. `NCE-V2/specs/database/MIGRATION-STRATEGY.md` (Phase 29)
3. Per-library `.library.md` specs (Phase 23) for D1 table definitions
4. Per-system STORAGE.md (Phase 21) for any system-level D1 tables outside the library pattern
5. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md)

---

## TASK

Per library (in dependency order):

1. **Migration directory**: `migrations/<library>/`
2. **`0001_init.sql`** implementing per LIBRARY-TEMPLATE v2.0.0:
   - `_index` table (TEXT primary key, name, status, latest_version, updated_at, tags TEXT JSON, preview TEXT JSON, ref_status)
   - `{items}` table (id, version, data TEXT JSON, created_at, created_by, PRIMARY KEY (id, version))
   - `{items}_latest` view
   - Optional `{items}_fts` FTS5 virtual table if FTS in scope
   - `{items}_drafts` if staging in scope
3. **Apply to dev**: `wrangler d1 migrations apply <library>-dev`
4. **Apply to staging**: `wrangler d1 migrations apply <library>-staging` (after dev verified)
5. **Apply to prod**: `wrangler d1 migrations apply <library>-prod` (after staging verified)
6. **Seed reference data** for `reference`-type libraries
7. **Smoke-test**: `SELECT * FROM _index LIMIT 1;` works

---

## MANDATORY RULES

- One migration directory per library
- Migrations idempotent + forward-only (D1 has no down migrations)
- JSON stored as TEXT (D1 has no JSON column)
- All queries parameterized (`db.prepare("...?...").bind(...)`)
- No business logic in migrations — schema + seeds only
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All library schemas applied to dev/staging/prod
- [ ] Reference data seeded
- [ ] Smoke tests pass
- [ ] IMPLEMENTATION-LOG.md updated
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 45 (Backend Core)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
