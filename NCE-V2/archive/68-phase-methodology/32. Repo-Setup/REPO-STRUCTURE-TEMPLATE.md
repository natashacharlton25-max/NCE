# Repository Structure

---
Project: {{Project Name}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Overview

| Metric | Value |
|--------|-------|
| Total Systems | {{n}} |
| Total Subsystems | {{n}} |
| Total Libraries | {{n}} |
| Total Integrations | {{n}} |

---

## Root Structure

```
{{project}}/
│
├── src/                          ← Source code
│   ├── {{system-a}}/             ← System A
│   │   ├── {{subsystem-1}}/      ← Subsystem 1
│   │   ├── {{subsystem-2}}/      ← Subsystem 2
│   │   └── index.ts              ← System entry
│   │
│   ├── {{system-b}}/             ← System B
│   │   └── ...
│   │
│   ├── lib/                      ← Shared libraries
│   │   ├── core/
│   │   └── {{library}}/
│   │
│   ├── integrations/             ← External integrations
│   │   └── {{integration}}/
│   │
│   └── index.ts                  ← Application entry
│
├── tests/                        ← Test files
│   ├── {{system-a}}/
│   │   ├── {{subsystem-1}}/
│   │   └── {{subsystem-2}}/
│   ├── {{system-b}}/
│   ├── lib/
│   └── integration/              ← Integration tests
│
├── config/                       ← Configuration
│   ├── default.json
│   ├── development.json
│   ├── staging.json
│   └── production.json
│
├── migrations/                   ← Database migrations
│   ├── 001_initial_schema.sql
│   └── ...
│
├── scripts/                      ← Build/deploy scripts
│   ├── build.sh
│   ├── deploy.sh
│   └── seed.sh
│
├── docs/                         ← Documentation
│   ├── api/
│   ├── architecture/
│   └── guides/
│
├── specs/                        ← Spec files (from 0c)
│   ├── {{system-a}}/spec/
│   ├── {{system-b}}/spec/
│   └── ...
│
├── .env.example                  ← Environment template
├── package.json                  ← Dependencies
├── tsconfig.json                 ← TypeScript config
├── README.md                     ← Project README
└── ...
```

---

## Component Mapping

### Systems

| System | Source Path | Test Path | Spec Path |
|--------|-------------|-----------|-----------|
| {{system-a}} | `src/{{system-a}}/` | `tests/{{system-a}}/` | `specs/{{system-a}}/spec/` |
| {{system-b}} | `src/{{system-b}}/` | `tests/{{system-b}}/` | `specs/{{system-b}}/spec/` |

### Subsystems

| Subsystem | Parent | Source Path | Test Path |
|-----------|--------|-------------|-----------|
| {{sub-1}} | {{system-a}} | `src/{{system-a}}/{{sub-1}}/` | `tests/{{system-a}}/{{sub-1}}/` |
| {{sub-2}} | {{system-a}} | `src/{{system-a}}/{{sub-2}}/` | `tests/{{system-a}}/{{sub-2}}/` |

### Libraries

| Library | Source Path | Test Path |
|---------|-------------|-----------|
| lib-core | `src/lib/core/` | `tests/lib/core/` |
| lib-{{name}} | `src/lib/{{name}}/` | `tests/lib/{{name}}/` |

### Integrations

| Integration | Source Path | Test Path |
|-------------|-------------|-----------|
| int-{{name}} | `src/integrations/{{name}}/` | `tests/integrations/{{name}}/` |

---

## File Naming Conventions

### Source Files

| Type | Convention | Example |
|------|------------|---------|
| Module | kebab-case | `user-service.ts` |
| Types | kebab-case + `.types` | `user-service.types.ts` |
| Constants | kebab-case + `.constants` | `user-service.constants.ts` |
| Utils | kebab-case + `.utils` | `string.utils.ts` |
| Index | `index` | `index.ts` |

### Test Files

| Type | Convention | Example |
|------|------------|---------|
| Unit test | `*.test.ts` | `user-service.test.ts` |
| Integration test | `*.integration.test.ts` | `auth.integration.test.ts` |
| E2E test | `*.e2e.test.ts` | `login.e2e.test.ts` |

### Config Files

| Type | Convention | Example |
|------|------------|---------|
| Environment | `{env}.json` | `production.json` |
| Tool config | Tool default | `tsconfig.json` |

---

## Folder Purposes

| Folder | Purpose | Contents |
|--------|---------|----------|
| `src/` | Production source code | Systems, libraries, integrations |
| `tests/` | All test files | Unit, integration, e2e tests |
| `config/` | Configuration files | Environment configs |
| `migrations/` | Database migrations | SQL migration files |
| `scripts/` | Automation scripts | Build, deploy, seed scripts |
| `docs/` | Documentation | API docs, guides, architecture |
| `specs/` | Specification files | From 0c (reference only) |

---

## Entry Points

| Entry Point | Path | Purpose |
|-------------|------|---------|
| Application | `src/index.ts` | Main application entry |
| System A | `src/{{system-a}}/index.ts` | System A entry |
| System B | `src/{{system-b}}/index.ts` | System B entry |
| Tests | `tests/` | Test runner entry |

---

## Special Directories

### `.gitignore` Patterns

```
node_modules/
dist/
.env
.env.local
*.log
coverage/
```

### Protected Paths

| Path | Protection | Reason |
|------|------------|--------|
| `specs/` | Read-only in CI | Specs are source of truth |
| `migrations/` | Append-only | Never modify existing migrations |

---

## Completeness Check

| Component | Has Source | Has Tests | Has Spec |
|-----------|------------|-----------|----------|
| {{system-a}} | ✅ | ✅ | ✅ |
| {{system-a}}/{{sub-1}} | ✅ | ✅ | ✅ |
| lib-core | ✅ | ✅ | ✅ |
| int-{{name}} | ✅ | ✅ | ✅ |

**Orphaned Components:** None / {{list any}}

---

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Bad | Instead |
|--------------|--------------|---------|
| Flat structure | Hard to navigate at scale | Group by domain/system |
| Deep nesting (>4 levels) | Hard to find files | Flatten where possible |
| Mixed concerns in folders | Unclear ownership | Separate by responsibility |
| `utils/` dumping ground | Becomes unmanageable | Specific, named utilities |
| `common/` without structure | Everything ends up there | Use libraries with clear purpose |
| Inconsistent naming | Cognitive overhead | Follow conventions strictly |
| Tests far from source | Hard to maintain | Mirror source structure |
| Config scattered | Hard to manage | Centralize in `/config` |
| No clear entry points | Hard to understand flow | Document in this file |

---

## Notes

{{Any additional repository structure notes}}

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
