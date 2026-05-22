# Phase 52: Integration

---
Phase: 52
Section: 0f. Implementation
Name: Integration
Purpose: Connect frontend to backend
---

## Role

You are integrating frontend with backend. Your job is to wire up all data fetching, mutations, and state management so the frontend communicates with the verified backend from Phase 47.

---

## Inputs

| Document | Location | What to Extract |
|----------|----------|-----------------|
| FRONTEND-BACKEND-CONTRACT.md | 0e outputs | API contract |
| API-MAPPING.md | 0e outputs | Component-to-endpoint mapping |
| STATE-MANAGEMENT.md | 0e outputs | State patterns |
| shared-types/ | From Phase 43 | Request/response types |
| Backend | From Phase 47 | Verified, running API |
| Frontend | From Phases 48-51 | Pages and components |

---

## Process

### Step 1: Review API-MAPPING.md

Extract all component-to-endpoint connections:

| Component/Page | Endpoint | Method | Purpose |
|----------------|----------|--------|---------|
| LoginForm | /auth/login | POST | User login |
| Dashboard | /users/me | GET | Current user |
| Dashboard | /projects | GET | User's projects |
| ProjectList | /projects | GET | List projects |
| ProjectForm | /projects | POST | Create project |
| ProjectDetail | /projects/:id | GET | Single project |
| ProjectForm | /projects/:id | PUT | Update project |
| DeleteButton | /projects/:id | DELETE | Delete project |

### Step 2: Implement API Client

Complete the API client from Phase 48:

**Requirements:**
- Base URL from environment
- Auth token handling (get, set, clear)
- Request methods (GET, POST, PUT, DELETE)
- Error handling (transform to typed errors)
- Response typing (using shared-types/)

**Implementation depends on TECH-STACK.md** (fetch, axios, etc.)

### Step 3: Wire Each Connection

For each entry in API-MAPPING.md:

#### 3a. Create API Function

```
// Pattern (adapt to your approach)
function getProjects(): Promise<PaginatedResponse<Project>>
function createProject(data: ProjectCreate): Promise<Project>
function updateProject(id: string, data: ProjectUpdate): Promise<Project>
function deleteProject(id: string): Promise<void>
```

**Use types from shared-types/.**

#### 3b. Connect to Component/Page

Wire the API function to the component:

| Step | Action |
|------|--------|
| 1 | Call API on appropriate trigger (mount, submit, etc.) |
| 2 | Handle loading state |
| 3 | Handle success (update state, show message) |
| 4 | Handle error (show error state/message) |

#### 3c. Update State

Per STATE-MANAGEMENT.md:

- Update global state (if applicable)
- Update local state
- Invalidate/refetch related data (if needed)

### Step 4: Implement Auth Flow

Complete authentication integration:

| Flow | Steps |
|------|-------|
| Login | Submit → API call → Store token → Update user state → Redirect |
| Logout | Clear token → Clear user state → Redirect to login |
| Auth check | Check token → Fetch user → Update state (or redirect) |
| Token refresh | Detect expiry → Refresh → Continue (or logout) |

### Step 5: Test Full Data Flows

For each major flow, verify end-to-end:

| Flow | Steps | Status |
|------|-------|--------|
| Login | Form → API → Token stored → User fetched → Dashboard | ☐ |
| View projects | Navigate → API call → Data displayed | ☐ |
| Create project | Form → API call → List updated → Success message | ☐ |
| Update project | Edit → API call → Data updated → Success message | ☐ |
| Delete project | Confirm → API call → Item removed → Success message | ☐ |

### Step 6: Verify Type Safety

Ensure no type mismatches:

- [ ] API responses match shared-types/
- [ ] No `any` types in data flow
- [ ] Error types handled correctly
- [ ] Request payloads typed correctly

### Step 7: Handle Edge Cases

| Edge Case | Handling |
|-----------|----------|
| Network error | Show error message, allow retry |
| 401 Unauthorized | Clear token, redirect to login |
| 403 Forbidden | Show access denied message |
| 404 Not Found | Show not found state |
| Validation error | Show field-level errors |
| Optimistic update failure | Rollback state |

---

## Output

| Output | Location | Status |
|--------|----------|--------|
| API client | Per TECH-STACK.md approach | ☐ Complete |
| API functions | Per feature/resource | ☐ Created |
| Connected components | All from API-MAPPING.md | ☐ Wired |
| State management | Per STATE-MANAGEMENT.md | ☐ Integrated |

---

## Checklist

Before moving to Phase 53:

- [ ] API client fully implemented
- [ ] Every connection in API-MAPPING.md wired
- [ ] Auth flow works (login, logout, persistence)
- [ ] Data displays correctly from API
- [ ] Mutations work (create, update, delete)
- [ ] State updates on data changes
- [ ] Loading states show during fetches
- [ ] Error states show on failures
- [ ] Type safety maintained (no `any` leaks)
- [ ] Edge cases handled

---

## Implementation Log Entry

```markdown
## Phase 52: Integration

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Connected frontend to backend per API-MAPPING.md.

### API Connections

| Resource | Endpoints | Status |
|----------|-----------|--------|
| Auth | 3 | ✅ |
| Users | 2 | ✅ |
| Projects | 5 | ✅ |
| **Total** | {{count}} | ✅ |

### Flows Verified

| Flow | Status |
|------|--------|
| Login | ✅ |
| View list | ✅ |
| Create | ✅ |
| Update | ✅ |
| Delete | ✅ |

### Type Safety
- API responses: ✅ Typed
- State updates: ✅ Typed
- Error handling: ✅ Typed

### Deviations
- None

### Notes
{{observations}}
```

---

## Rules

1. **Wire ALL connections** — Everything in API-MAPPING.md
2. **Use shared-types/** — No duplicate type definitions
3. **Follow STATE-MANAGEMENT.md** — State patterns as specified
4. **Handle ALL errors** — Network, auth, validation, not found
5. **Test full flows** — Not just individual calls

---

## Next Phase

**Phase 53: Implementation Verification** (MANDATORY)

---
