# MigrationHandler Module

> **Purpose:** Data and schema migrations for version upgrades
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

MigrationHandler manages **data migrations** when the system upgrades. It handles schema changes, data transformations, and ensures backward compatibility.

```
New version → MigrationHandler.migrate() → Data transformed → System updated
```

**MigrationHandler handles:**
- Schema version tracking
- Data transformations
- Rollback capability
- Migration validation

---

## Roles & Rules

### MigrationHandler DOES:
- Track schema versions
- Execute migrations in order
- Validate data after migration
- Support rollback
- Log migration history
- Handle partial failures

### MigrationHandler does NOT:
- Deploy code (external process)
- Make business decisions about data
- Skip failed migrations
- Modify running system state

### Boundaries:
- Data migration only
- Must complete or rollback
- Cannot bypass validation
- All migrations logged

---

## Migration Types

| Type | Description | Example |
|------|-------------|---------|
| `schema` | Structure changes | Add new field to brand |
| `data` | Data transformation | Convert old format to new |
| `rename` | Field/file renaming | Rename "colour" to "color" |
| `split` | Split data | Split config into modules |
| `merge` | Combine data | Merge related configs |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `getCurrentVersion()` | Get current schema version | - | version |
| `getAvailableMigrations()` | List pending migrations | - | migrations |
| `migrate(targetVersion)` | Run migrations to version | targetVersion | result |
| `rollback(toVersion)` | Rollback to version | toVersion | result |
| `validateData(version)` | Validate data for version | version | valid |
| `getMigrationHistory()` | Get migration history | - | history |

---

## Detailed Function Specs

### getCurrentVersion()

Get current schema version.

```javascript
// Output
{
  version: "1.5.0",
  migratedAt: "2026-01-15T10:00:00Z",
  stable: true
}
```

### getAvailableMigrations()

List migrations available to run.

```javascript
// Output
{
  currentVersion: "1.5.0",
  latestVersion: "1.7.0",
  pending: [
    {
      version: "1.6.0",
      description: "Add typography section to brands",
      type: "schema",
      breaking: false
    },
    {
      version: "1.7.0",
      description: "Split config into module configs",
      type: "split",
      breaking: true
    }
  ]
}
```

### migrate(targetVersion)

Run migrations to target version.

```javascript
// Input
{
  targetVersion: "1.7.0",
  dryRun: false,
  backup: true
}

// Output - Success
{
  success: true,
  from: "1.5.0",
  to: "1.7.0",
  migrations: [
    { version: "1.6.0", status: "completed", duration: 5000 },
    { version: "1.7.0", status: "completed", duration: 12000 }
  ],
  backup: "/repos/backups/pre-1.7.0-20260117.zip",
  duration: 17000
}

// Output - Failure with rollback
{
  success: false,
  from: "1.5.0",
  to: "1.7.0",
  migrations: [
    { version: "1.6.0", status: "completed", duration: 5000 },
    { version: "1.7.0", status: "failed", error: "Invalid data format" }
  ],
  rolledBack: true,
  currentVersion: "1.5.0",
  error: "Migration 1.7.0 failed - rolled back to 1.5.0"
}
```

### rollback(toVersion)

Rollback to previous version.

```javascript
// Input
{
  toVersion: "1.5.0",
  reason: "Compatibility issue with external system"
}

// Output
{
  success: true,
  from: "1.7.0",
  to: "1.5.0",
  restoredFrom: "/repos/backups/pre-1.7.0-20260117.zip",
  duration: 8000
}
```

---

## Migration Definition

```javascript
// /repos/migrations/1.6.0.js
{
  version: "1.6.0",
  description: "Add typography section to brands",
  type: "schema",
  breaking: false,

  up: async () => {
    // Get all brands
    const brands = await FileManager.list("/repos/brands/");

    for (const brand of brands) {
      const data = await FileManager.read(`/repos/brands/${brand}/brand.json`);

      // Add typography section if missing
      if (!data.typography) {
        data.typography = {
          status: "not_started",
          fonts: null
        };
        await FileManager.write(`/repos/brands/${brand}/brand.json`, data);
      }
    }
  },

  down: async () => {
    // Remove typography section
    const brands = await FileManager.list("/repos/brands/");

    for (const brand of brands) {
      const data = await FileManager.read(`/repos/brands/${brand}/brand.json`);
      delete data.typography;
      await FileManager.write(`/repos/brands/${brand}/brand.json`, data);
    }
  },

  validate: async () => {
    // Verify all brands have typography section
    const brands = await FileManager.list("/repos/brands/");

    for (const brand of brands) {
      const data = await FileManager.read(`/repos/brands/${brand}/brand.json`);
      if (!data.typography) {
        return { valid: false, error: `Brand ${brand} missing typography` };
      }
    }
    return { valid: true };
  }
}
```

---

## Version Tracking

```json
// /repos/system/schema-version.json
{
  "currentVersion": "1.5.0",
  "history": [
    {
      "version": "1.0.0",
      "migratedAt": "2025-01-01T00:00:00Z",
      "description": "Initial schema"
    },
    {
      "version": "1.5.0",
      "migratedAt": "2026-01-15T10:00:00Z",
      "description": "Added brand voice section"
    }
  ]
}
```

---

## Config

### module.config.json

```json
{
  "module": "MigrationHandler",
  "version": "1.0.0",
  "description": "Data and schema migrations",
  "type": "system-service",

  "functions": [
    {
      "name": "getCurrentVersion",
      "description": "Get current schema version",
      "input": [],
      "output": "version"
    },
    {
      "name": "getAvailableMigrations",
      "description": "List pending migrations",
      "input": [],
      "output": "migrations"
    },
    {
      "name": "migrate",
      "description": "Run migrations to version",
      "input": ["targetVersion"],
      "output": "result"
    },
    {
      "name": "rollback",
      "description": "Rollback to version",
      "input": ["toVersion"],
      "output": "result"
    },
    {
      "name": "validateData",
      "description": "Validate data for version",
      "input": ["version"],
      "output": "valid"
    },
    {
      "name": "getMigrationHistory",
      "description": "Get migration history",
      "input": [],
      "output": "history"
    }
  ],

  "config": {
    "migrationsPath": "/repos/migrations/",
    "backupsPath": "/repos/backups/",
    "autoBackup": true,
    "validateAfterMigration": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading/writing data |
| Logger | Logging migrations |
| Archivist | Creating backups |

---

## Used By

| Component | How |
|-----------|-----|
| System startup | Check for pending migrations |
| Admin | Manual migration triggers |

---

*Last updated: 2026-01-17*
