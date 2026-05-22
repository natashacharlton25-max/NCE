# Phase 58: E2E Testing

---
Section: 0g Testing
Phase: 58 of 60
Previous: Phase 57 (Component Testing)
Next: Phase 59 (Accessibility Testing)
Collapsible: Yes (can combine with 59)
---

## Purpose

Write end-to-end tests for complete user flows, testing the application as a user would experience it.

---

## Inputs Required

| Document | Location | Purpose |
|----------|----------|---------|
| PAGES.md | 0e output | Pages and flows |
| USER-JOURNEYS.md | 0e output | User flows to test |
| API-SURFACE.md | 0c/0d output | Endpoints involved |

---

## Critical Requirements

### 1. Test Complete User Flows

E2E tests simulate real user behaviour across multiple pages.

### 2. Include Negative Flows

**Required:** At least one negative flow per feature.

### 3. Verify Database State

**Required:** After mutations, verify state in database, not just UI.

---

## Steps

### Step 1: List User Flows

From PAGES.md and USER-JOURNEYS.md, identify flows:

```markdown
| Flow | Pages | Type | Priority |
|------|-------|------|----------|
| User Registration | /register → /verify → /dashboard | Happy | High |
| User Login | /login → /dashboard | Happy | High |
| Invalid Login | /login | Negative | High |
| Create Project | /dashboard → /projects/new → /projects/:id | Happy | High |
| Edit Project | /projects/:id → /projects/:id/edit → /projects/:id | Happy | Medium |
| Delete Project | /projects/:id → confirm → /projects | Happy | Medium |
| Forbidden Access | /projects/:id (other user's) | Negative | High |
```

### Step 2: Set Up E2E Framework

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  webServer: {
    // Use production build for stability and accuracy
    command: 'npm run build && npm run start',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
    timeout: 120000, // Allow time for build
  },
});
```

**Why production build?**
- Dev servers are slower and less stable
- Tests should run against the actual shipped code
- Catches build-time issues before deployment

### Step 3: Create Test Helpers

```typescript
// tests/e2e/helpers/auth.ts
import { Page } from '@playwright/test';
import { db } from '@/lib/db';
import { createTestUser } from '../../factories';

export async function createUserAndLogin(page: Page) {
  // Create user in database
  const user = createTestUser({ password: 'testpassword123' });
  await db.user.create({ data: user });
  
  // Login via UI
  await page.goto('/login');
  await page.fill('[name="email"]', user.email);
  await page.fill('[name="password"]', 'testpassword123');
  await page.click('button[type="submit"]');
  
  // Wait for redirect
  await page.waitForURL('/dashboard');
  
  return user;
}

export async function loginAsUser(page: Page, email: string, password: string) {
  await page.goto('/login');
  await page.fill('[name="email"]', email);
  await page.fill('[name="password"]', password);
  await page.click('button[type="submit"]');
}
```

```typescript
// tests/e2e/helpers/db.ts
import { db } from '@/lib/db';

export async function resetDatabase() {
  await db.project.deleteMany();
  await db.user.deleteMany();
}

export async function verifyInDatabase<T>(
  table: string,
  query: object
): Promise<T | null> {
  return db[table].findFirst({ where: query });
}
```

#### Database Access Security

**Critical Rule:** E2E tests must use test credentials only.

```typescript
// tests/e2e/helpers/db.ts
import { PrismaClient } from '@prisma/client';

// Verify we're using test database
const TEST_DB_URL = process.env.TEST_DATABASE_URL;
if (!TEST_DB_URL || !TEST_DB_URL.includes('_test')) {
  throw new Error('E2E tests must use TEST_DATABASE_URL with "_test" suffix');
}

export const db = new PrismaClient({
  datasources: { db: { url: TEST_DB_URL } },
});
```

**Safeguards:**
- Test database URL must be explicitly different from production
- Add runtime check to prevent accidental production access
- Never hardcode production credentials in test files
```

### Step 4: Write Happy Path E2E Tests

#### 4a: Authentication Flow

```typescript
// tests/e2e/auth.e2e.ts
import { test, expect } from '@playwright/test';
import { resetDatabase } from './helpers/db';
import { createTestUser } from '../factories';

test.describe('Authentication', () => {
  test.beforeEach(async () => {
    await resetDatabase();
  });

  /**
   * @spec PAGES.md#Login
   * @verifies User can log in successfully
   */
  test('user can log in with valid credentials', async ({ page }) => {
    // Setup: Create user
    const user = createTestUser({ password: 'testpassword123' });
    await db.user.create({ data: user });
    
    // Act: Login
    await page.goto('/login');
    await page.fill('[name="email"]', user.email);
    await page.fill('[name="password"]', 'testpassword123');
    await page.click('button[type="submit"]');
    
    // Assert: Redirected to dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('h1')).toContainText('Dashboard');
  });

  /**
   * @spec PAGES.md#Register
   * @verifies User can register new account
   */
  test('user can register new account', async ({ page }) => {
    // Act: Register
    await page.goto('/register');
    await page.fill('[name="name"]', 'Test User');
    await page.fill('[name="email"]', 'newuser@example.com');
    await page.fill('[name="password"]', 'securepassword123');
    await page.fill('[name="confirmPassword"]', 'securepassword123');
    await page.click('button[type="submit"]');
    
    // Assert: Account created
    await expect(page).toHaveURL('/dashboard');
    
    // Verify in database
    const user = await db.user.findFirst({
      where: { email: 'newuser@example.com' }
    });
    expect(user).toBeDefined();
    expect(user?.name).toBe('Test User');
  });
});
```

#### 4b: CRUD Flow

```typescript
// tests/e2e/projects.e2e.ts
import { test, expect } from '@playwright/test';
import { createUserAndLogin, resetDatabase, verifyInDatabase } from './helpers';

test.describe('Projects', () => {
  test.beforeEach(async ({ page }) => {
    await resetDatabase();
    await createUserAndLogin(page);
  });

  /**
   * @spec PAGES.md#Projects
   * @verifies User can create a project
   */
  test('user can create a project', async ({ page }) => {
    // Navigate to create
    await page.click('text=New Project');
    await expect(page).toHaveURL('/projects/new');
    
    // Fill form
    await page.fill('[name="name"]', 'E2E Test Project');
    await page.fill('[name="description"]', 'Created via E2E test');
    await page.click('button[type="submit"]');
    
    // Assert: Redirected to project
    await expect(page).toHaveURL(/\/projects\/[\w-]+$/);
    await expect(page.locator('h1')).toContainText('E2E Test Project');
    
    // REQUIRED: Verify in database
    const project = await verifyInDatabase('project', {
      name: 'E2E Test Project'
    });
    expect(project).toBeDefined();
    expect(project?.description).toBe('Created via E2E test');
  });

  /**
   * @spec PAGES.md#Projects
   * @verifies User can edit a project
   */
  test('user can edit a project', async ({ page }) => {
    // Setup: Create project
    const project = await db.project.create({
      data: { name: 'Original Name', ownerId: currentUser.id }
    });
    
    // Navigate to edit
    await page.goto(`/projects/${project.id}`);
    await page.click('text=Edit');
    
    // Update
    await page.fill('[name="name"]', 'Updated Name');
    await page.click('button[type="submit"]');
    
    // Assert
    await expect(page.locator('h1')).toContainText('Updated Name');
    
    // Verify in database
    const updated = await verifyInDatabase('project', { id: project.id });
    expect(updated?.name).toBe('Updated Name');
  });

  /**
   * @spec PAGES.md#Projects
   * @verifies User can delete a project
   */
  test('user can delete a project', async ({ page }) => {
    // Setup
    const project = await db.project.create({
      data: { name: 'To Delete', ownerId: currentUser.id }
    });
    
    // Navigate and delete
    await page.goto(`/projects/${project.id}`);
    await page.click('text=Delete');
    
    // Confirm dialog
    await page.click('text=Confirm Delete');
    
    // Assert: Redirected to list
    await expect(page).toHaveURL('/projects');
    await expect(page.locator('text=To Delete')).not.toBeVisible();
    
    // Verify in database
    const deleted = await verifyInDatabase('project', { id: project.id });
    expect(deleted).toBeNull();
  });
});
```

### Step 5: Write Negative Flow E2E Tests

```typescript
// tests/e2e/auth-negative.e2e.ts
test.describe('Authentication - Negative Flows', () => {
  /**
   * @spec ERROR-CODES.md#AUTH_001
   * @verifies Invalid login shows error
   */
  test('shows error for invalid password', async ({ page }) => {
    // Setup: Create user
    const user = createTestUser({ password: 'realpassword' });
    await db.user.create({ data: user });
    
    // Act: Login with wrong password
    await page.goto('/login');
    await page.fill('[name="email"]', user.email);
    await page.fill('[name="password"]', 'wrongpassword');
    await page.click('button[type="submit"]');
    
    // Assert: Still on login, error shown
    await expect(page).toHaveURL('/login');
    await expect(page.locator('[role="alert"]')).toContainText(/invalid/i);
  });

  /**
   * @spec ERROR-CODES.md#AUTH_002
   * @verifies Shows error for non-existent user
   */
  test('shows error for non-existent user', async ({ page }) => {
    await page.goto('/login');
    await page.fill('[name="email"]', 'nonexistent@example.com');
    await page.fill('[name="password"]', 'anypassword');
    await page.click('button[type="submit"]');
    
    await expect(page.locator('[role="alert"]')).toBeVisible();
  });
});

// tests/e2e/projects-negative.e2e.ts
test.describe('Projects - Negative Flows', () => {
  /**
   * @spec ERROR-CODES.md#FORBIDDEN
   * @verifies Cannot access other user's project
   */
  test('cannot access another user\'s project', async ({ page }) => {
    // Setup: Create owner and project
    const owner = await db.user.create({ data: createTestUser() });
    const project = await db.project.create({
      data: { name: 'Private', ownerId: owner.id }
    });
    
    // Login as different user
    const otherUser = createTestUser();
    await db.user.create({ data: otherUser });
    await loginAsUser(page, otherUser.email, 'testpassword123');
    
    // Try to access
    await page.goto(`/projects/${project.id}`);
    
    // Assert: Access denied
    await expect(page.locator('text=Access Denied')).toBeVisible();
    // OR redirected
    // await expect(page).toHaveURL('/dashboard');
  });

  /**
   * @spec ERROR-CODES.md#VALIDATION_ERROR
   * @verifies Form validation prevents submission
   */
  test('cannot create project with empty name', async ({ page }) => {
    await createUserAndLogin(page);
    
    await page.goto('/projects/new');
    await page.fill('[name="name"]', '');
    await page.click('button[type="submit"]');
    
    // Assert: Validation error shown
    await expect(page.locator('[name="name"]')).toHaveAttribute('aria-invalid', 'true');
    await expect(page.locator('text=Name is required')).toBeVisible();
    
    // Still on same page
    await expect(page).toHaveURL('/projects/new');
  });
});
```

### Step 6: Update TEST-TRACE.md

Update Page/Flow Coverage section:

```markdown
| Page | Flow | Test File | Test Name | Status |
|------|------|-----------|-----------|--------|
| Login | Happy path | e2e/auth.e2e.ts | user can log in | ✅ |
| Login | Invalid credentials | e2e/auth-negative.e2e.ts | shows error for invalid password | ✅ |
| Projects | Create | e2e/projects.e2e.ts | user can create project | ✅ |
| Projects | Forbidden | e2e/projects-negative.e2e.ts | cannot access other user's project | ✅ |
```

### Step 7: Run and Verify

```bash
# Run E2E tests
npm run test:e2e

# Run with UI for debugging
npx playwright test --ui
```

---

## Output

By end of Phase 58, you must have:

| Artifact | Location | Status |
|----------|----------|--------|
| E2E test files | tests/e2e/ | ☐ Created |
| TEST-TRACE.md | Project root | ☐ Flow section updated |
| All flows covered | TEST-TRACE.md | ☐ Happy + negative |

---

## Checklist

Before proceeding to Phase 59:

- [ ] All happy path flows have tests
- [ ] All features have at least one negative flow test
- [ ] All CRUD operations verified in database
- [ ] All auth flows tested
- [ ] All forbidden access flows tested
- [ ] All validation error flows tested
- [ ] All tests pass
- [ ] TEST-TRACE.md updated

---

## Test Patterns

### Pattern: Database Verification After Mutation

```typescript
// ALWAYS verify mutations in database
test('creates item', async ({ page }) => {
  // ... UI actions ...
  
  // Verify in database
  const item = await db.item.findFirst({ where: { name: 'Created' } });
  expect(item).toBeDefined();
  expect(item?.field).toBe('expected');
});
```

### Pattern: Testing Form Validation

```typescript
test('validates required fields', async ({ page }) => {
  await page.click('button[type="submit"]');
  
  await expect(page.locator('[aria-invalid="true"]')).toHaveCount(3);
  await expect(page.locator('text=Required')).toHaveCount(3);
});
```

### Pattern: Testing Auth Flows

```typescript
test('redirects unauthenticated user', async ({ page }) => {
  await page.goto('/protected-page');
  await expect(page).toHaveURL('/login?redirect=/protected-page');
});
```

---

## Rules

1. **Verify database state** — UI confirmation is not enough
2. **Test negative flows** — At least one per feature
3. **Isolate tests** — Reset database between tests
4. **Use realistic data** — Test with production-like data shapes
5. **Keep tests independent** — No test should depend on another

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Tests interfere with each other | Reset database in beforeEach |
| Flaky due to timing | Use proper waitFor, avoid fixed delays |
| Can't find element | Check element is visible, use proper selectors |
| Database not reset | Verify reset function is called and awaited |

---
Generated: {{timestamp}}
Phase: 58 (E2E Testing)
Section: 0g Testing
---
