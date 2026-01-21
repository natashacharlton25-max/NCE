# Reader Service Plan

> **Purpose:** Low-level file reading utility - reads non-library files.
> **Level:** Utility (Foundation)
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Reader handles reading non-library files (uploads, assets, outputs, configs, etc.). For library data, use Librarian instead.

**Key Principle:** Reader reads files. Builders tell it WHERE, Reader does the actual read.

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   SERVICE    │────▶│   BUILDERS   │────▶│    READER    │
│              │     │              │     │              │
│ "I need the  │     │ "That's at   │     │ (reads file  │
│  brand logo" │     │  /files/..." │     │  from disk)  │
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DATA ACCESS SPLIT                                  │
│                                                                              │
│  ┌─────────────────────────────┐    ┌─────────────────────────────┐        │
│  │         LIBRARIAN           │    │          READER             │        │
│  │     (Library Data)          │    │     (File Data)             │        │
│  │                             │    │                             │        │
│  │ • JSON library entries      │    │ • Images, PDFs, assets      │        │
│  │ • Has own registry          │    │ • Uses Builders for paths   │        │
│  │ • Schema validation         │    │ • No validation             │        │
│  │ • Caching                   │    │ • No caching                │        │
│  │ • Workers per library       │    │ • Just reads                │        │
│  └─────────────────────────────┘    └─────────────────────────────┘        │
│              │                                    │                         │
│              ▼                                    ▼                         │
│     /apps/*-library/data/               Builders.getPath() → fs            │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Split

| Need Library Data? | Need File Data? |
|--------------------|-----------------|
| Use **Librarian** | Use **Builders.getPath()** + **Reader** |
| `lib.get('brandProfiles', 'mind-the-box')` | `Reader.read(Builders.getPath({...}))` |

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Read file contents from disk | Cache results (Librarian does) |
| Check if file exists | Validate content (Librarian/FileHandler do) |
| Read JSON files | Parse/transform (Parsers do) |
| Read binary files (images, etc.) | Make decisions |
| Get file stats (size, modified) | Handle permissions (services do) |
| Return raw content | Know about libraries |

---

## API Design

```typescript
// lib/server/services/reader.ts

interface ReadOptions {
  encoding?: BufferEncoding;  // Default: 'utf-8'
}

interface ReadResult<T = string> {
  success: boolean;
  data?: T;
  error?: string;
  path: string;
  size?: number;
}

// Core functions
function readFile(path: string, options?: ReadOptions): Promise<ReadResult<string>>;
function readJSON<T>(path: string): Promise<ReadResult<T>>;
function readBinary(path: string): Promise<ReadResult<Buffer>>;
function exists(path: string): Promise<boolean>;
function getStats(path: string): Promise<ReadResult<fs.Stats>>;

// Batch operations
function readFiles(paths: string[], options?: ReadOptions): Promise<ReadResult<string>[]>;
function readJSONFiles<T>(paths: string[]): Promise<ReadResult<T>[]>;
```

---

## Usage by Librarian

```typescript
// Librarian uses Reader internally
class Librarian {
  private cache: Map<string, CacheEntry>;

  async get(library: string, id: string): Promise<any> {
    const cacheKey = `${library}:${id}`;

    // Check cache first
    if (this.cache.has(cacheKey)) {
      return this.cache.get(cacheKey).data;
    }

    // Use Reader for actual file read
    const path = this.registry.getPath(library, id);
    const result = await Reader.readJSON(path);

    if (!result.success) {
      throw new Error(result.error);
    }

    // Validate against schema
    this.validate(library, result.data);

    // Cache and return
    this.cache.set(cacheKey, { data: result.data, expires: Date.now() + 300000 });
    return result.data;
  }
}
```

---

## Usage by File Handler

```typescript
// File Handler uses Reader internally
class FileHandler {
  async get(location: string, filename: string): Promise<Buffer> {
    // Check permissions first
    if (!this.canRead(location)) {
      throw new Error('Permission denied');
    }

    // Use Reader for actual file read
    const path = this.registry.getPath(location, filename);
    const result = await Reader.readBinary(path);

    if (!result.success) {
      throw new Error(result.error);
    }

    return result.data;
  }
}
```

---

## Error Handling

Reader catches all errors and returns them in the result:

```typescript
interface ReadResult<T> {
  success: false;
  error: string;        // Human-readable error
  errorCode?: string;   // 'ENOENT', 'EACCES', etc.
  path: string;         // Path that was attempted
}
```

**Error mapping:**

| fs Error | Reader Error |
|----------|--------------|
| ENOENT | "File not found: {path}" |
| EACCES | "Permission denied: {path}" |
| EISDIR | "Path is a directory: {path}" |
| Other | "Read failed: {original message}" |

---

## Implementation Notes

### File Location

```
lib/server/services/reader.ts
```

### Dependencies

- Node.js `fs/promises`
- No external packages

### Size Estimate

~100-150 lines of code

---

## Build Phases

### R1: Core Read Functions
- `readFile()`
- `readJSON()`
- `exists()`
- Error mapping

### R2: Extended Functions
- `readBinary()`
- `getStats()`
- `readFiles()` batch
- `readJSONFiles()` batch

---

## Testing

| Test | Description |
|------|-------------|
| Read existing text file | Returns content |
| Read existing JSON file | Returns parsed object |
| Read non-existent file | Returns error |
| Read directory path | Returns error |
| Read binary file | Returns Buffer |
| Batch read | Returns array of results |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [WRITER-PLAN.md](./WRITER-PLAN.md) - Counterpart for writing
- [../Librarian/LIBRARIAN-ARCHITECTURE.md](../Librarian/LIBRARIAN-ARCHITECTURE.md) - Uses Reader

---

*Reader is the foundation - Librarian and File Handler both build on it.*
