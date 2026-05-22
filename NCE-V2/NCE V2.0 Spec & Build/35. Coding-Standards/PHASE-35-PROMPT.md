# PHASE 35: CODING STANDARDS

---
Phase: 35
Name: Coding Standards
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are establishing coding standards that ensure consistency, maintainability, and LLM-friendliness.

These standards will govern all implementation in 0f.

---

## TASK

1. Define code style rules
2. Define LLM-friendly patterns
3. Define file size limits
4. Define linting/formatting tools
5. Define review standards
6. Get approval

---

## CRITICAL: FILE SIZE LIMITS

| Limit | Value | Action |
|-------|-------|--------|
| **Soft target** | ~300 LOC per file | Aim for this |
| **Hard stop** | 500 LOC per file | **Refactor required** |
| **Component cap** | ~1,500 LOC total | Split if exceeded |

These limits exist for LLM context management. They are non-negotiable.

---

## LLM-FRIENDLY PATTERNS

Code must be written for AI comprehension:

### DO:
- **Composition over inheritance** — Flat structures are easier to parse
- **Explicit over implicit** — No magic, no hidden behavior
- **Small files** — Target ~300 LOC
- **Clear naming** — Descriptive, not clever
- **Comments for "why"** — Not "what" (code shows what)
- **Single responsibility** — One purpose per file/function

### DO NOT:
- Deep inheritance hierarchies
- Heavy use of decorators/metaprogramming
- Implicit behavior through naming conventions
- Clever one-liners that obscure intent
- Large files with multiple concerns

---

## STYLE RULES

### Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Files | kebab-case | `user-service.ts` |
| Functions | camelCase | `getUserById()` |
| Classes | PascalCase | `UserService` |
| Constants | SCREAMING_SNAKE | `MAX_RETRY_COUNT` |
| Types | PascalCase | `UserResponse` |
| Interfaces | PascalCase (no I prefix) | `UserService` |

### File Organization

```typescript
// 1. Imports (external, then internal)
import { external } from 'external-lib';
import { internal } from '../internal';

// 2. Types (if small, otherwise separate file)
interface Props { ... }

// 3. Constants
const MAX_ITEMS = 100;

// 4. Main export
export function mainFunction() { ... }

// 5. Helper functions (private)
function helperFunction() { ... }
```

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| TECH-STACK.md | Phase 33 | Language/framework specifics |
| LLM constraints | CLAUDE.MD | Size limits |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| CODING-STANDARDS.md | Project root | Style guide |

---

## TEMPLATES

- CODING-STANDARDS-TEMPLATE.md

---

## RULES

- Include hard file size limits
- Include LLM-friendly patterns
- Adapt to tech stack
- Be specific and enforceable

---
