# Migration Strategy

---
Project: {{Project Name}}
Database: {{PostgreSQL / SQLite / D1 / etc.}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Overview

This document defines how database schema changes are managed throughout the project lifecycle.

---

## Migration Approach

| Aspect | Decision |
|--------|----------|
| Migration Tool | {{tool: e.g., Drizzle, Prisma, raw SQL, etc.}} |
| Migration Type | Forward-only / Reversible |
| Versioning | Sequential numbers / Timestamps |
| Execution | Manual / Automated (CI/CD) |

---

## Migration File Structure

```
migrations/
├── 001_initial_schema.sql
├── 002_add_user_preferences.sql
├── 003_add_content_tags.sql
└── ...
```

**Naming Convention:** `{number}_{description}.sql`

- Number: 3-digit sequential (001, 002, ...)
- Description: snake_case, describes change
- Extension: .sql (or framework-specific)

---

## Migration File Format

```sql
-- Migration: {{number}}_{{description}}
-- Created: {{date}}
-- Author: {{author}}
-- Description: {{what this migration does}}

-- ============================================
-- UP MIGRATION
-- ============================================

{{SQL statements to apply the change}}

-- ============================================
-- DOWN MIGRATION (if reversible)
-- ============================================

{{SQL statements to reverse the change}}

-- ============================================
-- VERIFICATION
-- ============================================

-- Query to verify migration succeeded
{{verification query}}
```

---

## Migration Rules

### DO:
- One logical change per migration
- Test migrations on copy of production data
- Include verification queries
- Document breaking changes
- Back up before running

### DO NOT:
- Combine unrelated changes
- Modify existing migrations after deployment
- Delete migrations
- Skip migration numbers

---

## Change Categories

| Category | Example | Approach |
|----------|---------|----------|
| Add table | New `auth_tokens` table | Simple CREATE |
| Add column | Add `bio` to `auth_users` | ALTER with default |
| Add index | Index on `created_at` | CREATE INDEX CONCURRENTLY |
| Remove column | Drop unused column | Deprecate first, then remove |
| Rename column | `name` → `full_name` | Add new, migrate data, drop old |
| Change type | `VARCHAR` → `TEXT` | Depends on data compatibility |

---

## Breaking Changes

Changes that require coordination:

| Change Type | Impact | Process |
|-------------|--------|---------|
| Remove column | Code may reference it | 1. Remove code references, 2. Deploy, 3. Remove column |
| Rename column | Code uses old name | 1. Add new column, 2. Migrate data, 3. Update code, 4. Deploy, 5. Drop old |
| Change type | Data may not convert | 1. Add new column, 2. Migrate with conversion, 3. Update code, 4. Drop old |
| Remove table | Code may reference it | Same as column removal |

---

## Rollback Procedures

### Reversible Migration

```sql
-- Run the DOWN section of the migration
{{down_migration_sql}}
```

### Non-Reversible Migration

1. Restore from backup
2. Or: Create compensating migration

### Emergency Rollback

1. Stop application
2. Restore database from backup
3. Deploy previous application version
4. Investigate root cause

---

## Environment-Specific

| Environment | Migration Execution | Backup Required |
|-------------|---------------------|-----------------|
| Development | Manual / On-start | No |
| Staging | CI/CD Pipeline | Recommended |
| Production | CI/CD with approval | **Required** |

---

## Pre-Migration Checklist

- [ ] Migration tested locally
- [ ] Migration tested on staging
- [ ] Backup created (production)
- [ ] Rollback plan documented
- [ ] Team notified (if breaking)
- [ ] Maintenance window scheduled (if needed)

---

## Post-Migration Checklist

- [ ] Verification queries passed
- [ ] Application health check passed
- [ ] No error spikes in logs
- [ ] Performance within bounds

---

## Migration Tracking

Migrations are tracked in the database:

```sql
CREATE TABLE _migrations (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL UNIQUE,
  applied_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

Query to see applied migrations:
```sql
SELECT name, applied_at FROM _migrations ORDER BY id;
```

---

## Data Migrations

For changes requiring data transformation:

```sql
-- Migration: 005_normalize_email_addresses
-- Type: Data migration

-- Step 1: Update data
UPDATE auth_users SET email = LOWER(email);

-- Step 2: Add constraint to prevent future issues
ALTER TABLE auth_users ADD CONSTRAINT email_lowercase 
  CHECK (email = LOWER(email));
```

**Rules for data migrations:**
- Always test on production-size dataset
- Estimate execution time
- Consider batching for large tables
- Have rollback plan for data

---

## Seed Data

Initial data required for the application:

| Table | Seed Data | When |
|-------|-----------|------|
| {{table}} | {{description}} | Initial setup |

Seed files location: `migrations/seeds/`

---

## Notes

{{Any additional migration strategy notes}}

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
