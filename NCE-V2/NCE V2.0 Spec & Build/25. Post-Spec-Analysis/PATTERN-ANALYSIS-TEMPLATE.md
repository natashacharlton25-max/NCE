# Pattern Analysis

---
Phase: 25 (Post-Spec Analysis)
Analysis: 4 of 5 — Patterns & Duplication
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Identify repeated patterns across specs that should be extracted into shared libraries, utilities, or documented as project conventions.

---

## Scope

### Files Analyzed

| File Type | Count | Components |
|-----------|-------|------------|
| FUNCTIONS.md | {{n}} | All systems, subsystems, libraries |
| BEHAVIOUR.md | {{n}} | All systems, subsystems |
| ERRORS.md | {{n}} | All components |
| TYPES.md | {{n}} | All components |

**Total files analyzed:** {{n}}

---

## Pattern Categories

### 1. Function Patterns
Repeated function signatures and behaviours

### 2. Data Patterns
Repeated data structures and transformations

### 3. Error Handling Patterns
Repeated error handling approaches

### 4. Validation Patterns
Repeated validation logic

### 5. Workflow Patterns
Repeated process flows

---

## Function Patterns Found

### Pattern: CRUD Operations

**Occurrences:** {{n}} components

| Component | Functions |
|-----------|-----------|
| user-system | create, get, list, update, delete |
| project-system | create, get, list, update, delete |
| task-system | create, get, list, update, delete |

**Similarity:** 95% — same signatures, same patterns

**Recommendation:** 
- Document as standard CRUD pattern
- Consider generic CRUD base/mixin
- Ensure consistent naming

---

### Pattern: List with Pagination

**Occurrences:** {{n}} components

**Signature Pattern:**
```typescript
list(options?: {
  filter?: FilterParams;
  sort?: SortParams;
  pagination?: PaginationParams;
}): Promise<ListResult<T>>
```

**Found In:**
- user-system: `listUsers()`
- project-system: `listProjects()`
- task-system: `listTasks()`

**Variations:**
| Component | Filter | Sort | Pagination | Consistent |
|-----------|--------|------|------------|------------|
| user | ✅ | ✅ | ✅ | ✅ |
| project | ✅ | ✅ | ✅ | ✅ |
| task | ✅ | ❌ | ✅ | ❌ Missing sort |

**Recommendation:**
- Extract `ListOptions<T>` to shared types
- Standardize all list functions
- Add missing sort to task-system

---

### Pattern: Initialize / Health Check

**Occurrences:** {{n}} components

**Found In:** All systems and integrations

**Variations:**
| Component | initialize() | checkHealth() | Consistent |
|-----------|--------------|---------------|------------|
| user-system | ✅ | ✅ | ✅ |
| auth-system | ✅ | ❌ | ❌ Missing health |
| int-stripe | ✅ | ✅ | ✅ |

**Recommendation:**
- Document as required pattern
- Add checkHealth() to auth-system

---

### Pattern: {{Pattern Name}}

**Occurrences:** {{n}}

**Found In:** {{components}}

**Recommendation:** {{recommendation}}

---

## Data Patterns Found

### Pattern: Entity with Timestamps

**Occurrences:** All entities

**Structure:**
```typescript
{
  id: string;
  // ... entity fields
  createdAt: string;  // ISO 8601
  updatedAt: string;  // ISO 8601
}
```

**Variations:**
| Entity | id type | createdAt | updatedAt | deletedAt |
|--------|---------|-----------|-----------|-----------|
| User | string | ✅ | ✅ | ❌ |
| Project | string | ✅ | ✅ | ✅ |
| Task | string | ✅ | ✅ | ✅ |

**Recommendation:**
- Extract `Timestamped` interface
- Standardize soft delete (deletedAt) policy

---

### Pattern: Result Wrapper

**Occurrences:** {{n}} components

**Structure:**
```typescript
{
  success: boolean;
  data?: T;
  error?: ErrorDetail;
}
```

**Found In:** All components

**Consistency:** 100%

**Recommendation:** 
- Already consistent ✅
- Document in SCHEMAS.md

---

### Pattern: {{Pattern Name}}

(Repeat for each pattern)

---

## Error Handling Patterns Found

### Pattern: Retry with Backoff

**Occurrences:** {{n}} components

**Found In:**
- All integrations
- Systems with I/O

**Implementation Variations:**
| Component | Max Retries | Backoff Type | Consistent |
|-----------|-------------|--------------|------------|
| int-stripe | 3 | Exponential | ✅ |
| int-email | 3 | Exponential | ✅ |
| user-system | 3 | Fixed | ❌ Different |

**Recommendation:**
- Extract to `lib-retry`
- Standardize on exponential backoff

---

### Pattern: Error Transformation

**Occurrences:** All integrations

**Pattern:**
```
Provider Error → Validate → Map to Our Error → Return
```

**Consistency:** Good, but mapping logic duplicated

**Recommendation:**
- Extract error mapping utilities to lib-errors
- Standardize mapping approach

---

## Validation Patterns Found

### Pattern: Required Field Check

**Occurrences:** {{n}} functions across {{n}} components

**Pattern:**
```typescript
if (!input.field) {
  return error('REQUIRED_FIELD', 'field is required');
}
```

**Recommendation:**
- Extract to `lib-validation`
- Use: `validate.required(input, 'field')`

---

### Pattern: String Length Validation

**Occurrences:** {{n}}

**Variations:**
| Component | Min Check | Max Check | Consistent |
|-----------|-----------|-----------|------------|
| user | ✅ | ✅ | ✅ |
| project | ❌ | ✅ | ❌ Missing min |

**Recommendation:**
- Extract to `lib-validation`
- Use: `validate.length(value, { min: 1, max: 100 })`

---

### Pattern: Email Validation

**Occurrences:** {{n}}

**Found In:** user-system, auth-system, notification-system

**Recommendation:**
- Extract to `lib-validation`
- Single source of truth for email regex

---

## Workflow Patterns Found

### Pattern: Create with Validation

**Occurrences:** All create operations

**Flow:**
```
1. Validate input
2. Check permissions
3. Check uniqueness
4. Create entity
5. Emit event
6. Return result
```

**Consistency:** 90% — some skip permission check

**Recommendation:** Document as standard create workflow

---

### Pattern: Soft Delete

**Occurrences:** {{n}} entities

**Flow:**
```
1. Check exists
2. Check permissions
3. Check no dependents
4. Set deletedAt
5. Emit event
6. Return result
```

**Variations:**
| Entity | Soft Delete | Hard Delete Option |
|--------|-------------|-------------------|
| User | ✅ | ❌ |
| Project | ✅ | ✅ |
| Task | ✅ | ✅ |

**Recommendation:** Standardize delete policy across entities

---

## Library Extraction Candidates

### High Priority (3+ occurrences, significant code)

| Library | Functions | Used By | Effort | Priority |
|---------|-----------|---------|--------|----------|
| lib-validation | validate.required, validate.length, validate.email, validate.enum | All | 4 hr | High |
| lib-retry | retry, withBackoff, withTimeout | Integrations, I/O systems | 3 hr | High |
| lib-pagination | paginate, cursor, offset | All list operations | 2 hr | High |

### Medium Priority (2+ occurrences)

| Library | Functions | Used By | Effort | Priority |
|---------|-----------|---------|--------|----------|
| lib-datetime | format, parse, diff, add | Multiple | 2 hr | Medium |
| lib-strings | truncate, sanitize, slugify | Multiple | 1 hr | Medium |

### Low Priority (Nice to have)

| Library | Functions | Used By | Effort | Priority |
|---------|-----------|---------|--------|----------|
| lib-collections | groupBy, keyBy, unique | Few places | 1 hr | Low |

---

## Anti-Patterns Found

### Anti-Pattern: God Function

**Location:** `systems/project/spec/FUNCTIONS.md` — `processProject()`

**Issue:** Single function does validation, creation, notification, and audit logging.

**Recommendation:** Split into focused functions.

---

### Anti-Pattern: Inconsistent Error Handling

**Location:** Multiple

**Issue:** Some functions throw, some return Result, some return null.

**Recommendation:** Standardize on Result pattern everywhere.

---

### Anti-Pattern: {{Pattern Name}}

(Repeat for each anti-pattern)

---

## Summary

### Patterns Found

| Category | Patterns | Consistent | Action Needed |
|----------|----------|------------|---------------|
| Function | {{n}} | {{%}} | {{n}} |
| Data | {{n}} | {{%}} | {{n}} |
| Error Handling | {{n}} | {{%}} | {{n}} |
| Validation | {{n}} | {{%}} | {{n}} |
| Workflow | {{n}} | {{%}} | {{n}} |

### Library Candidates

| Priority | Libraries | Total Effort |
|----------|-----------|--------------|
| High | 3 | 9 hr |
| Medium | 2 | 3 hr |
| Low | 1 | 1 hr |

---

## Action Items

| Priority | Action | Type | Effort | Status |
|----------|--------|------|--------|--------|
| 1 | Create lib-validation | New library | 4 hr | TODO |
| 2 | Create lib-retry | New library | 3 hr | TODO |
| 3 | Standardize list pagination | Update specs | 2 hr | TODO |
| 4 | Add missing checkHealth() | Update specs | 1 hr | TODO |
| 5 | Document standard patterns | Phase 26 | 2 hr | TODO |

---

## Sign-off

- [ ] All function patterns identified
- [ ] All data patterns identified
- [ ] Library candidates listed
- [ ] Anti-patterns documented
- [ ] Consistency assessed
- [ ] Human reviewed

**Analyst:** Claude
**Date:** {{timestamp}}

---
Phase: 25 (Post-Spec Analysis)
Generated: {{timestamp}}
---
