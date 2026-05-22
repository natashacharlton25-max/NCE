# {{Component Name}} — State Specification

---
Component: {{component_name}}
Spec Type: Internal State
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Internal state management for {{component_name}}.

---

## State Shape

```typescript
interface {{ComponentName}}State {
  {{field}}: {{type}};
}
```

---

## State Fields

| Field | Type | Initial | Description |
|-------|------|---------|-------------|
| `{{field}}` | `{{type}}` | `{{initial}}` | {{description}} |

---

## State Machine

### States

| State | Description | Entry Condition |
|-------|-------------|-----------------|
| `idle` | Default state | Component mounted |
| `loading` | Fetching data | API call started |
| `success` | Data loaded | API call succeeded |
| `error` | Something failed | API call failed |

### Transitions

```
idle → loading (on: fetch)
loading → success (on: fetchSuccess)
loading → error (on: fetchError)
error → loading (on: retry)
success → loading (on: refresh)
```

### State Diagram

```
┌─────────┐
│  idle   │
└────┬────┘
     │ fetch
     ▼
┌─────────┐
│ loading │◄────────┐
└────┬────┘         │
     │              │ retry/refresh
  ┌──┴──┐           │
  ▼     ▼           │
┌────┐ ┌─────┐      │
│pass│ │error│──────┘
└────┘ └─────┘
```

---

## State Updates

### `{{action_name}}`

**Trigger:** {{what causes this update}}

**Before:**
```typescript
{ status: 'idle', data: null }
```

**After:**
```typescript
{ status: 'loading', data: null }
```

---

## Derived State

| Derived | From | Logic |
|---------|------|-------|
| `isLoading` | `status` | `status === 'loading'` |
| `hasError` | `status` | `status === 'error'` |
| `isEmpty` | `data` | `data?.length === 0` |

---

## State Persistence

| Field | Persist? | Storage | Key |
|-------|----------|---------|-----|
| `{{field}}` | Yes/No | localStorage / sessionStorage | `{{key}}` |

---

## Notes

{{Additional notes about state management}}

---
Template: DEEP-SPEC-STATE.md
Pack: Optional Deep Specs
---
