# OutlineGenerator Libraries Documentation

> **Module:** OutlineGenerator
> **Spec Version:** 1.3.1
> **Status:** Placeholder

---

## Libraries Overview

| Library | Path | Access | Purpose |
|---------|------|--------|---------|
| outlines | `/data/outlines.sqlite` | Read/Write (own) | Store generated outlines |
| themes | `/data/themes.sqlite` | Read via Librarian | Load theme context |
| section-specs | `/data/section-specs.sqlite` | Read via FileManager | Load section type definitions |
| content-types | `/data/content-types.sqlite` | Read via FileManager | Load content type config |
| cognitive-types | `/data/cognitive-types.sqlite` | Read via FileManager | Load cognitive frameworks |

---

## Data Access Pattern

| Library | Access Method | Rationale |
|---------|---------------|-----------|
| outlines | Direct via DatabaseHandler | Own library - full access |
| themes | Librarian.get() | Cross-system discovery |
| section-specs | FileManager.read() | External reference library |
| content-types | FileManager.read() | External reference library |
| cognitive-types | FileManager.read() | External reference library |

---

## Library: Outlines (Own)

> **Path:** `/data/outlines.sqlite`
> **Access:** Read/Write via DatabaseHandler

### Write Operations

| Operation | Method | Notes |
|-----------|--------|-------|
| Create outline | INSERT into outlines table | New version = 1 |
| Update outline | INSERT new version | Increments version |
| Update index | UPDATE _index table | After each write |

### Fields Written

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Outline identifier |
| `version` | integer | Version number |
| `data` | JSON | Full outline object |
| `created_at` | timestamp | Auto-set |
| `created_by` | string | Module identifier |

### Review Status

Outlines are AI-generated and require human review:

| Status | Meaning | Queryable? |
|--------|---------|------------|
| `pending` | Awaiting review | Yes, but hidden from AI |
| `approved` | Human verified | Yes, visible |
| `rejected` | Marked incorrect | No |

---

## Library: Themes (External)

> **Path:** `/data/themes.sqlite`
> **Access:** Read via Librarian

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Theme identifier |
| `name` | Essential | Theme name |
| `angles` | Essential | Content angles for outline |
| `target_outcomes` | Supporting | For outcome alignment |

### Integration Notes

OutlineGenerator uses Librarian (not FileManager) to access themes because:
- Themes are in a different system (Content vs Template)
- May need discovery/search capabilities
- Librarian handles cross-system access patterns

---

## Library: Section Specs (External)

> **Path:** `/data/section-specs.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Section type identifier |
| `name` | Essential | Section name |
| `fields` | Essential | Field definitions for outline guidance |
| `constraints` | Supporting | Validation rules |

### Integration Notes

OutlineGenerator uses section specs to understand what each section needs, then generates guidance for filling it.

---

## Library: Content Types (External)

> **Path:** `/data/content-types.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Content type identifier |
| `name` | Essential | Content type name |
| `planDepth` | Essential | How detailed outlines should be |
| `sectionRequirements` | Supporting | Which sections required |

### planDepth Values

| Value | Outline Detail |
|-------|----------------|
| `minimal` | High-level guidance only |
| `standard` | Section-by-section guidance |
| `detailed` | Paragraph-level planning |

---

## Library: Cognitive Types (External)

> **Path:** `/data/cognitive-types.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Cognitive type identifier |
| `name` | Essential | Cognitive type name |
| `structure` | Essential | Framework structure |
| `transitions` | Supporting | How sections connect |

### Integration Notes

Cognitive types define reasoning frameworks (problem-solution, compare-contrast, etc.) that shape outline structure and flow.

---

## Data Flow

```
OutlineGenerator.generate(params)
    │
    ├── Librarian.get('themes', params.themeId)
    │       → theme context, angles
    │
    ├── FileManager.read('content-types', params.contentType)
    │       → planDepth, structure rules
    │
    ├── FileManager.read('cognitive-types', params.cognitiveType)
    │       → reasoning framework (optional)
    │
    ├── For each section in template:
    │       FileManager.read('section-specs', section.type)
    │           → section fields, constraints
    │
    ├── [AI Generation]
    │       → generates outline
    │
    └── DatabaseHandler.insert('outlines', outlineData)
            → stores in own database
```

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| themes | themeId | hard | Outline orphaned if theme archived |
| content-types | contentType | soft | Reference only |
| cognitive-types | cognitiveType | soft | Optional reference |
| section-specs | section.type | soft | Reference only |

---

## Staging Area

**Path:** `/repos/drafts/outlines/`

AI-generated outlines go to staging for review before finalization:

```
1. OutlineGenerator.generate() → Creates draft
2. Draft saved to /repos/drafts/outlines/outline-{slug}-{timestamp}.json
3. Human reviews draft
4. On approval → Finalize to /data/outlines.sqlite
5. Delete draft from staging
```

---

## Notes

- OutlineGenerator owns outlines.sqlite (read/write access)
- Uses Librarian for cross-system theme access
- Uses FileManager for reference library access
- All generated outlines start in staging area with `review_status: pending`

---

*Created: 2026-01-20*
*Updated: 2026-01-20 - Updated for SQLite library structure*
*Based on spec version: 1.3.1*
