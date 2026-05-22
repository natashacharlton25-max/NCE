# {{Component Name}} â€” Types

## Overview

| Category | Count | Description |
|----------|-------|-------------|
| Core Types | {{n}} | Primary data structures |
| Input Types | {{n}} | Function parameters |
| Output Types | {{n}} | Function return values |
| Internal Types | {{n}} | Implementation details |

---

## Type Index

| Type | Category | Description |
|------|----------|-------------|
| {{Type}} | Core | {{description}} |
| {{Type}}Options | Input | {{description}} |
| {{Type}}Result | Output | {{description}} |

---

## Core Types

### {{TypeName}}

{{Description of what this type represents}}

```typescript
interface {{TypeName}} {
  // === REQUIRED FIELDS ===
  
  /** Unique identifier */
  id: string;
  
  /** Human-readable name */
  name: string;
  
  /** Creation timestamp (ISO 8601) */
  createdAt: string;
  
  // === OPTIONAL FIELDS ===
  
  /** Description (max 500 chars) */
  description?: string;
  
  /** Current status */
  status?: {{TypeName}}Status;
  
  // === NESTED STRUCTURES ===
  
  /** Metadata object */
  metadata?: {
    /** Who created this */
    createdBy: string;
    
    /** Last modification timestamp */
    updatedAt?: string;
  };
}
```

**Field Details:**

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| id | string | Yes | Format: `{{prefix}}_{{slug}}` | Unique identifier |
| name | string | Yes | 1-100 chars | Human-readable name |
| createdAt | string | Yes | ISO 8601 | When created |
| description | string | No | Max 500 chars | Optional description |
| status | {{TypeName}}Status | No | Enum value | Current status |

**Validation Rules:**
- `id` must be unique
- `id` must match pattern: `/^{{prefix}}_[a-z0-9-]+$/`
- `name` cannot be empty
- `createdAt` must be valid ISO 8601

**Example:**
```json
{
  "id": "{{prefix}}_example-item",
  "name": "Example Item",
  "createdAt": "2026-01-20T10:00:00Z",
  "description": "An example item",
  "status": "active",
  "metadata": {
    "createdBy": "user_123",
    "updatedAt": "2026-01-20T11:00:00Z"
  }
}
```

---

### {{TypeName}}Status

Enumeration of possible statuses.

```typescript
type {{TypeName}}Status = "draft" | "active" | "archived";
```

| Value | Description | Transitions To |
|-------|-------------|----------------|
| draft | Initial state, not ready | active |
| active | Ready for use | archived |
| archived | Soft deleted | active (restore) |

---

## Input Types

### {{FunctionName}}Options

Options for the `{{functionName}}()` function.

```typescript
interface {{FunctionName}}Options {
  // === FILTERING ===
  
  /** Filter by status */
  status?: {{TypeName}}Status;
  
  /** Filter by date range */
  dateRange?: {
    from?: string;  // ISO 8601
    to?: string;    // ISO 8601
  };
  
  // === PAGINATION ===
  
  /** Number of results (default: 50, max: 100) */
  limit?: number;
  
  /** Offset for pagination (default: 0) */
  offset?: number;
  
  // === SORTING ===
  
  /** Field to sort by */
  sortBy?: "createdAt" | "name" | "status";
  
  /** Sort direction (default: "desc") */
  sortOrder?: "asc" | "desc";
}
```

**Defaults:**

| Field | Default | Notes |
|-------|---------|-------|
| limit | 50 | Max 100 |
| offset | 0 | â€” |
| sortOrder | "desc" | For dates; "asc" for name |

---

### {{FunctionName}}Input

Input for the `{{functionName}}()` function.

```typescript
interface {{FunctionName}}Input {
  /** Required field */
  requiredField: string;
  
  /** Optional field with default */
  optionalField?: string;
  
  /** Nested options */
  options?: {
    setting1: boolean;
    setting2: number;
  };
}
```

**Validation:**
- `requiredField` is required
- `optionalField` defaults to `"default_value"`

---

## Output Types

### {{FunctionName}}Result

Result from the `{{functionName}}()` function.

```typescript
interface {{FunctionName}}Result {
  /** Whether operation succeeded */
  success: boolean;
  
  /** Result data (on success) */
  data?: {{TypeName}};
  
  /** Error details (on failure) */
  error?: ErrorDetail;
}
```

**Success Example:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_example",
    "name": "Example"
  }
}
```

**Error Example:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Name is required",
    "field": "name"
  }
}
```

---

### ListResult

Paginated list result.

```typescript
interface ListResult<T> {
  success: boolean;
  items: T[];
  total: number;
  limit: number;
  offset: number;
  hasMore: boolean;
}
```

---

### ErrorDetail

Standard error structure.

```typescript
interface ErrorDetail {
  /** Error code (see ERRORS.md) */
  code: string;
  
  /** Human-readable message */
  message: string;
  
  /** Affected field (for validation errors) */
  field?: string;
  
  /** Additional context */
  details?: Record<string, unknown>;
}
```

---

## Internal Types

### {{InternalType}}

(Types used internally, not exposed in public API)

```typescript
interface {{InternalType}} {
  // Internal fields
}
```

**Note:** Internal types may change without notice. Do not depend on them externally.

---

## Type Relationships

```
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚   {{TypeName}}  â”‚
â”œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¤
â”‚ id              â”‚
â”‚ name            â”‚
â”‚ status â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â–¶ {{TypeName}}Status (enum)
â”‚ metadata â”€â”€â”€â”€â”€â”€â”€â”¼â”€â”€â–¶ Metadata (nested)
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
         â”‚
         â”‚ used in
         â–¼
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ {{Fn}}Input     â”‚â”€â”€â”€â”€â–¶â”‚ {{Fn}}Result    â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜     â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

---

## Shared Types

These types are defined in project-wide SCHEMAS.md and used here:

| Type | Location | Usage |
|------|----------|-------|
| Timestamp | SCHEMAS.md | All date fields |
| UUID | SCHEMAS.md | ID generation |
| ErrorDetail | SCHEMAS.md | Error responses |

---

## Notes

- All types use TypeScript syntax for clarity
- JSON examples show runtime format
- Optional fields may be omitted or null
- See SCHEMAS.md for shared types

---
Source: PRE-SPEC-NOTES.md section 9
Phase: 21 (Internal Component Specs)
---
