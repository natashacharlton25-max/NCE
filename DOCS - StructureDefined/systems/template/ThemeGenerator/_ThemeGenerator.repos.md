# ThemeGenerator Libraries Documentation

> **Module:** ThemeGenerator
> **Spec Version:** 1.7.0
> **Status:** Placeholder

---

## Libraries Overview

| Library | Path | Access | Purpose |
|---------|------|--------|---------|
| themes | `/data/themes.sqlite` | Read/Write (own) | Store generated themes |
| brands | `/data/brands.sqlite` | Read via FileManager | Load brand context |
| audiences | `/data/audiences.sqlite` | Read via FileManager | Load audience context |
| outcomes | `/data/outcomes.sqlite` | Read via FileManager | Load transformation outcomes |

---

## Data Access Pattern

| Library | Access Method | Rationale |
|---------|---------------|-----------|
| themes | Direct via DatabaseHandler | Own library - full access |
| brands | FileManager.read() | External library |
| audiences | FileManager.read() | External library |
| outcomes | FileManager.read() | External library |

---

## Library: Themes (Own)

> **Path:** `/data/themes.sqlite`
> **Access:** Read/Write via DatabaseHandler

### Write Operations

| Operation | Method | Notes |
|-----------|--------|-------|
| Create theme | INSERT into themes table | New version = 1 |
| Update theme | INSERT new version | Increments version |
| Update index | UPDATE _index table | After each write |

### Fields Written

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Theme identifier |
| `version` | integer | Version number |
| `data` | JSON | Full theme object |
| `created_at` | timestamp | Auto-set |
| `created_by` | string | Module identifier |

### Review Status

Themes are AI-generated and require human review:

| Status | Meaning | Queryable? |
|--------|---------|------------|
| `pending` | Awaiting review | Yes, but hidden from AI |
| `approved` | Human verified | Yes, visible |
| `rejected` | Marked incorrect | No |

---

## Library: Brands (External)

> **Path:** `/data/brands.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Brand identifier |
| `name` | Essential | Brand name for context |
| `voice` | Essential | Brand voice for tone alignment |
| `values` | Essential | Brand values for angle governance |
| `truthApprovalMode` | Essential | Controls truth editing (strict/guided/trusted) |

---

## Library: Audiences (External)

> **Path:** `/data/audiences.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Audience identifier |
| `name` | Essential | Audience name for context |
| `painPoints` | Supporting | For theme relevance |
| `goals` | Supporting | For outcome alignment |

---

## Library: Outcomes (External)

> **Path:** `/data/outcomes.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Outcome identifier |
| `name` | Essential | Outcome name |
| `description` | Supporting | What transformation looks like |

### Integration Notes

ThemeGenerator uses outcomes for `target_outcomes[]` field - the transformation the content aims to produce in the audience.

---

## Data Flow

```
ThemeGenerator.generate(params)
    │
    ├── FileManager.read('brands', params.brandId)
    │       → brand context, values, truthApprovalMode
    │
    ├── FileManager.read('audiences', params.audienceId)
    │       → audience context
    │
    ├── For each target_outcome:
    │       FileManager.read('outcomes', outcomeId)
    │           → outcome details
    │
    ├── [AI Generation]
    │       → generates theme
    │
    └── DatabaseHandler.insert('themes', themeData)
            → stores in own database
```

---

## Dependencies

| Depends On | Field | Dependency Type | Cascade Rule |
|------------|-------|-----------------|--------------|
| brands | brandId | hard | Theme orphaned if brand archived |
| audiences | audienceId | hard | Theme orphaned if audience archived |
| outcomes | target_outcomes[] | soft | Theme stale if outcome archived |

---

## Staging Area

**Path:** `/repos/drafts/themes/`

AI-generated themes go to staging for review before finalization:

```
1. ThemeGenerator.generate() → Creates draft
2. Draft saved to /repos/drafts/themes/theme-{slug}-{timestamp}.json
3. Human reviews draft
4. On approval → Finalize to /data/themes.sqlite
5. Delete draft from staging
```

---

## Notes

- ThemeGenerator owns themes.sqlite (read/write access)
- External libraries accessed via FileManager (read-only)
- All generated themes start in staging area with `review_status: pending`
- Themes reference brands/audiences by ID - must validate on finalization

---

*Created: 2026-01-20*
*Updated: 2026-01-20 - Updated for SQLite library structure*
*Based on spec version: 1.7.0*
