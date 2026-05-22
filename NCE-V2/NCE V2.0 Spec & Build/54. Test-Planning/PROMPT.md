# Phase 54: Test Planning

---
Section: 0g Testing
Phase: 54 of 60
Previous: Phase 53 (Implementation Verification)
Next: Phase 55 (Unit Testing)
Collapsible: **NEVER** — Planning must happen before testing
---

## Purpose

Define testing strategy, set up test infrastructure, and create the test plan before writing any tests.

---

## Inputs Required

Before starting, ensure you have:

| Document | Location | Contains |
|----------|----------|----------|
| 0f-to-0g-HANDOFF.md | 0. Admin/0f/ | Entry requirements |
| IMPLEMENTATION-LOG.md | Project root | What was built |
| API-SURFACE.md | 0c/0d output | Endpoints to test |
| COMPONENTS.md | 0e output | Components to test |
| PAGES.md | 0e output | Flows to test |
| ERROR-CODES.md | 0c output | Errors to verify |
| A11Y-CHECKLIST.md | 0e output | A11y requirements |
| TECH-STACK.md | 0b output | Framework constraints |

---

## Steps

### Step 1: Review Handoff Contract

Read `0f-to-0g-HANDOFF.md` completely.

Verify:
- [ ] All handoff checklist items are checked
- [ ] Implementation is complete
- [ ] No blocking issues documented

**If handoff checklist is incomplete:** STOP. Return to 0f.

### Step 2: Survey Testing Scope

Create inventory of what needs testing:

**API Endpoints:**
```
Count endpoints from API-SURFACE.md
List: {{n}} endpoints across {{n}} resources
```

**Components:**
```
Count components from COMPONENTS.md
List: {{n}} components ({{n}} interactive)
```

**Pages/Flows:**
```
Count pages from PAGES.md
List: {{n}} pages, {{n}} user flows
```

**Error Codes:**
```
Count errors from ERROR-CODES.md
List: {{n}} error codes
```

**A11y Items:**
```
Count items from A11Y-CHECKLIST.md
List: {{n}} a11y requirements
```

### Step 3: Select Testing Tools

Based on TECH-STACK.md, select appropriate tools:

| Purpose | Options | Selection |
|---------|---------|-----------|
| Test runner | Jest, Vitest, Mocha | {{choice}} |
| Assertions | Built-in, Chai | {{choice}} |
| API testing | Supertest, native fetch | {{choice}} |
| Component testing | Testing Library, Enzyme | {{choice}} |
| E2E testing | Playwright, Cypress | {{choice}} |
| Accessibility | axe-core, pa11y | {{choice}} |
| Mocking | MSW, Jest mocks | {{choice}} |
| Coverage | Istanbul, c8 | {{choice}} |

**Selection criteria:**
- Matches framework in TECH-STACK.md
- Has good documentation
- Actively maintained

### Step 4: Define Core Test Entities

Define the 3-5 most common data objects used across all test types. This ensures consistency between unit, integration, and E2E tests.

**Core Entity Template:**

```markdown
## Core Test Entities

### Standard User
| Field | Value | Source |
|-------|-------|--------|
| id | test-user-{{uuid}} | TYPES.md#User |
| email | testuser@example.com | TYPES.md#User |
| name | Test User | TYPES.md#User |
| role | user | DATABASE-SCHEMA.md#roles |

### Standard Admin
| Field | Value | Source |
|-------|-------|--------|
| id | test-admin-{{uuid}} | TYPES.md#User |
| email | admin@example.com | TYPES.md#User |
| role | admin | DATABASE-SCHEMA.md#roles |

### Standard Project
| Field | Value | Source |
|-------|-------|--------|
| id | test-project-{{uuid}} | TYPES.md#Project |
| name | Test Project | TYPES.md#Project |
| ownerId | {{user.id}} | Foreign key |
```

**Rules:**
- Each entity must reference its source spec
- Entities must match the shape defined in shared-types/
- All test phases must use these definitions

### Step 5: Define Test Environment

**Database isolation:**
```
Dev database: {{dev_db_name}}
Test database: {{test_db_name}}
Isolation: Separate database for tests
```

**Reset strategy:**
| Test Type | Strategy |
|-----------|----------|
| Unit | No DB needed |
| Integration | Transaction rollback / Truncate |
| E2E | Truncate + re-seed |

**Environment variables:**
```env
# Test environment
DATABASE_URL={{test_db_url}}
NODE_ENV=test
{{other_vars}}
```

### Step 6: Define Test Data Strategy

Choose approach:
- [ ] Factory functions (recommended)
- [ ] Static fixtures
- [ ] Combination

**Factory location:**
```
tests/
├── factories/
│   ├── index.ts
│   ├── user.factory.ts
│   └── {{resource}}.factory.ts
```

**Factory Traceability Rules:**

1. **All factories must trace to specs**
   ```typescript
   /**
    * @spec TYPES.md#User
    */
   export function createTestUser(overrides?: Partial<User>): User {
     return { /* ... */ };
   }
   ```

2. **Factories must import shared-types**
   ```typescript
   // ✅ CORRECT
   import type { User } from '@/shared-types';
   
   // ❌ WRONG - No local type definitions
   interface TestUser { /* ... */ }
   ```

3. **Factories may compose, but not invent fields**

**Factory template:**
```typescript
import { faker } from '@faker-js/faker';
import type { {{Type}} } from '@/shared/types';

/**
 * @spec TYPES.md#{{Type}}
 */
export function createTest{{Type}}(overrides?: Partial<{{Type}}>): {{Type}} {
  return {
    id: faker.string.uuid(),
    // ... default values
    ...overrides,
  };
}
```

### Step 6: Create Folder Structure

```
tests/
├── unit/
│   ├── utils/
│   └── services/
├── integration/
│   └── api/
├── component/
├── e2e/
├── a11y/
├── factories/
├── fixtures/
└── helpers/
    ├── setup.ts
    └── teardown.ts
```

### Step 7: Configure Test Framework

**Install dependencies:**
```bash
npm install -D {{test_packages}}
```

**Configure test runner:**
```typescript
// vitest.config.ts or jest.config.ts
export default {
  // Configuration based on chosen tool
};
```

**Add test scripts:**
```json
{
  "scripts": {
    "test": "{{runner}} --run",
    "test:watch": "{{runner}} --watch",
    "test:coverage": "{{runner}} --coverage",
    "test:unit": "{{runner}} --run tests/unit",
    "test:integration": "{{runner}} --run tests/integration",
    "test:e2e": "{{e2e_runner}}"
  }
}
```

### Step 8: Verify CI Readiness

Run test command and verify:
```bash
npm test
echo $?  # Must be 0 if pass, 1 if fail
```

**CI requirements checklist:**
- [ ] `npm test` runs all tests
- [ ] Exit code 0 on success
- [ ] Exit code 1 on failure
- [ ] No interactive prompts
- [ ] Reasonable timeout

### Step 9: Create TEST-PLAN.md

Using `TEST-PLAN-TEMPLATE.md`, fill out:

1. Project information
2. Testing strategy and targets
3. Tool selections
4. Environment configuration
5. Test data strategy
6. Test scope (in/out)
7. Folder structure
8. Initial spec coverage map (empty, to be filled)
9. Risk areas

### Step 10: Initialize TEST-TRACE.md

Using `TEST-TRACE-TEMPLATE.md`, create skeleton:

1. List all API endpoints (Status: ☐)
2. List all error codes (Status: ☐)
3. List all components (Status: ☐)
4. List all pages/flows (Status: ☐)
5. List all a11y items (Status: ☐)

**Do not fill in test mappings yet** — that happens as tests are written.

---

## Output

By end of Phase 54, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| TEST-PLAN.md | Project root | ☐ Created |
| TEST-TRACE.md | Project root | ☐ Skeleton created |
| Test folder structure | tests/ | ☐ Created |
| Test configuration | Config files | ☐ Working |
| Test factories | tests/factories/ | ☐ Scaffolded |
| Package.json scripts | package.json | ☐ Added |

---

## Checklist

Before proceeding to Phase 55:

### Documentation
- [ ] TEST-PLAN.md complete
- [ ] TEST-TRACE.md skeleton created
- [ ] All specs inventoried

### Infrastructure
- [ ] Test framework installed
- [ ] Test configuration working
- [ ] Folder structure created
- [ ] Factory templates ready

### Environment
- [ ] Test database configured
- [ ] Reset strategy defined
- [ ] Environment variables documented

### CI Readiness
- [ ] `npm test` works
- [ ] Exit codes correct
- [ ] No interactive prompts

---

## Rules

1. **Do not write tests yet** — This phase is planning only
2. **Do not skip environment setup** — Isolation prevents flakiness
3. **Do not rely on dev seeds** — Plan for factory data
4. **Do not proceed without TEST-PLAN.md** — Strategy first

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Framework mismatch | Check TECH-STACK.md for correct tools |
| Missing dependencies | Run npm install for all test packages |
| CI not working | Verify exit codes and no interactive mode |
| Unclear scope | Re-read all spec documents |

---
Generated: {{timestamp}}
Phase: 54 (Test Planning)
Section: 0g Testing
---
