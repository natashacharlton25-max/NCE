# Project Conventions

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

This document defines project-wide conventions for naming, formatting, patterns, and practices. All components should follow these conventions for consistency.

---

## Naming Conventions

### Files and Folders

| Type | Convention | Example |
|------|------------|---------|
| System folder | `kebab-case` | `user-management/` |
| Subsystem folder | `kebab-case` | `authentication/` |
| Library folder | `lib-kebab-case` | `lib-validation/` |
| Integration folder | `integration-provider-service` | `integration-stripe-payments/` |
| Source files | `kebab-case.ts` | `user-service.ts` |
| Test files | `*.test.ts` | `user-service.test.ts` |
| Type definition files | `*.types.ts` | `user.types.ts` |
| Constant files | `*.constants.ts` | `user.constants.ts` |
| Index/barrel files | `index.ts` | `index.ts` |
| Spec files | `UPPER-CASE.md` | `FUNCTIONS.md` |

### Code Identifiers

| Type | Convention | Example |
|------|------------|---------|
| Functions | `camelCase` verb phrase | `createUser()`, `validateEmail()` |
| Variables | `camelCase` | `userData`, `isValid` |
| Constants | `SCREAMING_SNAKE_CASE` | `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT` |
| Classes | `PascalCase` noun | `UserService`, `EmailValidator` |
| Interfaces | `PascalCase` noun | `UserData`, `CreateOptions` |
| Types | `PascalCase` | `UserId`, `Timestamp` |
| Enums | `PascalCase` | `UserStatus`, `Priority` |
| Enum members | `PascalCase` | `UserStatus.Active` |
| Boolean variables | `is/has/can/should` prefix | `isValid`, `hasPermission` |

### Database

| Type | Convention | Example |
|------|------------|---------|
| Table names | `snake_case` plural | `users`, `project_tasks` |
| Column names | `snake_case` | `created_at`, `user_id` |
| Index names | `idx_{table}_{columns}` | `idx_users_email` |
| Foreign keys | `{table}_id` | `user_id`, `project_id` |

### API

| Type | Convention | Example |
|------|------------|---------|
| Endpoints | `/kebab-case` | `/user-profiles` |
| Query params | `camelCase` | `?sortBy=createdAt` |
| Request body | `camelCase` | `{ "firstName": "John" }` |
| Response body | `camelCase` | `{ "userId": "usr_123" }` |

### Error Codes

| Type | Convention | Example |
|------|------------|---------|
| System prefix | `UPPER_CASE` 3-4 chars | `USR`, `AUTH`, `PRJ` |
| Integration prefix | `INT_{PROVIDER}` | `INT_STRIPE` |
| Library prefix | `LIB_{NAME}` | `LIB_VAL` |
| Full code | `{PREFIX}_{NNN}` | `USR_001`, `AUTH_101` |

### Entity IDs

| Type | Convention | Example |
|------|------------|---------|
| Format | `{prefix}_{identifier}` | `usr_abc123def` |
| Prefix | 3 lowercase chars | `usr`, `prj`, `tsk` |
| Identifier | alphanumeric | `abc123def456` |

---

## Code Style

### Formatting

| Rule | Value |
|------|-------|
| Indentation | 2 spaces |
| Max line length | 100 characters |
| Quotes | Single quotes for strings |
| Semicolons | Required |
| Trailing commas | ES5 (arrays, objects) |
| Bracket spacing | Yes `{ foo }` |

### Imports

```typescript
// 1. External packages (alphabetical)
import { Injectable } from '@nestjs/common';
import { z } from 'zod';

// 2. Internal aliases (alphabetical)
import { Result } from '@/shared/types';
import { UserService } from '@/systems/user';

// 3. Relative imports (alphabetical)
import { CreateUserInput } from './user.types';
import { validateUser } from './user.validation';
```

### Exports

```typescript
// Prefer named exports
export function createUser() { }
export interface User { }

// Use barrel files (index.ts) for public API
// index.ts
export { createUser, getUser, updateUser } from './user.service';
export type { User, CreateUserInput } from './user.types';
```

---

## Function Conventions

### Naming

| Action | Pattern | Example |
|--------|---------|---------|
| Create | `create{Entity}` | `createUser()` |
| Read single | `get{Entity}` | `getUser()` |
| Read multiple | `list{Entities}` | `listUsers()` |
| Update | `update{Entity}` | `updateUser()` |
| Delete | `delete{Entity}` | `deleteUser()` |
| Check existence | `{entity}Exists` | `userExists()` |
| Validate | `validate{Thing}` | `validateEmail()` |
| Convert | `{from}To{To}` | `userToDto()` |
| Check condition | `is{Condition}` / `has{Thing}` | `isActive()`, `hasPermission()` |

### Signatures

```typescript
// Standard function signature pattern
async function doSomething(
  requiredParam: Type,
  options?: DoSomethingOptions
): Promise<Result<ReturnType>> {
  // ...
}

// Options object for 3+ optional params
interface DoSomethingOptions {
  optionalA?: string;
  optionalB?: number;
  optionalC?: boolean;
}
```

### Return Types

| Scenario | Return Type |
|----------|-------------|
| Success or failure | `Result<T>` |
| List with pagination | `ListResult<T>` |
| Boolean check | `boolean` |
| Void operation | `Promise<Result<void>>` |
| Never throws | `Result<T>` (errors in result) |

---

## Error Handling Conventions

### Pattern

```typescript
// DO: Return Result type
function doSomething(): Result<Data> {
  if (invalid) {
    return { success: false, error: { code: 'ERR_001', message: '...' } };
  }
  return { success: true, data: result };
}

// DON'T: Throw for expected errors
function doSomething(): Data {
  if (invalid) {
    throw new Error('...'); // ❌ Don't do this
  }
  return result;
}
```

### When to Use What

| Scenario | Approach |
|----------|----------|
| Validation failure | Return error in Result |
| Business rule violation | Return error in Result |
| Not found | Return error in Result |
| Programming error (bug) | Throw (should never happen) |
| Unrecoverable | Throw and let boundary catch |

---

## Async Conventions

### Always Async

```typescript
// All I/O operations are async
async function getUser(id: string): Promise<Result<User>> { }

// Even if currently sync, for future flexibility
async function validateUser(user: User): Promise<Result<User>> { }
```

### Error Handling in Async

```typescript
// Wrap external calls
async function callExternal(): Promise<Result<Data>> {
  try {
    const response = await externalService.call();
    return { success: true, data: response };
  } catch (error) {
    return { 
      success: false, 
      error: mapExternalError(error) 
    };
  }
}
```

---

## Testing Conventions

### File Location

```
src/systems/user/
├── src/
│   └── user.service.ts
└── tests/
    └── user.service.test.ts   ← Same name + .test
```

### Test Naming

```typescript
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid input', () => { });
    it('should return error when email exists', () => { });
    it('should return error when email invalid', () => { });
  });
});
```

### Test Structure

```typescript
it('should do something', () => {
  // Arrange
  const input = { ... };
  
  // Act
  const result = doSomething(input);
  
  // Assert
  expect(result.success).toBe(true);
});
```

---

## Documentation Conventions

### Code Comments

```typescript
// Single line for brief explanation
const result = process(data);

/**
 * Multi-line for complex logic.
 * Explains the "why", not the "what".
 */
function complexFunction() { }

// TODO: Description of what needs to be done
// FIXME: Description of bug to fix
// HACK: Explanation of workaround
```

### JSDoc

```typescript
/**
 * Creates a new user in the system.
 * 
 * @param input - User creation data
 * @returns Result containing the created user or error
 * 
 * @example
 * const result = await createUser({ email: 'user@example.com', name: 'John' });
 */
async function createUser(input: CreateUserInput): Promise<Result<User>> { }
```

---

## Git Conventions

### Branch Naming

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `feature/{ticket}-{description}` | `feature/ABC-123-user-auth` |
| Bug fix | `fix/{ticket}-{description}` | `fix/ABC-456-login-error` |
| Hotfix | `hotfix/{description}` | `hotfix/security-patch` |
| Release | `release/{version}` | `release/1.2.0` |
| Chore | `chore/{description}` | `chore/update-deps` |

### Commit Messages

```
{type}({scope}): {description}

{optional body}

{optional footer}
```

| Type | Use For |
|------|---------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, no code change |
| `refactor` | Code change, no feature/fix |
| `test` | Adding/updating tests |
| `chore` | Maintenance tasks |

**Examples:**
```
feat(user): add password reset functionality

fix(auth): correct token expiration calculation

Closes #123

docs(readme): update installation steps
```

---

## Logging Conventions

### Log Levels

| Level | Use For |
|-------|---------|
| `error` | Errors requiring attention |
| `warn` | Unexpected but handled |
| `info` | Significant events |
| `debug` | Detailed debugging |
| `trace` | Very detailed tracing |

### Log Format

```typescript
logger.info('User created', { 
  userId: user.id, 
  email: user.email,
  duration: ms 
});

logger.error('Failed to create user', { 
  error: error.message,
  input: sanitize(input),
  traceId 
});
```

### What NOT to Log

- Passwords
- API keys
- Personal data (unless required)
- Full request/response bodies in production

---

## Performance Conventions

### Pagination

- Default limit: 20
- Maximum limit: 100
- Always include `total` and `hasMore`

### Timeouts

| Operation | Default | Maximum |
|-----------|---------|---------|
| HTTP request | 30s | 60s |
| Database query | 5s | 30s |
| External API | 10s | 30s |

### Batch Sizes

| Operation | Default | Maximum |
|-----------|---------|---------|
| Bulk insert | 100 | 1000 |
| Bulk update | 50 | 500 |
| Bulk delete | 100 | 1000 |

---

## Notes

- Conventions can be overridden with documented justification
- New conventions require team agreement
- Review conventions quarterly

---
Phase: 26 (Project-Wide Specs)
Generated: {{timestamp}}
---
