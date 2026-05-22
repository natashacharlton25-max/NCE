# Dependency Analysis

---
Phase: 25 (Post-Spec Analysis)
Analysis: 3 of 5 — Dependencies
Date: {{timestamp}}
Status: DRAFT | COMPLETE
---

## Purpose

Analyze all dependencies across the project to identify circular dependencies, missing dependencies, and validate dependency direction rules.

---

## Scope

### Files Analyzed

| Component | Files Checked |
|-----------|---------------|
| {{system}} | OVERVIEW.md, INDEX.md |
| {{subsystem}} | OVERVIEW.md, INDEX.md |
| {{integration}} | OVERVIEW.md, INDEX.md |
| {{library}} | OVERVIEW.md, INDEX.md |

**Total components analyzed:** {{n}}

---

## Dependency Graph

### Visual Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        SYSTEMS                               │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐      ┌──────────┐      ┌──────────┐          │
│  │  User    │─────▶│   Auth   │      │ Project  │          │
│  │  System  │      │  System  │◀─────│  System  │          │
│  └────┬─────┘      └────┬─────┘      └────┬─────┘          │
│       │                 │                  │                 │
│       │                 │                  │                 │
│       ▼                 ▼                  ▼                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                    LIBRARIES                         │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐          │   │
│  │  │lib-valid │  │lib-errors│  │lib-ids   │          │   │
│  │  └──────────┘  └──────────┘  └──────────┘          │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
│       │                                                      │
│       ▼                                                      │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                  INTEGRATIONS                        │   │
│  │  ┌──────────┐  ┌──────────┐                         │   │
│  │  │  Stripe  │  │  Email   │                         │   │
│  │  └──────────┘  └──────────┘                         │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Dependency Matrix

| Component | Depends On | Depended On By |
|-----------|------------|----------------|
| user-system | lib-validation, lib-ids | auth-system, project-system |
| auth-system | user-system, lib-errors | project-system |
| project-system | auth-system, user-system | — |
| lib-validation | — | user-system, project-system |
| lib-errors | — | all systems |
| int-stripe | lib-errors | billing-subsystem |

---

## Dependency Rules Validation

### Allowed Dependencies

| From | To | Rule | Status |
|------|----|------|--------|
| System | System | ✅ Allowed | {{n}} found |
| System | Subsystem (own) | ✅ Allowed | {{n}} found |
| System | Library | ✅ Allowed | {{n}} found |
| System | Integration | ✅ Allowed | {{n}} found |
| Subsystem | Library | ✅ Allowed | {{n}} found |
| Subsystem | Sibling Subsystem | ✅ Allowed | {{n}} found |
| Library | Library | ✅ Allowed | {{n}} found |

### Forbidden Dependencies

| From | To | Rule | Violations |
|------|----|------|------------|
| Library | System | ❌ Forbidden | {{n}} found |
| Library | Subsystem | ❌ Forbidden | {{n}} found |
| Library | Integration | ❌ Forbidden | {{n}} found |
| Integration | System | ❌ Forbidden | {{n}} found |
| Subsystem | Other System | ❌ Forbidden | {{n}} found |

---

## Findings

### Finding: Circular Dependency Detected

**Severity:** Critical
**Location:** 
- `systems/user/spec/OVERVIEW.md` — depends on auth-system
- `systems/auth/spec/OVERVIEW.md` — depends on user-system

**Issue:** Circular dependency between user-system and auth-system.

**Impact:** 
- Cannot determine initialization order
- Tight coupling makes changes risky
- Testing becomes difficult

**Recommendation:** 
- Extract shared interface to library
- Or use dependency injection
- Or merge systems if truly inseparable

---

### Finding: Library Depends on System

**Severity:** Critical
**Location:** `lib/lib-utils/spec/OVERVIEW.md`

**Issue:** `lib-utils` imports from `user-system`.

**Impact:** Violates dependency direction rules. Libraries must be independent.

**Recommendation:** 
- Move system-specific code out of library
- Or extract needed types to shared

---

### Finding: Missing Dependency Declaration

**Severity:** High
**Location:** `systems/project/spec/FUNCTIONS.md`

**Issue:** `createProject()` calls `validateUser()` from user-system, but user-system not listed in OVERVIEW.md dependencies.

**Impact:** Undocumented coupling, may break unexpectedly.

**Recommendation:** Add user-system to dependencies in OVERVIEW.md.

---

### Finding: Dependency on Non-Existent Component

**Severity:** Critical
**Location:** `systems/billing/spec/OVERVIEW.md`

**Issue:** Lists dependency on `payment-system` which doesn't exist.

**Impact:** Implementation will fail.

**Recommendation:** 
- Create payment-system specs
- Or correct to existing component (int-stripe?)

---

### Finding: Deep Dependency Chain

**Severity:** Medium
**Location:** Multiple

**Issue:** Dependency chain 5 levels deep: A → B → C → D → E

**Impact:** 
- Hard to reason about
- Changes ripple far
- Testing requires many mocks

**Recommendation:** Consider flattening or introducing facade.

---

### Finding: {{Next Finding}}

(Repeat for each finding)

---

## Circular Dependency Check

### Cycles Found

| Cycle | Components | Severity |
|-------|------------|----------|
| 1 | user → auth → user | Critical |
| 2 | {{cycle}} | {{severity}} |
| _None_ | | |

### Resolution Strategies

| Cycle | Strategy | Effort |
|-------|----------|--------|
| user ↔ auth | Extract `user-identity` interface | 4 hr |
| {{cycle}} | {{strategy}} | {{effort}} |

---

## Dependency Depth Analysis

### Deepest Chains

| Start | Chain | Depth |
|-------|-------|-------|
| {{component}} | A → B → C → D | 4 |
| {{component}} | {{chain}} | {{n}} |

### Recommended Maximum Depth: 3

| Depth | Count | Action |
|-------|-------|--------|
| 1 | {{n}} | ✅ Good |
| 2 | {{n}} | ✅ Good |
| 3 | {{n}} | ⚠️ Review |
| 4+ | {{n}} | ❌ Refactor |

---

## Fan-In / Fan-Out Analysis

### High Fan-In (Many Dependents)

| Component | Dependents | Risk |
|-----------|------------|------|
| lib-errors | 12 | Changes affect many |
| lib-validation | 8 | Changes affect many |
| user-system | 5 | Changes affect many |

**Action:** These components need extra stability. Consider versioning.

### High Fan-Out (Many Dependencies)

| Component | Dependencies | Risk |
|-----------|--------------|------|
| project-system | 7 | Fragile, many failure points |
| {{component}} | {{n}} | {{risk}} |

**Action:** Consider if all dependencies are necessary.

---

## External Dependencies

### Third-Party Libraries

| Dependency | Used By | Version | Risk |
|------------|---------|---------|------|
| {{library}} | {{components}} | {{version}} | Low/Med/High |

### External Services (via Integrations)

| Service | Integration | Systems Using |
|---------|-------------|---------------|
| Stripe | int-stripe | billing-subsystem |
| {{service}} | {{integration}} | {{systems}} |

---

## Initialization Order

Based on dependency analysis, components should initialize in this order:

```
1. Libraries (no dependencies)
   - lib-errors
   - lib-validation
   - lib-ids

2. Integrations (depend only on libraries)
   - int-stripe
   - int-email

3. Systems (depend on libraries, integrations, other systems)
   - user-system (no system dependencies)
   - auth-system (depends on user-system)
   - project-system (depends on user, auth)
```

---

## Summary

### Statistics

| Metric | Value |
|--------|-------|
| Total components | {{n}} |
| Total dependencies | {{n}} |
| Circular dependencies | {{n}} |
| Rule violations | {{n}} |
| Missing declarations | {{n}} |
| Max dependency depth | {{n}} |

### Issues by Severity

| Severity | Count | Action |
|----------|-------|--------|
| Critical | {{n}} | Fix immediately |
| High | {{n}} | Fix before implementation |
| Medium | {{n}} | Address during implementation |
| Low | {{n}} | Track |

---

## Action Items

| Priority | Action | Effort | Status |
|----------|--------|--------|--------|
| 1 | Break user ↔ auth cycle | 4 hr | TODO |
| 2 | Remove lib-utils → user-system | 2 hr | TODO |
| 3 | Add missing dependency declarations | 1 hr | TODO |
| 4 | Create missing payment-system | 8 hr | TODO |

---

## Sign-off

- [ ] All components analyzed
- [ ] Dependency graph complete
- [ ] Circular dependencies identified
- [ ] Rule violations documented
- [ ] Initialization order determined
- [ ] Human reviewed

**Analyst:** Claude
**Date:** {{timestamp}}

---
Phase: 25 (Post-Spec Analysis)
Generated: {{timestamp}}
---
