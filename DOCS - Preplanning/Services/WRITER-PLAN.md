# Writer Service Plan

> **Purpose:** The ONLY service that writes to disk.
> **Level:** System (Foundation)
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Writer handles all **system data writes** - brand content, library entries, drafts, exports. It's the controlled gateway for persistent data.

**Key Principle:** Writer writes system data. Builder creates structure (mkdir). Logger handles operational logs separately.

---

## Who Uses Writer

| Service | Uses Writer? | Notes |
|---------|--------------|-------|
| **Archivist** | ✅ Yes | Brand data, drafts, approved content |
| **Librarian** | ✅ Yes | Library entries (via workers) |
| **Builder** | ❌ No | Creates folders directly (mkdir), not data |
| **Logger** | ❌ No | Handles own log files (operational data) |
| **Parsers** | ❌ No | Just transforms data |
| **Fetchers** | ❌ No | Just fetches data |

```
┌────────────┐     ┌────────────┐     ┌────────────┐
│ Archivist  │────▶│            │     │            │
│            │     │   WRITER   │────▶│    DISK    │
│ Librarian  │────▶│  (system   │     │  (data)    │
│            │     │   data)    │     │            │
└────────────┘     └────────────┘     └────────────┘

┌────────────┐                        ┌────────────┐
│  Builder   │───────────────────────▶│    DISK    │
│            │     mkdir (structure)  │ (folders)  │
└────────────┘                        └────────────┘

┌────────────┐                        ┌────────────┐
│  Logger    │───────────────────────▶│    DISK    │
│            │     own log handling   │  (logs)    │
└────────────┘                        └────────────┘
```

**Key distinction:**
- **Writer** = System data (brand content, library entries)
- **Builder** = Structure creation (folders via mkdir)
- **Logger** = Operational data (logs, separate flow)

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Write file contents | Validate content |
| Create directories | Decide where to write |
| Atomic writes (temp + rename) | Know about file types |
| Delete files | Catalog or track |
| Copy/move files | Make decisions |
| Return success/failure | Handle retries |

---

## API Design

```typescript
// lib/server/services/writer.ts

interface WriteOptions {
  encoding?: BufferEncoding;  // Default: 'utf-8'
  mode?: number;              // Default: 0o644
  atomic?: boolean;           // Default: true (write to temp, then rename)
  createDir?: boolean;        // Default: true (create parent dirs if needed)
}

interface WriteResult {
  success: boolean;
  error?: string;
  path: string;
  bytesWritten?: number;
}

// Core functions
function writeFile(path: string, content: string | Buffer, options?: WriteOptions): Promise<WriteResult>;
function writeJSON(path: string, data: unknown, options?: WriteOptions & { pretty?: boolean }): Promise<WriteResult>;
function appendFile(path: string, content: string, options?: WriteOptions): Promise<WriteResult>;

// Directory functions
function ensureDir(path: string): Promise<WriteResult>;
function removeDir(path: string, recursive?: boolean): Promise<WriteResult>;

// File management
function deleteFile(path: string): Promise<WriteResult>;
function copyFile(source: string, dest: string, options?: WriteOptions): Promise<WriteResult>;
function moveFile(source: string, dest: string, options?: WriteOptions): Promise<WriteResult>;

// Batch operations
function writeFiles(files: Array<{ path: string; content: string | Buffer }>): Promise<WriteResult[]>;
```

---

## Functions

### writeFile(path, content, options?)

Write text or binary content to file.

```typescript
const result = await writeFile('/path/to/file.txt', 'Hello World');
if (result.success) {
  console.log(`Wrote ${result.bytesWritten} bytes`);
} else {
  console.error(result.error);
}
```

### writeJSON(path, data, options?)

Write object as JSON file.

```typescript
const config = { name: 'Brand', domain: 'tech' };
const result = await writeJSON('/brand/_config/brand.json', config, { pretty: true });
```

**Options:**
- `pretty: true` - Format with 2-space indentation (default: true)
- `pretty: false` - Minified JSON

### appendFile(path, content, options?)

Append to existing file.

```typescript
const result = await appendFile('/logs/activity.log', `${new Date().toISOString()}: Action\n`);
```

### ensureDir(path)

Create directory and all parent directories.

```typescript
const result = await ensureDir('/brand/exports/2026-01');
// Creates /brand, /brand/exports, /brand/exports/2026-01 if needed
```

### deleteFile(path)

Delete a single file.

```typescript
const result = await deleteFile('/temp/draft.json');
```

### copyFile(source, dest, options?)

Copy file to new location.

```typescript
const result = await copyFile('/draft/section.json', '/brandkit/section.json');
```

### moveFile(source, dest, options?)

Move file (copy + delete source).

```typescript
const result = await moveFile('/temp/upload.png', '/brand/logos/main.png');
```

---

## Atomic Writes

By default, Writer uses atomic writes to prevent corruption:

```
1. Write to: /path/to/file.tmp
2. Sync to disk (fsync)
3. Rename: /path/to/file.tmp → /path/to/file
```

This ensures that if the process crashes mid-write, the original file is intact.

Disable with `atomic: false` for performance-critical, recoverable writes.

---

## Error Handling

Writer catches all errors and returns them in the result object:

```typescript
interface WriteResult {
  success: false;
  error: string;        // Human-readable error
  errorCode?: string;   // 'EACCES', 'ENOSPC', etc.
  path: string;         // Path that was attempted
}
```

**Error mapping:**

| fs Error | Writer Error |
|----------|--------------|
| EACCES | "Permission denied: {path}" |
| ENOSPC | "No space left on device" |
| EROFS | "Read-only file system" |
| ENOENT | "Parent directory not found: {path}" |
| Other | "Write failed: {original message}" |

---

## What Writer Does NOT Do

1. **No Validation** - Writes whatever is given
   - Validation is Archivist's job

2. **No Path Decisions** - Takes exact paths
   - Path decisions are Archivist's job

3. **No Cataloging** - Just writes
   - Cataloging is Archivist's job

4. **No Retry Logic** - Reports errors only
   - Retry logic is FailSys's job

5. **No File Type Awareness** - Writes bytes
   - Type handling is Parser's job

---

## Usage Examples

### By Archivist

```typescript
// Archivist validates, gets path from Builder, then calls Writer
class Archivist {
  async store(data: unknown, category: string, brandId: string): Promise<StoreResult> {
    // 1. Validate data
    const validation = this.validate(data, category);
    if (!validation.valid) {
      return { success: false, error: validation.error };
    }

    // 2. Get path from Builder
    const path = Builder.getPath({ location: category, brandId, params: {...} });

    // 3. Ensure folder exists (Builder uses Writer internally)
    await Builder.ensureDirForFile(path);

    // 4. Writer does the actual write
    const result = await Writer.writeJSON(path, data);

    // 5. Catalog if successful
    if (result.success) {
      await this.catalog(path, data);
    }

    return result;
  }
}
```

### By Librarian (Writing to Libraries)

```typescript
// Librarian workers use Writer for library writes
class BrandLibraryWorker {
  async save(id: string, data: BrandProfile): Promise<SaveResult> {
    // Librarian CEO already checked write permissions

    // Get path from registry
    const path = `${this.libraryPath}/data/${id}.json`;

    // Validate against schema
    const validation = this.validateSchema(data);
    if (!validation.valid) {
      return { success: false, error: validation.error };
    }

    // Writer does the actual write
    return Writer.writeJSON(path, data, { pretty: true });
  }
}
```

---

## Implementation Notes

### File Location

```
lib/server/services/writer.ts
```

### Dependencies

- Node.js `fs/promises`
- No external packages

### Size Estimate

~150-200 lines of code

---

## Build Phases

### W1: Core Write Functions
- `writeFile()`
- `writeJSON()`
- `ensureDir()`
- Atomic write logic
- Error mapping

### W2: Extended Functions
- `appendFile()`
- `deleteFile()`
- `copyFile()`
- `moveFile()`
- `removeDir()`
- `writeFiles()` batch

---

## Testing

| Test | Description |
|------|-------------|
| Write new file | Creates file with content |
| Write to existing | Overwrites content |
| Write JSON | Writes formatted JSON |
| Write with createDir | Creates parent directories |
| Atomic write | Uses temp file + rename |
| Non-atomic write | Writes directly |
| Delete file | Removes file |
| Copy file | Duplicates file |
| Move file | Relocates file |
| Batch write | Writes multiple files |
| Permission denied | Returns error |
| No space | Returns error |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [READER-PLAN.md](./READER-PLAN.md) - Counterpart for reading
- [ARCHIVIST-PLAN.md](./ARCHIVIST-PLAN.md) - Uses Writer for storing brand data
- [../Librarian/LIBRARIAN-ARCHITECTURE.md](../Librarian/LIBRARIAN-ARCHITECTURE.md) - Uses Writer for library writes

---

*Writer handles system data writes. Builder creates folders. Logger handles logs.*
