# PHASE 31: LIBRARY STRUCTURE

---
Phase: 31
Name: Library Structure
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are defining the folder structure and organization for shared libraries identified in Phase 23 (Library Specs).

**Critical Purpose:** Prevent code duplication BEFORE implementation begins.

If no libraries were identified in Phase 23, this phase is **N/A**.

---

## DEDUPLICATION CHECK

Before proceeding, scan all component specs for potential shared code:

| Pattern | Check For | If Found |
|---------|-----------|----------|
| Logging | Multiple systems needing logging | → lib-logger |
| Validation | Shared validation rules | → lib-validation |
| Auth wrappers | Auth checks in multiple places | → lib-auth |
| API clients | Calls to same external APIs | → lib-api-client |
| Date/time utils | Date formatting/parsing | → lib-datetime |
| Error handling | Common error patterns | → lib-errors |

**Ask:** "Are there any shared utilities that multiple systems need that weren't identified as libraries in Phase 23?"

If yes → Document as addition, get approval, then proceed.

---

## TASK

1. Check if Phase 23 identified any libraries
2. If no libraries → Mark phase as N/A, proceed to Phase 32
3. If libraries exist → Define folder structure for each
4. Define dependency order
5. Get approval

---

## STEP 1: Check for Libraries

Look for `lib-{{library}}/spec/` folders from Phase 23.

If none exist:
- Mark LIBRARY-STRUCTURE.md as "N/A — No libraries identified"
- Proceed to Phase 32

---

## STEP 2: List Libraries

For each library from Phase 23:

| Library | Purpose | Dependencies |
|---------|---------|--------------|
| `lib-{{name}}` | {{purpose}} | {{other libs}} |

---

## STEP 3: Define Folder Structure

For each library:

```
lib-{{name}}/
├── src/
│   ├── index.ts        ← Main export
│   ├── {{module}}.ts   ← Module files
│   └── types.ts        ← Type definitions
├── tests/
│   └── {{module}}.test.ts
├── package.json        ← Or equivalent
└── README.md
```

Adapt structure to tech stack (determined in Phase 33).

---

## STEP 4: Define Dependencies

**Build Order:** Libraries must be built in dependency order.

Example:
```
1. lib-core (no dependencies)
2. lib-validation (depends on lib-core)
3. lib-api-client (depends on lib-core, lib-validation)
```

Circular dependencies are **NOT allowed**. If found, flag for spec revision.

---

## STEP 5: Define Exports

For each library, define what it exports:

| Library | Export | Type | Used By |
|---------|--------|------|---------|
| `lib-core` | `Result<T>` | Type | All components |
| `lib-core` | `logger` | Function | All components |
| `lib-validation` | `validate()` | Function | API handlers |

---

## STEP 6: Get Approval

Present LIBRARY-STRUCTURE.md to user.

Wait for APPROVE or REVISE.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| Library specs | `lib-{{name}}/spec/` | Library definitions |
| DEPENDENCY-MAP.md | From Pass 3 | Dependency relationships |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| LIBRARY-STRUCTURE.md | Project root | Library organization |

---

## TEMPLATES

- LIBRARY-STRUCTURE-TEMPLATE.md

---

## RULES

- If no libraries identified → N/A
- No circular dependencies
- Define clear build order
- Adapt to tech stack (may be refined in Phase 33)

---
