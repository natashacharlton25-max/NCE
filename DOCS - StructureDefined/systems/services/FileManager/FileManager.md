# FileManager Module

> **Purpose:** Low-level file read operations for repos
> **Type:** Data Management Module
> **Status:** Spec Complete
> **Version:** 1.1.0

---

## Overview

FileManager is a low-level utility module for file system read operations. It is the read counterpart to Writer - intentionally "dumb" in that it fetches what you ask for without making decisions about what you should fetch. Intelligence lives in modules like Librarian (discovery) and Archivist (validation workflows).

```
Known ID → FileManager.get() → File content
```

**Kitchen Analogy:** FileManager is the pantry - you ask for a specific ingredient by name, it hands it to you. It doesn't suggest alternatives, doesn't check if the recipe needs it, doesn't know what you're cooking. It just retrieves.

**Two access patterns:**
- **FileManager**: Know exact ID → use this
- **Librarian**: Need to search/discover → use Librarian instead

---

## Roles & Rules

### FileManager DOES:
- Read files by exact ID from repos
- Check file existence
- List entry IDs in a repo
- List available repos
- Resolve repo names to filesystem paths
- Load and return schema definitions
- Validate data against schemas (utility function)
- Return structured error info for missing files

### FileManager does NOT:
- Write files (Writer does that)
- Search or discover files (Librarian does that)
- Create new entries (Archivist does that)
- Check write permissions (PolicyEngine does that)
- Cache data (CacheHandler does that)
- Make recommendations (Librarian does that)

### Boundaries:
- Read-only access to repos
- Cannot modify files
- Cannot create directories
- Cannot access paths outside repo registry

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `initialize()` | - | initResult | Startup: validate repo registry |
| `get(repo, entryId, options?)` | repo, entryId, options | entryData | Fetch entry by exact ID |
| `exists(repo, entryId, options?)` | repo, entryId, options | existsResult | Check if entry exists (with optional stats) |
| `list(repo)` | repo | entryIds[] | List all entry IDs in repo (sorted) |
| `listRepos()` | - | repos[] | List available repos |
| `getRepoPath(repo)` | repo | path | Get filesystem path for repo |
| `validate(data, schemaId)` | data, schemaId | validationResult | Validate data against schema (utility) |
| `getSchema(schemaId)` | schemaId | schema | Load schema definition |

---

## Options Type Definitions

### GetOptions

```javascript
{
  raw?: boolean,              // default: false - return parsed JSON
  encoding?: string,          // default: "utf-8"
  bypassSizeLimit?: boolean,  // default: false
  reason?: string             // required if bypassSizeLimit: true
}
```

### ExistsOptions

```javascript
{
  includeStats?: boolean      // default: false - return mtime/size if true
}
```

---

## Detailed Function Specs

### initialize()

Called on system startup to validate repo registry and ensure all configured paths exist.

```javascript
// Input
(none)

// Process
1. Load repo registry from config
2. For each repo path:
   - Verify directory exists
   - Verify readable
   - Record status
3. Return validation report

// Output
{
  success: true,
  repos: {
    valid: ["tones", "prompts", "palettes", "model-cards", "brand-profiles", "values", "fonts", "checkpoints"],
    missing: ["token-usage"],  // Path doesn't exist
    unreadable: [],            // Path exists but not readable
    total: 9
  },
  schemas: {
    valid: true,
    path: "/schemas/"
  }
}
```

**Startup behavior:** Missing repos generate warnings but don't fail initialization. Unreadable repos are critical errors.

**Readiness check:** All public functions check initialization status. Calls before `initialize()` completes return `MODULE_NOT_READY` error.

---

### get(repo, entryId, options?)

Fetches entry by exact ID from repo. Returns parsed JSON by default.

```javascript
// Input
{
  repo: "tones",
  entryId: "formal-corporate",
  options: {
    raw: false,           // default: return parsed JSON
    encoding: "utf-8"     // default
  }
}

// Process
1. Validate repo exists in registry
2. getRepoPath(repo) → /repos/tones/
3. Build path: /repos/tones/formal-corporate.json
4. validatePath(path)                    // Security check, symlink resolution
5. Check file exists (stat)
6. Check file size against limits
7. If size > rejectAt AND !bypassSizeLimit → reject
8. Read file contents
9. If raw: false → JSON.parse()
10. Return data

// Output (parsed - default)
{
  success: true,
  data: {
    "id": "formal-corporate",
    "name": "Formal Corporate",
    "style": "formal, professional",
    "vocabulary": ["therefore", "regarding", "pursuant"],
    "avoid": ["gonna", "wanna", "stuff"],
    "status": "active"
  }
}

// Output (raw: true)
{
  success: true,
  raw: true,
  data: "{\n  \"id\": \"formal-corporate\",\n  ..."
}

// Output (error - not found)
{
  success: false,
  error: {
    code: "ENTRY_NOT_FOUND",
    message: "Entry 'formal-corporate' not found in repo 'tones'",
    repo: "tones",
    entryId: "formal-corporate"
  }
}

// Output (error - parse failure)
{
  success: false,
  error: {
    code: "JSON_PARSE_ERROR",
    message: "Failed to parse JSON",
    repo: "tones",
    entryId: "formal-corporate",
    parseError: "Unexpected token at position 145"
  }
}

// Output (error - file too large)
{
  success: false,
  error: {
    code: "FILE_SIZE_EXCEEDED",
    message: "File exceeds read size limit",
    repo: "tones",
    entryId: "formal-corporate",
    size: 157286400,
    limit: 104857600
  }
}
```

### exists(repo, entryId, options?)

Checks if entry exists without reading full content. Lightweight existence check with optional stats.

```javascript
// Input
{
  repo: "tones",
  entryId: "formal-corporate",
  options: {
    includeStats: false        // default
  }
}

// Process
1. Validate repo exists in registry
2. getRepoPath(repo) → /repos/tones/
3. Build path: /repos/tones/formal-corporate.json
4. validatePath(path)                    // Security check
5. Stat file (not read)
6. If includeStats → extract mtime, size
7. Return result

// Output (basic)
{
  success: true,
  exists: true
}

// Output (with stats)
{
  success: true,
  exists: true,
  stats: {
    size: 1245,
    mtime: "2026-01-18T14:30:00.000Z",
    created: "2026-01-15T09:00:00.000Z"
  }
}

// Output (doesn't exist - NOT an error)
{
  success: true,
  exists: false
}

// Output (error - invalid repo)
{
  success: false,
  error: {
    code: "REPO_NOT_FOUND",
    message: "Repo 'invalid-repo' not found in registry"
  }
}
```

### list(repo)

Lists all entry IDs in a repo. Returns IDs only, not full content (for performance).

```javascript
// Input
"tones"

// Process
1. Validate repo exists in registry
2. getRepoPath(repo) → /repos/tones/
3. Read directory contents
4. Filter to .json files only (ignore .tmp, other extensions)
5. Ignore zero-byte files
6. Extract IDs (filename without extension)
7. Sort lexicographically (byte order)
8. Return list

// Output
{
  success: true,
  repo: "tones",
  entries: [
    "casual-friendly",
    "formal-corporate",
    "professional-tech",
    "warm-supportive"
  ],
  count: 4
}
```

**Ordering guarantee:** Entry IDs are sorted lexicographically using byte order (ASCII). This ensures deterministic results across runs.

**Ignored files:** `.tmp` files, non-`.json` files, and zero-byte files are excluded from listing.

### listRepos()

Lists all available repos in the registry.

```javascript
// Input
(none)

// Process
1. Return repo registry keys

// Output
{
  success: true,
  repos: [
    "tones",
    "prompts",
    "model-cards",
    "brand-profiles",
    "palettes",
    "values",
    "fonts",
    "checkpoints",
    "token-usage"
  ],
  count: 9
}
```

### getRepoPath(repo)

Resolves repo name to filesystem path.

```javascript
// Input
"tones"

// Process
1. Look up repo in registry
2. Return path

// Output
{
  success: true,
  repo: "tones",
  path: "/repos/tones/"
}

// Output (error)
{
  success: false,
  error: {
    code: "REPO_NOT_FOUND",
    message: "Repo 'invalid-repo' not found in registry"
  }
}
```

### validate(data, schemaId)

Validates data against a JSON schema. Utility function used by Archivist.

```javascript
// Input
{
  data: {
    id: "new-tone",
    name: "New Tone",
    style: "casual"
  },
  schemaId: "tone"
}

// Process
1. getSchema(schemaId)
2. Validate data against schema
3. Return result

// Output (valid)
{
  success: true,
  valid: true
  // Note: original data is NOT returned - caller already has it
}

// Output (invalid)
{
  success: true,
  valid: false,
  errors: [
    { field: "status", message: "Required field missing" },
    { field: "vocabulary", message: "Must be array" }
  ]
}

// Output (error - schema not found)
{
  success: false,
  error: {
    code: "SCHEMA_NOT_FOUND",
    message: "Schema 'invalid-schema' not found"
  }
}
```

### getSchema(schemaId)

Loads schema definition from schemas directory.

```javascript
// Input
"tone"

// Process
1. Build path: /schemas/tone.schema.json
2. Read and parse schema file
3. Return schema

// Output
{
  success: true,
  schemaId: "tone",
  schema: {
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
}
```

---

## Repo Registry

FileManager maintains a registry of all repo locations:

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
  "token-usage": "/repos/token-usage/",
  "schemas": "/schemas/"
};
```

**Note:** Registry is loaded from config at startup. New repos require config update.

---

## Configuration

```json
{
  "module": "FileManager",
  "version": "1.1.0",
  "description": "Low-level file read operations for repos",
  "type": "data-management",

  "config": {
    "encoding": "utf-8",
    "reposRoot": "/repos/",
    "schemasRoot": "/schemas/",
    "fileExtension": ".json",
    "schemaExtension": ".schema.json",

    "pathSafety": {
      "validatePaths": true,
      "blockTraversal": true,
      "resolveSymlinks": true
    },

    "readLimits": {
      "warnAt": 10485760,
      "rejectAt": 104857600,
      "bypassable": true
    },

    "repos": {
      "tones": "/repos/tones/",
      "prompts": "/repos/prompts/",
      "model-cards": "/repos/model-cards/",
      "brand-profiles": "/repos/brand-profiles/",
      "palettes": "/repos/palettes/",
      "values": "/repos/values/",
      "fonts": "/repos/fonts/",
      "checkpoints": "/repos/checkpoints/",
      "token-usage": "/repos/token-usage/"
    }
  }
}
```

### Config Explained

| Setting | Value | Purpose |
|---------|-------|---------|
| `encoding` | utf-8 | Default file encoding |
| `reposRoot` | /repos/ | Base path for repos |
| `schemasRoot` | /schemas/ | Base path for schemas |
| `fileExtension` | .json | Default extension for entries |
| `schemaExtension` | .schema.json | Extension for schema files |
| `pathSafety.validatePaths` | true | Validate paths before read |
| `pathSafety.blockTraversal` | true | Block `..` path attacks |
| `pathSafety.resolveSymlinks` | true | Resolve symlinks and validate real path |
| `readLimits.warnAt` | 10MB | Log warning above this |
| `readLimits.rejectAt` | 100MB | Reject read above this |
| `readLimits.bypassable` | true | Allow override with option |
| `repos` | {...} | Repo name → path mapping |

---

## Error Handling

FileManager reports errors but does not retry or recover.

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `REPO_NOT_FOUND` | Repo name not in registry | Check repo name |
| `ENTRY_NOT_FOUND` | File doesn't exist | Use `exists()` first or handle gracefully |
| `SCHEMA_NOT_FOUND` | Schema file doesn't exist | Check schema name |
| `JSON_PARSE_ERROR` | File contains invalid JSON | Fix file manually, use `raw: true` to inspect |
| `PATH_TRAVERSAL_DETECTED` | Path contains `..` after normalization | Security rejection |
| `PATH_OUTSIDE_ALLOWED` | Symlink resolves outside repo | Security rejection |
| `FILE_SIZE_EXCEEDED` | File larger than rejectAt limit | Use bypassSizeLimit option |
| `FILE_EMPTY` | Zero-byte file | Check for corruption |
| `MODULE_NOT_READY` | Called before initialize() completed | Ensure bootstrap calls initialize() first |
| `READ_ERROR` | OS-level read failure | Check permissions, disk |

---

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Read-only | Yes | Separation of concerns - Writer handles writes |
| Parsed by default | Yes | Most callers want JSON objects, not strings |
| Raw option | Yes | Debugging and edge cases need raw access |
| No caching | Correct | CacheHandler's responsibility |
| No search | Correct | Librarian's responsibility |
| No validation workflow | Correct | Archivist's responsibility |
| Repo registry | Config-based | Centralized, auditable, changeable |
| Schema loading | In FileManager | Schemas are just files to read |
| Validation utility | In FileManager | Generic utility; FileManager does not interpret results |
| Symlink resolution | Yes | Mirrors Writer's security model |
| Size limits | Yes | Prevent memory exhaustion |
| No file locking | Correct | FileManager does not participate in file locking |
| No schema caching | Correct | Schema loading is uncached by default; CacheHandler can wrap if needed |

---

## Operational Guarantees

### 1. Symlink Policy

FileManager follows symlinks for the target file but validates the **resolved** path:

```javascript
// validatePath behavior
1. Resolve all symlinks in path → get real path
2. Check real path is within repo root
3. If real path outside allowed repo → reject with PATH_OUTSIDE_ALLOWED
```

**Rationale:** Mirrors Writer's security model. A symlink inside `/repos/tones/bad.json → /etc/passwd` will be rejected.

### 2. Read-During-Write Safety

FileManager does NOT lock files for reading. Writer's atomic pattern (temp+rename) ensures readers always see consistent data - either the old version or the new version, never partial content.

```javascript
// Writer's atomic pattern guarantees:
// - Before rename: reader sees old file (consistent)
// - After rename: reader sees new file (consistent)
// - During rename: atomic operation - no partial state visible
```

**Edge case:** If a file is deleted between `exists()` and `get()`, `get()` returns `ENTRY_NOT_FOUND`. Callers should handle this gracefully.

### 3. Schema Responsibility Boundary

Schema loading (`getSchema()`) and validation (`validate()`) are **utility functions**. FileManager:
- Loads schema files (just another read operation)
- Runs JSON Schema validation (stateless utility)
- Does NOT interpret validation results
- Does NOT enforce validation as workflow

**Archivist** owns the validation *workflow* - deciding when to validate and what to do with results.

### 4. Size Limit Check

Size is checked **before** loading file into memory:

```javascript
// get() size check timing
1. Stat file to get size
2. If size > rejectAt AND !bypassSizeLimit → reject immediately
3. If size > warnAt → log warning (continue)
4. Read file into memory
5. Parse if needed
```

**Rationale:** Fail fast to prevent memory exhaustion. A 200MB JSON file should never start loading.

### 5. Ignored Files

FileManager ignores certain files to prevent edge case issues:

| File Type | Behavior |
|-----------|----------|
| `.tmp` files | Ignored in list(), not readable |
| Non-`.json` files | Ignored in list() |
| Zero-byte files | Ignored in list(), return FILE_EMPTY if read directly |

**Rationale:** `.tmp` files are Writer's in-progress writes. Zero-byte files indicate corruption or incomplete writes.

### 6. List Ordering Guarantee

`list()` returns entry IDs sorted lexicographically using byte order (ASCII).

```javascript
// Sort behavior
["Apple", "apple", "banana"] // A < a in ASCII
// Returns: ["Apple", "apple", "banana"]
```

**Rationale:** Deterministic ordering across runs. Important for indexing, diffing, and reproducible builds.

---

## Constraints & Assumptions

### Platform Semantics

FileManager assumes POSIX-style filesystem semantics:
- Symlink resolution via `realpath()`
- Stat operations are reliable
- UTF-8 encoding is valid

Windows compatibility notes:
- Symlink behavior differs (admin rights required)
- Path separators need normalization

### Scaling Notes

| Operation | Scaling Consideration |
|-----------|----------------------|
| `list()` | Directory scan; for 10,000+ entries, use Librarian's indexed search |
| `initialize()` | Runs once at startup; validates all repos |
| Large files | 100MB limit prevents memory issues; bypassable for migrations |

---

## Used By

| Module | Function | Purpose |
|--------|----------|---------|
| **PromptBuilder** | get() | Load prompt templates, tones, brands |
| **AICaller** | get() | Load model configs |
| **Librarian** | list(), get() | Build search indexes |
| **Archivist** | validate(), getSchema() | Entry validation before write |
| **All modules** | get() | Direct data access |

---

## Dependencies

| Depends On | For |
|------------|-----|
| File System | Actual read operations |
| (None) | FileManager is foundational - no module dependencies |

---

## Build Notes

*Notes for implementation and future updates:*

### Version Compatibility (Dev Team Gap - 2026-01-20)
| Dependency | Min Version | Notes |
|------------|-------------|-------|
| All libraries | 1.0.0+ | FileManager reads any library schema |

### Cross-Library Integrity
- FileManager provides read access to all repos for external modules
- Does not modify data - read-only access pattern

---

*Spec version: 1.1*
*Created: 2026-01-18*
*Updated: 2026-01-18 - Added Operational Guarantees, initialize(), readLimits, symlink policy, stats option*
*Status: Approved*
