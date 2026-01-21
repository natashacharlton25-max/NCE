# FileManager Libraries Documentation

> **Module:** FileManager
> **Spec Version:** 1.1.0
> **Status:** Placeholder

---

## Libraries Overview

FileManager provides **external access** to all content libraries for modules that don't own them.

| Library | Path | Access | Purpose |
|---------|------|--------|---------|
| brands | `/data/brands.sqlite` | Read | Load brand entries for other modules |
| audiences | `/data/audiences.sqlite` | Read | Load audience entries for other modules |
| outcomes | `/data/outcomes.sqlite` | Read | Load outcome entries for other modules |
| voices | `/data/voices.sqlite` | Read | Load voice entries for other modules |
| tones | `/data/tones.sqlite` | Read | Load tone entries for other modules |
| prompts | `/data/prompts.sqlite` | Read | Load prompt templates |
| section-specs | `/data/section-specs.sqlite` | Read | Load section specs |
| content-types | `/data/content-types.sqlite` | Read | Load content types |
| cognitive-types | `/data/cognitive-types.sqlite` | Read | Load cognitive types |

---

## Responsibilities

FileManager is the **abstraction layer** for external library access:

| Operation | What FileManager Does |
|-----------|----------------------|
| `read(library, id)` | Load single entry from SQLite library |
| `list(library)` | Query _index table for fast listing |
| `exists(library, id)` | Check if entry exists in library |

**Key Rule:** Modules read their **own** database directly via DatabaseHandler. Modules read **other** databases via FileManager.

---

## Access Pattern

```
External module needs brand data:
    │
    ├── Module calls FileManager.read('brands', 'brand_mtb')
    │
    ├── FileManager opens /data/brands.sqlite
    │       → Queries brands_latest view
    │
    └── Returns entry data (JSON from data column)
```

---

## Staging Area Management

FileManager also manages the JSON staging area for drafts:

| Staging Path | Purpose |
|--------------|---------|
| `/repos/drafts/{library}/` | JSON drafts before finalization |
| `/repos/imports/` | Incoming data to be processed |

### Draft Operations

| Operation | What FileManager Does |
|-----------|----------------------|
| `readDraft(library, filename)` | Load JSON draft file |
| `writeDraft(library, filename, data)` | Save JSON draft file |
| `listDrafts(library)` | List draft files in staging folder |
| `deleteDraft(library, filename)` | Remove draft after finalization |

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| Library exists | Path must exist in _index.sqlite | `FILEMANAGER_LIBRARY_NOT_FOUND` |
| Entry exists | ID must exist in library | `FILEMANAGER_ENTRY_NOT_FOUND` |
| Valid JSON | Draft files must parse as JSON | `FILEMANAGER_INVALID_JSON` |

---

## Notes

- FileManager doesn't understand library schemas - it reads/returns JSON
- Schema validation is done by the requesting module
- FileManager never writes to SQLite libraries (that's each owner's job)
- FileManager manages the staging area for all libraries

---

*Created: 2026-01-20*
*Updated: 2026-01-20 - Updated for SQLite library structure*
*Based on spec version: 1.1.0*
