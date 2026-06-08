> **Superseded by `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 (which merges in REPOS-TEMPLATE.md); retained for history.**

# Content Library (Repos) Documentation Template

> **Purpose:** Template for documenting content libraries in /repos/
> **Use:** Copy this template to create REPOS.md or individual repo docs

---

## How To Use

1. Create REPOS.md as master index of all repos
2. Optionally create `/repos/{repo-name}/README.md` for detailed repo docs
3. Document schema that validates entries
4. Add example entries for reference

---

# {RepoName} Library

> **Path:** `/repos/{repo-name}/`
> **Purpose:** {what this library contains}
> **Status:** Draft | Active

---

## Overview

{Brief description of what this content library holds and why it exists}

---

## Used By

| Module | How Used |
|--------|----------|
| `{ModuleName}` | {reads/writes, what for} |
| `{ModuleName}` | {reads/writes, what for} |

---

## Entry Schema

> Validated by: `/src/schemas/{SchemaName}.ts`

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique identifier (e.g., "brand_mind-the-box") |
| `name` | string | Human-readable name |
| `schemaVersion` | string | Version of schema this entry adheres to (e.g., "1.0.0") |
| `status` | EntryStatus | Lifecycle state (see enum below) |
| `{field}` | {type} | {description} |

> **Why schemaVersion?** Enables automatic migration when schema evolves. `initialize()` can detect old entries and upgrade them.

### Entry Status Enum

| Value | Description | AI Visibility |
|-------|-------------|---------------|
| `draft` | Incomplete, not ready for use | Hidden from AI |
| `active` | Production-ready, safe for AI use | Visible to AI |
| `archived` | Deprecated, kept for history | Hidden from AI |
| `flagged` | Needs human review before use | Hidden from AI |

**Rule:** AI modules only see `status: "active"` entries unless explicitly overridden.

### Review Status Enum (for AI-generated content)

> Use when entries can be AI-generated and need human verification.

| Value | Description | Can AI Use? |
|-------|-------------|-------------|
| `pending` | AI-generated, awaiting human review | No |
| `approved` | Human verified as correct | Yes |
| `rejected` | Human marked as incorrect | No |
| `—` | Human-authored (no review needed) | Yes |

**Rule:** For repos with `Source of Truth: AI + Human`, require `reviewStatus` field. Human-authored repos can omit this field.

### Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `{field}` | {type} | {default} | {description} |

---

## File Naming Convention

```
/repos/{repo-name}/
  ├── {id}.json           ← Individual entries
  ├── _index.json         ← Lightweight index of all entries
  └── README.md           ← Optional: repo-specific docs
```

**Naming rules:**
- Use kebab-case for filenames
- Prefix with category if needed (e.g., `brand_`, `audience_`)

**CRITICAL: ID-to-Filename Contract**

The `id` field MUST derive from the filename:

```
Filename: mind-the-box.json
     ↓
ID: brand_mind-the-box  (prefix + filename without extension)
```

| Filename | Valid ID | Invalid ID |
|----------|----------|------------|
| `mind-the-box.json` | `brand_mind-the-box` | `brand-1`, `mtb` |
| `entrepreneurs.json` | `audience_entrepreneurs` | `aud_1`, `audience-1` |

**Why?** FileManager locates entries by ID. If `id` doesn't match filename, lookups fail silently.

**Validation:** `FileManager.save()` must reject entries where `id` suffix doesn't match filename.

---

## Indexing Strategy

> **File:** `_index.json`
> **Update Trigger:** On save/delete via FileManager or Librarian
> **Purpose:** Avoid reading all files for listing operations

### Index Contents

| Included in Index | Excluded from Index |
|-------------------|---------------------|
| `id`, `name` | Full content/body fields |
| `tags`, `category` | Nested heavy objects |
| `schemaVersion` | Historical versions |
| `updatedAt`, `status` | Large metadata blobs |

### Index Structure

```json
{
  "repoName": "{repo-name}",
  "schemaVersion": "1.0.0",
  "updatedAt": "2026-01-19T00:00:00Z",
  "count": 5,
  "entries": [
    { "id": "brand_mtb", "name": "Mind the Box", "status": "active" },
    { "id": "brand_bylw", "name": "Be You Love Wins", "status": "active" }
  ]
}
```

**Rule:** A module needing a list of 50 items reads `_index.json` (1 file), not 50 individual files.

---

## Queryable Fields

> Which fields support search via Librarian (SQLite FTS5 index).

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match only |
| `name` | Yes | Yes | Yes | Full-text search |
| `tags` | Yes | Yes | No | Multi-value filter |
| `status` | No | Yes | No | Enum filter |
| `description` | Yes | No | No | Search only, not filterable |
| `{field}` | {yes/no} | {yes/no} | {yes/no} | {notes} |

**Search Types:**
- **FTS Indexed** = Full-text search ("find themes about productivity")
- **Filter** = Exact/enum match ("status = active")
- **Sort** = Can order results by this field

**Rule:** Only index fields users will actually search. Over-indexing wastes space and slows writes.

---

## Performance & Scale

> How this repo handles growth.

| Aspect | Strategy | Notes |
|--------|----------|-------|
| **Expected Volume** | {< 100 / 100-1000 / 1000+} | {current count, growth expectation} |
| **Index Strategy** | {shallow/deep} | Shallow = ID + name only; Deep = includes tags, category |
| **Lazy Loading** | {yes/no} | Full entry loaded only when needed |
| **Entry Size** | {small/medium/large} | Small < 1KB, Medium 1-10KB, Large > 10KB |

**Scaling Rules:**
- If expected volume > 1000, lazy loading required
- If entry size > 10KB, exclude body fields from index
- Consider splitting into sub-repos if volume > 5000

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "{prefix}_{slug}",
  "name": "{Human Readable Name}",
  "schemaVersion": "1.0.0",
  "status": "active"
}
```

### Complete (all fields)

```json
{
  "id": "{prefix}_{slug}",
  "name": "{Human Readable Name}",
  "schemaVersion": "1.0.0",
  "status": "active",
  "description": "{optional description}",
  "field1": "value1",
  "field2": {
    "nested": "value"
  },
  "createdAt": "2026-01-19T00:00:00Z",
  "updatedAt": "2026-01-19T00:00:00Z"
}
```

---

## Validation

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `{prefix}_{slug}` pattern | `{REPO}_INVALID_ID` |
| ID-filename match | ID suffix must equal filename (minus .json) | `{REPO}_ID_FILENAME_MISMATCH` |
| Required fields | All required fields present | `{REPO}_MISSING_FIELD` |
| Unique ID | No duplicate IDs in repo | `{REPO}_DUPLICATE_ID` |
| Review status | AI-generated entries require reviewStatus | `{REPO}_MISSING_REVIEW_STATUS` |

---

## Write Policy

> Who can create, update, delete entries in this repo.

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | `{Module}` | {conditions} |
| Update | `{Module}` | {which fields} |
| Delete | Human only | Never auto-deleted |
| Auto-update | system fields only | updatedAt, usageCount |

**Rules:**
- Only one module should own writes to a repo
- Cross-module writes require explicit justification
- Deletes are soft (archive) by default

---

## Migration Rules

> How this repo handles schema evolution.

| Version Change | Type | Handling |
|----------------|------|----------|
| 1.0.0 → 1.0.1 | Patch | Auto-migrate, no review |
| 1.0.x → 1.1.0 | Minor | Auto-migrate, backward-compatible |
| 1.x.x → 2.0.0 | Major | Manual review required |

**Migration Principles:**
- Migrations are idempotent (safe to run multiple times)
- AI is never allowed to migrate belief-level fields
- Old entries without schemaVersion treated as "0.0.0"
- Failed migrations logged, entry preserved as-is

---

## Related

| Related To | Relationship |
|------------|--------------|
| `/repos/{other}/` | {how they relate} |
| `{Module}` | {how module uses this repo} |

---

## Notes

*Migration notes, versioning, special handling, etc.*

---

*Created: {date}*
*Updated: {date}*

---

# Master Repos Index Template

> Use this section as template for the main REPOS.md file

## All Content Libraries

| # | Repo | Path | Source of Truth | Used By | Write Owner |
|---|------|------|-----------------|---------|-------------|
| 1 | Brands | `/repos/brands/` | Human | BrandManager, ThemeGenerator | BrandManager |
| 2 | Audiences | `/repos/audiences/` | Human | AudienceManager, ThemeGenerator | AudienceManager |
| 3 | Outcomes | `/repos/outcomes/` | Human | ThemeGenerator | Human (manual) |
| 4 | Prompts | `/repos/prompts/` | Human | PromptBuilder | Human (manual) |
| 5 | Section Specs | `/repos/section-specs/` | System | TemplateEngine, OutlineGenerator | Human (manual) |
| 6 | Content Types | `/repos/content-types/` | System | TemplateEngine, OutlineGenerator | Human (manual) |
| 7 | Cognitive Types | `/repos/cognitive-types/` | System | OutlineGenerator | Human (manual) |
| 8 | Email Templates | `/repos/email-templates/` | Human | Email | Human (manual) |
| 9 | Social Templates | `/repos/social-templates/` | Human | SocialMediaManager | Human (manual) |
| 10 | Templates | `/repos/templates/` | Human | LegalDocCreator | TemplateFactory |

**Source of Truth:**
- **Human** = Human-authored, AI cannot modify
- **System** = System-defined, versioned with code
- **AI + Human** = AI-generated but human-approved

## Repo Status

| Status | Meaning |
|--------|---------|
| **Draft** | Structure defined, no entries yet |
| **Seeded** | Has example/starter entries |
| **Active** | Production ready with real data |
