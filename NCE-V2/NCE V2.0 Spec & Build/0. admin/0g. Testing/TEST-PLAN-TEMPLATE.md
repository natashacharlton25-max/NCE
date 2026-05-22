# TEST-PLAN.md Template

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Define testing strategy, scope, and environment
Fill out: During Phase 54 (Test Planning)
---

# Test Plan

## Project Information

| Field | Value |
|-------|-------|
| Project Name | {{project_name}} |
| Version | {{version}} |
| Date | {{date}} |
| Author | {{author}} |

---

## Testing Strategy

### Test Pyramid

| Layer | Scope | Priority |
|-------|-------|----------|
| Unit | Business logic, utilities, validators | High |
| Integration | API endpoints, database operations | High |
| Component | UI components with mocked data | Medium |
| E2E | Critical user flows | Medium |
| Accessibility | A11y compliance | Medium |

### Coverage Targets

| Type | Target | Minimum | Enforcement |
|------|--------|---------|-------------|
| Unit tests | {{target}}% | 70% | Phase 60 gate |
| Integration tests | All endpoints | All critical paths | Phase 60 gate |
| Component tests | All components | All interactive | Phase 60 gate |
| E2E tests | All flows | Happy paths + negatives | Phase 60 gate |

---

## Testing Tools

### Framework Selection

| Purpose | Tool | Version | Rationale |
|---------|------|---------|-----------|
| Test runner | {{tool}} | {{version}} | {{why}} |
| Assertions | {{tool}} | {{version}} | {{why}} |
| Mocking | {{tool}} | {{version}} | {{why}} |
| API testing | {{tool}} | {{version}} | {{why}} |
| Component testing | {{tool}} | {{version}} | {{why}} |
| E2E testing | {{tool}} | {{version}} | {{why}} |
| Accessibility | {{tool}} | {{version}} | {{why}} |
| Coverage | {{tool}} | {{version}} | {{why}} |

### Example Configuration

```json
// package.json scripts
{
  "scripts": {
    "test": "{{test_command}}",
    "test:unit": "{{unit_command}}",
    "test:integration": "{{integration_command}}",
    "test:e2e": "{{e2e_command}}",
    "test:coverage": "{{coverage_command}}"
  }
}
```

---

## Test Environment

### Environment Isolation

| Environment | Database | Purpose |
|-------------|----------|---------|
| Development | {{dev_db}} | Local development |
| Test | {{test_db}} | Automated testing |
| CI | {{ci_db}} | Pipeline testing |

**Rule:** Tests run against test environment only, never development.

### Environment Variables

| Variable | Test Value | Description |
|----------|------------|-------------|
| DATABASE_URL | {{test_db_url}} | Test database connection |
| {{var}} | {{value}} | {{description}} |

### Reset Strategy

| Strategy | Used For | Implementation |
|----------|----------|----------------|
| {{strategy}} | {{test_type}} | {{how}} |

Options:
- Transaction rollback (fast, unit/integration)
- Truncate + re-seed (E2E)
- Snapshot restore (complex E2E)

---

## Test Data Strategy

### Approach

- [ ] Factory functions (dynamic generation)
- [ ] Test fixtures (static data files)
- [ ] Combination

### Factory Location

```
tests/
├── factories/
│   ├── user.factory.ts
│   ├── project.factory.ts
│   └── index.ts
```

### Factory Pattern

```typescript
// Example factory
import { faker } from '@faker-js/faker';

export function createTestUser(overrides?: Partial<User>): User {
  return {
    id: faker.string.uuid(),
    email: faker.internet.email(),
    name: faker.person.fullName(),
    ...overrides,
  };
}
```

### Seed Data Dependency

- [ ] Tests DO NOT rely on development seeds
- [ ] Tests create their own data via factories
- [ ] Edge case data is explicitly constructed

---

## Test Scope

### In Scope

| Area | What Gets Tested |
|------|------------------|
| Unit | {{list}} |
| Integration | {{list}} |
| Component | {{list}} |
| E2E | {{list}} |
| Accessibility | {{list}} |

### Out of Scope

| Area | Why Excluded | Where Covered |
|------|--------------|---------------|
| Performance | 0h concern | 0h Prerelease |
| Security | 0h concern | 0h Prerelease |
| Load testing | 0h concern | 0h Prerelease |
| {{other}} | {{reason}} | {{where}} |

---

## Test Organization

### Folder Structure

```
tests/
├── unit/
│   ├── utils/
│   └── services/
├── integration/
│   ├── api/
│   └── db/
├── component/
│   └── {{framework}}/
├── e2e/
│   └── flows/
├── a11y/
├── factories/
├── fixtures/
└── helpers/
```

### Naming Convention

| Type | Pattern | Example |
|------|---------|---------|
| Unit | `{{name}}.test.ts` | `validateEmail.test.ts` |
| Integration | `{{resource}}.api.test.ts` | `projects.api.test.ts` |
| Component | `{{Component}}.test.tsx` | `Button.test.tsx` |
| E2E | `{{flow}}.e2e.ts` | `login.e2e.ts` |

---

## Spec Coverage Map

### API Endpoints (from API-SURFACE.md)

| Endpoint | Test File | Status |
|----------|-----------|--------|
| `POST /api/auth/login` | | ☐ |
| `POST /api/auth/logout` | | ☐ |
| {{endpoint}} | | ☐ |

### Components (from COMPONENTS.md)

| Component | Test File | Status |
|-----------|-----------|--------|
| Button | | ☐ |
| Input | | ☐ |
| {{component}} | | ☐ |

### Pages (from PAGES.md)

| Page | E2E Flow | Status |
|------|----------|--------|
| Login | | ☐ |
| Dashboard | | ☐ |
| {{page}} | | ☐ |

---

## CI Configuration

### Test Command

```bash
# Must exit 1 on failure
npm test
```

### CI Requirements

- [ ] Tests run on every PR
- [ ] Tests run on merge to main
- [ ] Coverage report generated
- [ ] Failure blocks merge

---

## Risk Areas

### High Priority Testing

| Area | Risk | Test Focus |
|------|------|------------|
| Authentication | Security critical | All auth flows |
| Data mutations | Data integrity | Create/update/delete |
| {{area}} | {{risk}} | {{focus}} |

### Known Edge Cases

| Scenario | Test Required |
|----------|---------------|
| {{scenario}} | {{test}} |

---

## Dependencies

### External Services to Mock

| Service | Mock Strategy |
|---------|---------------|
| {{service}} | {{how}} |

### Test Dependencies

| Package | Purpose |
|---------|---------|
| {{package}} | {{purpose}} |

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Author | | | ☐ Plan Complete |
| Reviewer | | | ☐ Plan Approved |

---
Generated: {{timestamp}}
Phase: 54 (Test Planning)
---
