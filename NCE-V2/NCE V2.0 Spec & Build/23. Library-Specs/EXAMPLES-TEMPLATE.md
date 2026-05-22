# {{Component Name}} â€” Examples

---
Component: {{component_name}}
Type: System / Subsystem / Library
Parent: {{parent_name or N/A}}
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

Complete, copy-pasteable examples showing how this component behaves.
Each example should be runnable and demonstrate real usage.

---

## Quick Reference

| Example | Demonstrates | Section |
|---------|--------------|---------|
| Basic Create | Happy path CRUD | #basic-crud |
| Validation Error | Input rejection | #validation-errors |
| Business Rule | Policy enforcement | #business-rules |
| Batch Operation | Multiple items | #batch-operations |
| Error Recovery | Failure handling | #error-handling |

---

## Basic CRUD Examples

### Create â€” Success

**Request:**
```typescript
const result = await {{component}}.create({
  name: "Example Item",
  type: "standard",
  metadata: {
    source: "user_input",
    priority: "normal"
  }
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_abc123",
    "name": "Example Item",
    "type": "standard",
    "status": "active",
    "metadata": {
      "source": "user_input",
      "priority": "normal"
    },
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T10:30:00Z"
  }
}
```

### Read â€” Success

**Request:**
```typescript
const result = await {{component}}.get("{{prefix}}_abc123");
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_abc123",
    "name": "Example Item",
    "type": "standard",
    "status": "active",
    "metadata": {
      "source": "user_input",
      "priority": "normal"
    },
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T10:30:00Z"
  }
}
```

### Read â€” Not Found

**Request:**
```typescript
const result = await {{component}}.get("{{prefix}}_nonexistent");
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_001",
    "message": "Item not found",
    "details": {
      "id": "{{prefix}}_nonexistent"
    }
  }
}
```

### Update â€” Success

**Request:**
```typescript
const result = await {{component}}.update("{{prefix}}_abc123", {
  name: "Updated Item Name",
  metadata: {
    priority: "high"
  }
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_abc123",
    "name": "Updated Item Name",
    "type": "standard",
    "status": "active",
    "metadata": {
      "source": "user_input",
      "priority": "high"
    },
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T11:45:00Z"
  }
}
```

### Delete â€” Success (Soft)

**Request:**
```typescript
const result = await {{component}}.delete("{{prefix}}_abc123");
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_abc123",
    "deleted": true,
    "deletedAt": "2024-01-15T12:00:00Z"
  }
}
```

### Delete â€” Success (Hard)

**Request:**
```typescript
const result = await {{component}}.delete("{{prefix}}_abc123", { hard: true });
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_abc123",
    "purged": true
  }
}
```

---

## List & Query Examples

### List â€” Basic

**Request:**
```typescript
const result = await {{component}}.list();
```

**Response:**
```json
{
  "success": true,
  "data": {
    "items": [
      { "id": "{{prefix}}_abc123", "name": "Item A", "status": "active" },
      { "id": "{{prefix}}_def456", "name": "Item B", "status": "active" }
    ],
    "total": 2,
    "hasMore": false
  }
}
```

### List â€” With Filters

**Request:**
```typescript
const result = await {{component}}.list({
  filter: {
    status: "active",
    type: "standard"
  },
  sort: { field: "createdAt", order: "desc" },
  limit: 10,
  offset: 0
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "items": [
      { "id": "{{prefix}}_abc123", "name": "Item A", "status": "active", "type": "standard" }
    ],
    "total": 1,
    "hasMore": false,
    "filter": { "status": "active", "type": "standard" },
    "sort": { "field": "createdAt", "order": "desc" }
  }
}
```

### List â€” Paginated

**Request:**
```typescript
const result = await {{component}}.list({
  limit: 2,
  cursor: "eyJpZCI6IjEyMyJ9"
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "items": [
      { "id": "{{prefix}}_ghi789", "name": "Item C" },
      { "id": "{{prefix}}_jkl012", "name": "Item D" }
    ],
    "total": 10,
    "hasMore": true,
    "nextCursor": "eyJpZCI6IjQ1NiJ9"
  }
}
```

---

## Validation Error Examples

### Missing Required Field

**Request:**
```typescript
const result = await {{component}}.create({
  // name is missing
  type: "standard"
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_002",
    "message": "Validation failed",
    "details": {
      "field": "name",
      "reason": "required",
      "message": "Name is required"
    }
  }
}
```

### Invalid Field Format

**Request:**
```typescript
const result = await {{component}}.create({
  name: "x",
  type: "standard"
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_002",
    "message": "Validation failed",
    "details": {
      "field": "name",
      "reason": "minLength",
      "expected": 2,
      "received": 1,
      "message": "Name must be at least 2 characters"
    }
  }
}
```

### Invalid Enum Value

**Request:**
```typescript
const result = await {{component}}.create({
  name: "Test Item",
  type: "invalid_type"
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_002",
    "message": "Validation failed",
    "details": {
      "field": "type",
      "reason": "enum",
      "allowed": ["standard", "premium", "custom"],
      "received": "invalid_type"
    }
  }
}
```

### Multiple Validation Errors

**Request:**
```typescript
const result = await {{component}}.create({
  name: "",
  type: "invalid"
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_002",
    "message": "Validation failed",
    "details": {
      "errors": [
        { "field": "name", "reason": "required", "message": "Name is required" },
        { "field": "type", "reason": "enum", "message": "Invalid type value" }
      ],
      "errorCount": 2
    }
  }
}
```

---

## Business Rule Examples

### State Transition â€” Valid

**Request:**
```typescript
const result = await {{component}}.transition("{{prefix}}_abc123", {
  from: "draft",
  to: "active"
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_abc123",
    "previousStatus": "draft",
    "currentStatus": "active",
    "transitionedAt": "2024-01-15T14:00:00Z"
  }
}
```

### State Transition â€” Invalid

**Request:**
```typescript
const result = await {{component}}.transition("{{prefix}}_abc123", {
  from: "draft",
  to: "archived"
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_103",
    "message": "Invalid state transition",
    "details": {
      "currentState": "draft",
      "requestedState": "archived",
      "allowedTransitions": ["active"],
      "message": "Cannot transition from draft to archived"
    }
  }
}
```

### Dependency Constraint â€” Has Dependents

**Request:**
```typescript
const result = await {{component}}.delete("{{prefix}}_parent", { hard: true });
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_104",
    "message": "Cannot delete: has dependents",
    "details": {
      "dependents": [
        { "id": "{{prefix}}_child1", "type": "child" },
        { "id": "{{prefix}}_child2", "type": "child" }
      ],
      "dependentCount": 2,
      "suggestion": "Delete dependents first or use cascade: true"
    }
  }
}
```

### Quota/Limit Exceeded

**Request:**
```typescript
const result = await {{component}}.create({
  name: "One More Item",
  type: "standard"
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_105",
    "message": "Quota exceeded",
    "details": {
      "limit": 100,
      "current": 100,
      "type": "items_per_account",
      "suggestion": "Delete unused items or upgrade plan"
    }
  }
}
```

---

## Batch Operation Examples

### Batch Create â€” All Success

**Request:**
```typescript
const result = await {{component}}.createBatch([
  { name: "Item A", type: "standard" },
  { name: "Item B", type: "standard" },
  { name: "Item C", type: "premium" }
]);
```

**Response:**
```json
{
  "success": true,
  "data": {
    "results": [
      { "index": 0, "success": true, "id": "{{prefix}}_aaa111" },
      { "index": 1, "success": true, "id": "{{prefix}}_bbb222" },
      { "index": 2, "success": true, "id": "{{prefix}}_ccc333" }
    ],
    "succeeded": 3,
    "failed": 0
  }
}
```

### Batch Create â€” Partial Failure

**Request:**
```typescript
const result = await {{component}}.createBatch([
  { name: "Item D", type: "standard" },
  { name: "", type: "standard" },  // Invalid
  { name: "Item E", type: "standard" }
]);
```

**Response:**
```json
{
  "success": true,
  "data": {
    "results": [
      { "index": 0, "success": true, "id": "{{prefix}}_ddd444" },
      { 
        "index": 1, 
        "success": false, 
        "error": { 
          "code": "{{PREFIX}}_002", 
          "message": "Name is required" 
        }
      },
      { "index": 2, "success": true, "id": "{{prefix}}_eee555" }
    ],
    "succeeded": 2,
    "failed": 1
  }
}
```

### Batch Update

**Request:**
```typescript
const result = await {{component}}.updateBatch([
  { id: "{{prefix}}_aaa111", changes: { status: "active" } },
  { id: "{{prefix}}_bbb222", changes: { status: "active" } }
]);
```

**Response:**
```json
{
  "success": true,
  "data": {
    "results": [
      { "id": "{{prefix}}_aaa111", "success": true },
      { "id": "{{prefix}}_bbb222", "success": true }
    ],
    "succeeded": 2,
    "failed": 0
  }
}
```

---

## Error Handling Examples

### Transient Error â€” Retry Suggested

**Request:**
```typescript
const result = await {{component}}.create({ name: "Test" });
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_301",
    "message": "Storage temporarily unavailable",
    "details": {
      "retryable": true,
      "retryAfter": 1000,
      "reason": "Database connection pool exhausted"
    },
    "traceId": "trace_abc123"
  }
}
```

### Transient Error â€” After Retry

**Request:**
```typescript
// Same request after waiting
const result = await {{component}}.create({ name: "Test" });
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_fff666",
    "name": "Test",
    "status": "active"
  }
}
```

### Dependency Failure

**Request:**
```typescript
const result = await {{component}}.process("{{prefix}}_abc123");
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_302",
    "message": "Dependency unavailable",
    "details": {
      "dependency": "ExternalService",
      "reason": "Connection timeout",
      "retryable": true,
      "retryAfter": 5000
    },
    "traceId": "trace_def456"
  }
}
```

---

## Edge Case Examples

### Empty Result Set

**Request:**
```typescript
const result = await {{component}}.list({
  filter: { status: "nonexistent_status" }
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "items": [],
    "total": 0,
    "hasMore": false
  }
}
```

### Maximum Field Length

**Request:**
```typescript
const result = await {{component}}.create({
  name: "A".repeat(100),  // Max allowed
  type: "standard"
});
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "{{prefix}}_ggg777",
    "name": "AAAA...AAAA",
    "type": "standard"
  }
}
```

### Concurrent Modification

**Request:**
```typescript
const result = await {{component}}.update("{{prefix}}_abc123", {
  name: "New Name",
  version: 1  // Stale version
});
```

**Response:**
```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_106",
    "message": "Concurrent modification detected",
    "details": {
      "yourVersion": 1,
      "currentVersion": 2,
      "suggestion": "Fetch latest and retry"
    }
  }
}
```

---

## Integration Examples

### With {{Dependency Component}}

**Scenario:** {{Describe the integration scenario}}

**Request:**
```typescript
// Step 1: Create in this component
const item = await {{component}}.create({ name: "Integration Test" });

// Step 2: Use with dependency
const result = await {{dependency}}.process(item.data.id);
```

**Response:**
```json
{
  "success": true,
  "data": {
    "sourceId": "{{prefix}}_abc123",
    "processedBy": "{{dependency}}",
    "result": "processed"
  }
}
```

---

## Notes

- All examples use TypeScript syntax but apply to any language
- Error codes follow the pattern: {{PREFIX}}_XXX
- All timestamps are ISO 8601 UTC
- IDs are prefixed with {{prefix}}_ for namespacing

---
Generated: {{timestamp}}
Phase: 21 (Internal Component Specs)
---
