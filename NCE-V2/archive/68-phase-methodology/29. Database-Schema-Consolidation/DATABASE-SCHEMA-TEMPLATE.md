# Database Schema

---
Project: {{Project Name}}
Database Type: {{SQL / Document / Key-Value / Graph}}
Database: {{PostgreSQL / SQLite / D1 / MongoDB / etc.}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Overview

| Metric | Value |
|--------|-------|
| Total Tables/Collections | {{n}} |
| Components with Storage | {{n}} |
| Cross-Component References | {{n}} |

---

## Naming Convention

**Pattern:** `{component}_{entity}`

| Prefix | Component | Examples |
|--------|-----------|----------|
| `auth_` | Auth System | `auth_users`, `auth_sessions` |
| `content_` | Content System | `content_posts`, `content_tags` |
| {{prefix}} | {{component}} | {{examples}} |

**Exceptions:** See PRECODE-DECISION-NOTES.md

---

## Schema by Component

### {{Component Name}}

Source: `{{component}}/spec/STORAGE.md`

#### {{component}}_{{entity}}

**Purpose:** {{what this table stores}}

```sql
CREATE TABLE {{component}}_{{entity}} (
  -- Primary Key
  id {{TYPE}} PRIMARY KEY,
  
  -- Fields
  {{field}} {{TYPE}} {{CONSTRAINTS}},
  {{field}} {{TYPE}} {{CONSTRAINTS}},
  
  -- Timestamps
  created_at TIMESTAMP NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMP NOT NULL DEFAULT NOW(),
  
  -- Foreign Keys
  {{fk_field}} {{TYPE}} REFERENCES {{other_table}}(id),
  
  -- Constraints
  CONSTRAINT {{name}} {{CONSTRAINT}}
);
```

**Columns:**

| Column | Type | Nullable | Default | Description |
|--------|------|----------|---------|-------------|
| id | {{type}} | No | — | Primary key |
| {{column}} | {{type}} | Yes/No | {{default}} | {{description}} |

**Indexes:**

| Index | Columns | Type | Purpose |
|-------|---------|------|---------|
| PRIMARY | id | B-tree | Primary key |
| idx_{{name}} | {{columns}} | {{type}} | {{purpose}} |

**Foreign Keys:**

| Column | References | On Delete |
|--------|------------|-----------|
| {{column}} | {{table}}.{{column}} | CASCADE / SET NULL / RESTRICT |

---

### {{Next Component}}

(Repeat pattern for each component)

---

## Cross-Component Relationships

```
┌─────────────────┐         ┌─────────────────┐
│   auth_users    │         │  content_posts  │
├─────────────────┤         ├─────────────────┤
│ id (PK)         │◄────────│ author_id (FK)  │
│ email           │         │ id (PK)         │
│ ...             │         │ ...             │
└─────────────────┘         └─────────────────┘
```

| From Table | From Column | To Table | To Column | Relationship |
|------------|-------------|----------|-----------|--------------|
| {{table}} | {{column}} | {{table}} | {{column}} | 1:N / N:M / 1:1 |

---

## Shared Types

From `standards/SCHEMAS.md`:

| Type | Definition | Used By |
|------|------------|---------|
| UUID | `TEXT` / `VARCHAR(36)` | All `id` fields |
| Timestamp | `TIMESTAMP WITH TIME ZONE` | All `*_at` fields |
| {{type}} | {{definition}} | {{tables}} |

---

## Index Summary

All indexes across the schema:

| Table | Index | Columns | Type |
|-------|-------|---------|------|
| {{table}} | {{index}} | {{columns}} | {{type}} |

---

## Constraint Summary

All constraints across the schema:

| Table | Constraint | Type | Definition |
|-------|------------|------|------------|
| {{table}} | {{name}} | UNIQUE / CHECK / FK | {{definition}} |

---

## Storage Estimates

| Table | Row Size (est.) | Expected Rows | Total Size |
|-------|-----------------|---------------|------------|
| {{table}} | ~{{n}} bytes | {{n}} | ~{{size}} |

---

## Conflict Check

| Check | Result |
|-------|--------|
| No naming collisions | ✅ / ❌ |
| No type mismatches | ✅ / ❌ |
| All references valid | ✅ / ❌ |
| No circular references | ✅ / ❌ |

**Conflicts Found:** None / See PRECODE-DECISION-NOTES.md

---

## Change Log

| Version | Date | Change | Author |
|---------|------|--------|--------|
| v1.0.0 | {{date}} | Initial schema | Claude |

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
