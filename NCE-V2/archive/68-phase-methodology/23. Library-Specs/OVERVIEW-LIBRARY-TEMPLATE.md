# {{Library Name}} — Overview

---
Component: lib-{{name}}
Type: Library
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

(2-3 sentences explaining what utility this library provides and why it was extracted as a shared library)

---

## Library Principles

### This Library IS:

- **Pure:** Functions have no side effects (or clearly documented when they do)
- **Stateless:** No internal state between calls
- **Focused:** One concern only
- **Reusable:** Generic enough for multiple consumers

### This Library is NOT:

- A place for business logic
- A decision-maker
- A data store
- An orchestrator

---

## What This Library Provides

### Functions

| Category | Functions | Purpose |
|----------|-----------|---------|
| {{category}} | `{{fn}}`, `{{fn}}` | {{purpose}} |
| {{category}} | `{{fn}}`, `{{fn}}` | {{purpose}} |

### Types

| Type | Purpose |
|------|---------|
| {{Type}} | {{purpose}} |
| {{Type}} | {{purpose}} |

---

## Why This Library Exists

### Pattern Identified

This library was extracted because:

| Signal | Evidence |
|--------|----------|
| Repeated code | Found in {{n}} places |
| Common utility | Used by {{systems}} |
| Pure function | No business context needed |

### Before (Duplication)

```
System A: has own date formatter
System B: has own date formatter  
System C: has own date formatter (slightly different!)
```

### After (Library)

```
lib-datetime: single date formatter
System A, B, C: all use lib-datetime
```

---

## Scope

### In Scope

- {{Responsibility 1}}
- {{Responsibility 2}}
- {{Responsibility 3}}

### Out of Scope

- {{What consumers handle}}
- {{What other libraries handle}}
- {{Business logic that belongs elsewhere}}

---

## Boundaries

### Inputs

| Input | Type | From |
|-------|------|------|
| {{input}} | {{type}} | Any caller |

### Outputs

| Output | Type | To |
|--------|------|-----|
| {{output}} | {{type}} | Caller |

**No side effects.** Functions return values, nothing else.

---

## Guarantees

What consumers can rely on:

| Guarantee | Description |
|-----------|-------------|
| **Deterministic** | Same input → same output |
| **No side effects** | Won't modify external state |
| **No exceptions** | Returns Result type or throws documented errors only |
| **Thread-safe** | Can be called concurrently |

---

## Constraints

| Constraint | Description |
|------------|-------------|
| No I/O | No file, network, or database access |
| No state | No internal mutable state |
| No dependencies on systems | Cannot import from systems |
| Minimal library deps | Only other utility libraries |

---

## Dependencies

### Allowed Dependencies

| Dependency | Type | Why |
|------------|------|-----|
| lib-{{other}} | Library | {{reason}} |
| Built-in stdlib | Language | Standard utilities |

### Forbidden Dependencies

| Dependency | Why Forbidden |
|------------|---------------|
| Any System | Libraries don't depend on systems |
| Any Subsystem | Libraries don't depend on subsystems |
| External services | Libraries are pure |

---

## Error Philosophy

| Principle | Implementation |
|-----------|----------------|
| Fail clearly | Return typed errors, not exceptions |
| Fail fast | Validate inputs immediately |
| No silent failures | Always return success or error |

---

## Testing Philosophy

| Test Type | Coverage |
|-----------|----------|
| Unit tests | 100% of public functions |
| Edge cases | Nulls, empty, boundary values |
| Property tests | If applicable (e.g., roundtrip) |

---

## Versioning

| Rule | Description |
|------|-------------|
| Semantic versioning | MAJOR.MINOR.PATCH |
| Breaking changes | MAJOR bump, migration guide |
| New functions | MINOR bump |
| Bug fixes | PATCH bump |

---

## Notes

(Any additional context, assumptions, or clarifications)

---
Source: PRE-SPEC-NOTES.md sections 1, 2
Phase: 23 (Library Specs)
Generated: {{timestamp}}
---
