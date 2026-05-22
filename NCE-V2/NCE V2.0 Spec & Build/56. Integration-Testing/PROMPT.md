# Phase 56: Integration Testing

---
Section: 0g Testing
Phase: 56 of 60
Previous: Phase 55 (Unit Testing)
Next: Phase 57 (Component Testing)
Collapsible: Yes (can combine with 55, 57)
---

## Purpose

Write integration tests for all API endpoints and database operations, verifying the backend works correctly.

---

## Inputs Required

| Document | Location | Purpose |
|----------|----------|---------|
| API-SURFACE.md | 0c/0d output | Endpoints to test |
| ERROR-CODES.md | 0c output | Error responses |
| DATABASE-SCHEMA.md | 0c output | Data structures |
| BACKEND-VERIFICATION-REPORT.md | 0f output | Manual test results to automate |
| shared-types/ | Implementation | Request/response types |

---

## Relationship to Phase 47

> Phase 47 proves endpoints exist and behave correctly (manual verification).
> Phase 56 automates those behaviours and expands coverage with edge and negative cases.

**Do not duplicate Phase 47.** Instead:
- Automate the manual tests from Phase 47
- Add edge cases not covered manually
- Add all error scenarios

---

## Steps

### Step 1: List All Endpoints

From API-SURFACE.md, create test inventory:

```markdown
| Endpoint | Method | Auth | Test Categories |
|----------|--------|------|-----------------|
| /api/auth/login | POST | No | Happy, validation, not found |
| /api/auth/logout | POST | Yes | Happy, unauthorized |
| /api/projects | GET | Yes | Happy, unauthorized, empty |
| /api/projects | POST | Yes | Happy, validation, unauthorized |
| /api/projects/:id | GET | Yes | Happy, not found, unauthorized |
| /api/projects/:id | PUT | Yes | Happy, validation, not found, forbidden |
| /api/projects/:id | DELETE | Yes | Happy, not found, forbidden |
```

### Step 2: Set Up Test Utilities

```typescript
// tests/helpers/api.ts
import { createTestUser } from '../factories/user.factory';

export async function getAuthToken(): Promise<string> {
  const user = await createTestUser();
  const response = await request(app)
    .post('/api/auth/login')
    .send({ email: user.email, password: 'testpassword' });
  return response.body.token;
}

export async function resetDatabase(): Promise<void> {
  // Truncate or rollback based on strategy
}
```

```typescript
// tests/helpers/setup.ts
import { beforeEach, afterEach } from 'vitest';
import { resetDatabase } from './api';

beforeEach(async () => {
  await resetDatabase();
});
```

#### Global Setup: Static Data

Some tests depend on static data that must survive database resets (roles, configurations, default records).

```typescript
// tests/helpers/globalSetup.ts
export async function seedStaticData() {
  // Roles that must exist
  await db.role.upsert({
    where: { name: 'admin' },
    create: { name: 'admin', permissions: ['*'] },
    update: {},
  });
  
  await db.role.upsert({
    where: { name: 'user' },
    create: { name: 'user', permissions: ['read', 'write:own'] },
    update: {},
  });
}
```

```typescript
// tests/helpers/setup.ts
beforeAll(async () => {
  await seedStaticData(); // Run once before all tests
});

beforeEach(async () => {
  await resetDatabase(); // Reset per-test data, preserve static
});
```

**Rule:** Document all static data requirements in TEST-PLAN.md.

### Step 3: Write Tests Per Endpoint

#### 3a: Test Categories

For each endpoint, test these categories:

| Category | What | Required |
|----------|------|----------|
| Happy path | Valid request → correct response | Always |
| Validation | Invalid input → 400 + error | If has body |
| Authentication | No token → 401 | If protected |
| Authorization | Wrong user → 403 | If ownership |
| Not found | Invalid ID → 404 | If has params |
| Edge cases | Empty results, limits | As needed |

#### 3b: Happy Path Tests

```typescript
// tests/integration/api/projects.api.test.ts
import request from 'supertest';
import { app } from '@/app';
import { createTestUser, createTestProject } from '../../factories';

describe('POST /api/projects', () => {
  /**
   * @spec API-SURFACE.md#POST-/api/projects
   * @verifies Creates project and returns 201
   */
  it('creates project with valid data', async () => {
    const token = await getAuthToken();
    
    const response = await request(app)
      .post('/api/projects')
      .set('Authorization', `Bearer ${token}`)
      .send({ name: 'Test Project', description: 'A test' });
    
    expect(response.status).toBe(201);
    expect(response.body).toMatchObject({
      id: expect.any(String),
      name: 'Test Project',
      description: 'A test',
    });
  });
});
```

#### 3c: Validation Tests

```typescript
describe('POST /api/projects - validation', () => {
  /**
   * @spec ERROR-CODES.md#VALIDATION_ERROR
   * @verifies Returns 400 for missing required field
   */
  it('returns 400 for missing name', async () => {
    const token = await getAuthToken();
    
    const response = await request(app)
      .post('/api/projects')
      .set('Authorization', `Bearer ${token}`)
      .send({ description: 'No name' });
    
    expect(response.status).toBe(400);
    expect(response.body).toMatchObject({
      error_code: 'VALIDATION_ERROR',
      message: expect.stringContaining('name'),
    });
  });

  it('returns 400 for name too short', async () => {
    const token = await getAuthToken();
    
    const response = await request(app)
      .post('/api/projects')
      .set('Authorization', `Bearer ${token}`)
      .send({ name: 'a' }); // Min length 3
    
    expect(response.status).toBe(400);
  });
});
```

#### 3d: Authentication Tests

```typescript
describe('POST /api/projects - authentication', () => {
  /**
   * @spec API-SURFACE.md#Authentication
   * @verifies Returns 401 for missing token
   */
  it('returns 401 without token', async () => {
    const response = await request(app)
      .post('/api/projects')
      .send({ name: 'Test' });
    
    expect(response.status).toBe(401);
    expect(response.body.error_code).toBe('UNAUTHORIZED');
  });

  it('returns 401 for invalid token', async () => {
    const response = await request(app)
      .post('/api/projects')
      .set('Authorization', 'Bearer invalid-token')
      .send({ name: 'Test' });
    
    expect(response.status).toBe(401);
  });
});
```

#### 3e: Authorization Tests

```typescript
describe('PUT /api/projects/:id - authorization', () => {
  /**
   * @spec API-SURFACE.md#Authorization
   * @verifies Returns 403 when user doesn't own project
   */
  it('returns 403 for non-owner', async () => {
    const owner = await createTestUser();
    const project = await createTestProject({ ownerId: owner.id });
    
    const otherUser = await createTestUser();
    const otherToken = await getAuthTokenFor(otherUser);
    
    const response = await request(app)
      .put(`/api/projects/${project.id}`)
      .set('Authorization', `Bearer ${otherToken}`)
      .send({ name: 'Hijacked' });
    
    expect(response.status).toBe(403);
    expect(response.body.error_code).toBe('FORBIDDEN');
  });
});
```

#### 3f: Not Found Tests

```typescript
describe('GET /api/projects/:id - not found', () => {
  /**
   * @spec ERROR-CODES.md#NOT_FOUND
   * @verifies Returns 404 for invalid ID
   */
  it('returns 404 for non-existent project', async () => {
    const token = await getAuthToken();
    
    const response = await request(app)
      .get('/api/projects/non-existent-id')
      .set('Authorization', `Bearer ${token}`);
    
    expect(response.status).toBe(404);
    expect(response.body.error_code).toBe('NOT_FOUND');
  });
});
```

### Step 4: Test Database Operations

```typescript
// tests/integration/db/users.db.test.ts
import { db } from '@/lib/db';
import { createTestUser } from '../../factories';

describe('User database operations', () => {
  it('creates user record', async () => {
    const userData = createTestUser();
    
    const created = await db.user.create({ data: userData });
    
    expect(created.id).toBeDefined();
    expect(created.email).toBe(userData.email);
  });

  it('finds user by email', async () => {
    const user = await createTestUser();
    await db.user.create({ data: user });
    
    const found = await db.user.findByEmail(user.email);
    
    expect(found).toBeDefined();
    expect(found?.id).toBe(user.id);
  });

  it('enforces unique email constraint', async () => {
    const user = await createTestUser();
    await db.user.create({ data: user });
    
    await expect(
      db.user.create({ data: { ...user, id: 'different-id' } })
    ).rejects.toThrow();
  });
});
```

### Step 5: Update TEST-TRACE.md

Update API Endpoint Coverage section:

```markdown
| Endpoint | Method | Test File | Test Name | Status |
|----------|--------|-----------|-----------|--------|
| `/api/projects` | POST | integration/projects.api.test.ts | creates project with valid data | ✅ |
| `/api/projects` | POST | integration/projects.api.test.ts | returns 400 for missing name | ✅ |
| `/api/projects` | POST | integration/projects.api.test.ts | returns 401 without token | ✅ |
```

Update Error Code Coverage section:

```markdown
| Error Code | Test File | Test Name | Status |
|------------|-----------|-----------|--------|
| VALIDATION_ERROR | integration/projects.api.test.ts | returns 400 for missing name | ✅ |
| UNAUTHORIZED | integration/projects.api.test.ts | returns 401 without token | ✅ |
```

### Step 6: Run and Verify

```bash
# Run integration tests
npm run test:integration

# Verify all pass
```

---

## Output

By end of Phase 56, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| Integration test files | tests/integration/ | ☐ Created |
| TEST-TRACE.md | Project root | ☐ API section updated |
| All endpoints covered | TEST-TRACE.md | ☐ 100% |

---

## Checklist

Before proceeding to Phase 57:

- [ ] All endpoints have happy path tests
- [ ] All endpoints have validation tests (if applicable)
- [ ] All protected endpoints have auth tests
- [ ] All ownership endpoints have authorization tests
- [ ] All parameterized endpoints have not found tests
- [ ] All error codes are triggered
- [ ] All tests pass
- [ ] TEST-TRACE.md updated

---

## Test Patterns

### Pattern: Test File Structure

```typescript
describe('{{METHOD}} {{endpoint}}', () => {
  describe('happy path', () => {
    it('{{expected behaviour}}', async () => {});
  });
  
  describe('validation', () => {
    it('returns 400 for missing required field', async () => {});
    it('returns 400 for invalid format', async () => {});
  });
  
  describe('authentication', () => {
    it('returns 401 without token', async () => {});
    it('returns 401 for invalid token', async () => {});
  });
  
  describe('authorization', () => {
    it('returns 403 for non-owner', async () => {});
  });
  
  describe('not found', () => {
    it('returns 404 for non-existent resource', async () => {});
  });
});
```

### Pattern: Database Reset

```typescript
// Transaction rollback (fast)
beforeEach(async () => {
  await db.$transaction(async (tx) => {
    // Tests run inside transaction
  });
});

// Truncate (clean slate)
beforeEach(async () => {
  await db.project.deleteMany();
  await db.user.deleteMany();
});
```

### Pattern: Auth Helper

```typescript
async function authenticatedRequest(
  method: 'get' | 'post' | 'put' | 'delete',
  url: string,
  body?: object
) {
  const token = await getAuthToken();
  const req = request(app)[method](url)
    .set('Authorization', `Bearer ${token}`);
  
  if (body) req.send(body);
  return req;
}
```

---

## Rules

1. **Test against API-SURFACE.md** — Every endpoint must have tests
2. **Use test factories** — Don't rely on seed data
3. **Isolate tests** — Reset database between tests
4. **Test all error codes** — Every error code in ERROR-CODES.md must be triggered
5. **No external services** — Mock third-party APIs

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Tests pollute each other | Ensure proper database reset |
| Auth token issues | Create fresh user per test or test group |
| Flaky async tests | Ensure proper awaiting, check for race conditions |
| Missing error tests | Cross-reference ERROR-CODES.md |

---
Generated: {{timestamp}}
Phase: 56 (Integration Testing)
Section: 0g Testing
---
