# Claude Testing Instructions

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Instructions for Claude Code during 0g Testing (Phases 54-60)
---

## Your Role

You are writing and executing tests that verify the implementation from 0f matches the specifications from 0c-0e.

**You test against specs, not implementation details.**

---

## Context for Claude

When executing 0g Testing phases, remember:

### Spec Authority
You have access to specs from 0c-0e. These are the source of truth.

**If you see code in 0f that does not match those specs:**
1. File a BUG-REPORT
2. Do NOT "fix" the test to match the code
3. The spec is correct; the code is wrong

### What You Know
- API-SURFACE.md defines endpoint behaviour
- TYPES.md defines data shapes
- ERROR-CODES.md defines error responses
- COMPONENTS.md defines UI behaviour
- PAGES.md defines page flows

### What You Must Not Do
- Assume code is correct when it differs from spec
- Invent test scenarios not derived from specs
- Skip traceability annotations
- Accept flaky tests as "good enough"

### When Uncertain
If a spec is ambiguous:
1. Document the ambiguity
2. Make a reasonable assumption
3. Note the assumption in the test
4. Flag for human review

---

## Core Rules

### 1. Test Against Specs

```
✅ DO: "POST /api/projects returns 201 with project data" (from API-SURFACE.md)
❌ DON'T: "projectService.create() calls database" (implementation detail)
```

Your tests verify **behaviour**, not **how it's achieved**.

### 2. Tests Must Be Deterministic

Every test must:
- Pass or fail consistently
- Not depend on execution order
- Not depend on real time
- Not depend on randomness
- Not depend on external services

If a test is flaky, it's a bug — fix it before proceeding.

### 3. You Find, You Don't Fix

When you discover a bug:
1. Document it in a BUG-REPORT
2. Continue testing (don't stop)
3. Implementation fixes happen in 0f, not 0g

**Never modify implementation code during testing.**

### 4. Traceability Is Mandatory

Every test must trace to a spec. Use this format:

```typescript
/**
 * @spec API-SURFACE.md#POST-/api/projects
 * @verifies Project creation returns 201 with created project
 */
it('creates project with valid data', async () => {
  // ...
});
```

Update TEST-TRACE.md as you write tests.

### 5. Specs Are the Source of Truth

If a test fails because code doesn't match spec:
- ✅ File a BUG-REPORT
- ❌ Do NOT modify the test to match the code

If a test fails because spec is unclear:
- ✅ Document assumption
- ✅ Write test based on reasonable interpretation
- ✅ Flag for review

### 6. Test File Location Is Flexible

Tests may live:
- In `/tests/` (centralised)
- Co-located with source as `*.test.ts` (distributed)

Choose one pattern and apply consistently. Document in TEST-PLAN.md.

---

## Phase-by-Phase Instructions

### Phase 54: Test Planning

**Read first:**
- 0f-to-0g-HANDOFF.md
- All specs from 0c/0d/0e that need testing

**Create:**
- TEST-PLAN.md using template
- Test folder structure
- Test data factories

**Verify:**
- Test framework configured
- Test environment isolated
- `npm test` exits 1 on failure (CI-ready)

### Phase 55: Unit Testing

**Read first:**
- TYPES.md, ERROR-CODES.md
- Utility and helper files in implementation

**Test:**
- Utility functions
- Business logic functions
- Validators
- Data transformations

**Don't test:**
- Framework code
- Third-party libraries
- Simple getters/setters

**Pattern:**
```typescript
describe('validateEmail', () => {
  it('returns true for valid email', () => {
    expect(validateEmail('user@example.com')).toBe(true);
  });
  
  it('returns false for invalid email', () => {
    expect(validateEmail('not-an-email')).toBe(false);
  });
});
```

### Phase 56: Integration Testing

**Read first:**
- API-SURFACE.md
- ERROR-CODES.md
- DATABASE-SCHEMA.md

**Test every endpoint for:**
| Category | Example |
|----------|---------|
| Happy path | Valid request → correct response |
| Validation | Invalid input → 400 + error |
| Auth | No token → 401 |
| Authorization | Wrong user → 403 |
| Not found | Invalid ID → 404 |

**Pattern:**
```typescript
describe('POST /api/projects', () => {
  it('creates project with valid data', async () => {
    const response = await request(app)
      .post('/api/projects')
      .set('Authorization', `Bearer ${token}`)
      .send({ name: 'Test Project' });
    
    expect(response.status).toBe(201);
    expect(response.body).toMatchObject({
      id: expect.any(String),
      name: 'Test Project',
    });
  });
});
```

### Phase 57: Component Testing

**Read first:**
- COMPONENTS.md
- DESIGN-SYSTEM.md

**Critical rule:** Use mocks for data providers. Components must test without live backend.

**Test:**
- Props handling
- Event emission
- User interactions
- Visual states
- Accessibility basics

**Pattern:**
```typescript
describe('Button', () => {
  it('renders with children', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });
  
  it('calls onClick when clicked', async () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    await userEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledOnce();
  });
});
```

### Phase 58: E2E Testing

**Read first:**
- PAGES.md
- User flows from specs

**Test:**
- All critical user journeys
- At least one negative flow per feature
- Database state after mutations

**Required: Database Verification**

```typescript
// After UI creates project
await page.click('button[type="submit"]');
await expect(page.locator('text=Project created')).toBeVisible();

// Verify in database (not just UI)
const project = await db.project.findFirst({
  where: { name: 'E2E Test Project' }
});
expect(project).toBeDefined();
```

**Required: Negative Flows**

- Invalid login attempt
- Forbidden access attempt
- Form validation failure

### Phase 59: Accessibility Testing

**Read first:**
- A11Y-CHECKLIST.md
- COMPONENTS.md accessibility sections

**Automated tests:**
```typescript
it('has no accessibility violations', async () => {
  const { container } = render(<HomePage />);
  const results = await axe(container);
  expect(results.violations).toHaveLength(0);
});
```

**Manual tests (document results):**
- [ ] Tab through entire app
- [ ] Complete flows with keyboard only
- [ ] Test with screen reader
- [ ] Verify focus indicators

### Phase 60: Test Verification

**Run:**
1. All tests (must pass)
2. Flakiness check (3x consecutive)
3. Coverage report

**Create:**
- TEST-TRACE.md (complete)
- COVERAGE-REPORT.md
- TEST-RESULTS.md

**Verify:**
- All coverage targets met
- All specs have test coverage
- All bugs resolved or documented

---

## Test Data Rules

### Don't Use Dev Seeds

```typescript
// ❌ WRONG - relies on seed data
it('finds user', async () => {
  const user = await db.user.findByEmail('admin@seed.com');
  expect(user).toBeDefined();
});

// ✅ RIGHT - creates own test data
it('finds user', async () => {
  const user = await createTestUser({ email: 'test@example.com' });
  const found = await db.user.findByEmail('test@example.com');
  expect(found.id).toBe(user.id);
});
```

### Use Factories

```typescript
// test/factories/user.ts
export function createTestUser(overrides?: Partial<User>): User {
  return {
    id: faker.string.uuid(),
    email: faker.internet.email(),
    name: faker.person.fullName(),
    ...overrides,
  };
}
```

---

## Bug Reporting

When you find a bug:

1. **Create BUG-REPORT** using template
2. **Include:**
   - Steps to reproduce
   - Expected behaviour (from spec)
   - Actual behaviour
   - Severity classification
3. **Continue testing** — don't stop
4. **Don't fix** — that's 0f's job

---

## Output Checklist

By end of 0g, you must have:

- [ ] TEST-PLAN.md
- [ ] All test files (unit, integration, component, E2E, a11y)
- [ ] TEST-TRACE.md (every test → spec)
- [ ] COVERAGE-REPORT.md
- [ ] TEST-RESULTS.md
- [ ] BUG-REPORTS/ (any bugs found)
- [ ] All tests passing
- [ ] Flakiness check passed (3x)
- [ ] All coverage targets met

---

## When Stuck

### Test keeps failing
1. Check if it's a real bug (log a BUG-REPORT)
2. Check if test is wrong (fix the test)
3. Check if spec is ambiguous (document, proceed with assumption)

### Flaky test
1. Find the non-deterministic element
2. Mock time, random, external services
3. Ensure test isolation
4. **Do not proceed until fixed**

### Unsure what to test
1. Re-read the relevant spec
2. Test the behaviour described in spec
3. Test error cases from ERROR-CODES.md
4. Ask for clarification if spec is unclear

---

## Quick Reference

| Rule | Action |
|------|--------|
| Test what? | Spec behaviour, not implementation |
| Flaky test? | Fix before proceeding |
| Found bug? | Document, don't fix |
| Test data? | Use factories, not seeds |
| Components? | Mock the backend |
| E2E mutations? | Verify database state |
| Missing spec? | Document, proceed with assumption |
| Phase 60? | 3x flakiness check required |

---
Generated: {{timestamp}}
Section: 0g Testing
For: Claude Code
---
