# IngestionEngine Module

> **Purpose:** Manage draft-to-library promotion workflow
> **Type:** Data Management Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

IngestionEngine manages the promotion of drafts from staging areas to active library entries. Validates references, handles version increments, and maintains audit trails for all promotions.

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Dev Team Gap Analysis (2026-01-20)
- **Staging validation**: Validate draft entries before promotion
  - Schema validation against library specs
  - Reference integrity (all referenced IDs exist)
  - Required fields present
- **Promotion workflow**:
  1. Validate draft in staging area
  2. Check reference integrity via Librarian
  3. Assign version number (increment or initial)
  4. Insert into library database
  5. Update `_index` table
  6. Log promotion in audit trail
  7. Remove from staging area
- **Version increment logic**:
  - New entry: version = 1
  - Update: version = latest + 1
- **Rollback support**: Ability to revert promotion if issues discovered
- **Batch promotion**: Promote multiple related drafts atomically
- **Audit trail**: Log who promoted what and when

### Integration Points
- Reads from: staging area (`/repos/drafts/`)
- Writes to: library databases via DatabaseHandler
- Validates via: Librarian (reference integrity)
- Notifies: CacheHandler (invalidate related cache)

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
