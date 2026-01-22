# Writer Module

> **Purpose:** Low-level file write operations with safety guarantees
> **Type:** Data Management Module
> **Status:** Spec Complete
> **Version:** 1.2.1

---

## Overview

Writer is a low-level utility module for file system write operations. It is intentionally "dumb" - it writes what it's given without making decisions about content validity. Intelligence lives upstream in modules like Archivist (validation) and PolicyEngine (permissions).

```
Caller (Archivist, Builders, Logger) → Writer → File System
```

**Kitchen Analogy:** Writer is the oven - you put food in, it cooks. It doesn't decide WHAT to cook, doesn't check if the recipe is right, doesn't clean up if things burn. It just applies heat to whatever you give it.

---

## Roles & Rules

### Writer DOES:
- Write JSON files (pretty-printed)
- Write raw text files (HTML, markdown, configs)
- Append content to existing files
- Create directories
- Move, copy, and delete files
- Validate paths for security (defense in depth)
- Use atomic write patterns for data integrity
- Acquire file locks to prevent corruption

### Writer does NOT:
- Validate data against schemas (Archivist does that)
- Check permissions (PolicyEngine does that)
- Retry on failure (FailureHandler does that)
- Handle binary files (PDFMaker, ImageCollections do that)
- Log writes (Logger handles that separately)
- Make decisions about what to write

### Boundaries:
- Trusted callers only (no access control)
- Cannot write outside allowed directories
- Cannot bypass path validation
- Cannot skip atomic write pattern for JSON

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `initialize()` | - | initResult | Startup: clean orphan .tmp files |
| `write(path, data, options?)` | path, JS object, options | writeResult | Write JSON to file (atomic) |
| `writeRaw(path, content, options?)` | path, string, options | writeResult | Write raw content to file (atomic) |
| `append(path, content, options?)` | path, string, options | appendResult | Append content to existing file |
| `ensureDir(path)` | directory path | created | Create directory if not exists |
| `move(from, to)` | source path, dest path | moveResult | Atomic relocate file |
| `copy(from, to)` | source path, dest path | copyResult | Copy file |
| `remove(path)` | file path | removeResult | Delete file |

## Functions (Internal)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `validatePath(path)` | path | validation | Security validation (called by all write operations) |

---

## Options Type Definitions

### WriteOptions (for write and writeRaw)

```javascript
{
  encoding?: string,           // default: "utf-8"
  indent?: number,             // default: 2 (write only, ignored by writeRaw)
  bypassSizeLimit?: boolean,   // default: false
  reason?: string,             // required if bypassSizeLimit: true
  dryRun?: boolean             // default: false - validate without writing
}
```

### AppendOptions

```javascript
{
  encoding?: string,           // default: "utf-8"
  createIfMissing?: boolean,   // default: true
  newline?: boolean            // default: true - ensure content ends with \n
}
```

---

## Detailed Function Specs

### initialize()

Called on system startup to clean up orphaned .tmp files from previous crashes.

```javascript
// Input
(none)

// Process
1. Scan all allowed directories for .tmp files
2. For each .tmp file:
   - Check age against orphanMaxAge
   - If older than threshold → delete
   - If newer → leave (may be in-progress write)
3. Return cleanup report

// Output
{
  success: true,
  scanned: ["/repos/", "/jobs/", "/exports/", "/logs/"],
  orphansFound: 3,
  orphansDeleted: 2,
  orphansSkipped: 1,    // Too recent, might be active
  skippedFiles: ["/jobs/temp/file.1705590000000.tmp"]
}
```

---

### write(path, data, options?)

Write JSON to file using atomic pattern (temp file + fsync + rename).

```javascript
// Input
{
  path: "/repos/tones/friendly-casual.json",
  data: {
    id: "friendly-casual",
    name: "Friendly Casual",
    style: "warm, approachable",
    tags: ["casual", "friendly"]
  },
  options: {
    encoding: "utf-8",           // default
    indent: 2,                   // default
    bypassSizeLimit: false       // default
  }
}

// Process
1. validatePath(path)                    // Security check
2. Check file size against limits
3. acquireLock(path)                     // File locking
4. ensureDir(dirname(path))              // Create parent dirs
5. Write to temp file: path.{timestamp}.tmp
6. fsync temp file                       // Force to disk
7. Atomic rename temp → final
8. releaseLock(path)
9. Return result

// Output (success)
{
  success: true,
  path: "/repos/tones/friendly-casual.json",
  bytes: 245,
  atomic: true
}

// Output (failure)
{
  success: false,
  error: {
    code: "PATH_OUTSIDE_ALLOWED",
    message: "Path not in allowed directories"
  }
}
```

### writeRaw(path, content, options?)

Write raw text content (HTML, markdown, configs) using atomic pattern.

```javascript
// Input
{
  path: "/exports/brand-guide.html",
  content: "<!DOCTYPE html><html>...</html>",
  options: {
    encoding: "utf-8",           // default, can override
    bypassSizeLimit: false       // default
  }
}

// Process
Same as write() but without JSON.stringify

// Output
{
  success: true,
  path: "/exports/brand-guide.html",
  bytes: 15234,
  atomic: true
}
```

### append(path, content, options?)

Append content to existing file. Creates file if it doesn't exist.

```javascript
// Input
{
  path: "/repos/logs/system/2026-01-17.log",
  content: "[2026-01-17T14:30:52Z] [INFO] Job job_xxx started",
  options: {
    encoding: "utf-8",           // default
    createIfMissing: true,       // default
    newline: true                // default - ensure ends with \n
  }
}

// Process
1. validatePath(path)
2. acquireLock(path)
3. If file doesn't exist AND createIfMissing → create it
4. Append content (add \n if newline option and content doesn't end with one)
5. releaseLock(path)
6. Return result

// Output
{
  success: true,
  path: "/repos/logs/system/2026-01-17.log",
  bytesAppended: 52,
  totalBytes: 15234
}
```

**Note:** Append does not use atomic pattern - appending is inherently safer than overwriting.

### ensureDir(path)

Create directory and all parent directories if they don't exist.

```javascript
// Input
"/repos/brands/acme/assets"

// Process
1. validatePath(path)           // Must be in allowed roots
2. mkdir -p equivalent
3. Return result

// Output
{
  success: true,
  path: "/repos/brands/acme/assets",
  created: true                  // false if already existed
}
```

### move(from, to)

Atomically relocate a file. Used primarily for temp → permanent transitions.

```javascript
// Input
{
  from: "/jobs/temp/job_20260117_143052_draft.json",
  to: "/repos/jobs/completed/2026-01/job_20260117_143052.json"
}

// Process
1. validatePath(from)
2. validatePath(to)
3. ensureDir(dirname(to))
4. If same filesystem → atomic rename
5. If cross-filesystem → copy then delete (logged as non-atomic)
6. Return result

// Output
{
  success: true,
  from: "/jobs/temp/job_20260117_143052_draft.json",
  to: "/repos/jobs/completed/2026-01/job_20260117_143052.json",
  atomic: true                   // false if cross-filesystem
}
```

### copy(from, to)

Copy a file. Used for backups.

```javascript
// Input
{
  from: "/repos/tones/friendly-casual.json",
  to: "/repos/_backups/tones/friendly-casual_20260117.json"
}

// Process
1. validatePath(from)
2. validatePath(to)
3. acquireLock([from, to])       // Alphabetical order per lock ordering
4. ensureDir(dirname(to))
5. Copy file contents
6. releaseLock([from, to])
7. Return result

// Output
{
  success: true,
  from: "/repos/tones/friendly-casual.json",
  to: "/repos/_backups/tones/friendly-casual_20260117.json",
  bytes: 245
}
```

### remove(path)

Delete a file.

```javascript
// Input
"/jobs/temp/job_20260117_143052_draft.json"

// Process
1. validatePath(path)
2. Check file exists
3. Delete file
4. Return result

// Output
{
  success: true,
  path: "/jobs/temp/job_20260117_143052_draft.json"
}

// Output (file not found - not an error)
{
  success: true,
  path: "/jobs/temp/job_20260117_143052_draft.json",
  existed: false
}
```

### validatePath(path) - Internal

Security validation called by all write operations.

```javascript
// Input
"/repos/tones/../../../etc/passwd"

// Process
1. Normalize path (resolve .., .)
2. Check for traversal AFTER normalization
3. Check against allowed roots

// Output (invalid)
{
  valid: false,
  code: "PATH_TRAVERSAL_DETECTED",
  original: "/repos/tones/../../../etc/passwd",
  normalized: "/etc/passwd"
}

// Output (valid)
{
  valid: true,
  normalized: "/repos/tones/friendly-casual.json"
}
```

---

## Configuration

```json
{
  "module": "Writer",
  "version": "1.2.1",
  "description": "Low-level file write operations with safety guarantees",
  "type": "data-management",

  "config": {
    "encoding": "utf-8",
    "jsonIndent": 2,
    "createParentDirs": true,

    "atomicWrites": {
      "enabled": true,
      "fsync": true,
      "tempSuffix": ".tmp",
      "cleanupOrphans": true,
      "cleanupOnStartup": true,
      "orphanMaxAge": 3600000
    },

    "pathSafety": {
      "validatePaths": true,
      "allowedRoots": ["/repos/", "/jobs/", "/exports/", "/logs/"],
      "blockTraversal": true
    },

    "fileLocking": {
      "enabled": true,
      "strategy": "advisory",
      "lockLocation": "colocated",
      "timeout": 5000,
      "retryInterval": 100,
      "staleLockRecovery": true,
      "staleLockMaxAge": 10000
    },

    "diskSpace": {
      "preflightCheck": true,
      "safetyMultiplier": 2
    },

    "fileSizeLimits": {
      "warnAt": 5242880,
      "rejectAt": 52428800,
      "bypassable": true
    }
  }
}
```

### Config Explained

| Setting | Value | Purpose |
|---------|-------|---------|
| `encoding` | utf-8 | Default encoding, can override per-call |
| `jsonIndent` | 2 | Spaces for JSON pretty-print |
| `createParentDirs` | true | Auto-create parent directories |
| `atomicWrites.enabled` | true | Use temp+rename pattern |
| `atomicWrites.fsync` | true | Force disk sync before rename |
| `atomicWrites.cleanupOrphans` | true | Enable orphan .tmp file cleanup |
| `atomicWrites.cleanupOnStartup` | true | Run cleanup when Writer initializes |
| `atomicWrites.orphanMaxAge` | 3600000 | Max age in ms (1 hour) before .tmp is considered orphan |
| `pathSafety.allowedRoots` | [...] | Only write to these directories |
| `pathSafety.blockTraversal` | true | Block `..` path attacks |
| `fileLocking.strategy` | advisory | Lock type (advisory vs exclusive) |
| `fileLocking.lockLocation` | colocated | Lock files stored alongside target (path.lock) |
| `fileLocking.timeout` | 5000 | Max ms to wait for lock |
| `fileLocking.staleLockRecovery` | true | Enable breaking stale locks from dead processes |
| `fileLocking.staleLockMaxAge` | 10000 | Max age in ms before lock considered stale (2× timeout) |
| `diskSpace.preflightCheck` | true | Check disk space before writing |
| `diskSpace.safetyMultiplier` | 2 | Required free space = content size × multiplier |
| `fileSizeLimits.warnAt` | 5MB | Log warning above this |
| `fileSizeLimits.rejectAt` | 50MB | Reject write above this |
| `fileSizeLimits.bypassable` | true | Allow override with option |

---

## Error Handling

Writer reports errors but does not retry or recover. That's FailureHandler's job.

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `PATH_OUTSIDE_ALLOWED` | Path not in allowed roots | None - security rejection |
| `PATH_TRAVERSAL_DETECTED` | Path contains `..` after normalization | None - security rejection |
| `FILE_NOT_FOUND` | Source file missing (for move/copy) | Check upstream process |
| `FILE_SIZE_EXCEEDED` | File larger than rejectAt limit | Use bypassSizeLimit option |
| `LOCK_TIMEOUT` | Could not acquire lock in time | Retry later (FailureHandler) |
| `DISK_FULL` | No space left on device | Human escalation |
| `PERMISSION_DENIED` | OS-level permission error | Human escalation |
| `PATH_CREATE_FAILED` | Cannot create parent directory | Check permissions |

---

## File Types

| Type | Function | Handled? |
|------|----------|----------|
| JSON | `write()` | Yes |
| Text/Markdown | `writeRaw()` | Yes |
| HTML | `writeRaw()` | Yes |
| Config files | `writeRaw()` | Yes |
| Log files | `append()` | Yes |
| Images | - | No (ImageCollections) |
| PDFs | - | No (PDFMaker) |
| Binary | - | No |

---

## Used By

| Module | Function | Purpose |
|--------|----------|---------|
| **Archivist** | write() | Save repo entries |
| **Archivist** | copy() | Create backups |
| **Logger** | append() | Write log entries |
| **Builders** | ensureDir() | Create job directories |
| **SuccessHandler** | move() | Archive completed jobs |
| **Librarian** | write() | Rebuild index files |
| **Export modules** | writeRaw() | Save HTML/markdown exports |

---

## Dependencies

| Depends On | For |
|------------|-----|
| File System | Actual read/write operations |
| (None) | Writer is foundational - no module dependencies |

---

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Atomic writes | Yes (temp+fsync+rename) | Prevent corruption on crash |
| File locking | Yes (advisory) | Defense in depth for concurrency |
| Path validation | Yes (in Writer) | Security layers should be independent |
| Schema validation | No | Archivist's responsibility |
| Binary files | No | Specialized modules handle those |
| Retry logic | No | FailureHandler's responsibility |
| Logging | No | Logger handles separately |

---

## Operational Guarantees

### 1. Lock Ordering (Deadlock Prevention)

When multiple files must be locked, acquire locks in deterministic order (alphabetically by normalized path) to prevent deadlock.

```javascript
// For operations involving multiple files (move, copy)
// Always lock in alphabetical order
const paths = [from, to].sort();
for (const path of paths) {
  await acquireLock(path);
}
// ... perform operation ...
// Release in reverse order
for (const path of paths.reverse()) {
  releaseLock(path);
}
```

### 2. Cross-Filesystem Move Safety

When `move()` detects cross-filesystem (different mount points), use atomic destination pattern:

```javascript
// Process for cross-filesystem move
1. Copy source → destination.tmp
2. fsync destination.tmp
3. Rename destination.tmp → destination (atomic on dest filesystem)
4. Delete source
5. If step 4 fails → return success with warning, source remains as orphan
```

**Partial failure behavior:** If source deletion fails after successful copy, operation returns `success: true` with `warning: "source_deletion_failed"`. The source file becomes an orphan that can be cleaned manually.

### 3. Symlink Policy

Writer follows symlinks for the target file but validates the **resolved** path:

```javascript
// validatePath behavior
1. Resolve all symlinks in path → get real path
2. Check real path against allowedRoots
3. If real path outside allowed roots → reject with PATH_OUTSIDE_ALLOWED
```

**Rationale:** Defense in depth. Even if symlink was legitimately created, write operations must land in allowed directories.

### 4. Stale Lock Recovery

Locks include process ID and timestamp. Recovery mechanism:

```javascript
// Lock file contents
{
  pid: 12345,
  timestamp: 1705590000000,
  hostname: "server-01"
}

// On lock acquisition failure
1. Read existing lock file
2. If pid no longer exists (process died) → break lock, acquire new
3. If timestamp older than lockTimeout × 2 → break lock, acquire new
4. Otherwise → wait and retry per normal timeout
```

**Config addition:**
```json
"fileLocking": {
  "staleLockRecovery": true,
  "staleLockMaxAge": 10000  // 2× timeout
}
```

### 5. Append Ordering Guarantees

For concurrent appends to the same file:

- **Within single process:** Lock ensures serialization, order matches call order
- **Across processes:** Advisory lock provides ordering; entries never interleave
- **No global ordering guarantee:** If two processes append "simultaneously," either order is valid

```javascript
// append() behavior
1. acquireLock(path)
2. Seek to end of file
3. Write content
4. fsync (if configured)
5. releaseLock(path)
```

### 6. Permission Preservation

Atomic writes preserve original file permissions:

```javascript
// In write() and writeRaw()
1. If file exists → stat to get current mode (permissions)
2. Write to temp file (uses umask for new files)
3. If original existed → chmod temp file to match original
4. Atomic rename
```

**For new files:** Uses process umask (typically 0644 for files, 0755 for directories).

### 7. Disk Space Pre-flight Check

Before writing, check available disk space:

```javascript
// Pre-flight check
1. Calculate content size (JSON.stringify for write(), byte length for writeRaw())
2. Check available space on target filesystem
3. If availableSpace < contentSize × 2 → reject with DISK_SPACE_LOW
4. The 2× multiplier accounts for temp file + final file during atomic write

// Error response
{
  success: false,
  error: {
    code: "DISK_SPACE_LOW",
    message: "Insufficient disk space",
    required: 10485760,
    available: 5242880
  }
}
```

**Config addition:**
```json
"diskSpace": {
  "preflightCheck": true,
  "safetyMultiplier": 2
}
```

### 8. Initialize Concurrency Safety

Only one `initialize()` should run at a time (system startup):

```javascript
// Initialize behavior
1. Acquire global initialization lock (separate from file locks)
2. If lock already held → return { success: true, skipped: true, reason: "already_running" }
3. Perform orphan cleanup
4. Release initialization lock
5. Return cleanup report
```

**Startup coordination:** Caller (typically system bootstrap) should call `initialize()` once. Multiple calls are safe but only first runs.

### 9. Size Check Timing

Size validation happens **before** writing:

```javascript
// write() and writeRaw() size check timing
1. validatePath(path)
2. Calculate content size
3. If size > rejectAt AND !bypassSizeLimit → reject immediately (no lock, no I/O)
4. If size > warnAt → log warning (continue)
5. acquireLock(path)
6. Proceed with write
```

**Rationale:** Fail fast. Don't acquire locks or perform I/O for writes that will be rejected.

### 10. Encoding Contract

Writer guarantees encoding consistency:

| Operation | Default Encoding | Override Allowed | BOM Handling |
|-----------|------------------|------------------|--------------|
| `write()` | UTF-8 | Yes (options.encoding) | No BOM added |
| `writeRaw()` | UTF-8 | Yes (options.encoding) | Preserved if present |
| `append()` | UTF-8 | Yes (options.encoding) | Not applicable |

**Encoding validation:** Writer does not validate content matches declared encoding. Caller is responsible for ensuring content is valid for the specified encoding.

### 11. Remove Race Conditions (TOCTOU)

The `remove()` function handles Time-of-Check-to-Time-of-Use races:

```javascript
// remove() behavior
1. validatePath(path)
2. acquireLock(path)  // Prevents concurrent operations
3. Attempt unlink
4. If ENOENT (file gone) → return { success: true, existed: false }
5. If success → return { success: true, existed: true }
6. releaseLock(path)
```

**Race-safe guarantee:** Lock prevents file recreation between check and delete. If file disappears between `validatePath` and `unlink`, that's acceptable (idempotent delete).

### 12. Dry Run Mode

Optional dry-run mode for testing and validation:

```javascript
// All write operations accept dryRun option
{
  path: "/repos/test.json",
  data: { ... },
  options: {
    dryRun: true  // Validate but don't write
  }
}

// Dry run behavior
1. Perform all validation (path, size, permissions)
2. Acquire and release lock (test lock availability)
3. Calculate what would be written
4. Return result with dryRun: true flag

// Output
{
  success: true,
  dryRun: true,
  wouldWrite: {
    path: "/repos/test.json",
    bytes: 245,
    atomic: true
  }
}
```

**Use cases:**
- Pre-flight validation before batch operations
- Testing permission and path configurations
- Capacity planning

---

## Additional Error Codes

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `DISK_SPACE_LOW` | Insufficient disk space for write | Free space or write smaller content |
| `LOCK_STALE_RECOVERED` | Broke stale lock from dead process | Informational, operation proceeds |
| `CROSS_FS_SOURCE_ORPHAN` | Cross-filesystem move succeeded but source deletion failed | Manual cleanup of source |
| `INITIALIZE_ALREADY_RUNNING` | Another initialize() in progress | Safe to ignore |

---

## Constraints & Assumptions

### Platform Semantics

Writer assumes POSIX-style filesystem semantics:
- Atomic rename within same filesystem
- Advisory file locking via lock files
- Symlink resolution via `realpath()`

Windows compatibility would require additional handling for rename atomicity and locking differences.

### Scaling Notes

| Operation | Scaling Consideration |
|-----------|----------------------|
| `initialize()` | Scans all allowedRoots recursively for .tmp files. At very large scale, consider restricting to .tmp suffix only or capping directory depth. |
| Lock files | Colocated with target files. Large file counts = proportional lock file counts. |

---

*Spec version: 1.2.1*
*Created: 2026-01-18*
*Updated: 2026-01-18 - Added Operational Guarantees, Constraints & Assumptions, lockLocation config*
*Status: Approved*
