# Writer Module

> **Purpose:** Low-level file write operations
> **Type:** System Service
> **Status:** Planning

---

## Overview

Writer is the lowest-level write module. It handles the actual file system writes. **Never call Writer directly** - always go through Archivist for repo writes, which handles validation first.

```
Archivist.save() → validates → Writer.write() → file system
```

**Why separate Writer:**
- Single point for all file writes
- Easier to mock in tests
- Can add write logging/audit
- Can handle cross-platform differences

---

## Roles & Rules

### Writer DOES:
- Write JSON to files (pretty formatted)
- Write raw content to files
- Create directories when needed
- Move and copy files
- Delete files when instructed

### Writer does NOT:
- Validate data (Archivist does that)
- Decide what to write (called by other modules)
- Handle repos directly (Archivist coordinates)
- Log writes (Logger handles that)

### Boundaries:
- Never called directly for repo writes (go through Archivist)
- Cannot validate data
- Cannot choose file paths
- No access control (trusted callers only)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `write(path, data)` | Write JSON to file | path, data | writeResult |
| `writeRaw(path, content)` | Write raw content to file | path, content | writeResult |
| `ensureDir(path)` | Create directory if not exists | path | created |
| `move(from, to)` | Move file | from, to | moveResult |
| `copy(from, to)` | Copy file | from, to | copyResult |
| `remove(path)` | Delete file | path | removeResult |

---

## Detailed Function Specs

### write(path, data)

Writes JSON data to file with pretty formatting.

```javascript
// Input
{
  path: "/repos/tones/friendly-casual.json",
  data: {
    id: "friendly-casual",
    name: "Friendly Casual",
    style: "warm, approachable"
  }
}

// Process
1. JSON.stringify(data, null, 2)  // Pretty print
2. ensureDir(dirname(path))
3. Write to path
4. Return result

// Output
{
  success: true,
  path: "/repos/tones/friendly-casual.json",
  bytes: 245
}
```

### writeRaw(path, content)

Writes raw content (non-JSON) to file.

```javascript
// Input
{
  path: "/exports/brand-guide.html",
  content: "<html>...</html>"
}

// Output
{
  success: true,
  path: "/exports/brand-guide.html",
  bytes: 15234
}
```

### ensureDir(path)

Creates directory and all parent directories if they don't exist.

```javascript
// Input
"/repos/new-repo/subfolder/"

// Process
1. Check if exists
2. If not → create recursively

// Output
{
  created: true,
  path: "/repos/new-repo/subfolder/"
}
```

---

## Config

### module.config.json

```json
{
  "module": "Writer",
  "version": "1.0.0",
  "description": "Low-level file write operations",

  "functions": [
    {
      "name": "write",
      "description": "Write JSON to file",
      "input": ["path", "data"],
      "output": "writeResult"
    },
    {
      "name": "writeRaw",
      "description": "Write raw content to file",
      "input": ["path", "content"],
      "output": "writeResult"
    },
    {
      "name": "ensureDir",
      "description": "Create directory if not exists",
      "input": ["path"],
      "output": "created"
    },
    {
      "name": "move",
      "description": "Move file",
      "input": ["from", "to"],
      "output": "moveResult"
    },
    {
      "name": "copy",
      "description": "Copy file",
      "input": ["from", "to"],
      "output": "copyResult"
    },
    {
      "name": "remove",
      "description": "Delete file",
      "input": ["path"],
      "output": "removeResult"
    }
  ],

  "config": {
    "encoding": "utf-8",
    "jsonIndent": 2,
    "createParentDirs": true
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
| Archivist | Writing repo entries |
| Librarian | Writing _index.json files |
| Logger | Writing log files |
| Export modules | Writing output files |

---

*Last updated: 2026-01-17*
