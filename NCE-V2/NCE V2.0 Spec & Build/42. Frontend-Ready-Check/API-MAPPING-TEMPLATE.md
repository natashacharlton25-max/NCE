# API Mapping — {{Project Name}}

---
Status: DRAFT | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
Phase: 42 (Frontend Ready Check)
---

## Purpose

This document maps every frontend element that communicates with the backend. It serves as:

1. **Verification** — Ensure no frontend element calls an endpoint that doesn't exist
2. **Quick reference** — See at a glance which elements need which data
3. **Impact analysis** — If an endpoint changes, know exactly what's affected

---

## Summary

| Metric | Count |
|--------|-------|
| Total screens with data needs | {{n}} |
| Total elements with data needs | {{n}} |
| Total unique endpoints used | {{n}} |
| Endpoints in contract but unused | {{n}} |
| Elements calling missing endpoints | {{n}} ← **Should be 0** |

---

## Screen → Endpoint Mapping

Which screens call which endpoints.

| Screen | Endpoints Called | When Called |
|--------|------------------|-------------|
{{#each pages}}
| {{name}} | {{endpoints}} | {{trigger}} |
{{/each}}

### Detailed Screen Data Requirements

#### {{Screen Name}}

| Endpoint | Purpose | When Called | On Error |
|----------|---------|-------------|----------|
| `GET /api/users/me` | Show current user in header | Screen loads | Redirect to login |
| `GET /api/projects` | Populate project list | Screen loads | Show error state |
| `POST /api/projects` | Create new project | User clicks "Create" | Show error toast |

---

## Element → Endpoint Mapping

Which reusable elements call which endpoints.

| Element | Endpoints Called | When Called |
|---------|------------------|-------------|
{{#each components}}
| {{name}} | {{endpoints}} | {{trigger}} |
{{/each}}

### Detailed Element Data Requirements

#### UserMenu

| Endpoint | Purpose | When Called | Caching |
|----------|---------|-------------|---------|
| `GET /api/users/me` | Get user name + avatar | Element mounts | Cache for session |

#### ProjectGrid

| Endpoint | Purpose | When Called | Caching |
|----------|---------|-------------|---------|
| `GET /api/projects` | Fetch project list | Mount, filter change, refresh | 30 seconds |

#### LoginForm

| Endpoint | Purpose | When Called | Caching |
|----------|---------|-------------|---------|
| `POST /api/auth/login` | Authenticate user | Form submit | None |

---

## Endpoint → Consumer Mapping

Reverse lookup: which elements use each endpoint.

| Endpoint | Method | Used By | Count |
|----------|--------|---------|-------|
{{#each endpoints}}
| `{{path}}` | {{method}} | {{consumers}} | {{count}} |
{{/each}}

### Example (filled):

| Endpoint | Method | Used By | Count |
|----------|--------|---------|-------|
| `/api/users/me` | GET | UserMenu, DashboardHeader, SettingsForm | 3 |
| `/api/projects` | GET | ProjectGrid, Sidebar, Dashboard | 3 |
| `/api/projects` | POST | CreateProjectModal | 1 |
| `/api/projects/:id` | GET | ProjectDetail | 1 |
| `/api/projects/:id` | PUT | ProjectSettings | 1 |
| `/api/projects/:id` | DELETE | ProjectSettings | 1 |
| `/api/auth/login` | POST | LoginForm | 1 |
| `/api/auth/register` | POST | RegisterForm | 1 |
| `/api/auth/logout` | POST | UserMenu | 1 |

---

## Coverage Check

### ✅ Endpoints in Contract AND Used by Frontend

| Endpoint | Method | Consumers |
|----------|--------|-----------|
{{#each used_endpoints}}
| `{{path}}` | {{method}} | {{consumers}} |
{{/each}}

### ⚠️ Endpoints in Contract but NOT Used

These exist in the contract but no frontend element uses them. Verify if this is intentional.

| Endpoint | Method | Notes |
|----------|--------|-------|
{{#each unused_endpoints}}
| `{{path}}` | {{method}} | {{notes}} |
{{/each}}

**Possible reasons:**
- Admin-only endpoint (separate admin UI)
- Backend-to-backend only
- Future feature not yet built
- **Mistake — should be used somewhere**

### ❌ Frontend Elements Calling Missing Endpoints

These elements reference endpoints NOT in the contract. **This must be resolved before implementation.**

| Element | Calls | Status |
|---------|-------|--------|
{{#each missing_endpoints}}
| {{component}} | `{{endpoint}}` | ❌ Missing from contract |
{{/each}}

**If this table has entries:** Stop. Either add the endpoint to the contract (escalate to Phase 37) or remove the call from the element.

---

## Error Handling Coverage

Every endpoint call should have error handling defined.

| Endpoint | Element | Error Handling Defined? |
|----------|---------|------------------------|
{{#each calls}}
| `{{endpoint}}` | {{component}} | ✅ / ❌ |
{{/each}}

**Required for each call:**
- [ ] What happens on network error?
- [ ] What happens on 4xx error?
- [ ] What happens on 5xx error?
- [ ] Does it use a global pattern (toast, inline) or custom handling?

---

## Authentication Requirements

| Endpoint | Auth Required? | Elements Using It | Guard in Place? |
|----------|----------------|-------------------|-----------------|
{{#each endpoints}}
| `{{path}}` | {{auth_required}} | {{consumers}} | ✅ / ❌ |
{{/each}}

**Check:** If an endpoint requires auth, every screen using it should have a route guard.

---

## Quick Reference by Feature

### Authentication Feature
| Action | Endpoint | Element |
|--------|----------|---------|
| Log in | POST /api/auth/login | LoginForm |
| Log out | POST /api/auth/logout | UserMenu |
| Register | POST /api/auth/register | RegisterForm |
| Get current user | GET /api/users/me | UserMenu, Header |

### Projects Feature
| Action | Endpoint | Element |
|--------|----------|---------|
| List projects | GET /api/projects | ProjectGrid, Sidebar |
| View project | GET /api/projects/:id | ProjectDetail |
| Create project | POST /api/projects | CreateProjectModal |
| Update project | PUT /api/projects/:id | ProjectSettings |
| Delete project | DELETE /api/projects/:id | ProjectSettings |

### {{Other Feature}}
| Action | Endpoint | Element |
|--------|----------|---------|
| {{action}} | {{endpoint}} | {{element}} |

---

## Verification Checklist

- [ ] Every endpoint in contract is accounted for (used or intentionally unused)
- [ ] Every frontend data call maps to a contract endpoint
- [ ] No elements call endpoints that don't exist
- [ ] Error handling is defined for every call
- [ ] Auth requirements match route guards

---

## Sign-Off

| Check | Status | Verified By | Date |
|-------|--------|-------------|------|
| All endpoints mapped | ✅ / ❌ | | |
| No missing endpoints | ✅ / ❌ | | |
| Error handling complete | ✅ / ❌ | | |
| Auth requirements match | ✅ / ❌ | | |

---
Generated: {{timestamp}}
Phase: 42 (Frontend Ready Check)
---
