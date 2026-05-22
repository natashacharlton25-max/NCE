# API Standards

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

Defines API design standards for all internal APIs between components. External/public APIs may have additional requirements.

---

## General Principles

1. **Consistency** — All APIs follow the same patterns
2. **Predictability** — Behavior is obvious from the signature
3. **Discoverability** — Easy to find the right function
4. **Safety** — Errors are explicit, not exceptional

---

## Function Signatures

### Standard Pattern

```typescript
async function {verb}{Entity}(
  required: RequiredType,
  options?: {Entity}{Verb}Options
): Promise<Result<{ReturnType}>>
```

### Naming Conventions

| Operation | Naming Pattern | Example |
|-----------|----------------|---------|
| Create | `create{Entity}` | `createUser(input)` |
| Get single | `get{Entity}` | `getUser(id)` |
| Get multiple | `list{Entities}` | `listUsers(options)` |
| Update | `update{Entity}` | `updateUser(id, changes)` |
| Delete | `delete{Entity}` | `deleteUser(id, options)` |
| Check exists | `{entity}Exists` | `userExists(id)` |
| Validate | `validate{Thing}` | `validateEmail(email)` |
| Search | `search{Entities}` | `searchUsers(query)` |
| Count | `count{Entities}` | `countUsers(filter)` |

### Parameter Order

1. Required identifiers (id, parent_id)
2. Required data (input, changes)
3. Optional options object

```typescript
// Good
updateUser(id: string, changes: UserChanges, options?: UpdateOptions)

// Bad
updateUser(options: UpdateOptions, id: string, changes: UserChanges)
```

---

## Return Types

### Result Type (All Operations)

```typescript
type Result<T> = 
  | { success: true; data: T }
  | { success: false; error: ErrorDetail };
```

**All functions return `Result<T>`**, even for:
- Void operations: `Result<void>`
- Boolean checks: `Result<boolean>` or just `boolean` for simple checks
- Existence checks: Can be `boolean` directly

### List Result Type

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

### Error Detail

```typescript
interface ErrorDetail {
  code: string;        // Error code from ERROR-CODES.md
  message: string;     // Human-readable message
  field?: string;      // For validation errors
  details?: object;    // Additional context
  traceId?: string;    // For debugging
}
```

---

## CRUD Operations

### Create

```typescript
async function createUser(
  input: CreateUserInput
): Promise<Result<User>>
```

**Input validation:**
- Validate all required fields
- Return `{PREFIX}_001-099` for validation errors

**Behavior:**
- Generate ID if not provided
- Set `createdAt` and `updatedAt`
- Return created entity with ID

**Errors:**
- Validation errors (400)
- Duplicate errors (409)
- Storage errors (500)

### Get (Single)

```typescript
async function getUser(
  id: string
): Promise<Result<User>>
```

**Behavior:**
- Return entity if found
- Return `NOT_FOUND` error if not found

**Errors:**
- Not found (404)
- Invalid ID format (400)

### List (Multiple)

```typescript
async function listUsers(
  options?: ListUsersOptions
): Promise<ListResult<User>>
```

**Options structure:**
```typescript
interface ListUsersOptions {
  filter?: {
    status?: UserStatus;
    createdAfter?: string;
    // entity-specific filters
  };
  sort?: {
    field: 'createdAt' | 'name' | 'email';
    order: 'asc' | 'desc';
  };
  pagination?: {
    limit?: number;   // Default: 20, Max: 100
    offset?: number;
    cursor?: string;
  };
}
```

**Behavior:**
- Apply filters
- Apply sorting (default: `createdAt desc`)
- Apply pagination
- Return total count
- Return `hasMore` indicator

### Update

```typescript
async function updateUser(
  id: string,
  changes: UpdateUserInput
): Promise<Result<User>>
```

**Behavior:**
- Partial update (only provided fields)
- Set `updatedAt`
- Return updated entity

**Errors:**
- Not found (404)
- Validation errors (400)
- Conflict errors (409)

### Delete

```typescript
async function deleteUser(
  id: string,
  options?: DeleteOptions
): Promise<Result<void>>
```

**Options:**
```typescript
interface DeleteOptions {
  hard?: boolean;     // Default: false (soft delete)
  cascade?: boolean;  // Delete dependents
}
```

**Behavior:**
- Default: soft delete (set `deletedAt`)
- `hard: true`: permanent delete
- Check for dependents unless `cascade: true`

**Errors:**
- Not found (404)
- Has dependents (409)

---

## Options Objects

### When to Use

| # of Optional Params | Approach |
|---------------------|----------|
| 0-2 | Individual parameters OK |
| 3+ | Use options object |

### Structure

```typescript
interface {Entity}{Operation}Options {
  // Group related options
  filter?: { ... };
  sort?: { ... };
  pagination?: { ... };
  
  // Simple flags
  includeDeleted?: boolean;
  skipValidation?: boolean;
}
```

### Defaults

- Document all defaults in spec
- Apply defaults in function, not caller
- Prefer safe defaults

---

## Batch Operations

### Naming

```typescript
create{Entities}Batch(items: Input[]): Promise<BatchResult<Entity>>
update{Entities}Batch(updates: Update[]): Promise<BatchResult<Entity>>
delete{Entities}Batch(ids: string[]): Promise<BatchResult<void>>
```

### Return Type

```typescript
interface BatchResult<T> {
  success: boolean;      // True if any succeeded
  results: Array<{
    index: number;
    success: boolean;
    data?: T;
    error?: ErrorDetail;
  }>;
  succeeded: number;
  failed: number;
}
```

### Behavior

- Process all items (don't stop on first error)
- Return individual results for each item
- `success: true` if at least one succeeded

### Limits

| Operation | Default Max | Hard Max |
|-----------|-------------|----------|
| Batch create | 100 | 1000 |
| Batch update | 50 | 500 |
| Batch delete | 100 | 1000 |

---

## Pagination

### Offset-Based (Simple)

```typescript
{
  pagination: {
    limit: 20,
    offset: 40
  }
}
```

**Use for:** Small datasets, random access needed

### Cursor-Based (Scalable)

```typescript
{
  pagination: {
    limit: 20,
    cursor: "eyJpZCI6IjEyMyJ9"
  }
}
```

**Use for:** Large datasets, real-time data

### Response

```typescript
{
  items: [...],
  total: 150,
  pagination: {
    limit: 20,
    offset: 0,        // or cursor used
    hasMore: true,
    nextCursor: "..."  // for cursor-based
  }
}
```

---

## Filtering

### Standard Filters

| Filter | Syntax | Example |
|--------|--------|---------|
| Equality | `field: value` | `status: 'active'` |
| Multiple values | `field: [values]` | `status: ['active', 'pending']` |
| Range | `fieldMin/Max` | `createdAtMin: '2024-01-01'` |
| Text search | `search` | `search: 'john'` |

### Example

```typescript
listUsers({
  filter: {
    status: 'active',
    role: ['admin', 'user'],
    createdAtMin: '2024-01-01',
    search: 'john'
  }
})
```

---

## Sorting

### Standard Format

```typescript
{
  sort: {
    field: 'createdAt',
    order: 'desc'
  }
}
```

### Multiple Sort Fields

```typescript
{
  sort: [
    { field: 'status', order: 'asc' },
    { field: 'createdAt', order: 'desc' }
  ]
}
```

### Defaults

- Default sort: `createdAt desc`
- Always have a deterministic sort (add `id` as tiebreaker)

---

## Error Handling

### Error Response

```typescript
{
  success: false,
  error: {
    code: "USR_101",
    message: "User not found",
    details: { id: "usr_123" },
    traceId: "trace_abc"
  }
}
```

### Error Categories

| Category | Codes | HTTP Equivalent |
|----------|-------|-----------------|
| Validation | *_001-099 | 400 |
| Business | *_100-199 | 400/404/409 |
| Authorization | *_200-299 | 403 |
| Dependency | *_300-399 | 500/503 |
| Storage | *_400-499 | 500 |
| External | *_500-599 | 502/503 |
| System | *_900-999 | 500 |

### Never Throw for Expected Errors

```typescript
// Good
if (!user) {
  return { success: false, error: { code: 'USR_101', message: 'User not found' } };
}

// Bad
if (!user) {
  throw new NotFoundError('User not found');
}
```

---

## Versioning

### Internal APIs

- No versioning for internal component APIs
- Breaking changes require migration of all callers
- Use deprecation warnings before removal

### Deprecation

```typescript
/**
 * @deprecated Use createUserV2 instead. Will be removed in v2.0.
 */
function createUser(input: CreateUserInput): Promise<Result<User>>
```

---

## Documentation Requirements

Every public function must have:

1. **Description** — What it does
2. **Parameters** — Type and description for each
3. **Returns** — What it returns
4. **Errors** — What errors it can return
5. **Example** — At least one usage example

---

## Summary Checklist

- [ ] Function name follows convention
- [ ] Parameters in correct order
- [ ] Returns `Result<T>` or `ListResult<T>`
- [ ] Options object for 3+ optional params
- [ ] Errors return Result, not throw
- [ ] Pagination for lists
- [ ] Documented in FUNCTIONS.md
- [ ] Example in EXAMPLES.md

---
Phase: 26 (Project-Wide Specs)
Generated: {{timestamp}}
---
