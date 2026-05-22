# Project Schemas — Shared Types

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document defines shared types used across multiple components. These are the canonical definitions — component specs reference this document rather than redefining.

---

## Schema Index

| Type | Category | Used By |
|------|----------|---------|
| `Result<T>` | Utility | All components |
| `ListResult<T>` | Utility | All list operations |
| `Pagination` | Utility | All list operations |
| `ErrorDetail` | Error | All components |
| `Timestamp` | Primitive | All entities |
| `EntityId` | Primitive | All entities |

---

## Utility Types

### Result<T>

Standard result wrapper for all operations.

```typescript
type Result<T> = 
  | { success: true; data: T }
  | { success: false; error: ErrorDetail };
```

**Used By:** All components

**Usage:**
```typescript
// Success
{ success: true, data: { id: "usr_123", name: "John" } }

// Failure
{ success: false, error: { code: "USR_001", message: "User not found" } }
```

**Rules:**
- Always return `Result<T>`, never throw for expected errors
- `data` is present only when `success: true`
- `error` is present only when `success: false`

---

### ListResult<T>

Standard result for list operations with pagination.

```typescript
interface ListResult<T> {
  success: boolean;
  items: T[];
  total: number;
  pagination: {
    limit: number;
    offset: number;
    hasMore: boolean;
    nextCursor?: string;
  };
  error?: ErrorDetail;
}
```

**Used By:** All list/query operations

**Usage:**
```typescript
{
  success: true,
  items: [{ id: "usr_1" }, { id: "usr_2" }],
  total: 150,
  pagination: {
    limit: 20,
    offset: 0,
    hasMore: true,
    nextCursor: "eyJpZCI6InVzcl8yIn0="
  }
}
```

---

### Pagination

Pagination parameters for list requests.

```typescript
interface Pagination {
  limit?: number;    // Default: 20, Max: 100
  offset?: number;   // Default: 0
  cursor?: string;   // For cursor-based pagination
}
```

**Used By:** All list operations

**Defaults:**

| Field | Default | Min | Max |
|-------|---------|-----|-----|
| limit | 20 | 1 | 100 |
| offset | 0 | 0 | — |

---

### Sort

Sort parameters for list requests.

```typescript
interface Sort {
  field: string;
  order: 'asc' | 'desc';
}
```

**Used By:** All sortable list operations

---

### Filter

Generic filter parameters.

```typescript
interface Filter {
  [field: string]: string | number | boolean | string[] | undefined;
}
```

**Used By:** All filterable list operations

---

## Error Types

### ErrorDetail

Standard error structure.

```typescript
interface ErrorDetail {
  code: string;
  message: string;
  field?: string;
  details?: Record<string, unknown>;
  traceId?: string;
}
```

**Used By:** All components

**Fields:**

| Field | Required | Description |
|-------|----------|-------------|
| code | Yes | Error code (see ERROR-CODES.md) |
| message | Yes | Human-readable message |
| field | No | Affected field (for validation) |
| details | No | Additional context |
| traceId | No | For debugging (system errors) |

**Usage:**
```typescript
{
  code: "USR_002",
  message: "Email already exists",
  field: "email",
  details: { existingUserId: "usr_456" }
}
```

---

### ValidationError

Multiple validation errors.

```typescript
interface ValidationError {
  code: string;
  message: string;
  errors: Array<{
    field: string;
    code: string;
    message: string;
  }>;
}
```

**Used By:** Input validation

---

## Primitive Types

### Timestamp

ISO 8601 timestamp string.

```typescript
type Timestamp = string;  // ISO 8601 format
```

**Format:** `YYYY-MM-DDTHH:mm:ss.sssZ`

**Example:** `"2024-01-15T10:30:00.000Z"`

**Rules:**
- Always store in UTC
- Always use ISO 8601 format
- Include milliseconds
- Include Z suffix

---

### EntityId

Standard entity identifier.

```typescript
type EntityId = string;  // Format: {prefix}_{identifier}
```

**Format:** `{prefix}_{identifier}`

**Prefixes:**

| Entity | Prefix | Example |
|--------|--------|---------|
| User | `usr` | `usr_abc123` |
| Project | `prj` | `prj_def456` |
| Task | `tsk` | `tsk_ghi789` |
| Session | `ses` | `ses_jkl012` |

**Rules:**
- Always use lowercase prefix
- Use underscore separator
- Identifier part is URL-safe

---

### Email

Validated email address.

```typescript
type Email = string;  // RFC 5322 compliant
```

**Validation:** Must match RFC 5322 email format

---

### URL

Validated URL.

```typescript
type URL = string;  // Valid URL format
```

**Validation:** Must be valid URL with protocol

---

## Entity Base Types

### BaseEntity

Fields common to all entities.

```typescript
interface BaseEntity {
  id: EntityId;
  createdAt: Timestamp;
  updatedAt: Timestamp;
}
```

**Used By:** All entity types extend this

---

### SoftDeletableEntity

Entity that supports soft delete.

```typescript
interface SoftDeletableEntity extends BaseEntity {
  deletedAt?: Timestamp;
}
```

**Used By:** Entities with soft delete

---

### AuditableEntity

Entity with audit fields.

```typescript
interface AuditableEntity extends BaseEntity {
  createdBy: EntityId;
  updatedBy: EntityId;
}
```

**Used By:** Entities requiring audit trail

---

## Status Enums

### EntityStatus

Generic entity status.

```typescript
type EntityStatus = 'draft' | 'active' | 'archived';
```

**State Transitions:**
```
draft → active
active → archived
archived → active (restore)
```

**Used By:** Default for entities without custom status

---

### OperationStatus

Status of async operations.

```typescript
type OperationStatus = 'pending' | 'processing' | 'completed' | 'failed';
```

**Used By:** Async operation tracking

---

## Request/Response Types

### ApiRequest

Standard API request wrapper (if applicable).

```typescript
interface ApiRequest<T> {
  data: T;
  requestId?: string;
  timestamp?: Timestamp;
}
```

---

### ApiResponse

Standard API response wrapper (if applicable).

```typescript
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: ErrorDetail;
  requestId?: string;
  timestamp: Timestamp;
}
```

---

## Type Aliases

### Common Aliases

```typescript
// ID types
type UserId = EntityId;
type ProjectId = EntityId;
type TaskId = EntityId;

// Collection types
type EntityList<T> = T[];

// Optional wrapper
type Maybe<T> = T | null | undefined;

// Record types
type StringRecord = Record<string, string>;
type AnyRecord = Record<string, unknown>;
```

---

## Type Validation Rules

### ID Validation

| Type | Pattern | Example Valid | Example Invalid |
|------|---------|---------------|-----------------|
| EntityId | `^[a-z]{3}_[a-zA-Z0-9]+$` | `usr_abc123` | `user-123` |
| Email | RFC 5322 | `user@example.com` | `not-an-email` |
| Timestamp | ISO 8601 | `2024-01-15T10:30:00Z` | `Jan 15, 2024` |

---

## Usage in Components

### How to Reference

In component TYPES.md:
```markdown
## Shared Types

This component uses the following shared types from SCHEMAS.md:
- `Result<T>` — All function returns
- `EntityId` — User references
- `Timestamp` — Date fields
```

### Import Pattern

```typescript
// In implementation
import { Result, EntityId, Timestamp } from '@/shared/types';
```

---

## Change Log

| Version | Date | Change | Impact |
|---------|------|--------|--------|
| 1.0.0 | {{date}} | Initial schemas | — |

---

## Notes

- All shared types are immutable once in use
- Breaking changes require major version bump
- Add new types here when 3+ components need them
- Component-specific types stay in component specs

---
Phase: 26 (Project-Wide Specs)
Generated: {{timestamp}}
---
