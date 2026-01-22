# TemplateEngine Libraries Documentation

> **Module:** TemplateEngine
> **Spec Version:** 1.1.1
> **Status:** Placeholder

---

## Libraries Overview

| Library | Path | Access | Purpose |
|---------|------|--------|---------|
| templates | `/data/templates.sqlite` | Read (own) | Load template definitions |
| section-specs | `/data/section-specs.sqlite` | Read via FileManager | Load section type definitions |
| brands | `/data/brands.sqlite` | Read via FileManager | Load brand context for generation |
| audiences | `/data/audiences.sqlite` | Read via FileManager | Load audience context for generation |

---

## Data Access Pattern

| Library | Access Method | Rationale |
|---------|---------------|-----------|
| templates | Direct via DatabaseHandler | Own library - full access |
| section-specs | FileManager.read() | External library |
| brands | FileManager.read() | External library |
| audiences | FileManager.read() | External library |

---

## Library: Templates (Own)

> **Path:** `/data/templates.sqlite`
> **Access:** Direct via DatabaseHandler

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Template identifier |
| `name` | Essential | Template name |
| `sections` | Essential | Section definitions |
| `contentType` | Essential | Content type reference |
| `metadata` | Supporting | Template metadata |

### Access Notes

TemplateEngine reads templates directly from its own database. Writes are handled by TemplateFactory.

---

## Library: Section Specs (External)

> **Path:** `/data/section-specs.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Section type identifier |
| `name` | Essential | Section name |
| `fields` | Essential | Field definitions |
| `constraints` | Supporting | Validation rules |

### Integration Notes

Section specs define the structure for each section type in templates.

---

## Library: Brands (External)

> **Path:** `/data/brands.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Brand identifier |
| `name` | Essential | Brand name |
| `voice` | Essential | Voice for content tone |
| `values` | Supporting | Brand values for alignment |

---

## Library: Audiences (External)

> **Path:** `/data/audiences.sqlite`
> **Access:** Read via FileManager

### Fields Used

| Field | Token Priority | Purpose |
|-------|----------------|---------|
| `id` | Essential | Audience identifier |
| `name` | Essential | Audience name |
| `painPoints` | Supporting | For content relevance |
| `goals` | Supporting | For content targeting |

---

## Data Flow

```
TemplateEngine.run(templateId, context)
    │
    ├── DatabaseHandler.query(templates, templateId)
    │       → template definition (own database)
    │
    ├── FileManager.read('brands', context.brandId)
    │       → brand context (external library)
    │
    ├── FileManager.read('audiences', context.audienceId)
    │       → audience context (optional, external library)
    │
    └── For each section in template:
            FileManager.read('section-specs', section.type)
                → section spec (external library)
```

---

## Dependencies

| Depends On | Field | Dependency Type | Notes |
|------------|-------|-----------------|-------|
| templates | n/a | own | Direct access |
| section-specs | section.type | soft | Template references spec |
| brands | context.brandId | soft | Runtime context |
| audiences | context.audienceId | soft | Optional runtime context |

---

## Notes

- TemplateEngine reads templates directly (own database)
- External libraries accessed via FileManager
- Template writes handled by TemplateFactory, not TemplateEngine

---

*Created: 2026-01-20*
*Updated: 2026-01-20 - Updated for SQLite library structure*
*Based on spec version: 1.1.1*
