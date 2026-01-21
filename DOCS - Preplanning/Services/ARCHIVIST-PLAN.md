# Archivist Service Plan

> **Purpose:** Validate data and orchestrate storage.
> **Level:** Processing Layer
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Archivist is the gatekeeper for all data storage. Before any data is written to permanent storage, Archivist validates it and catalogs it.

**Key Principle:** Archivist validates WHAT gets stored. Builder knows WHERE. Archivist orchestrates the flow.

---

## Archivist vs Builder

| Archivist | Builder |
|-----------|---------|
| Validates data | Knows file locations (registry) |
| Catalogs storage | Provides paths via `getPath()` |
| Orchestrates store flow | Creates folder structures |
| Uses Builder for paths | Doesn't validate data |
| Uses Writer for disk ops | Doesn't write data |

**Flow:**
```
Service → Archivist.store(data) → validate → Builder.getPath() → Writer.write()
```

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Validate incoming data | Write to disk (uses Writer) |
| Catalog data (track what's stored) | Handle retry logic (FailSys does) |
| Orchestrate storage flow | Know where files go (Builder does) |
| Check for duplicates | Transform data (Parsers do) |
| Maintain storage index | Fetch external data (Fetchers do) |
| Report failures to FailSys | Cache data (Librarian does) |
| Ask Builder for paths | Create folders (Builder does) |

---

## The Write Chain

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Service │───▶│ Archivist│───▶│  Builder │───▶│  Writer  │───▶│   Disk   │
│  Output  │    │          │    │          │    │          │    │          │
│          │    │ validates│    │ getPath()│    │ write()  │    │          │
│          │    │ catalogs │    │ ensureDir│    │          │    │          │
└──────────┘    └────┬─────┘    └──────────┘    └──────────┘    └──────────┘
                     │
                     │ On failure
                     ▼
               ┌──────────┐
               │ FailSys  │
               │ (rules)  │
               └──────────┘
```

1. **Service** has data, calls **Archivist.store()**
2. **Archivist** validates data
3. If valid: Archivist asks **Builder** for path + ensures folder exists
4. **Writer** writes to disk
5. **Archivist** catalogs the storage
6. If any step fails: report to **FailSys**

---

## API Design

```typescript
// lib/server/services/archivist.ts

interface StoreRequest {
  data: unknown;
  category: DataCategory;
  brandPath?: string;
  metadata?: Record<string, unknown>;
}

interface StoreResult {
  success: boolean;
  path?: string;
  catalogId?: string;
  error?: string;
  validation?: ValidationResult;
}

interface ValidationResult {
  valid: boolean;
  errors: ValidationError[];
  warnings: ValidationWarning[];
}

interface CatalogEntry {
  id: string;
  category: DataCategory;
  path: string;
  storedAt: string;
  checksum?: string;
  metadata?: Record<string, unknown>;
}

type DataCategory =
  | 'brand-config'
  | 'section-draft'
  | 'section-approved'
  | 'library-entry'
  | 'export'
  | 'image'
  | 'temp';

// Core functions
function store(request: StoreRequest): Promise<StoreResult>;
function validate(data: unknown, category: DataCategory): ValidationResult;
function catalog(entry: CatalogEntry): Promise<void>;

// Note: Paths come from Builder.getPath(), Archivist doesn't decide paths

// Query functions
function findByCategory(category: DataCategory, brandPath?: string): Promise<CatalogEntry[]>;
function findByPath(path: string): Promise<CatalogEntry | null>;
function exists(category: DataCategory, id: string, brandPath?: string): Promise<boolean>;

// Cleanup
function removeFromCatalog(path: string): Promise<void>;
function cleanupTemp(olderThan?: number): Promise<number>;
```

---

## Functions

### store(request)

Main entry point - validate, catalog, and store data.

```typescript
const result = await store({
  data: sectionContent,
  category: 'section-draft',
  brandPath: '/brands/acme',
  metadata: { sectionId: 'core/identity' }
});

if (result.success) {
  console.log(`Stored at: ${result.path}`);
} else {
  console.error(`Failed: ${result.error}`);
  // FailSys has already been notified
}
```

### validate(data, category)

Validate data against category rules.

```typescript
const validation = validate(sectionContent, 'section-draft');
if (!validation.valid) {
  console.log(validation.errors);
}
```

### Getting Paths from Builder

Archivist asks Builder for paths - doesn't decide itself:

```typescript
import { Builder } from '@/lib/server/builders';

// Archivist internally calls Builder
const path = Builder.getPath({
  location: 'brandKit',
  brandId: 'acme',
  params: { sectionPath: 'core/identity' }
});
// Returns: '/apps/acme/brandkit/core/identity.json'

// Builder also ensures folder exists
await Builder.ensureDir(path);
```

---

## Validation Rules

Archivist validates based on category:

### brand-config

```typescript
{
  required: ['name', 'id'],
  types: {
    name: 'string',
    id: 'string',
    domain: 'string?'
  }
}
```

### section-draft / section-approved

```typescript
{
  required: ['sectionId'],
  mustHaveContent: true,
  schemaPath: '/schemas/{sectionId}.json'  // Optional schema validation
}
```

### library-entry

```typescript
{
  required: ['id', 'name'],
  types: {
    id: 'string',
    name: 'string',
    description: 'string?'
  }
}
```

### image

```typescript
{
  maxSize: 10 * 1024 * 1024,  // 10MB
  allowedTypes: ['image/png', 'image/jpeg', 'image/svg+xml'],
  dimensions: { maxWidth: 4096, maxHeight: 4096 }
}
```

---

## Category to Location Mapping

Archivist maps data categories to Builder locations:

| Category | Builder Location | Builder Params |
|----------|------------------|----------------|
| brand-config | `brandConfig` | `{ configFile }` |
| section-draft | `brandDraft` | `{ sectionPath }` |
| section-approved | `brandKit` | `{ sectionPath }` |
| export | `exports` | `{ date, format, filename }` |
| image | `assets` | `{ assetType, filename }` |
| temp | `temp` | `{ sessionId, filename }` |

**Note:** Builder owns the actual path patterns in its registry. Archivist just knows which location to ask for.

---

## Catalog System

Archivist maintains a catalog index of all stored data:

```typescript
// {brandPath}/_meta/catalog.json
{
  "entries": [
    {
      "id": "cat_abc123",
      "category": "section-draft",
      "path": "/brands/acme/draft/core/identity.json",
      "storedAt": "2026-01-16T10:30:00Z",
      "checksum": "sha256:abc...",
      "metadata": {
        "sectionId": "core/identity"
      }
    }
  ],
  "lastUpdated": "2026-01-16T10:30:00Z"
}
```

---

## Failure Reporting

When validation fails or storage fails, Archivist reports to FailSys:

```typescript
// Archivist reports, does NOT handle
async function store(request: StoreRequest): Promise<StoreResult> {
  const validation = validate(request.data, request.category);

  if (!validation.valid) {
    // Report to FailSys
    await FailSys.report({
      service: 'archivist',
      operation: 'validate',
      category: request.category,
      errors: validation.errors,
      data: request.data  // For retry
    });

    return {
      success: false,
      error: 'Validation failed',
      validation
    };
  }

  // ... continue with storage
}
```

**Archivist does NOT:**
- Retry operations
- Decide what to do with failures
- Store failed data for later

**FailSys handles:**
- All retry logic
- Failure rules
- Pattern detection
- Data storage for manual review

---

## Temp Storage

Data flows through temp before Archivist processes it:

```
/temp/
  /{brandId}/
    /{uuid}.json       # Pending data
    /{uuid}.meta.json  # Metadata about pending
```

Archivist picks up from temp, processes, then cleans up:

```typescript
// Cleanup old temp files (default: older than 24 hours)
const cleaned = await cleanupTemp(24 * 60 * 60 * 1000);
console.log(`Cleaned ${cleaned} temp files`);
```

---

## Implementation Notes

### File Location

```
lib/server/services/archivist.ts
```

### Dependencies

- Builder service (for paths and folder creation)
- Writer service (for actual writes)
- FailSys (for failure reporting)
- Logger (for logging)
- JSON schema validator (optional)

### Size Estimate

~300-400 lines of code

---

## Build Phases

### A1: Core Validation
- `validate()` function
- Basic validation rules per category
- Error/warning types

### A2: Catalog System
- `catalog()` function
- Catalog index management
- Query functions
- Duplicate detection

### A3: Store Flow
- `store()` main function
- Builder.getPath() integration
- Writer integration
- FailSys reporting

### A4: Cleanup
- `cleanupTemp()`
- Old catalog cleanup

---

## Testing

| Test | Description |
|------|-------------|
| Validate brand config | Required fields check |
| Validate section draft | Schema validation |
| Validate image | Size/type check |
| Invalid data | Returns validation errors |
| Store valid data | Calls Builder.getPath, Writer.write, catalogs |
| Store invalid data | Reports to FailSys, no write |
| Catalog entry | Adds to index |
| Find by category | Queries catalog |
| Duplicate detection | Prevents overwrite (or handles) |
| Cleanup temp | Removes old temp files |
| Builder integration | Correctly maps category → location |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [BUILDERS-PLAN.md](./BUILDERS-PLAN.md) - Provides paths and folder creation
- [WRITER-PLAN.md](./WRITER-PLAN.md) - Used by Archivist for writes
- [FAILSYS-PLAN.md](./FAILSYS-PLAN.md) - Receives failure reports
- [LOGGER-PLAN.md](./LOGGER-PLAN.md) - Logging

---

*Archivist validates and orchestrates - Builder knows where, Writer does the write.*
