# Library Structure

---
Project: {{Project Name}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED | N/A
---

## Overview

| Metric | Value |
|--------|-------|
| Total Libraries | {{n}} |
| Build Order Levels | {{n}} |

**If N/A:** No shared libraries were identified in Phase 23. Skip to Phase 32.

---

## Library Summary

| Library | Purpose | Dependencies | Build Order |
|---------|---------|--------------|-------------|
| `lib-{{name}}` | {{purpose}} | None | 1 |
| `lib-{{name}}` | {{purpose}} | `lib-core` | 2 |
| `lib-{{name}}` | {{purpose}} | `lib-core`, `lib-validation` | 3 |

---

## Build Order

Libraries must be built in this order:

```
Level 1 (no dependencies):
  └── lib-core

Level 2 (depends on Level 1):
  └── lib-validation

Level 3 (depends on Level 1-2):
  └── lib-api-client
```

**Circular dependencies:** None / {{describe if found — requires spec fix}}

---

## Library Details

### lib-{{name}}

**Purpose:** {{one sentence}}

**Source:** `lib-{{name}}/spec/`

**Folder Structure:**
```
lib-{{name}}/
├── src/
│   ├── index.ts           ← Main export file
│   ├── {{module}}.ts      ← Module implementation
│   ├── {{module}}.ts
│   └── types.ts           ← Type definitions
├── tests/
│   ├── {{module}}.test.ts
│   └── {{module}}.test.ts
├── package.json
├── tsconfig.json
└── README.md
```

**Dependencies:**

| Dependency | Type | Purpose |
|------------|------|---------|
| None | — | — |
| `lib-core` | Internal | Shared utilities |
| `{{package}}` | External | {{purpose}} |

**Exports:**

| Export | Type | Description |
|--------|------|-------------|
| `{{name}}` | Function | {{description}} |
| `{{TypeName}}` | Type | {{description}} |
| `{{constant}}` | Constant | {{description}} |

**Consumers:**

| Component | Uses |
|-----------|------|
| {{system}} | `{{export}}` |
| {{system}} | `{{export}}` |

---

### lib-{{next}}

(Repeat pattern for each library)

---

## Export Index

All library exports in one view:

| Library | Export | Type | Used By |
|---------|--------|------|---------|
| `lib-core` | `Result<T>` | Type | All |
| `lib-core` | `logger` | Function | All |
| `lib-validation` | `validate` | Function | API handlers |
| `lib-{{name}}` | `{{export}}` | {{type}} | {{consumers}} |

---

## Dependency Graph

```
┌─────────────┐
│  lib-core   │
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│ lib-validation  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ lib-api-client  │
└─────────────────┘
```

---

## Installation / Setup

**For Monorepo:**
```bash
# Install all library dependencies
npm install

# Build in order
npm run build:libs
```

**Build Script:**
```json
{
  "scripts": {
    "build:libs": "npm run build -w lib-core && npm run build -w lib-validation && npm run build -w lib-api-client"
  }
}
```

---

## Testing

| Library | Test Command | Coverage Target |
|---------|--------------|-----------------|
| `lib-core` | `npm test -w lib-core` | 90% |
| `lib-validation` | `npm test -w lib-validation` | 90% |
| `lib-{{name}}` | `npm test -w lib-{{name}}` | 90% |

---

## Versioning

| Approach | Description |
|----------|-------------|
| Internal | All libraries share project version |
| Independent | Each library has own semver |

**Chosen:** {{Internal / Independent}}

---

## Notes

{{Any additional library structure notes}}

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING / N/A |

---
