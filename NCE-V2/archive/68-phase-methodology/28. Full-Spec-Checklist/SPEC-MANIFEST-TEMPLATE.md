# Specification Manifest

---
Project: {{project_name}}
Version: {{version}}
Date: {{timestamp}}
Total Spec Files: {{n}}
---

## Purpose

Complete index of all specification files in the project.

---

## Summary

| Category | Components | Spec Files | Total LOC |
|----------|------------|------------|-----------|
| Project-Wide | 1 | {{n}} | ~{{n}} |
| Systems | {{n}} | {{n}} | ~{{n}} |
| Subsystems | {{n}} | {{n}} | ~{{n}} |
| Integrations (Providers) | {{n}} | {{n}} | ~{{n}} |
| Integration Services | {{n}} | {{n}} | ~{{n}} |
| Libraries | {{n}} | {{n}} | ~{{n}} |
| **Total** | **{{n}}** | **{{n}}** | **~{{n}}** |

---

## Project-Wide Specifications

Location: `/0. Admin/0c. Full Specs/`

| File | Purpose | Status | LOC |
|------|---------|--------|-----|
| SCHEMAS.md | Shared types and interfaces | ✅ / 🔄 | ~{{n}} |
| ERROR-CODES.md | Master error code registry | ✅ / 🔄 | ~{{n}} |
| CONVENTIONS.md | Naming and style standards | ✅ / 🔄 | ~{{n}} |
| GLOSSARY.md | Domain term definitions | ✅ / 🔄 | ~{{n}} |
| API-STANDARDS.md | Function patterns | ✅ / 🔄 | ~{{n}} |
| SECURITY-STANDARDS.md | Security requirements | ✅ / 🔄 | ~{{n}} |
| REPO-SPEC.md | Repository setup | ✅ / 🔄 | ~{{n}} |

---

## Systems

### {{System Name}}

Location: `/systems/{{system-name}}/`

**System Definition:**

| File | Status |
|------|--------|
| SYSTEM.md | ✅ / 🔄 |
| CONTRACT.md | ✅ / 🔄 |

**Specifications:** `/systems/{{system-name}}/spec/`

| File | Purpose | Status | LOC |
|------|---------|--------|-----|
| INDEX.md | Quick reference | ✅ / 🔄 | ~{{n}} |
| OVERVIEW.md | Purpose, boundaries | ✅ / 🔄 | ~{{n}} |
| FUNCTIONS.md | Public API | ✅ / 🔄 | ~{{n}} |
| TYPES.md | Data structures | ✅ / 🔄 | ~{{n}} |
| BEHAVIOUR.md | Flows, states | ✅ / 🔄 | ~{{n}} |
| ERRORS.md | Error codes | ✅ / 🔄 | ~{{n}} |
| CONFIG.md | Configuration | ✅ / 🔄 | ~{{n}} |
| STORAGE.md | Database schema | ✅ / 🔄 / N/A | ~{{n}} |
| DECISIONS.md | Design rationale | ✅ / 🔄 | ~{{n}} |
| EXAMPLES.md | Usage examples | ✅ / 🔄 | ~{{n}} |

**Subsystems:** {{n}}

---

## Subsystems

### {{Parent System}} / {{Subsystem Name}}

Location: `/systems/{{parent}}/{{subsystem}}/`

**Subsystem Definition:**

| File | Status |
|------|--------|
| SUBSYSTEM.md | ✅ / 🔄 |

**Specifications:** `/systems/{{parent}}/{{subsystem}}/spec/`

| File | Purpose | Status | LOC |
|------|---------|--------|-----|
| INDEX.md | Quick reference | ✅ / 🔄 | ~{{n}} |
| OVERVIEW.md | Purpose, boundaries | ✅ / 🔄 | ~{{n}} |
| FUNCTIONS.md | Public API | ✅ / 🔄 | ~{{n}} |
| TYPES.md | Data structures | ✅ / 🔄 | ~{{n}} |
| BEHAVIOUR.md | Flows, states | ✅ / 🔄 | ~{{n}} |
| ERRORS.md | Error codes | ✅ / 🔄 | ~{{n}} |
| CONFIG.md | Configuration | ✅ / 🔄 | ~{{n}} |
| STORAGE.md | Database schema | ✅ / 🔄 / N/A | ~{{n}} |
| DECISIONS.md | Design rationale | ✅ / 🔄 | ~{{n}} |
| EXAMPLES.md | Usage examples | ✅ / 🔄 | ~{{n}} |

---

## Integration Providers

### {{Provider Name}}

Location: `/integrations/{{provider}}/`

**Provider Definition:**

| File | Purpose | Status |
|------|---------|--------|
| SYSTEM-NOTES.md | Provider overview, shared auth, base config | ✅ / 🔄 |

**Services:** {{n}}

---

## Integration Services

### {{Provider}} / {{Service Name}}

Location: `/integrations/{{provider}}/{{service}}/`

**Specifications:** `/integrations/{{provider}}/{{service}}/spec/`

| File | Purpose | Status | LOC |
|------|---------|--------|-----|
| INDEX.md | Quick reference | ✅ / 🔄 | ~{{n}} |
| OVERVIEW.md | Service purpose | ✅ / 🔄 | ~{{n}} |
| API-SURFACE.md | External API docs | ✅ / 🔄 | ~{{n}} |
| OUR-WRAPPER.md | Our wrapper functions | ✅ / 🔄 | ~{{n}} |
| FIELD-MAPPING.md | Data transformations | ✅ / 🔄 | ~{{n}} |
| ERRORS.md | Error mapping | ✅ / 🔄 | ~{{n}} |
| CONSTRAINTS.md | Rate limits, quotas | ✅ / 🔄 | ~{{n}} |
| FAILURE-MODES.md | Failure handling | ✅ / 🔄 | ~{{n}} |
| EXAMPLES.md | Usage examples | ✅ / 🔄 | ~{{n}} |

---

## Libraries

### {{Library Name}}

Location: `/libraries/{{library}}/`

**Specifications:** `/libraries/{{library}}/spec/`

| File | Purpose | Status | LOC |
|------|---------|--------|-----|
| INDEX.md | Quick reference | ✅ / 🔄 | ~{{n}} |
| OVERVIEW.md | Purpose, scope | ✅ / 🔄 | ~{{n}} |
| FUNCTIONS.md | Public API | ✅ / 🔄 | ~{{n}} |
| TYPES.md | Data structures | ✅ / 🔄 | ~{{n}} |
| ERRORS.md | Error codes | ✅ / 🔄 | ~{{n}} |
| EXAMPLES.md | Usage examples | ✅ / 🔄 | ~{{n}} |

---

## File Tree

```
project/
├── 0. Admin/
│   └── 0c. Full Specs/
│       ├── SCHEMAS.md
│       ├── ERROR-CODES.md
│       ├── CONVENTIONS.md
│       ├── GLOSSARY.md
│       ├── API-STANDARDS.md
│       ├── SECURITY-STANDARDS.md
│       └── REPO-SPEC.md
│
├── systems/
│   └── {{system}}/
│       ├── SYSTEM.md
│       ├── CONTRACT.md
│       ├── spec/
│       │   ├── INDEX.md
│       │   ├── OVERVIEW.md
│       │   ├── FUNCTIONS.md
│       │   ├── TYPES.md
│       │   ├── BEHAVIOUR.md
│       │   ├── ERRORS.md
│       │   ├── CONFIG.md
│       │   ├── STORAGE.md
│       │   ├── DECISIONS.md
│       │   └── EXAMPLES.md
│       └── {{subsystem}}/
│           ├── SUBSYSTEM.md
│           └── spec/
│               └── (same structure)
│
├── integrations/
│   └── {{provider}}/
│       ├── SYSTEM-NOTES.md
│       └── {{service}}/
│           └── spec/
│               ├── INDEX.md
│               ├── OVERVIEW.md
│               ├── API-SURFACE.md
│               ├── OUR-WRAPPER.md
│               ├── FIELD-MAPPING.md
│               ├── ERRORS.md
│               ├── CONSTRAINTS.md
│               ├── FAILURE-MODES.md
│               └── EXAMPLES.md
│
└── libraries/
    └── {{library}}/
        └── spec/
            ├── INDEX.md
            ├── OVERVIEW.md
            ├── FUNCTIONS.md
            ├── TYPES.md
            ├── ERRORS.md
            └── EXAMPLES.md
```

---

## File Counts

| Type | Components | Files per Component | Total Files |
|------|------------|---------------------|-------------|
| Project-Wide | 1 | 7 | 7 |
| Systems | {{n}} | 12 | {{n}} |
| Subsystems | {{n}} | 11 | {{n}} |
| Providers | {{n}} | 1 | {{n}} |
| Services | {{n}} | 9 | {{n}} |
| Libraries | {{n}} | 6 | {{n}} |
| **Total** | | | **{{n}}** |

---
Phase: 28 (Full Spec Checklist)
Generated: {{timestamp}}
---
