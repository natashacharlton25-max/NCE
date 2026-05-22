# Type Analysis

---
Phase: 25 (Post-Spec Analysis)
Analysis: 2 of 5 — Types & Schemas
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Analyze all type definitions across the project to identify duplicates, inconsistencies, and candidates for shared types.

---

## Scope

### Files Analyzed

| Component | File | Status |
|-----------|------|--------|
| {{system-a}} | `spec/TYPES.md` | ✅ Analyzed |
| {{system-b}} | `spec/TYPES.md` | ✅ Analyzed |
| {{integration}} | `spec/FIELD-MAPPING.md` | ✅ Analyzed |
| {{library}} | `spec/TYPES.md` | ✅ Analyzed |

**Total files analyzed:** {{n}}

---

## Type Registry

### All Types Defined

| Type | Component | Category | Description |
|------|-----------|----------|-------------|
| `{{TypeName}}` | {{component}} | Entity | {{description}} |
| `{{TypeName}}Input` | {{component}} | Input | {{description}} |
| `{{TypeName}}Result` | {{component}} | Output | {{description}} |
| `{{TypeName}}` | {{component}} | Enum | {{description}} |

**Total types:** {{n}}

### By Category

| Category | Count | Examples |
|----------|-------|----------|
| Entities | {{n}} | User, Project, Task |
| Input DTOs | {{n}} | CreateUserInput, UpdateTaskInput |
| Output DTOs | {{n}} | UserResult, ListResult |
| Enums | {{n}} | Status, Role, Priority |
| Utility | {{n}} | Pagination, ErrorDetail |

### By Component

| Component | Types Defined | Shared | Local |
|-----------|---------------|--------|-------|
| {{system-a}} | {{n}} | {{n}} | {{n}} |
| {{system-b}} | {{n}} | {{n}} | {{n}} |
| {{library}} | {{n}} | {{n}} | {{n}} |

---

## Findings

### Finding: Duplicate Type Definition

**Severity:** High
**Location:** 
- `systems/user/spec/TYPES.md` — `Pagination`
- `systems/project/spec/TYPES.md` — `Pagination`
- `systems/task/spec/TYPES.md` — `PaginationParams`

**Issue:** Same pagination type defined 3 times with slightly different names/shapes.

**Impact:** Inconsistent pagination handling, code duplication.

**Recommendation:** 
- Extract to `src/shared/types/pagination.ts`
- Document in Phase 26 SCHEMAS.md
- Update all components to use shared type

---

### Finding: Inconsistent Naming

**Severity:** Medium
**Location:** 
- `systems/user/spec/TYPES.md` — `UserStatus`
- `systems/project/spec/TYPES.md` — `ProjectState`
- `systems/task/spec/TYPES.md` — `TaskStatus`

**Issue:** Mix of "Status" and "State" for similar concepts.

**Impact:** Confusing API, harder to learn.

**Recommendation:** Standardize on "Status" for all state enums.

---

### Finding: Missing Type Definition

**Severity:** High
**Location:** `systems/{{system}}/spec/FUNCTIONS.md`

**Issue:** Function `doSomething()` returns `SomeType` but `SomeType` not defined in TYPES.md.

**Impact:** Implementation will lack type definition.

**Recommendation:** Add `SomeType` definition to TYPES.md.

---

### Finding: Type Mismatch Across Components

**Severity:** Critical
**Location:** 
- `systems/user/spec/TYPES.md` — `UserId: string`
- `systems/auth/spec/TYPES.md` — `UserId: number`

**Issue:** Same type name with different underlying types.

**Impact:** Runtime errors when systems communicate.

**Recommendation:** 
- Align on single type (recommend `string`)
- Extract to shared types

---

### Finding: {{Next Finding}}

(Repeat for each finding)

---

## Duplicate Analysis

### Exact Duplicates

| Type | Locations | Action |
|------|-----------|--------|
| `Pagination` | user, project, task | Extract to shared |
| `ErrorDetail` | all systems | Extract to shared |
| `Timestamp` | all systems | Extract to shared |

### Similar Types (Candidates for Consolidation)

| Types | Similarity | Recommendation |
|-------|------------|----------------|
| `UserStatus`, `ProjectStatus`, `TaskStatus` | 90% same values | Create generic `EntityStatus` |
| `CreateUserInput`, `CreateProjectInput` | Same structure | Keep separate (different domains) |

---

## Shared Type Candidates

### Recommended for Extraction

| Type | Used By | Extract To |
|------|---------|------------|
| `Pagination` | 5 components | `src/shared/types/pagination.ts` |
| `ErrorDetail` | All components | `src/shared/types/errors.ts` |
| `Timestamp` | All components | `src/shared/types/common.ts` |
| `Result<T>` | All components | `src/shared/types/result.ts` |
| `ListResult<T>` | 4 components | `src/shared/types/list.ts` |

### Should Remain Local

| Type | Component | Reason |
|------|-----------|--------|
| `User` | user-system | Domain-specific entity |
| `AuthToken` | auth-system | Security isolation |

---

## Naming Convention Check

### Type Naming

| Convention | Expected | Violations |
|------------|----------|------------|
| Entities | `PascalCase` noun | {{list or "None"}} |
| Input DTOs | `{{Entity}}Input` or `{{Entity}}CreateInput` | {{list or "None"}} |
| Output DTOs | `{{Entity}}Result` or `{{Entity}}Response` | {{list or "None"}} |
| Enums | `PascalCase` noun | {{list or "None"}} |
| Type aliases | `PascalCase` | {{list or "None"}} |

### Field Naming

| Convention | Expected | Violations |
|------------|----------|------------|
| Fields | `camelCase` | {{list or "None"}} |
| ID fields | `id` or `{{entity}}Id` | {{list or "None"}} |
| Timestamps | `createdAt`, `updatedAt` | {{list or "None"}} |
| Booleans | `is{{Something}}`, `has{{Something}}` | {{list or "None"}} |

---

## Cross-Reference Check

### Types Referenced But Not Defined

| Type | Referenced In | Should Be Defined In |
|------|---------------|----------------------|
| `{{Type}}` | {{file}} | {{location}} |
| _None found_ | | |

### Types Defined But Not Used

| Type | Defined In | Action |
|------|------------|--------|
| `{{Type}}` | {{file}} | Remove or document why |
| _None found_ | | |

---

## Type Compatibility Matrix

### Entity ID Types

| Entity | ID Type | Compatible |
|--------|---------|------------|
| User | string | ✅ |
| Project | string | ✅ |
| Task | string | ✅ |
| {{Entity}} | {{type}} | ✅ / ❌ |

### Timestamp Types

| Component | Format | Compatible |
|-----------|--------|------------|
| {{all}} | ISO 8601 string | ✅ |

---

## Summary

### Statistics

| Metric | Value |
|--------|-------|
| Total types defined | {{n}} |
| Duplicate types | {{n}} |
| Candidates for shared | {{n}} |
| Naming violations | {{n}} |
| Missing definitions | {{n}} |
| Type mismatches | {{n}} |

### Issues by Severity

| Severity | Count | Action |
|----------|-------|--------|
| Critical | {{n}} | Fix immediately |
| High | {{n}} | Fix before implementation |
| Medium | {{n}} | Fix during implementation |
| Low | {{n}} | Track for later |

---

## Action Items

| Priority | Action | Effort | Status |
|----------|--------|--------|--------|
| 1 | Fix `UserId` type mismatch | 1 hr | TODO |
| 2 | Extract `Pagination` to shared | 30 min | TODO |
| 3 | Extract `ErrorDetail` to shared | 30 min | TODO |
| 4 | Standardize "Status" naming | 1 hr | TODO |
| 5 | Add missing type definitions | 2 hr | TODO |

---

## Input for Phase 26

The following types should be documented in SCHEMAS.md (Phase 26):

| Type | Category | Priority |
|------|----------|----------|
| `Pagination` | Utility | High |
| `ErrorDetail` | Utility | High |
| `Result<T>` | Utility | High |
| `ListResult<T>` | Utility | High |
| `Timestamp` | Primitive | Medium |
| `EntityId` | Primitive | Medium |

---

## Sign-off

- [ ] All TYPES.md files analyzed
- [ ] All FIELD-MAPPING.md files analyzed
- [ ] Duplicates identified
- [ ] Shared candidates listed
- [ ] Cross-references validated
- [ ] Human reviewed

**Analyst:** Claude
**Date:** {{timestamp}}

---
Phase: 25 (Post-Spec Analysis)
Generated: {{timestamp}}
---
