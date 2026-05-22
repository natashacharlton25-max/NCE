# Coding Standards

---
Project: {{Project Name}}
Language: {{TypeScript / Python / etc.}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Overview

These standards ensure consistent, maintainable, and LLM-friendly code across the project.

**Key Principle:** Code should be as readable to an AI as to a human.

---

## File Size Limits

| Limit | Value | Action |
|-------|-------|--------|
| **Soft target** | ~300 LOC | Aim for this |
| **Hard stop** | 500 LOC | **Refactor required before merge** |
| **Component total** | ~1,500 LOC | Split component if exceeded |

**Enforcement:** Files exceeding 500 LOC MUST be refactored before PR approval. No exceptions.

### What Counts as LOC

- ✅ Executable code
- ✅ Type definitions
- ✅ Configuration
- ❌ Comments (don't count)
- ❌ Blank lines (don't count)
- ❌ Import statements (don't count)

### When to Split

Split a file when:
- Approaching 400 LOC
- Multiple distinct responsibilities
- Separate testable units emerge
- Different change frequencies

---

## LLM-Friendly Patterns

### Required Patterns

| Pattern | Why |
|---------|-----|
| **Composition over inheritance** | Flat structures parse better |
| **Explicit over implicit** | No hidden behavior |
| **Pure functions where possible** | Predictable, testable |
| **Early returns** | Reduces nesting |
| **Named constants** | Self-documenting |

### Forbidden Patterns

| Pattern | Why | Alternative |
|---------|-----|-------------|
| Deep inheritance (>2 levels) | Hard to trace | Composition |
| Heavy decorators | Magic behavior | Explicit wrappers |
| Metaprogramming | Unpredictable | Explicit code |
| Clever one-liners | Obscures intent | Readable multi-line |
| Implicit type coercion | Hidden bugs | Explicit conversion |

---

## Naming Conventions

### Files

| Type | Convention | Example |
|------|------------|---------|
| Module | kebab-case | `user-service.ts` |
| Types | kebab-case + `.types` | `user-service.types.ts` |
| Constants | kebab-case + `.constants` | `api.constants.ts` |
| Utils | kebab-case + `.utils` | `string.utils.ts` |
| Tests | kebab-case + `.test` | `user-service.test.ts` |

### Code Elements

| Element | Convention | Example |
|---------|------------|---------|
| Functions | camelCase, verb-first | `getUserById`, `validateInput` |
| Variables | camelCase, noun | `currentUser`, `itemCount` |
| Constants | SCREAMING_SNAKE | `MAX_RETRIES`, `API_TIMEOUT` |
| Classes | PascalCase | `UserService`, `ApiClient` |
| Types/Interfaces | PascalCase | `UserResponse`, `ApiConfig` |
| Enums | PascalCase | `UserStatus`, `ErrorCode` |
| Enum values | PascalCase or SCREAMING_SNAKE | `Active` or `ACTIVE` |

### Naming Rules

1. **Be descriptive** — `getUserById` not `getUser` or `get`
2. **Be consistent** — Same concept = same name everywhere
3. **Avoid abbreviations** — `configuration` not `cfg`
4. **Boolean prefixes** — `isActive`, `hasPermission`, `canEdit`

---

## File Organization

### Standard File Structure

```typescript
// ============================================
// 1. IMPORTS
// ============================================

// External imports first
import { something } from 'external-library';

// Internal imports second (organized by distance)
import { util } from '../../shared/utils';
import { type } from '../types';
import { local } from './local';

// ============================================
// 2. TYPES (if small; otherwise separate file)
// ============================================

interface FunctionInput {
  // ...
}

interface FunctionOutput {
  // ...
}

// ============================================
// 3. CONSTANTS
// ============================================

const MAX_ITEMS = 100;
const DEFAULT_TIMEOUT = 5000;

// ============================================
// 4. MAIN EXPORTS
// ============================================

export function mainFunction(input: FunctionInput): FunctionOutput {
  // Implementation
}

export function secondaryFunction() {
  // Implementation
}

// ============================================
// 5. PRIVATE HELPERS (not exported)
// ============================================

function helperFunction() {
  // Implementation
}
```

---

## Function Standards

### Function Size

| Metric | Target | Maximum |
|--------|--------|---------|
| Lines | ~20 | 50 |
| Parameters | ~3 | 5 |
| Nesting depth | 2 | 3 |

### Function Structure

```typescript
/**
 * Brief description of what this function does.
 * 
 * @param input - Description of input
 * @returns Description of output
 * @throws ErrorType - When this error occurs
 */
export function exampleFunction(input: Input): Output {
  // 1. Validate input (early return on failure)
  if (!input.required) {
    throw new ValidationError('required field missing');
  }

  // 2. Transform/prepare data
  const prepared = prepareData(input);

  // 3. Execute main logic
  const result = executeLogic(prepared);

  // 4. Return result
  return result;
}
```

### Early Returns

```typescript
// ✅ Good - Early returns
function processUser(user: User | null): Result {
  if (!user) {
    return { success: false, error: 'No user' };
  }

  if (!user.isActive) {
    return { success: false, error: 'Inactive user' };
  }

  // Main logic here (not nested)
  return { success: true, data: user };
}

// ❌ Bad - Deep nesting
function processUser(user: User | null): Result {
  if (user) {
    if (user.isActive) {
      // Main logic buried in nesting
      return { success: true, data: user };
    } else {
      return { success: false, error: 'Inactive user' };
    }
  } else {
    return { success: false, error: 'No user' };
  }
}
```

---

## Comments

### When to Comment

| Comment Type | When | Example |
|--------------|------|---------|
| **Why** | Non-obvious decisions | `// Using retry because API is flaky` |
| **Context** | Business rules | `// Must match accounting system format` |
| **Warnings** | Gotchas | `// Order matters: auth before validation` |
| **TODO** | Known debt | `// TODO: Optimize for large datasets` |

### When NOT to Comment

| Don't Comment | Why | Instead |
|---------------|-----|---------|
| What code does | Code should be self-evident | Better naming |
| Obvious logic | Adds noise | Remove comment |
| Changelog | Use git | Commit messages |

---

## Error Handling

### Standard Pattern

```typescript
// Use Result type for expected failures
type Result<T> = 
  | { success: true; data: T }
  | { success: false; error: ErrorDetail };

// Throw only for unexpected failures
function riskyOperation(): Result<Data> {
  try {
    // Attempt operation
    const data = performOperation();
    return { success: true, data };
  } catch (error) {
    // Known error types → Result
    if (error instanceof ValidationError) {
      return { success: false, error: mapError(error) };
    }
    // Unknown errors → rethrow
    throw error;
  }
}
```

---

## Testing Standards

### Test File Organization

```typescript
describe('UserService', () => {
  describe('getUserById', () => {
    it('returns user when found', () => { });
    it('returns null when not found', () => { });
    it('throws on invalid id format', () => { });
  });

  describe('createUser', () => {
    it('creates user with valid input', () => { });
    it('fails on duplicate email', () => { });
  });
});
```

### Test Naming

```
it('[action] [expected result] [condition]')
it('returns user when found')
it('throws ValidationError when email is invalid')
```

---

## Import Organization

```typescript
// 1. Node/runtime built-ins
import { readFile } from 'fs/promises';

// 2. External packages (alphabetical)
import { z } from 'zod';

// 3. Internal absolute imports
import { logger } from '@/lib/logger';

// 4. Internal relative imports (by distance)
import { UserService } from '../../services';
import { formatDate } from '../utils';
import { CONSTANTS } from './constants';

// 5. Type-only imports last
import type { User } from './types';
```

---

## Tooling

### Linting

| Tool | Config File | Purpose |
|------|-------------|---------|
| {{ESLint / Biome}} | `{{config}}` | Code quality |

### Formatting

| Tool | Config File | Purpose |
|------|-------------|---------|
| {{Prettier / Biome}} | `{{config}}` | Code formatting |

### Pre-commit

```bash
# Run before every commit
npm run lint
npm run format:check
npm run typecheck
```

---

## Code Review Checklist

Before approving PR, verify:

- [ ] No file exceeds 500 LOC
- [ ] Functions under 50 LOC
- [ ] No deep nesting (>3 levels)
- [ ] Naming follows conventions
- [ ] No magic numbers (use constants)
- [ ] Error handling follows pattern
- [ ] Tests exist for new code
- [ ] No TODO without issue reference

---

## Notes

{{Any additional coding standards notes}}

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
