# Archivist Module

> **Purpose:** Validate and save entries to repos, maintain data integrity
> **Type:** System Service
> **Status:** Planning

---

## Overview

Archivist is the gatekeeper for all repo writes. Nothing gets written to repos without going through Archivist. It validates data against schemas, ensures required fields exist, and coordinates with Librarian to update indexes.

```
New data → Archivist.save() → Validate → Writer.write() → Librarian.invalidateCache()
```

**Why Archivist:**
- Single point for all writes
- Enforces schema validation
- Maintains data integrity
- Triggers index updates

---

## Roles & Rules

### Archivist DOES:
- Validate data against repo schemas
- Add required fields (created, updated, version, status)
- Save entries to repos via Writer
- Create backups before modifying existing entries
- Soft delete (mark deprecated, never hard delete)
- Trigger Librarian cache invalidation after writes

### Archivist does NOT:
- Read files (FileManager does that)
- Search for entries (Librarian does that)
- Write directly to disk (Writer does that)
- Define schemas (human/admin only)

### Boundaries:
- Cannot bypass validation
- Cannot write without schema compliance
- Cannot hard delete entries
- Cannot modify backup files

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `save(repo, entry)` | Validate and save entry | repo, entry | saveResult |
| `update(repo, entryId, changes)` | Update existing entry | repo, entryId, changes | updateResult |
| `delete(repo, entryId)` | Mark entry as deprecated | repo, entryId | deleteResult |
| `validateEntry(entry, repo)` | Validate against repo schema | entry, repo | validationResult |
| `prepareEntry(data, repo)` | Add required fields, normalize | data, repo | preparedEntry |
| `backup(repo, entryId)` | Create backup before change | repo, entryId | backupId |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Archivist.saveWithBackup` | Save with backup of existing | backup → save |
| `Archivist.updateSafe` | Safe update with validation | validateEntry → backup → update |

---

## Detailed Function Specs

### save(repo, entry)

Main entry point - validates and saves to repo.

```javascript
// Input
{
  repo: "tones",
  entry: {
    id: "friendly-casual",
    name: "Friendly Casual",
    style: "warm, approachable, conversational",
    vocabulary: ["hey", "awesome", "love"],
    avoid: ["pursuant", "whereas", "heretofore"]
  }
}

// Process
1. prepareEntry(entry, repo) → add created, updated, status, version
2. validateEntry(preparedEntry, repo) → check against tone.schema.json
3. If invalid → return errors
4. If entry exists → backup(repo, entry.id)
5. Writer.write(repo, entry.id, preparedEntry)
6. Librarian.cache.invalidate(repo)

// Output (success)
{
  success: true,
  repo: "tones",
  entryId: "friendly-casual",
  action: "created",  // or "updated"
  path: "/repos/tones/friendly-casual.json"
}

// Output (validation failed)
{
  success: false,
  errors: [
    { field: "style", message: "Required field missing" }
  ]
}
```

### update(repo, entryId, changes)

Updates specific fields on existing entry.

```javascript
// Input
{
  repo: "tones",
  entryId: "friendly-casual",
  changes: {
    vocabulary: ["hey", "awesome", "love", "great"],
    notes: "Added 'great' to vocabulary"
  }
}

// Process
1. FileManager.get(repo, entryId) → existing entry
2. Merge changes into existing
3. Update "updated" timestamp
4. Increment version
5. validateEntry(merged, repo)
6. backup(repo, entryId)
7. Writer.write(repo, entryId, merged)
8. Librarian.cache.invalidate(repo)

// Output
{
  success: true,
  repo: "tones",
  entryId: "friendly-casual",
  action: "updated",
  version: "1.1",
  changes: ["vocabulary", "notes"]
}
```

### delete(repo, entryId)

Soft delete - marks as deprecated, doesn't remove file.

```javascript
// Input
{
  repo: "tones",
  entryId: "old-formal"
}

// Process
1. FileManager.get(repo, entryId)
2. Set status = "deprecated"
3. Set deprecated_date = now
4. Optionally set deprecated_by (replacement entry)
5. Writer.write(repo, entryId, updated)
6. Librarian.cache.invalidate(repo)

// Output
{
  success: true,
  repo: "tones",
  entryId: "old-formal",
  action: "deprecated",
  message: "Entry marked as deprecated, not deleted"
}
```

### prepareEntry(data, repo)

Adds required fields and normalizes data.

```javascript
// Input
{
  data: {
    id: "new-tone",
    name: "New Tone",
    style: "energetic"
  },
  repo: "tones"
}

// Process
1. Ensure id is kebab-case
2. Add created = now (if new)
3. Add updated = now
4. Add status = "active" (if not set)
5. Add version = "1.0" (if new)

// Output
{
  id: "new-tone",
  name: "New Tone",
  style: "energetic",
  created: "2026-01-17",
  updated: "2026-01-17",
  status: "active",
  version: "1.0"
}
```

### validateEntry(entry, repo)

Validates entry against repo's schema.

```javascript
// Input
{
  entry: { id: "new-tone", name: "New Tone" },
  repo: "tones"
}

// Process
1. Get schema for repo: FileManager.getSchema('tone')
2. Validate entry against schema
3. Return result

// Output (valid)
{
  valid: true,
  entry: { ... }
}

// Output (invalid)
{
  valid: false,
  errors: [
    { field: "style", message: "Required field missing", rule: "required" },
    { field: "id", message: "Must be kebab-case", rule: "pattern" }
  ]
}
```

### backup(repo, entryId)

Creates backup before modifying existing entry.

```javascript
// Input
{
  repo: "tones",
  entryId: "friendly-casual"
}

// Process
1. FileManager.get(repo, entryId)
2. Write to /repos/_backups/{repo}/{entryId}_{timestamp}.json

// Output
{
  backupId: "friendly-casual_20260117143052",
  path: "/repos/_backups/tones/friendly-casual_20260117143052.json"
}
```

---

## Repo Schema Mapping

```javascript
const REPO_SCHEMAS = {
  "tones": "tone.schema.json",
  "prompts": "prompt.schema.json",
  "model-cards": "model-card.schema.json",
  "brand-profiles": "brand-profile.schema.json",
  "palettes": "palette.schema.json",
  "values": "values.schema.json",
  "fonts": "font.schema.json"
};
```

---

## Config

### module.config.json

```json
{
  "module": "Archivist",
  "version": "1.0.0",
  "description": "Validate and save entries to repos",

  "functions": [
    {
      "name": "save",
      "description": "Validate and save entry",
      "input": ["repo", "entry"],
      "output": "saveResult"
    },
    {
      "name": "update",
      "description": "Update existing entry",
      "input": ["repo", "entryId", "changes"],
      "output": "updateResult"
    },
    {
      "name": "delete",
      "description": "Mark entry as deprecated",
      "input": ["repo", "entryId"],
      "output": "deleteResult"
    },
    {
      "name": "validateEntry",
      "description": "Validate against repo schema",
      "input": ["entry", "repo"],
      "output": "validationResult"
    },
    {
      "name": "prepareEntry",
      "description": "Add required fields, normalize",
      "input": ["data", "repo"],
      "output": "preparedEntry"
    },
    {
      "name": "backup",
      "description": "Create backup before change",
      "input": ["repo", "entryId"],
      "output": "backupId"
    }
  ],

  "steps": [
    {
      "name": "saveWithBackup",
      "description": "Save with backup of existing",
      "sequence": ["backup", "save"]
    },
    {
      "name": "updateSafe",
      "description": "Safe update with validation",
      "sequence": ["validateEntry", "backup", "update"]
    }
  ],

  "config": {
    "backupsEnabled": true,
    "backupsPath": "/repos/_backups/",
    "maxBackupsPerEntry": 10,
    "autoInvalidateCache": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading existing entries, schemas |
| Writer | Writing files to disk |
| Librarian | Cache invalidation after writes |

---

## Used By

| Module | How |
|--------|-----|
| Self-healing | Creating new entries (Level 3) |
| AICaller | Saving usage records |
| All modules | Any repo writes |

---

*Last updated: 2026-01-17*
