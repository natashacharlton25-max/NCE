# PHASE 39: PAGE SPECS

---
Phase: 39
Name: Page Specs
Section: 0e. Frontend
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are specifying every page/route in the frontend application. Each page spec defines what the user sees, what they can do, and what data is required.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| DESIGN-SYSTEM.md | `frontend/` | Layout patterns, components |
| FRONTEND-BACKEND-CONTRACT.md | `frontend/` | Available endpoints |
| Mockups | From user | Visual reference |

---

## TASK

### Step 1: List All Routes

From mockups and design system, list every page:

| Route | Page Name | Auth Required |
|-------|-----------|---------------|
| `/` | Home | No |
| `/login` | Login | No |
| `/dashboard` | Dashboard | Yes |
| `/projects` | Project List | Yes |
| `/projects/:id` | Project Detail | Yes |
| etc. | | |

### Step 2: Specify Each Page

For each page, define:

1. **Route information**
   - Path (with parameters)
   - Query parameters (if any)
   - Page title

2. **Route guards**
   - Requires authentication?
   - Required roles/permissions?
   - Redirect on auth failure?
   - Loading state while checking?

3. **Layout**
   - Which layout template (from design system)?
   - Header variant?
   - Sidebar visible?

4. **Components used**
   - List every component on the page
   - Note component props where obvious

5. **Data requirements**
   - What API calls does this page make?
   - When are they made (on mount, on action)?
   - What data is displayed?

6. **User actions**
   - What can the user do on this page?
   - What happens when they do it?

7. **Navigation**
   - Where can the user go from here?
   - Back button behaviour?

8. **States**
   - Loading state
   - Error state
   - Empty state

### Step 3: Route Guards (First-Class)

For each protected page, explicitly define:

```
Route Guards for /dashboard:
├── Requires Auth: Yes
├── Check: User has valid session
├── On Fail: Redirect to /login?returnUrl=/dashboard
├── Loading: Show LOADING_SPINNER centered
└── Roles Required: None (any authenticated user)

Route Guards for /admin:
├── Requires Auth: Yes
├── Check: User has "admin" role
├── On Fail: Redirect to /403 (Forbidden page)
├── Loading: Show LOADING_SPINNER centered
└── Roles Required: ["admin"]
```

### Step 4: Map API Calls

For each page, list exactly which endpoints are called:

| Endpoint | When Called | On Success | On Error |
|----------|-------------|------------|----------|
| GET /api/users/me | Page mount | Populate user data | ERROR_FULLPAGE |
| GET /api/projects | Page mount | Populate list | ERROR_FULLPAGE |
| POST /api/projects | Form submit | Redirect to new project | ERROR_TOAST |

### Step 5: Cross-Reference Components

Every component referenced must exist in DESIGN-SYSTEM.md component inventory.

If a page needs a component not in inventory:
1. Note it as "NEW: ComponentName"
2. It will be defined in Phase 40

### Step 6: Verify Completeness

- [ ] Every route from mockups is specified
- [ ] Every page has route guards defined
- [ ] Every page has data requirements listed
- [ ] Every page has all three states (loading, error, empty)
- [ ] All API calls map to FRONTEND-BACKEND-CONTRACT.md

---

## FILE SPLIT RULES

| Condition | Action |
|-----------|--------|
| Default | Single PAGES.md file |
| File > ~500 LOC | Split into `pages/PAGE-{name}.md` |
| Different domains | Split by domain |

---

## OUTPUT

Create: `frontend/PAGES.md`

Or if split:
```
frontend/
└── pages/
    ├── PAGE-home.md
    ├── PAGE-login.md
    ├── PAGE-dashboard.md
    └── ...
```

Use template: `PAGES-TEMPLATE.md`

---

## APPROVAL

Present pages to user.

Verify:
- [ ] All routes from mockups are covered
- [ ] Route guards are explicit
- [ ] Data requirements are clear
- [ ] States are defined

**If user says APPROVE:** Proceed to Phase 40
**If user says REVISE:** Make changes, re-present

---
Generated: {{timestamp}}
Phase: 39 (Page Specs)
---
