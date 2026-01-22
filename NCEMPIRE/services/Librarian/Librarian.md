# Librarian Module

> **Purpose:** Discovery layer for cross-system search, list, recommend, and suggest operations
> **Type:** Data Management Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

Librarian provides the discovery layer across repos and databases. While FileManager handles direct fetches (know the ID), Librarian handles discovery (search, list, recommend, suggest).

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

- FileManager handles direct fetch operations
- Librarian handles discovery operations

### Dev Team Gap Analysis (2026-01-20)
- **Multi-library search**: Aggregate results across brands, audiences, themes, templates
- **Recommendation engine**: Suggest themes/templates based on usage patterns
- **Reference integrity checking**: Validate cross-library references exist
- **Cascade notifications**: When entity archived, notify dependents
  - Archive cascade: Mark `ref_status='stale'` on soft dependents
  - Block archive if active hard dependents exist
- **FTS query helpers**: Unified search interface across all FTS-enabled libraries
- **Cache invalidation signals**: Notify CacheHandler when library data changes

### Reference Integrity Workflow
1. **On Archive Request**: Check dependents via `ref_status` queries
2. **Hard Dependency Found**: Block archive, return dependent IDs
3. **Soft Dependency Found**: Archive proceeds, mark dependents `ref_status='stale'`
4. **No Dependents**: Archive proceeds normally
5. **Notify**: CacheHandler invalidates affected entries

### Cascade Rules by Library
| Library | On Archive | Dependents Affected |
|---------|------------|---------------------|
| brands | warn | themes, audiences, frameworks (proprietary) |
| audiences | warn | themes |
| themes | block if active outlines | outlines |
| templates | block if active outlines | outlines |
| outcomes | mark themes stale | themes |
| cognitive-types | warn | templates, outlines |
| frameworks | warn | content-type-frameworks, themes, outlines |
| content-type-frameworks | warn | templates, outlines |
| paragraph-structures | warn | outlines |
| section-specs | block if active mappings | content-type-frameworks, paragraph-structures, templates, outlines |
| content-types | warn | templates |
| voices | warn | brands |
| tones | warn | brands |

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Status: Placeholder*
