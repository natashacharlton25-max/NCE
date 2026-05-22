# PHASE 41: STATE MANAGEMENT

---
Phase: 41
Name: State Management
Section: 0e. Frontend
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are defining how state flows through the frontend application. This includes categorizing state, choosing patterns, and documenting anti-patterns to avoid.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| PAGES.md | `frontend/` | State needs per page |
| COMPONENTS.md | `frontend/` | Component state needs |
| TECH-STACK.md | `precode/` | State library choice |
| FRONTEND-BACKEND-CONTRACT.md | `frontend/` | Server data structure |

---

## TASK

### Step 1: Categorize All State

Review PAGES.md and COMPONENTS.md to identify all state.

**State Categories:**

| Category | Description | Typical Tool |
|----------|-------------|--------------|
| **Server State** | Data from API (cached) | TanStack Query, SWR, RTK Query |
| **Client State** | UI state, user preferences | Zustand, Redux, Svelte stores |
| **URL State** | Route params, query strings | Router |
| **Form State** | In-progress user input | React Hook Form, Formik, Superforms |
| **Persisted State** | Survives page refresh | LocalStorage, SessionStorage |

### Step 2: Define Server State

List all data fetched from API:

| Data | Endpoint | Cache Strategy | Stale Time |
|------|----------|----------------|------------|
| Current user | GET /api/users/me | Cache until logout | Infinite |
| Projects list | GET /api/projects | Cache + revalidate | 30 seconds |
| Project detail | GET /api/projects/:id | Cache + revalidate | 30 seconds |

**Server State Rules:**
- Use server state library (not global state) for API data
- Define stale time per resource
- Define refetch triggers

### Step 3: Define Client State

List UI state that isn't from the server:

| State | Scope | Initial | Persistence |
|-------|-------|---------|-------------|
| Sidebar collapsed | Global | `false` | LocalStorage |
| Theme | Global | `"system"` | LocalStorage |
| Modal open | Component | `false` | None |
| Active tab | Component | `0` | None |

**Client State Rules:**
- Global only if truly shared across pages
- Prefer component state when possible
- Document what's persisted

### Step 4: Define URL State

List state stored in URL:

| Page | Param | Type | Purpose |
|------|-------|------|---------|
| Project List | `?page` | Query | Pagination |
| Project List | `?search` | Query | Search term |
| Project Detail | `:id` | Route | Project ID |

**URL State Rules:**
- Shareable/bookmarkable state goes in URL
- Transient state stays in component

### Step 5: Define Form State

List forms and their state management:

| Form | Tool | Validation | On Submit |
|------|------|------------|-----------|
| Login | Hook Form | Zod schema | API call |
| Create Project | Hook Form | Zod schema | API call |
| Settings | Hook Form | Zod schema | API call |

### Step 6: Define Persisted State

**Critical Decision:** What survives page refresh?

| State | Storage | Key | Sync Strategy |
|-------|---------|-----|---------------|
| Theme | LocalStorage | `theme` | Read on mount |
| Sidebar | LocalStorage | `sidebar_collapsed` | Read on mount |
| Auth token | Secure cookie | — | Handled by auth |

**Ask user:**
> What state should survive page refresh? How should it be stored?

### Step 7: Document State Shape (if using global store)

If using Zustand/Redux:

```typescript
interface AppState {
  // UI State
  ui: {
    sidebarCollapsed: boolean;
    theme: "light" | "dark" | "system";
  };
  
  // Actions
  toggleSidebar: () => void;
  setTheme: (theme: Theme) => void;
}
```

### Step 8: Document Anti-Patterns

Include these warnings:

| Anti-Pattern | Why Bad | Instead |
|--------------|---------|---------|
| Duplicating server state in global state | Sync bugs, stale data | Use server state cache |
| Storing derived state | Recalculation bugs | Compute on read |
| Coupling navigation to data state | Breaks back button | Keep separate |
| Global state for local concerns | Unnecessary complexity | Component state |
| No loading/error states | Poor UX | Always define both |
| Storing entire API response | Memory bloat | Store only what's needed |

### Step 9: Create Required Artifacts

**MANDATORY OUTPUTS:**

1. **State Ownership Table** — Who owns what state

| State | Owner | Category | Persistence |
|-------|-------|----------|-------------|
| {{state}} | {{component/global/URL}} | Server/Client/Form | Yes/No |

2. **State Flow Diagram** — Visual representation of state flow

```
URL State → Server State → Client State → Component State
                ↓
         Persisted State
```

Must show:
- Where state originates
- How it flows between categories
- What triggers updates

---

## OUTPUT

Create: `frontend/STATE-MANAGEMENT.md`

Use template: `STATE-MANAGEMENT-TEMPLATE.md`

---

## APPROVAL

Present state management to user.

Verify:
- [ ] All state categories are addressed
- [ ] Server state caching is defined
- [ ] Persisted state is documented
- [ ] Anti-patterns are noted
- [ ] Matches TECH-STACK.md choices

**If user says APPROVE:** Proceed to Phase 42
**If user says REVISE:** Make changes, re-present

---
Generated: {{timestamp}}
Phase: 41 (State Management)
---
