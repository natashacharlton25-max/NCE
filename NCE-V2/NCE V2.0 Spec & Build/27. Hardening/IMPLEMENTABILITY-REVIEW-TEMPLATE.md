# Implementability Review

---
Phase: 27 (Hardening)
Review: 3 of 4 — Implementability
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Ensure specs can be directly implemented by Claude Code without requiring additional clarification or design decisions.

---

## Summary

| Metric | Count |
|--------|-------|
| Components reviewed | {{n}} |
| Ready to implement | {{n}} |
| Blocking issues | {{n}} |
| Warnings | {{n}} |

**Implementability Score:** {{ready}}/{{total}} = {{%}}

---

## Implementation Readiness Criteria

| Criterion | Description |
|-----------|-------------|
| **Data flows clear** | Input → Process → Output fully specified |
| **Dependencies exist** | All referenced components have specs |
| **Interfaces match** | Caller and callee agree on signatures |
| **Types consistent** | Same type = same structure everywhere |
| **Errors mapped** | All error conditions have codes |
| **Config complete** | All settings have defaults |
| **Storage aligned** | Schema matches types |

---

## Blocking Issues

Issues that MUST be resolved before implementation.

### IMP-001: {{Title}}

**Severity:** BLOCKING
**Component:** {{component}}
**Location:** `{{file}}`

**Issue:**
{{Description of what prevents implementation}}

**Impact:**
{{What cannot be built without resolving this}}

**Resolution Options:**
1. {{Option A}}
2. {{Option B}}

**Recommended:** {{which option}}

**Status:** OPEN / RESOLVED

---

### IMP-002: Missing Dependency Specification

**Severity:** BLOCKING
**Component:** billing-subsystem
**Location:** `OVERVIEW.md` → Dependencies

**Issue:**
billing-subsystem depends on `payment-system` which has no specification.

**Impact:**
Cannot implement billing without knowing payment interface.

**Resolution Options:**
1. Create payment-system specs (8 hrs)
2. Use int-stripe directly, remove payment-system abstraction (2 hrs)
3. Stub payment-system interface, spec later (1 hr)

**Recommended:** Option 2 — direct integration is simpler for MVP

**Status:** OPEN

---

### IMP-003: Circular Dependency

**Severity:** BLOCKING
**Component:** user-system, auth-system
**Location:** Both OVERVIEW.md

**Issue:**
user-system depends on auth-system for permission checks.
auth-system depends on user-system for user lookup.
Circular dependency prevents clean initialization.

**Impact:**
Cannot determine module load order. Testing requires complex mocking.

**Resolution Options:**
1. Extract shared interface to lib-identity
2. Merge into single user-auth-system
3. Use dependency injection with lazy loading

**Recommended:** Option 1 — maintains separation, breaks cycle

**Status:** OPEN

---

## Warnings

Issues that should be addressed but don't block implementation.

### WARN-001: {{Title}}

**Severity:** WARNING
**Component:** {{component}}
**Location:** `{{file}}`

**Issue:**
{{Description}}

**Impact:**
{{What might go wrong}}

**Recommendation:**
{{How to address}}

**Status:** NOTED / ADDRESSED

---

### WARN-002: Inconsistent Function Signatures

**Severity:** WARNING
**Component:** Multiple
**Location:** FUNCTIONS.md files

**Issue:**
`listUsers()` returns `ListResult<User>`
`listProjects()` returns `{ items: Project[], total: number }`

**Impact:**
Inconsistent API, harder to write generic code.

**Recommendation:**
Standardize all list functions to return `ListResult<T>`.

**Status:** NOTED

---

### WARN-003: Missing Retry Configuration

**Severity:** WARNING
**Component:** All integrations
**Location:** CONFIG.md

**Issue:**
Retry counts mentioned in BEHAVIOUR.md but not configurable in CONFIG.md.

**Impact:**
Cannot tune retry behavior without code changes.

**Recommendation:**
Add `retry.maxAttempts`, `retry.backoffMs` to integration configs.

**Status:** NOTED

---

## Component-by-Component Review

### {{System Name}}

**Status:** READY / BLOCKED / WARNINGS

| Check | Status | Notes |
|-------|--------|-------|
| All dependencies exist | ✅ / ❌ | |
| Function signatures clear | ✅ / ❌ | |
| Types fully defined | ✅ / ❌ | |
| Error codes assigned | ✅ / ❌ | |
| Config has defaults | ✅ / ❌ | |
| Storage schema matches types | ✅ / ❌ / N/A | |
| Examples are runnable | ✅ / ❌ | |

**Blocking Issues:** None / IMP-001, IMP-002
**Warnings:** None / WARN-001

---

### {{Subsystem Name}}

**Status:** READY / BLOCKED / WARNINGS

| Check | Status | Notes |
|-------|--------|-------|
| All dependencies exist | ✅ / ❌ | |
| Function signatures clear | ✅ / ❌ | |
| Types fully defined | ✅ / ❌ | |
| Error codes assigned | ✅ / ❌ | |
| Config has defaults | ✅ / ❌ | |
| Storage schema matches types | ✅ / ❌ / N/A | |
| Examples are runnable | ✅ / ❌ | |

---

### {{Integration Name}}

**Status:** READY / BLOCKED / WARNINGS

| Check | Status | Notes |
|-------|--------|-------|
| Provider API documented | ✅ / ❌ | |
| Authentication clear | ✅ / ❌ | |
| Rate limits documented | ✅ / ❌ | |
| Error mapping complete | ✅ / ❌ | |
| Field mapping complete | ✅ / ❌ | |
| Failure modes documented | ✅ / ❌ | |

---

## Cross-Component Checks

### Interface Compatibility

| Caller | Callee | Interface | Compatible |
|--------|--------|-----------|------------|
| project-system | user-system | getUser(id) | ✅ / ❌ |
| billing | int-stripe | createCharge() | ✅ / ❌ |
| {{caller}} | {{callee}} | {{interface}} | ✅ / ❌ |

**Incompatibilities Found:**
- {{description}}

---

### Type Consistency

| Type | Defined In | Used In | Consistent |
|------|------------|---------|------------|
| User | user-system | auth, project, billing | ✅ / ❌ |
| UserId | SCHEMAS.md | All | ✅ / ❌ |
| {{type}} | {{source}} | {{users}} | ✅ / ❌ |

**Inconsistencies Found:**
- {{description}}

---

### Error Code Uniqueness

| Code | Components Using | Conflict |
|------|------------------|----------|
| USR_101 | user-system only | None |
| {{code}} | {{components}} | {{conflict or None}} |

**Conflicts Found:**
- {{description}}

---

## Dependency Graph Validation

```
✅ user-system
   └── ✅ lib-validation
   └── ✅ lib-ids

✅ auth-system
   └── ❌ user-system (CIRCULAR)
   └── ✅ lib-jwt

❌ billing-subsystem
   └── ❌ payment-system (MISSING)
   └── ✅ int-stripe
```

**Issues:**
- Circular: user-system ↔ auth-system
- Missing: payment-system

---

## Implementation Order

Based on dependencies, implement in this order:

```
Phase 1: Libraries (no dependencies)
├── lib-validation
├── lib-ids
├── lib-errors
└── lib-datetime

Phase 2: Integrations (depend on libraries)
├── int-stripe
├── int-email
└── int-storage

Phase 3: Core Systems
├── user-system (after lib-*)
└── auth-system (after user-system resolved)

Phase 4: Business Systems
├── project-system (after user, auth)
└── billing-subsystem (after int-stripe)

Phase 5: Subsystems
└── (per parent system)
```

---

## Action Items

### Blocking (Must Fix)

| ID | Action | Component | Effort | Owner |
|----|--------|-----------|--------|-------|
| IMP-002 | Resolve payment-system | billing | 2 hr | |
| IMP-003 | Break circular dependency | user/auth | 4 hr | |

### Warnings (Should Fix)

| ID | Action | Component | Effort | Owner |
|----|--------|-----------|--------|-------|
| WARN-002 | Standardize list returns | Multiple | 2 hr | |
| WARN-003 | Add retry config | Integrations | 1 hr | |

---

## Sign-off

- [ ] All components reviewed for implementability
- [ ] All dependencies validated
- [ ] All interfaces checked for compatibility
- [ ] All blocking issues documented
- [ ] Implementation order determined

**Reviewer:** Claude
**Date:** {{timestamp}}

---
Phase: 27 (Hardening)
Generated: {{timestamp}}
---
