# Phase 55: Unit Testing

---
Section: 0g Testing
Phase: 55 of 60
Previous: Phase 54 (Test Planning)
Next: Phase 56 (Integration Testing)
Collapsible: Yes (can combine with 56, 57)
---

## Purpose

Write unit tests for business logic, utilities, validators, and data transformations.

---

## Inputs Required

| Document | Location | Purpose |
|----------|----------|---------|
| TEST-PLAN.md | Project root | Testing strategy |
| TYPES.md | 0c output | Data types to test |
| ERROR-CODES.md | 0c output | Error scenarios |
| Implementation code | src/ | Code to test |

---

## What to Test

### Test These

| Category | Examples | Priority |
|----------|----------|----------|
| Utility functions | formatDate, debounce, slugify | High |
| Validators | validateEmail, validatePhone | High |
| **Validation schemas** | **Zod schemas, Joi schemas, input validators** | **High** |
| Business logic | calculateTotal, applyDiscount | High |
| Data transformations | mapUserToDTO, parseResponse | High |
| Pure functions | Any function with no side effects | High |
| Error handlers | Error formatting, categorization | Medium |

### Do NOT Test

| Category | Why | Alternative |
|----------|-----|-------------|
| Framework code | Not your code | Trust the framework |
| Third-party libraries | Not your code | Trust the library |
| Simple getters/setters | Trivial | Skip |
| Type definitions | No runtime behaviour | Skip |
| Configuration files | No logic | Skip |
| Generated code | Will be regenerated | Skip |

---

## Steps

### Step 1: Identify Testable Units

Scan implementation for:
- `utils/` folder
- `helpers/` folder
- `services/` folder (pure functions only)
- `lib/` folder (non-integration code)

Create list:
```
| File | Functions | Priority |
|------|-----------|----------|
| utils/date.ts | formatDate, parseDate, isValidDate | High |
| utils/validation.ts | validateEmail, validatePhone | High |
| {{file}} | {{functions}} | {{priority}} |
```

### Step 2: Write Tests by Category

#### 2a: Utility Function Tests

```typescript
// tests/unit/utils/date.test.ts
import { describe, it, expect } from 'vitest'; // or jest
import { formatDate, parseDate } from '@/utils/date';

describe('formatDate', () => {
  it('formats ISO date to readable string', () => {
    expect(formatDate('2024-01-15T10:30:00Z')).toBe('Jan 15, 2024');
  });

  it('handles invalid date gracefully', () => {
    expect(formatDate('invalid')).toBe('Invalid date');
  });

  it('handles null input', () => {
    expect(formatDate(null)).toBe('Invalid date');
  });
});
```

#### 2b: Validator Tests

```typescript
// tests/unit/utils/validation.test.ts
import { validateEmail } from '@/utils/validation';

describe('validateEmail', () => {
  it('returns true for valid email', () => {
    expect(validateEmail('user@example.com')).toBe(true);
  });

  it('returns true for email with subdomain', () => {
    expect(validateEmail('user@mail.example.com')).toBe(true);
  });

  it('returns false for missing @', () => {
    expect(validateEmail('userexample.com')).toBe(false);
  });

  it('returns false for missing domain', () => {
    expect(validateEmail('user@')).toBe(false);
  });

  it('returns false for empty string', () => {
    expect(validateEmail('')).toBe(false);
  });
});
```

#### 2c: Business Logic Tests

```typescript
// tests/unit/services/pricing.test.ts
import { calculateTotal, applyDiscount } from '@/services/pricing';

describe('calculateTotal', () => {
  it('sums item prices', () => {
    const items = [
      { price: 10, quantity: 2 },
      { price: 5, quantity: 3 },
    ];
    expect(calculateTotal(items)).toBe(35);
  });

  it('returns 0 for empty array', () => {
    expect(calculateTotal([])).toBe(0);
  });
});

describe('applyDiscount', () => {
  it('applies percentage discount', () => {
    expect(applyDiscount(100, { type: 'percentage', value: 10 })).toBe(90);
  });

  it('applies fixed discount', () => {
    expect(applyDiscount(100, { type: 'fixed', value: 15 })).toBe(85);
  });

  it('does not go below zero', () => {
    expect(applyDiscount(10, { type: 'fixed', value: 20 })).toBe(0);
  });
});
```

#### 2d: Data Transformation Tests

```typescript
// tests/unit/mappers/user.test.ts
import { mapUserToDTO } from '@/mappers/user';

describe('mapUserToDTO', () => {
  it('maps user entity to DTO', () => {
    const user = {
      id: '123',
      email: 'test@example.com',
      passwordHash: 'secret',
      createdAt: new Date('2024-01-01'),
    };

    const dto = mapUserToDTO(user);

    expect(dto).toEqual({
      id: '123',
      email: 'test@example.com',
      createdAt: '2024-01-01T00:00:00.000Z',
    });
    expect(dto).not.toHaveProperty('passwordHash');
  });
});
```

### Step 3: Test Edge Cases

For each function, consider:

| Edge Case | Example Test |
|-----------|--------------|
| Empty input | `expect(fn([])).toBe(...)` |
| Null/undefined | `expect(fn(null)).toBe(...)` |
| Boundary values | `expect(fn(0)).toBe(...)` |
| Large values | `expect(fn(Number.MAX_SAFE_INTEGER)).toBe(...)` |
| Invalid types | `expect(() => fn('wrong')).toThrow()` |
| Unicode | `expect(fn('日本語')).toBe(...)` |

### Step 4: Test Error Cases

From ERROR-CODES.md, identify errors thrown by unit-level code:

```typescript
describe('parseConfig', () => {
  it('throws CONFIG_INVALID for malformed JSON', () => {
    expect(() => parseConfig('not json')).toThrow('CONFIG_INVALID');
  });
});
```

### Step 5: Add Spec Annotations

Every test should trace to a spec:

```typescript
/**
 * @spec TYPES.md#User
 * @verifies User email validation rules
 */
describe('validateUserEmail', () => {
  // ...
});
```

### Step 6: Update TEST-TRACE.md

As you write tests, update the Unit Test Coverage section:

```markdown
| Module | Function | Test File | Test Name | Status |
|--------|----------|-----------|-----------|--------|
| utils/date | formatDate | unit/date.test.ts | formats ISO to readable | ✅ |
```

### Step 7: Run and Verify

```bash
# Run unit tests
npm run test:unit

# Check coverage
npm run test:coverage
```

**Coverage target:** ≥ 70% for business logic

---

## Output

By end of Phase 55, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| Unit test files | tests/unit/ | ☐ Created |
| TEST-TRACE.md | Project root | ☐ Unit section updated |
| Coverage passing | Coverage report | ☐ ≥ 70% |

---

## Checklist

Before proceeding to Phase 56:

- [ ] All utility functions have tests
- [ ] All validators have tests
- [ ] All business logic functions have tests
- [ ] Edge cases covered
- [ ] Error cases covered
- [ ] All tests pass
- [ ] Coverage ≥ 70%
- [ ] TEST-TRACE.md updated

---

## Test Patterns

### Pattern: Testing Pure Functions

```typescript
describe('pureFunction', () => {
  // Happy path
  it('returns expected output for valid input', () => {});
  
  // Edge cases
  it('handles empty input', () => {});
  it('handles null/undefined', () => {});
  it('handles boundary values', () => {});
  
  // Error cases
  it('throws for invalid input', () => {});
});
```

### Pattern: Testing with Mocked Dependencies

```typescript
import { vi } from 'vitest';

describe('functionWithDeps', () => {
  it('calls dependency correctly', () => {
    const mockDep = vi.fn().mockReturnValue('result');
    const result = functionWithDeps(mockDep);
    
    expect(mockDep).toHaveBeenCalledWith(expectedArgs);
    expect(result).toBe('expected');
  });
});
```

### Pattern: Testing Async Functions

```typescript
describe('asyncFunction', () => {
  it('resolves with expected value', async () => {
    const result = await asyncFunction();
    expect(result).toBe('expected');
  });

  it('rejects with error for invalid input', async () => {
    await expect(asyncFunction('invalid')).rejects.toThrow('ERROR');
  });
});
```

---

## Rules

1. **Test behaviour, not implementation** — Test what it does, not how
2. **One assertion concept per test** — Keep tests focused
3. **No shared mutable state** — Each test must be independent
4. **No real external calls** — Mock everything external
5. **Fast execution** — Unit tests should run in milliseconds

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Test depends on other tests | Ensure isolation, reset state |
| Flaky async tests | Use proper async/await, increase timeout |
| Low coverage | Add edge case and error tests |
| Slow tests | Mock expensive operations |

---
Generated: {{timestamp}}
Phase: 55 (Unit Testing)
Section: 0g Testing
---
