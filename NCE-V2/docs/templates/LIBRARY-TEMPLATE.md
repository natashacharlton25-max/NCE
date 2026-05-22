# Library Documentation Template

> **Purpose:** Template for documenting D1-based content libraries
> **Use:** Copy this template to document each library in a system's repository
> **Version:** 2.0.0
> **Supersedes:** LIBRARY-TEMPLATE.md v1.0.0 and REPOS-TEMPLATE.md (merged; storage reframed to D1)

---

## What Changed from v1

v1 had two templates — `LIBRARY-TEMPLATE.md` (SQLite files at `/data/*.sqlite`) and `REPOS-TEMPLATE.md` (JSON files at `/repos/`). On Cloudflare, that split no longer means anything: both described "a versioned, schema-validated, single-write-owner collection of entries," and every difference between them was storage mechanics. This v2 merges them into one concept — **a library** — stored in D1.

| v1 concept | v2 |
|---|---|
| `/data/{library}.sqlite` file path | D1 database, accessed by Worker binding |
| `/repos/{repo}/` JSON files | D1 tables |
| `_index.json` file | `_index` table in D1 |
| ID-to-filename contract | D1 primary key; no filenames |
| FileManager locating by filename | `library/FileManager` querying D1 by ID |

What carries over unchanged: entry schemas, versioning, status enums, library types, validation rules, write policy, migration rules, the `_index`/`{items}`/`_latest` table pattern.

Libraries hold **text and JSON only** — brand configs, themes, knowledge entries, prompts, audiences, and similar. D1 holds all of it. Binary assets (images, PDFs) are not library entries; they are handled by the `assets/` system and stored in R2. A library entry that needs an image holds the asset's ID or URL as a text field — never the binary itself.

---

## How To Use

1. Copy this template to `specs/{System}/{Library}.library.md`
2. Fill in each section based on the library's purpose
3. Document the D1 table schemas
4. Add entry examples (JSON format for the `data` column)
5. Document the staging approach if the library supports drafts

---

# {LibraryName} Library

> **System:** {System Name}
> **D1 binding:** `{BINDING_NAME}` (e.g. `BRANDS_DB`)
> **Library Type:** core | derived | generated | reference
> **Status:** Draft | Seeded | Active

---

## Overview

{Brief description of what this library holds and why it exists}

---

## Storage

This library is stored in **D1** (Cloudflare's SQLite). It is accessed by Worker binding, not by file path — there is no `.sqlite` file to locate.

| Aspect | Value |
|---|---|
| D1 binding name | `{BINDING_NAME}` |
| Owning system | `{system}` |
| Tables | `_index`, `{items}`, `{items}_latest` (view), optionally `{items}_fts` |
| Content | Text and JSON only — no binary data |

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `{ModuleName}` | Read/Write (Owner) | {what for} |
| `{ModuleName}` | Read via Librarian | {what for} |

---

## D1 Schema

### Tables

```sql
-- Lightweight index for fast listing
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,                       -- JSON array (stored as TEXT)
  preview TEXT,                    -- JSON: key fields for UI (stored as TEXT)
  ref_status TEXT DEFAULT 'valid'  -- 'valid', 'stale', 'orphaned'
);

-- Full versioned data
CREATE TABLE {items} (
  id TEXT,
  version INTEGER,
  data TEXT,                       -- Full entry as JSON (stored as TEXT)
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

-- Latest version view
CREATE VIEW {items}_latest AS
SELECT * FROM {items} t1
WHERE version = (SELECT MAX(version) FROM {items} t2 WHERE t2.id = t1.id);

-- Full-text search (optional, if needed)
CREATE VIRTUAL TABLE {items}_fts USING fts5(
  id,
  name,
  {searchable_fields},
  content='{items}',
  content_rowid='rowid'
);
```

> **D1 notes:**
> - D1 supports `CREATE TABLE`, views, and FTS5 — all of the above runs unchanged.
> - D1 has no native JSON column type; JSON is stored as `TEXT`. D1's SQL `json_*()` functions query into it.
> - D1 queries are async (`await env.{BINDING}.prepare(...).all()`), unlike synchronous local SQLite.
> - Schema is created and evolved via D1 migrations in `migrations/{library}/`.

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/{SchemaName}.ts`

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier (e.g., "{type}_{slug}") |
| `name` | string | Human-readable name |
| `schemaVersion` | string | Schema version (e.g., "1.0.0") |
| `status` | EntryStatus | Lifecycle state |
| `{field}` | {type} | {description} |

> **Why schemaVersion?** Enables automatic migration when the schema evolves. Read paths detect old entries and upgrade them.

### Entry Status Enum

| Value | Description | AI Visibility |
|-------|-------------|---------------|
| `draft` | Incomplete, not ready | Hidden |
| `active` | Production-ready | Visible |
| `archived` | Deprecated, kept for history | Hidden |
| `flagged` | Needs human review | Hidden |

**Rule:** AI modules only see `status: "active"` entries unless explicitly overridden.

### Review Status Enum (for AI-generated libraries)

> Use when entries can be AI-generated and need human verification.

| Value | Description | Can AI Use? |
|-------|-------------|-------------|
| `pending` | AI-generated, awaiting review | No |
| `approved` | Human verified | Yes |
| `rejected` | Human marked incorrect | No |
| `—` | Human-authored (no review needed) | Yes |

**Rule:** For libraries with `Library Type: generated`, require a `reviewStatus` field. `core` and `reference` libraries can omit it.

### Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `{field}` | {type} | {default} | {description} |

---

## Preview Fields

> Fields included in `_index.preview` for fast UI display, avoiding a full entry read.

| Field | Purpose |
|-------|---------|
| `title` | Display name in lists |
| `{field}` | {why included in preview} |

**Example preview JSON:**

```json
{
  "title": "Example Entry",
  "owner": "user_nce",
  "category": "productivity"
}
```

---

## Staging (Drafts)

> For libraries that support human review before an entry becomes part of the library.

Drafts are held in a separate `{items}_drafts` table in the same D1 database — not in the live `{items}` table. The live library only ever contains finalised entries.

```sql
CREATE TABLE {items}_drafts (
  id TEXT PRIMARY KEY,
  data TEXT,                       -- Draft entry as JSON
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  updated_at TEXT
);
```

### Draft Lifecycle

```
1. Create draft   → INSERT INTO {items}_drafts
2. Edit / review  → UPDATE {items}_drafts (via dashboard or tooling)
3. Approve        → validated against the entry schema
4. Finalise       → INSERT INTO {items} (id, version, data, created_by)
5. Update index   → UPSERT _index row
6. Remove draft   → DELETE FROM {items}_drafts
```

**Rule:** A draft is not part of the library until finalised. The `{items}` table contains only finalised, immutable, versioned records.

---

## Dependencies

> Other libraries this library references.

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `{library}` | `{fieldId}` | hard / soft / reference | {what happens on archive} |

**Dependency Types:**
- **hard** = Required; entry invalid if dependency missing
- **soft** = Optional enrichment
- **reference** = ID-only, loose coupling

---

## Queryable Fields

> Which fields support search via Librarian.

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `tags` | Yes | Yes | No | Multi-value filter |
| `status` | No | Yes | No | Enum filter |
| `{field}` | {yes/no} | {yes/no} | {yes/no} | {notes} |

**Search types:**
- **FTS Indexed** = full-text search via the `{items}_fts` virtual table
- **Filter** = exact / enum match
- **Sort** = can order results by this field

**Rule:** Only index fields that will actually be searched. Over-indexing wastes space and slows writes.

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | {< 100 / 100–1000 / 1000+} | {growth expectation} |
| **Entry Size** | {small / medium / large} | Small < 1KB, Medium 1–10KB, Large > 10KB |
| **FTS Enabled** | {yes/no} | {which fields} |
| **Lazy Loading** | {yes/no} | Preview in `_index`, full entry loaded on demand |

**Scaling rules:**
- If expected volume > 1000, lazy loading is required (read `_index`, not all rows)
- Keep large text fields out of `_index.preview`

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "{type}_{slug}",
  "name": "{Human Readable Name}",
  "schemaVersion": "1.0.0",
  "status": "active"
}
```

### Complete (all fields)

```json
{
  "id": "{type}_{slug}",
  "name": "{Human Readable Name}",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "{optional description}",
  "field1": "value1",
  "field2": { "nested": "value" },
  "createdAt": "2026-01-20T00:00:00Z",
  "updatedAt": "2026-01-20T00:00:00Z"
}
```

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `{type}_{slug}` pattern | `{LIBRARY}_INVALID_ID` |
| Required fields | All required fields present | `{LIBRARY}_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `{LIBRARY}_DUPLICATE_ID` |
| Dependency valid | Referenced entries must exist and be active | `{LIBRARY}_INVALID_REFERENCE` |
| Schema version | Must be valid semver | `{LIBRARY}_INVALID_SCHEMA_VERSION` |
| Review status | `generated` libraries require `reviewStatus` | `{LIBRARY}_MISSING_REVIEW_STATUS` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `{OwnerModule}` | Via drafts table → finalise |
| Update | `{OwnerModule}` | Creates a new version |
| Archive | `{OwnerModule}` | Soft delete; cascades `ref_status` |
| Delete | Never | Archive instead |

**Rules:**
- One module owns writes to this library
- Other modules read via Librarian
- All writes create new versions (immutable records)

---

## Migration Rules

| Version Change | Type | Handling |
|----------------|------|----------|
| 1.0.0 → 1.0.1 | Patch | Auto-migrate on read |
| 1.0.x → 1.1.0 | Minor | Auto-migrate, backward-compatible |
| 1.x.x → 2.0.0 | Major | Manual review required |

**Migration principles:**
- Migrations are idempotent (safe to run more than once)
- AI is never allowed to migrate belief-level fields
- Entries without `schemaVersion` are treated as `0.0.0`
- Failed migrations are logged; the entry is preserved as-is

**Migration scripts:** `migrations/{library}/`

---

## Related

| Related To | Relationship |
|------------|--------------|
| `{other-library}` | {how they relate} |
| `{Module}` | {how the module uses this library} |

---

## Notes

*Implementation notes, edge cases, special handling, etc.*

---

*Created: {date}*
*Updated: {date}*

---

# Master Libraries Index Template

> Use this section as the template for the main `LIBRARIES.md` file.

## All Libraries by System

### Brand System

| Library | D1 Binding | Type | Write Owner | Used By |
|---------|-----------|------|-------------|---------|
| brands | BRANDS_DB | core | BrandManager | ThemeGenerator, TemplateEngine |
| audiences | AUDIENCES_DB | core | AudienceManager | ThemeGenerator, TemplateEngine |
| voices | VOICES_DB | core | BrandVoiceManager | PromptBuilder |
| tones | TONES_DB | core | BrandVoiceManager | PromptBuilder |

### Content System

| Library | D1 Binding | Type | Write Owner | Used By |
|---------|-----------|------|-------------|---------|
| themes | THEMES_DB | generated | ContentManager | OutlineGenerator, TemplateEngine |
| outlines | OUTLINES_DB | generated | ContentManager | TemplateEngine |
| templates | TEMPLATES_DB | derived | ContentManager | TemplateEngine |

### AI System

| Library | D1 Binding | Type | Write Owner | Used By |
|---------|-----------|------|-------------|---------|
| prompts | PROMPTS_DB | reference | AISystemManager | PromptBuilder |
| model-configs | MODEL_CONFIGS_DB | reference | ModelManager | AICaller |

*(Continue per system.)*

## Library Types

| Type | Description | Can Rebuild? | Staging? |
|------|-------------|--------------|----------|
| **core** | Human-curated source content | No | Yes |
| **derived** | Computed from other libraries | Yes | Optional |
| **generated** | AI-generated content | Yes | Yes (for review) |
| **reference** | System configuration | No | Yes |

## Library Status

| Status | Meaning |
|--------|---------|
| **Draft** | Schema defined, no entries yet |
| **Seeded** | Has example/starter entries |
| **Active** | Production-ready with real data |

---

*Template version: 2.0.0*
*Created: 2026-05-18*
*Storage model: Cloudflare D1*
