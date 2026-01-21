# Library Documentation Template

> **Purpose:** Template for documenting SQLite-based content libraries
> **Use:** Copy this template to document each library in a system's repository
> **Version:** 1.0.0

---

## How To Use

1. Copy this template to `systems/{System}/{Library}.library.md`
2. Fill in each section based on the library's purpose
3. Document the SQLite table schemas
4. Add entry examples (JSON format for the `data` column)
5. Document staging area if library supports drafts

---

# {LibraryName} Library

> **System:** {System Name}
> **Repository:** {Repository Name}
> **Path:** `/data/{library}.sqlite`
> **Library Type:** core | derived | generated | reference
> **Status:** Draft | Active

---

## Overview

{Brief description of what this library holds and why it exists}

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `{ModuleName}` | Read/Write (Owner) | {what for} |
| `{ModuleName}` | Read via Librarian | {what for} |

---

## SQLite Schema

### Tables

```sql
-- Lightweight index for fast listing
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,                     -- JSON array
  preview JSON,                  -- Key fields for UI
  ref_status TEXT DEFAULT 'valid'  -- 'valid', 'stale', 'orphaned'
);

-- Full versioned data
CREATE TABLE {items} (
  id TEXT,
  version INTEGER,
  data JSON,                     -- Full entry as JSON
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

### Entry Status Enum

| Value | Description | AI Visibility |
|-------|-------------|---------------|
| `draft` | Incomplete, not ready | Hidden |
| `active` | Production-ready | Visible |
| `archived` | Deprecated, kept for history | Hidden |
| `flagged` | Needs human review | Hidden |

### Review Status Enum (for AI-generated libraries)

| Value | Description | Can AI Use? |
|-------|-------------|-------------|
| `pending` | AI-generated, awaiting review | No |
| `approved` | Human verified | Yes |
| `rejected` | Human marked incorrect | No |

### Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `{field}` | {type} | {default} | {description} |

---

## Preview Fields

> Fields included in `_index.preview` for fast UI display.

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

## Staging Area (Drafts)

> For libraries that support human editing before finalization.

**Staging Path:** `/repos/drafts/{library}/`

### Draft Lifecycle

```
1. Create draft        → /repos/drafts/{library}/{type}-{slug}-{timestamp}.json
2. Edit/review         → Human modifies JSON file
3. Approve             → Finalize to library
4. Finalize            → INSERT INTO {items} (id, version, data, created_by)
5. Update index        → UPDATE _index, UPDATE _index.sqlite libraries
6. Delete draft        → Remove JSON file from staging
```

### Draft File Format

Same as entry schema, stored as JSON:

```json
{
  "id": "{type}_{slug}",
  "name": "Draft Entry",
  "schemaVersion": "1.0.0",
  "status": "draft",
  ...
}
```

**Rule:** Drafts are NOT in the library until approved. The library only contains finalized, immutable records.

---

## Dependencies

> Other libraries this library references.

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| `{library}` | `{fieldId}` | hard/soft/reference | {what happens on archive} |

**Dependency Types:**
- **hard** = Required, entry invalid if dependency missing
- **soft** = Optional enrichment
- **reference** = ID-only, loose coupling

---

## Queryable Fields

> Which fields support search via Librarian.

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `status` | No | Yes | No | Enum filter |
| `{field}` | {yes/no} | {yes/no} | {yes/no} | {notes} |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | {< 100 / 100-1000 / 1000+} | {growth expectation} |
| **Entry Size** | {small/medium/large} | Small < 1KB, Medium 1-10KB, Large > 10KB |
| **FTS Enabled** | {yes/no} | {which fields} |
| **Lazy Loading** | {yes/no} | Preview in _index, full load on demand |

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

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `{OwnerModule}` | Via staging → finalize |
| Update | `{OwnerModule}` | Creates new version |
| Archive | `{OwnerModule}` | Soft delete, cascades ref_status |
| Delete | Never | Archive instead |

**Rules:**
- One module owns writes to this library
- Others read via Librarian
- All writes create new versions (immutable records)

---

## Migration Rules

| Version Change | Type | Handling |
|----------------|------|----------|
| 1.0.0 → 1.0.1 | Patch | Auto-migrate on read |
| 1.0.x → 1.1.0 | Minor | Auto-migrate, backward-compatible |
| 1.x.x → 2.0.0 | Major | Manual review required |

**Migration Script Location:** `/migrations/{library}/`

---

## Related

| Related To | Relationship |
|------------|--------------|
| `{other-library}` | {how they relate} |
| `{Module}` | {how module uses this library} |

---

## Notes

*Implementation notes, edge cases, special handling, etc.*

---

*Created: {date}*
*Updated: {date}*

---

# Master Libraries Index Template

> Use this section as template for the main LIBRARIES.md file

## All Libraries by System

### Brand System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| brands | /data/brands.sqlite | core | BrandManager | ThemeGenerator, TemplateEngine |
| audiences | /data/audiences.sqlite | core | BrandManager | ThemeGenerator, TemplateEngine |
| voices | /data/voices.sqlite | core | BrandManager | PromptBuilder |
| tones | /data/tones.sqlite | core | BrandManager | PromptBuilder |

### Content System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| themes | /data/themes.sqlite | generated | ContentManager | OutlineGenerator, TemplateEngine |
| outlines | /data/outlines.sqlite | generated | ContentManager | TemplateEngine |
| templates | /data/templates.sqlite | derived | ContentManager | TemplateEngine |

### AI System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| prompts | /data/prompts.sqlite | reference | AIManager | PromptBuilder |
| model-configs | /data/model-configs.sqlite | reference | AIManager | AICaller |

### Template System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| section-specs | /data/section-specs.sqlite | reference | TemplateEngine | OutlineGenerator |
| content-types | /data/content-types.sqlite | reference | TemplateEngine | OutlineGenerator |
| cognitive-types | /data/cognitive-types.sqlite | reference | TemplateEngine | OutlineGenerator |

### Social System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| social-templates | /data/social-templates.sqlite | reference | SocialMediaManager | Builders |

### Visual System

| Library | Path | Type | Write Owner | Used By |
|---------|------|------|-------------|---------|
| visual-brands | /data/visual-brands.sqlite | core | VisualManager | Renderers |

## Library Types

| Type | Description | Can Rebuild? | Staging Area? |
|------|-------------|--------------|---------------|
| **core** | Human-curated source content | No | Yes |
| **derived** | Computed from other libraries | Yes | Optional |
| **generated** | AI-generated content | Yes | Yes (for review) |
| **reference** | System configuration | No | Yes |

## Library Status

| Status | Meaning |
|--------|---------|
| **Draft** | Schema defined, no entries yet |
| **Seeded** | Has example/starter entries |
| **Active** | Production ready with real data |
