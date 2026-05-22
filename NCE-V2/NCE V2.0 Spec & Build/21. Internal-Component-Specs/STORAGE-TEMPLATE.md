# {{Component Name}} â€” Storage

## Overview

| Attribute | Value |
|-----------|-------|
| Storage Type | SQLite / PostgreSQL / File / Memory |
| Database Path | `/data/{{component}}.db` |
| Tables | {{n}} |
| Indexes | {{n}} |

---

## Database Schema

### Table: `{{table_name}}`

Primary table for {{purpose}}.

```sql
CREATE TABLE {{table_name}} (
  -- Primary Key
  id TEXT PRIMARY KEY,
  
  -- Required Fields
  name TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'draft',
  created_at TEXT NOT NULL,
  
  -- Optional Fields
  description TEXT,
  updated_at TEXT,
  
  -- JSON Fields (stored as TEXT)
  metadata TEXT,          -- JSON object
  tags TEXT,              -- JSON array
  
  -- Foreign Keys
  parent_id TEXT,
  
  -- Constraints
  FOREIGN KEY (parent_id) REFERENCES {{parent_table}}(id),
  CHECK (status IN ('draft', 'active', 'archived'))
);
```

**Column Details:**

| Column | Type | Nullable | Default | Description |
|--------|------|----------|---------|-------------|
| id | TEXT | No | â€” | Primary key, format: `{{prefix}}_{{slug}}` |
| name | TEXT | No | â€” | Human-readable name |
| status | TEXT | No | 'draft' | Current status |
| created_at | TEXT | No | â€” | ISO 8601 timestamp |
| description | TEXT | Yes | NULL | Optional description |
| updated_at | TEXT | Yes | NULL | Last modification time |
| metadata | TEXT | Yes | NULL | JSON object for extensibility |
| tags | TEXT | Yes | NULL | JSON array of strings |
| parent_id | TEXT | Yes | NULL | Reference to parent |

---

### Table: `{{secondary_table}}`

(Repeat pattern for additional tables)

```sql
CREATE TABLE {{secondary_table}} (
  id TEXT PRIMARY KEY,
  {{main_table}}_id TEXT NOT NULL,
  -- ... fields
  FOREIGN KEY ({{main_table}}_id) REFERENCES {{main_table}}(id) ON DELETE CASCADE
);
```

---

## Indexes

### Primary Indexes

```sql
-- Already created by PRIMARY KEY
-- {{table_name}}.id
```

### Secondary Indexes

```sql
-- Status queries (most common filter)
CREATE INDEX idx_{{table}}_status 
  ON {{table_name}}(status);

-- Date range queries
CREATE INDEX idx_{{table}}_created 
  ON {{table_name}}(created_at DESC);

-- Parent lookup
CREATE INDEX idx_{{table}}_parent 
  ON {{table_name}}(parent_id);

-- Compound index for common query pattern
CREATE INDEX idx_{{table}}_status_created 
  ON {{table_name}}(status, created_at DESC);
```

### Full-Text Search Index

```sql
-- FTS5 virtual table for search
CREATE VIRTUAL TABLE {{table_name}}_fts USING fts5(
  id,
  name,
  description,
  tags,
  content='{{table_name}}',
  content_rowid='rowid'
);

-- Triggers to keep FTS in sync
CREATE TRIGGER {{table}}_fts_insert AFTER INSERT ON {{table_name}} BEGIN
  INSERT INTO {{table_name}}_fts(id, name, description, tags)
  VALUES (NEW.id, NEW.name, NEW.description, NEW.tags);
END;

CREATE TRIGGER {{table}}_fts_update AFTER UPDATE ON {{table_name}} BEGIN
  DELETE FROM {{table_name}}_fts WHERE id = OLD.id;
  INSERT INTO {{table_name}}_fts(id, name, description, tags)
  VALUES (NEW.id, NEW.name, NEW.description, NEW.tags);
END;

CREATE TRIGGER {{table}}_fts_delete AFTER DELETE ON {{table_name}} BEGIN
  DELETE FROM {{table_name}}_fts WHERE id = OLD.id;
END;
```

---

## JSON Field Schemas

### metadata

```typescript
{
  createdBy?: string,
  source?: "ai" | "manual",
  version?: number,
  custom?: Record<string, unknown>
}
```

### tags

```typescript
string[]  // e.g., ["important", "reviewed", "Q1"]
```

---

## Query Patterns

### Common Queries

#### Get by ID
```sql
SELECT * FROM {{table_name}} WHERE id = ?;
```

#### List with Filters
```sql
SELECT * FROM {{table_name}}
WHERE status = ?
  AND created_at > ?
ORDER BY created_at DESC
LIMIT ? OFFSET ?;
```

#### Full-Text Search
```sql
SELECT t.* FROM {{table_name}} t
JOIN {{table_name}}_fts fts ON t.id = fts.id
WHERE {{table_name}}_fts MATCH ?
ORDER BY rank;
```

#### Count by Status
```sql
SELECT status, COUNT(*) as count
FROM {{table_name}}
GROUP BY status;
```

---

## Migrations

### Migration Strategy

- Migrations are forward-only (no rollback)
- Migrations run on `initialize()`
- Each migration is idempotent
- Backup created before migration

### Migration Files

```
/migrations/{{component}}/
â”œâ”€â”€ 001_initial.sql
â”œâ”€â”€ 002_add_tags.sql
â”œâ”€â”€ 003_add_fts.sql
â””â”€â”€ ...
```

### Migration Format

```sql
-- Migration: 002_add_tags
-- Created: 2026-01-20
-- Description: Add tags column for categorization

-- Check if migration already applied
-- (handled by migration runner)

ALTER TABLE {{table_name}} ADD COLUMN tags TEXT;

CREATE INDEX idx_{{table}}_tags ON {{table_name}}(tags);
```

### Migration Tracking

```sql
CREATE TABLE IF NOT EXISTS _migrations (
  id INTEGER PRIMARY KEY,
  name TEXT NOT NULL UNIQUE,
  applied_at TEXT NOT NULL
);
```

---

## Data Integrity

### Constraints

| Constraint | Type | Purpose |
|------------|------|---------|
| Primary Key | UNIQUE, NOT NULL | Unique identifier |
| Foreign Key | REFERENCES | Referential integrity |
| Check | CHECK | Valid enum values |
| Not Null | NOT NULL | Required fields |

### Referential Integrity

| Parent Table | Child Table | On Delete |
|--------------|-------------|-----------|
| {{table_name}} | {{child_table}} | CASCADE |
| {{table_name}} | {{other_table}} | SET NULL |

---

## Backup & Recovery

### Backup Strategy

| Type | Frequency | Retention |
|------|-----------|-----------|
| Full backup | Daily | 7 days |
| WAL checkpoint | Hourly | 24 hours |

### Backup Commands

```bash
# Full backup
sqlite3 {{component}}.db ".backup {{component}}_backup.db"

# Export to SQL
sqlite3 {{component}}.db ".dump" > {{component}}_dump.sql
```

### Recovery

```bash
# Restore from backup
cp {{component}}_backup.db {{component}}.db

# Restore from SQL dump
sqlite3 {{component}}.db < {{component}}_dump.sql
```

---

## Performance Considerations

### Index Usage

| Query Pattern | Index Used | Notes |
|---------------|------------|-------|
| Get by ID | Primary key | O(log n) |
| Filter by status | idx_status | Good selectivity |
| Date range | idx_created | Use with LIMIT |
| Full-text search | FTS5 | Use MATCH syntax |

### Query Optimization

- Use LIMIT for pagination
- Avoid SELECT * in production
- Use prepared statements
- Monitor query duration

### Storage Limits

| Limit | Value | Action When Exceeded |
|-------|-------|---------------------|
| Database size | 10 GB | Alert, archive old data |
| Row count | 1,000,000 | Partition or archive |
| Single row size | 1 MB | Split large JSON fields |

---

## Maintenance

### Regular Tasks

| Task | Frequency | Command |
|------|-----------|---------|
| VACUUM | Weekly | `VACUUM;` |
| ANALYZE | Daily | `ANALYZE;` |
| Integrity check | Monthly | `PRAGMA integrity_check;` |
| Index rebuild | Monthly | `REINDEX;` |

### Monitoring

| Metric | Query | Alert Threshold |
|--------|-------|-----------------|
| Database size | `PRAGMA page_count * page_size` | > 8 GB |
| Table rows | `SELECT COUNT(*) FROM {{table}}` | > 800,000 |
| Query time | Application metrics | > 100ms avg |

---

## Notes

- SQLite in WAL mode for better concurrency
- JSON fields use TEXT type (SQLite JSON1 extension for queries)
- All timestamps in ISO 8601 UTC format
- See CONFIG.md for database path configuration

---
Source: PRE-SPEC-NOTES.md section 9
Phase: 21 (Internal Component Specs)
---
