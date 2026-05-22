# {{Component Name}} — Behaviour

## Overview

This document describes how the component behaves: process flows, state transitions, and runtime characteristics.

---

## Process Flows

### {{Primary Process Name}}

**Trigger:** {{What initiates this process}}

**Flow:**

```
┌─────────────┐
│   START     │
└──────┬──────┘
       │
       ▼
┌─────────────┐     ┌─────────────┐
│  Validate   │────▶│   Error     │
│   Input     │ fail└─────────────┘
└──────┬──────┘
       │ pass
       ▼
┌─────────────┐
│  Process    │
│   Data      │
└──────┬──────┘
       │
       ▼
┌─────────────┐     ┌─────────────┐
│   Save      │────▶│   Retry     │
│  Result     │ fail└──────┬──────┘
└──────┬──────┘            │
       │ success           │ max retries
       ▼                   ▼
┌─────────────┐     ┌─────────────┐
│   Emit      │     │   Error     │
│   Event     │     │  Response   │
└──────┬──────┘     └─────────────┘
       │
       ▼
┌─────────────┐
│  Return     │
│  Success    │
└─────────────┘
```

**Steps:**

| Step | Action | Success | Failure |
|------|--------|---------|---------|
| 1 | Validate input | Continue | Return error |
| 2 | Process data | Continue | Return error |
| 3 | Save result | Continue | Retry (3x) |
| 4 | Emit event | Continue | Log warning |
| 5 | Return success | Done | — |

**Detailed Steps:**

1. **Validate Input**
   - Check required fields present
   - Validate field formats
   - Check business rules
   - On failure: return `VALIDATION_ERROR`

2. **Process Data**
   - {{Processing step 1}}
   - {{Processing step 2}}
   - {{Processing step 3}}
   - On failure: return `PROCESSING_ERROR`

3. **Save Result**
   - Write to storage
   - On failure: retry with exponential backoff
   - Max 3 retries, then return `STORAGE_ERROR`

4. **Emit Event**
   - Emit `{{event_name}}` event
   - Non-blocking (failure doesn't stop flow)

5. **Return Success**
   - Return result with data

---

### {{Secondary Process Name}}

(Repeat pattern for each major process)

---

## State Machine

### {{Entity}} States

```
                    ┌──────────────┐
                    │    DRAFT     │
                    └──────┬───────┘
                           │ activate()
                           ▼
┌──────────────┐    ┌──────────────┐
│   ARCHIVED   │◀───│    ACTIVE    │
└──────────────┘    └──────────────┘
       │ restore()         │ archive()
       └───────────────────┘
```

**State Definitions:**

| State | Description | Allowed Actions |
|-------|-------------|-----------------|
| DRAFT | Initial state, incomplete | edit, activate, delete |
| ACTIVE | Ready for use | edit, archive |
| ARCHIVED | Soft deleted | restore, hard delete |

**Transitions:**

| From | To | Trigger | Validation |
|------|-----|---------|------------|
| DRAFT | ACTIVE | `activate()` | All required fields present |
| ACTIVE | ARCHIVED | `archive()` | No active dependents |
| ARCHIVED | ACTIVE | `restore()` | None |
| DRAFT | (deleted) | `delete(hard=true)` | None |
| ARCHIVED | (deleted) | `delete(hard=true)` | None |

**Invalid Transitions:**

| From | To | Why |
|------|-----|-----|
| ACTIVE | DRAFT | Cannot go back to draft |
| Any | Any | Cannot skip states |

---

## Event Emission

### Events Emitted

| Event | When | Payload |
|-------|------|---------|
| `{{component}}.created` | New item created | `{ id, name, createdBy }` |
| `{{component}}.updated` | Item modified | `{ id, changes, updatedBy }` |
| `{{component}}.deleted` | Item deleted | `{ id, deletedBy, hard }` |
| `{{component}}.status_changed` | Status transition | `{ id, from, to }` |

### Event Format

```typescript
{
  type: "{{component}}.{{action}}",
  timestamp: "2026-01-20T10:00:00Z",
  source: "{{component}}",
  payload: {
    // Event-specific data
  }
}
```

### Event Consumers

| Event | Consumer | Action |
|-------|----------|--------|
| `{{component}}.created` | Logger | Log creation |
| `{{component}}.created` | Analytics | Track usage |
| `{{component}}.deleted` | Dependents | Update references |

---

## Concurrency

### Concurrent Access

| Scenario | Behaviour | Resolution |
|----------|-----------|------------|
| Two writes to same item | Last write wins | Optimistic locking via version |
| Read during write | Read may see old data | Acceptable (eventual consistency) |
| Delete during read | Read completes, delete waits | Lock on delete |

### Locking Strategy

```typescript
// Optimistic locking with version field
{
  id: "item_123",
  version: 5,
  // ... other fields
}

// On update:
// 1. Read current version
// 2. Make changes
// 3. Write with version check
// 4. If version mismatch, retry or fail
```

---

## Caching

### Cache Strategy

| Data | Cache Location | TTL | Invalidation |
|------|----------------|-----|--------------|
| Item by ID | Memory | 5 min | On update/delete |
| List results | Memory | 1 min | On any mutation |
| Computed data | Memory | 10 min | Manual |

### Cache Keys

```
{{component}}:item:{id}
{{component}}:list:{hash_of_filters}
{{component}}:computed:{key}
```

### Cache Invalidation

| Event | Invalidate |
|-------|------------|
| Create | List caches |
| Update | Item cache + list caches |
| Delete | Item cache + list caches |

---

## Retry Behaviour

### Retryable Operations

| Operation | Retries | Backoff | Timeout |
|-----------|---------|---------|---------|
| Database write | 3 | Exponential (100ms, 200ms, 400ms) | 5s |
| External API call | 3 | Exponential (1s, 2s, 4s) | 30s |
| Event emission | 2 | Fixed (100ms) | 1s |

### Non-Retryable

| Operation | Why |
|-----------|-----|
| Validation | Deterministic failure |
| Business rule violation | Deterministic failure |
| Authentication failure | Won't succeed on retry |

---

## Timeouts

| Operation | Timeout | On Timeout |
|-----------|---------|------------|
| Overall request | 30s | Return `TIMEOUT_ERROR` |
| Database query | 5s | Return `STORAGE_ERROR` |
| External call | 10s | Return `EXTERNAL_ERROR` |

---

## Batch Processing

### Batch Operations

| Operation | Max Batch Size | Behaviour |
|-----------|----------------|-----------|
| Bulk create | 100 | Atomic (all or nothing) |
| Bulk update | 50 | Partial success allowed |
| Bulk delete | 100 | Atomic |

### Partial Success

```typescript
// Bulk update result
{
  success: true,  // At least one succeeded
  results: [
    { id: "item_1", success: true },
    { id: "item_2", success: false, error: { code: "...", message: "..." } },
    { id: "item_3", success: true }
  ],
  succeeded: 2,
  failed: 1
}
```

---

## Observability

### Logging

| Level | When | Example |
|-------|------|---------|
| DEBUG | Detailed flow | "Processing item_123" |
| INFO | Significant events | "Created item_123" |
| WARN | Recoverable issues | "Retry 2/3 for storage write" |
| ERROR | Failures | "Failed to save item_123: timeout" |

### Metrics

| Metric | Type | Description |
|--------|------|-------------|
| `{{component}}_operations_total` | Counter | Total operations by type |
| `{{component}}_operation_duration_ms` | Histogram | Operation latency |
| `{{component}}_errors_total` | Counter | Errors by code |
| `{{component}}_cache_hits` | Counter | Cache hit rate |

### Tracing

- All operations include trace ID
- Propagate trace to downstream calls
- Log trace ID in errors

---

## Notes

- All behaviour assumes happy path unless otherwise noted
- See ERRORS.md for failure handling
- See CONFIG.md for configurable values

---
Source: PRE-SPEC-NOTES.md sections 3, 8
Phase: 21 (Internal Component Specs)
---
