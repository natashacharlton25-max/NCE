# FileManager Module

> **Purpose:** Direct file operations - read, validate, check existence
> **Type:** Service Module
> **Status:** Planning

---

## Overview

FileManager handles direct file access when you know the exact ID. It reads from repos, validates against schemas, and checks file existence. All reads go through FileManager.

```
Known ID → FileManager.get() → File content
```

**Two access patterns:**
- **FileManager**: Know exact ID → use this
- **Librarian**: Need to search/discover → use Librarian instead

---

## Roles & Rules

### FileManager DOES:
- Read files by exact ID from repos
- Check file existence
- Validate data against schemas
- Know all repo locations
- Return structured error info for missing files

### FileManager does NOT:
- Write files (Writer does that)
- Search or discover files (Librarian does that)
- Create new entries (Archivist does that)
- Handle write permissions (PolicyEngine does that)

### Boundaries:
- Read-only access to repos
- Cannot modify files
- Cannot create directories
- Cannot access paths outside repo registry

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `get(repo, entryId)` | Fetch entry by ID | repo, entryId | entryData |
| `exists(repo, entryId)` | Check if entry exists | repo, entryId | boolean |
| `validate(data, schemaId)` | Validate data against schema | data, schemaId | validationResult |
| `getSchema(schemaId)` | Get schema definition | schemaId | schema |
| `listRepos()` | List all available repos | - | repoList |
| `getRepoPath(repo)` | Get filesystem path for repo | repo | path |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `FileManager.getValidated` | Get and validate entry | get → validate |
| `FileManager.getOrNull` | Get entry or return null if missing | exists → get (if exists) |

---

## Detailed Function Specs

### get(repo, entryId)

Fetches entry by exact ID from repo.

```javascript
// Input
{
  repo: "tones",
  entryId: "formal-corporate"
}

// Process
1. getRepoPath(repo) → /repos/tones/
2. Read /repos/tones/formal-corporate.json
3. Parse JSON
4. Return data

// Output
{
  "id": "formal-corporate",
  "name": "Formal Corporate",
  "style": "formal, professional",
  "vocabulary": ["therefore", "regarding", "pursuant"],
  "avoid": ["gonna", "wanna", "stuff"],
  "status": "active"
}

// Error if not found
{
  "error": true,
  "code": "NOT_FOUND_ENTRY",
  "message": "Entry 'formal-corporate' not found in 'tones'",
  "recoverable": true
}
```

### exists(repo, entryId)

Checks if entry exists without reading full content.

```javascript
// Input
{
  repo: "tones",
  entryId: "formal-corporate"
}

// Process
1. getRepoPath(repo)
2. Check if file exists at path
3. Return boolean

// Output
true  // or false
```

### validate(data, schemaId)

Validates data against schema.

```javascript
// Input
{
  data: { id: "new-tone", name: "New Tone", style: "casual" },
  schemaId: "tone"
}

// Process
1. getSchema(schemaId) → load tone.schema.json
2. Validate data against schema
3. Return result

// Output (valid)
{
  valid: true,
  data: { ... }
}

// Output (invalid)
{
  valid: false,
  errors: [
    { field: "status", message: "Required field missing" },
    { field: "vocabulary", message: "Must be array" }
  ]
}
```

### getSchema(schemaId)

Gets schema definition for validation.

```javascript
// Input
"tone"

// Process
1. Read /schemas/tone.schema.json
2. Return schema

// Output
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": ["id", "name", "style", "status"],
  "properties": {
    "id": { "type": "string", "pattern": "^[a-z0-9-]+$" },
    "name": { "type": "string" },
    "style": { "type": "string" },
    "status": { "enum": ["active", "deprecated", "draft"] }
  }
}
```

---

## Repo Registry

FileManager knows all repo locations:

```javascript
const REPOS = {
  "tones": "/repos/tones/",
  "prompts": "/repos/prompts/",
  "model-cards": "/repos/model-cards/",
  "brand-profiles": "/repos/brand-profiles/",
  "palettes": "/repos/palettes/",
  "values": "/repos/values/",
  "fonts": "/repos/fonts/",
  "checkpoints": "/repos/checkpoints/",
  "token-usage": "/repos/token-usage/"
};
```

---

## Config

### module.config.json

```json
{
  "module": "FileManager",
  "version": "1.0.0",
  "description": "Direct file operations for repos",

  "functions": [
    {
      "name": "get",
      "description": "Fetch entry by ID",
      "input": ["repo", "entryId"],
      "output": "entryData"
    },
    {
      "name": "exists",
      "description": "Check if entry exists",
      "input": ["repo", "entryId"],
      "output": "boolean"
    },
    {
      "name": "validate",
      "description": "Validate data against schema",
      "input": ["data", "schemaId"],
      "output": "validationResult"
    },
    {
      "name": "getSchema",
      "description": "Get schema definition",
      "input": ["schemaId"],
      "output": "schema"
    },
    {
      "name": "listRepos",
      "description": "List all available repos",
      "input": [],
      "output": "repoList"
    },
    {
      "name": "getRepoPath",
      "description": "Get filesystem path for repo",
      "input": ["repo"],
      "output": "path"
    }
  ],

  "steps": [
    {
      "name": "getValidated",
      "description": "Get and validate entry",
      "sequence": ["get", "validate"]
    },
    {
      "name": "getOrNull",
      "description": "Get entry or return null",
      "sequence": ["exists", "get"]
    }
  ],

  "config": {
    "reposRoot": "/repos/",
    "schemasRoot": "/schemas/",
    "encoding": "utf-8"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| (filesystem) | Direct file system access |

---

## Used By

| Module | How |
|--------|-----|
| PromptBuilder | Loading templates, tones, brands |
| AICaller | Loading model configs |
| Librarian | Reading files for indexing |
| All modules | Direct data access |

---

*Last updated: 2026-01-17*
