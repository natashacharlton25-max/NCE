# {{Library Name}} — Spec Index

---
Component: lib-{{name}}
Type: Library
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
Last Updated: {{timestamp}}
---

## Purpose

(One sentence — what utility this library provides)

---

## Quick Reference

### Functions

| Function | Purpose | File |
|----------|---------|------|
| `{{function}}()` | {{purpose}} | FUNCTIONS.md#{{anchor}} |
| `{{function}}()` | {{purpose}} | FUNCTIONS.md#{{anchor}} |
| `{{function}}()` | {{purpose}} | FUNCTIONS.md#{{anchor}} |
| `{{function}}()` | {{purpose}} | FUNCTIONS.md#{{anchor}} |

### Types

| Type | Purpose | File |
|------|---------|------|
| {{Type}} | {{purpose}} | TYPES.md#{{anchor}} |
| {{Type}} | {{purpose}} | TYPES.md#{{anchor}} |

### Errors

| Code | Meaning | File |
|------|---------|------|
| `{{PREFIX}}_XXX` | {{meaning}} | ERRORS.md#{{anchor}} |

---

## Spec Files

| File | Description | Status | LOC | Updated |
|------|-------------|--------|-----|---------|
| INDEX.md | This file | ✅ | ~{{n}} | {{date}} |
| OVERVIEW.md | Purpose, scope, principles | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| FUNCTIONS.md | All functions (primary spec) | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| TYPES.md | Data structures | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| ERRORS.md | Error codes | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |
| EXAMPLES.md | Usage examples | ✅ / 🔄 / ❌ | ~{{n}} | {{date}} |

**Legend:** ✅ Complete | 🔄 In Progress | ❌ Not Started

---

## Consumers

### Systems That Use This Library

| System/Subsystem | Functions Used |
|------------------|----------------|
| {{system}} | `{{function}}`, `{{function}}` |
| {{system}} | `{{function}}` |
| {{subsystem}} | `{{function}}`, `{{function}}` |

### Usage Count

| Function | # of Callers |
|----------|--------------|
| `{{function}}()` | {{n}} |
| `{{function}}()` | {{n}} |

---

## Dependencies

### This Library Depends On

| Dependency | Type | Why |
|------------|------|-----|
| {{lib-other}} | Library | {{reason}} |
| _None_ | | |

**Note:** Libraries should have MINIMAL dependencies.

### This Library Is Used By

| Consumer | Type |
|----------|------|
| {{system}} | System |
| {{subsystem}} | Subsystem |
| {{lib-other}} | Library |

---

## Build Notes & Constraints

### Implementation Constraints

| Constraint | Reason |
|------------|--------|
| No side effects | Must be pure functions |
| No external calls | Utilities only |
| {{constraint}} | {{reason}} |

### Questions for Human Review

| Question | Context | Decision |
|----------|---------|----------|
| {{question}} | {{context}} | PENDING / {{answer}} |
| _None_ | | |

---

## Related Documentation

| Document | Location | Purpose |
|----------|----------|---------|
| PRE-SPEC-NOTES.md | `../PRE-SPEC-NOTES.md` | Pre-spec thinking |
| Pattern Analysis | Phase 25 output | Why this was extracted |

---

## Change Log

| Version | Date | Change | Author |
|---------|------|--------|--------|
| v0.1.0 | {{date}} | Initial creation | Claude |

---
Phase: 23 (Library Specs)
Generated: {{timestamp}}
---
