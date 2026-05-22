# {{Component Name}} â€” Functions

## Overview

| Category | Count | Description |
|----------|-------|-------------|
| Lifecycle | {{n}} | Initialization, shutdown |
| Core | {{n}} | Primary functionality |
| Query | {{n}} | Read operations |
| Mutation | {{n}} | Write operations |
| Utility | {{n}} | Helper functions |

---

## Function Index

| Function | Category | Description |
|----------|----------|-------------|
| `initialize()` | Lifecycle | {{description}} |
| `{{function}}()` | Core | {{description}} |
| `{{function}}()` | Query | {{description}} |
| `{{function}}()` | Mutation | {{description}} |

---

## Lifecycle Functions

### initialize()

Initialize the component. Must be called before any other function.

**Signature:**
```typescript
initialize(options?: InitOptions): Promise<InitResult>
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| options | InitOptions | No | {} | Initialization options |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<InitResult> | Initialization result |

**InitOptions Type:**
```typescript
{
  // Options fields
}
```

**InitResult Type:**
```typescript
{
  success: boolean,
  version: string,
  readyAt: string  // ISO timestamp
}
```

**Process:**
1. {{Step 1}}
2. {{Step 2}}
3. {{Step 3}}

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| {{CODE}} | {{when}} | {{how}} |

**Example:**
```typescript
const result = await component.initialize();
// { success: true, version: "1.0.0", readyAt: "2026-01-20T10:00:00Z" }
```

---

## Core Functions

### {{functionName}}()

{{One sentence description of what this function does}}

**Signature:**
```typescript
{{functionName}}({{params}}): Promise<{{ReturnType}}>
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {{param}} | {{type}} | Yes/No | {{default}} | {{description}} |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<{{Type}}> | {{description}} |

**Process:**
1. **Validate Input**
   - {{validation step}}
   - {{validation step}}

2. **Process**
   - {{processing step}}
   - {{processing step}}

3. **Return Result**
   - {{result step}}

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| {{CODE}} | {{when}} | {{how}} |

**Example (Success):**
```typescript
// Input
const input = {
  {{field}}: {{value}}
};

// Call
const result = await component.{{functionName}}(input);

// Output
{
  success: true,
  data: {
    {{field}}: {{value}}
  }
}
```

**Example (Error):**
```typescript
// Input (invalid)
const input = {
  {{field}}: {{invalidValue}}
};

// Call
const result = await component.{{functionName}}(input);

// Output
{
  success: false,
  error: {
    code: "{{ERROR_CODE}}",
    message: "{{error message}}"
  }
}
```

---

### {{nextFunction}}()

(Repeat the pattern above for each function)

---

## Query Functions

### list()

Query items with filtering, sorting, and pagination.

**Signature:**
```typescript
list(filters?: ListFilters): Promise<ListResult>
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| filters | ListFilters | No | {} | Query filters |

**ListFilters Type:**
```typescript
{
  // Filter fields
  status?: "active" | "archived" | "all",
  
  // Pagination
  limit?: number,      // default: 50, max: 100
  offset?: number,     // default: 0
  
  // Sorting
  sortBy?: string,     // field name
  sortOrder?: "asc" | "desc"
}
```

**Returns:**

| Type | Description |
|------|-------------|
| Promise<ListResult> | Paginated results |

**ListResult Type:**
```typescript
{
  success: boolean,
  items: Item[],
  total: number,
  limit: number,
  offset: number,
  hasMore: boolean
}
```

**Process:**
1. Apply filters to query
2. Apply sorting
3. Apply pagination
4. Return results with metadata

**Example:**
```typescript
const result = await component.list({
  status: "active",
  limit: 10,
  sortBy: "createdAt",
  sortOrder: "desc"
});

// {
//   success: true,
//   items: [...],
//   total: 45,
//   limit: 10,
//   offset: 0,
//   hasMore: true
// }
```

---

### get()

Retrieve a single item by ID.

**Signature:**
```typescript
get(id: string): Promise<GetResult>
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Yes | Item identifier |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<GetResult> | Item or not found error |

**Process:**
1. Validate ID format
2. Query storage
3. Return item or error

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| NOT_FOUND | ID doesn't exist | Check ID, create if needed |
| INVALID_ID | ID format invalid | Fix ID format |

---

## Mutation Functions

### save()

Create or update an item.

**Signature:**
```typescript
save(item: Item): Promise<SaveResult>
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| item | Item | Yes | Item to save |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<SaveResult> | Saved item with ID |

**Process:**
1. Validate item structure
2. Generate ID if new
3. Set timestamps
4. Write to storage
5. Return saved item

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| VALIDATION_ERROR | Invalid item | Fix item structure |
| DUPLICATE | ID already exists | Use update or change ID |
| STORAGE_ERROR | Write failed | Retry |

---

### delete()

Remove an item.

**Signature:**
```typescript
delete(id: string, options?: DeleteOptions): Promise<DeleteResult>
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| id | string | Yes | â€” | Item ID |
| options | DeleteOptions | No | {} | Delete options |

**DeleteOptions Type:**
```typescript
{
  hard?: boolean,     // true = permanent, false = archive (default)
  cascade?: boolean   // delete dependents
}
```

**Process:**
1. Check item exists
2. Check for dependents
3. Soft delete (archive) or hard delete
4. Return confirmation

**Errors:**

| Code | Condition | Recovery |
|------|-----------|----------|
| NOT_FOUND | ID doesn't exist | Check ID |
| HAS_DEPENDENTS | Other items depend on this | Delete dependents first or use cascade |

---

## Utility Functions

### exists()

Check if an item exists.

**Signature:**
```typescript
exists(id: string): Promise<boolean>
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | Yes | Item ID |

**Returns:**

| Type | Description |
|------|-------------|
| Promise<boolean> | true if exists |

---

## Notes

- All functions return Promises (async)
- All mutations emit events (see BEHAVIOUR.md)
- All errors follow standard format (see ERRORS.md)

---
Source: PRE-SPEC-NOTES.md sections 3, 4
Phase: 21 (Internal Component Specs)
---
