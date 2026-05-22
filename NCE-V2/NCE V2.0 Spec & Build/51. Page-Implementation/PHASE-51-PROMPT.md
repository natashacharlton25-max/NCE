# Phase 51: Page Implementation

---
Phase: 51
Section: 0f. Implementation
Name: Page Implementation
Purpose: Build all pages using components
---

## Role

You are implementing pages. Your job is to create every page specified in PAGES.md by composing components from Phase 50 and following the framework patterns from TECH-STACK.md.

---

## Inputs

| Document | Location | What to Extract |
|----------|----------|-----------------|
| PAGES.md | 0e outputs | All pages, routes, guards, data requirements |
| COMPONENTS.md | 0e outputs | Which components to use |
| STATE-MANAGEMENT.md | 0e outputs | Page-level state patterns |
| TECH-STACK.md | 0d outputs | Framework, routing approach |
| Components | From Phase 50 | Implemented components |

---

## Process

### Step 1: Extract Page List

From PAGES.md:

| Route | Page Name | Components | Guard | Data Requirements |
|-------|-----------|------------|-------|-------------------|
| / | Home | Hero, Features | None | None |
| /auth/login | Login | LoginForm | Guest only | None |
| /dashboard | Dashboard | Stats, RecentActivity | Auth required | User, Projects |
| /projects | ProjectList | ProjectCard, Pagination | Auth required | Projects |
| /projects/:id | ProjectDetail | ProjectHeader, TaskList | Auth + Owner | Project, Tasks |
| ... | ... | ... | ... | ... |

### Step 2: Implement Each Page

For each page in PAGES.md:

#### 2a. Create Page File

Per framework routing conventions from TECH-STACK.md.

#### 2b. Compose from Components

Use components from Phase 50 as specified in PAGES.md:

```
Page: Dashboard
├── Header (from layout)
├── Sidebar (from layout)
└── Main Content
    ├── StatsGrid
    │   └── StatCard (×4)
    ├── RecentProjects
    │   └── ProjectCard (×n)
    └── ActivityFeed
        └── ActivityItem (×n)
```

#### 2c. Implement Route Guards

Per PAGES.md guard requirements:

| Guard Type | Behavior |
|------------|----------|
| None | Public access |
| Auth required | Redirect to login if not authenticated |
| Guest only | Redirect to dashboard if authenticated |
| Owner | Check resource ownership |
| Role-based | Check user role |

**Implementation depends on framework from TECH-STACK.md.**

#### 2d. Set Up Page State

Per STATE-MANAGEMENT.md:

- Server state (data from API)
- UI state (modals, tabs, etc.)
- Form state (if forms on page)

**Note on State and Integration:**

Modern frameworks blur the line between "page state" (Phase 51) and "data fetching" (Phase 52).

**Guidance:**
- Phase 51: Set up state **structures** using types from shared-types/
- Phase 51: Use mock data or loading states as placeholders
- Phase 52: Replace mocks with real API calls

**Do not:** Implement full async data fetching in Phase 51. Prepare the structure, defer the wiring. This prevents rework when real data patterns differ from assumptions.

#### 2e. Implement Loading States

Every page with data requirements needs:

- Loading state while fetching
- Skeleton or spinner

#### 2f. Implement Error States

Every page with data requirements needs:

- Error state if fetch fails
- Retry option (if appropriate)

#### 2g. Implement Empty States

Pages with lists need:

- Empty state when no data
- Call to action (if appropriate)

### Step 3: Page State Handling

For each page, ensure proper state management:

| Page | Loading State | Error State | Empty State |
|------|---------------|-------------|-------------|
| Dashboard | ☐ | ☐ | N/A |
| ProjectList | ☐ | ☐ | ☐ |
| ProjectDetail | ☐ | ☐ | N/A |

---

## Output

| Output | Location | Status |
|--------|----------|--------|
| All pages | Per routing conventions | ☐ Created |
| Route guards | Per framework | ☐ Implemented |
| State handling | Per STATE-MANAGEMENT.md | ☐ Implemented |

---

## Checklist

Before moving to Phase 52:

- [ ] Every page in PAGES.md implemented
- [ ] Pages use components from Phase 50
- [ ] Route guards work per spec
- [ ] Loading states show while fetching
- [ ] Error states show on failure
- [ ] Empty states show when no data
- [ ] Page state follows STATE-MANAGEMENT.md
- [ ] Navigation between pages works

---

## Implementation Log Entry

```markdown
## Phase 51: Page Implementation

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Implemented {{count}} pages per PAGES.md.

### Pages

| Page | Route | Guard | Status |
|------|-------|-------|--------|
| {{name}} | {{route}} | {{guard}} | ✅ |

### State Handling

| Page | Loading | Error | Empty |
|------|---------|-------|-------|
| {{page}} | ✅ | ✅ | ✅/N/A |

### Deviations
- None

### Notes
{{observations}}
```

---

## Rules

1. **Implement ALL pages** — Nothing missing from PAGES.md
2. **Use Phase 50 components** — Don't create new components here
3. **Follow STATE-MANAGEMENT.md** — State patterns as specified
4. **Handle ALL states** — Loading, error, empty
5. **Guards per spec** — Authentication/authorization as specified

---

## Next Phase

**Phase 52: Integration**

---
