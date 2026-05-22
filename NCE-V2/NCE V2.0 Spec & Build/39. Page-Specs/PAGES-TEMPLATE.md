# Pages — {{Project Name}}

---
Status: DRAFT | IN REVIEW | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
---

## Route Overview

| Route | Page | Auth | Roles | Layout |
|-------|------|------|-------|--------|
| `/` | Home | No | — | Public |
| `/login` | Login | No | — | Auth |
| `/register` | Register | No | — | Auth |
| `/dashboard` | Dashboard | Yes | any | App |
| `/projects` | Project List | Yes | any | App |
| `/projects/:id` | Project Detail | Yes | any | App |
| `/admin` | Admin | Yes | admin | Admin |
| `/404` | Not Found | No | — | Error |

---

## Public Pages

### Home (`/`)

**Route:**
| Property | Value |
|----------|-------|
| Path | `/` |
| Page Title | `{{Project Name}}` |
| Meta Description | `{{description}}` |

**Route Guards:**
| Check | Value |
|-------|-------|
| Requires Auth | No |
| Roles Required | None |
| Redirect Auth Users | Yes → `/dashboard` |

**Layout:** Public (Header + Footer, no sidebar)

**Components:**
| Component | Location | Props |
|-----------|----------|-------|
| HeroSection | Main | `title`, `subtitle`, `cta` |
| FeatureGrid | Main | `features[]` |
| Testimonials | Main | `testimonials[]` |
| CTASection | Main | `heading`, `buttonText` |

**Data Requirements:**
| Data | Source | When Loaded |
|------|--------|-------------|
| None | — | — |

**User Actions:**
| Action | Trigger | Result |
|--------|---------|--------|
| Click CTA | Button click | Navigate to `/register` |
| Click Login | Header link | Navigate to `/login` |

**Navigation:**
| Destination | Trigger |
|-------------|---------|
| `/login` | Header link |
| `/register` | CTA button |

**States:**
| State | Pattern | Trigger |
|-------|---------|---------|
| Loading | None | — |
| Error | None | — |
| Default | Show content | — |

---

## Auth Pages

### Login (`/login`)

**Route:**
| Property | Value |
|----------|-------|
| Path | `/login` |
| Query Params | `?returnUrl={{url}}` (optional) |
| Page Title | `Login - {{Project Name}}` |

**Route Guards:**
| Check | Value |
|-------|-------|
| Requires Auth | No |
| Redirect Auth Users | Yes → `returnUrl` or `/dashboard` |

**Layout:** Auth (centered card, minimal)

**Components:**
| Component | Location | Props |
|-----------|----------|-------|
| LoginForm | Center | `onSubmit`, `onError` |
| SocialLogins | Below form | `providers[]` |
| ForgotPasswordLink | Below form | — |

**Data Requirements:**
| Data | Source | When Loaded |
|------|--------|-------------|
| None | — | — |

**API Calls:**
| Endpoint | When | On Success | On Error |
|----------|------|------------|----------|
| POST /api/auth/login | Form submit | Store token, redirect | `ERROR_INLINE` on form |

**User Actions:**
| Action | Trigger | Result |
|--------|---------|--------|
| Submit login | Form submit | Call login API |
| Forgot password | Link click | Navigate to `/forgot-password` |
| Register | Link click | Navigate to `/register` |

**States:**
| State | Pattern | Trigger |
|-------|---------|---------|
| Default | Show form | Initial |
| Submitting | `LOADING_SPINNER` on button | Form submit |
| Error | `ERROR_INLINE` | API error |

---

## Protected Pages

### Dashboard (`/dashboard`)

**Route:**
| Property | Value |
|----------|-------|
| Path | `/dashboard` |
| Page Title | `Dashboard - {{Project Name}}` |

**Route Guards:**
| Check | Value |
|-------|-------|
| Requires Auth | Yes |
| Roles Required | None (any authenticated) |
| On Auth Fail | Redirect to `/login?returnUrl=/dashboard` |
| Loading While Checking | `LOADING_SPINNER` (centered) |

**Layout:** App (Header + Sidebar + Main)

**Components:**
| Component | Location | Props |
|-----------|----------|-------|
| DashboardHeader | Header area | `user` |
| StatCards | Main (top) | `stats[]` |
| RecentActivity | Main (middle) | `activities[]` |
| QuickActions | Main (bottom) | `actions[]` |

**Data Requirements:**
| Data | Source | When Loaded |
|------|--------|-------------|
| User | GET /api/users/me | Page mount |
| Stats | GET /api/stats | Page mount |
| Activities | GET /api/activities?limit=10 | Page mount |

**API Calls:**
| Endpoint | When | On Success | On Error |
|----------|------|------------|----------|
| GET /api/users/me | Mount | Set user state | `ERROR_FULLPAGE` |
| GET /api/stats | Mount | Set stats state | `ERROR_TOAST` (partial fail OK) |
| GET /api/activities | Mount | Set activities state | `ERROR_TOAST` |

**User Actions:**
| Action | Trigger | Result |
|--------|---------|--------|
| View project | Card click | Navigate to `/projects/:id` |
| Create new | Quick action | Navigate to `/projects/new` |
| View all activity | Link click | Navigate to `/activity` |

**Navigation:**
| Destination | Trigger |
|-------------|---------|
| `/projects/:id` | Card click |
| `/projects/new` | Quick action |
| `/activity` | "View all" link |
| `/settings` | Header menu |

**States:**
| State | Pattern | Trigger |
|-------|---------|---------|
| Loading | `LOADING_SKELETON` | Initial load |
| Error | `ERROR_FULLPAGE` | Critical API fail |
| Partial Error | `ERROR_TOAST` + partial content | Non-critical API fail |
| Empty | `EMPTY_GENERIC` | No activities |
| Default | Show all content | Data loaded |

---

### Project List (`/projects`)

**Route:**
| Property | Value |
|----------|-------|
| Path | `/projects` |
| Query Params | `?page`, `?search`, `?status` |
| Page Title | `Projects - {{Project Name}}` |

**Route Guards:**
| Check | Value |
|-------|-------|
| Requires Auth | Yes |
| Roles Required | None |
| On Auth Fail | Redirect to `/login?returnUrl=/projects` |

**Layout:** App

**Components:**
| Component | Location | Props |
|-----------|----------|-------|
| PageHeader | Top | `title`, `actions` |
| SearchInput | Top (right) | `value`, `onChange` |
| FilterBar | Below header | `filters`, `onFilterChange` |
| ProjectGrid | Main | `projects[]`, `onSelect` |
| Pagination | Bottom | `page`, `total`, `onPageChange` |

**Data Requirements:**
| Data | Source | When Loaded |
|------|--------|-------------|
| Projects | GET /api/projects | Mount + filter/page change |

**API Calls:**
| Endpoint | When | On Success | On Error |
|----------|------|------------|----------|
| GET /api/projects?page=X&search=Y | Mount, filter change | Update list | `ERROR_FULLPAGE` |

**User Actions:**
| Action | Trigger | Result |
|--------|---------|--------|
| Search | Input change (debounced) | Re-fetch with search param |
| Filter | Filter select | Re-fetch with filter |
| Page change | Pagination click | Re-fetch with page |
| Create | "New" button | Navigate to `/projects/new` |
| Select project | Card click | Navigate to `/projects/:id` |

**States:**
| State | Pattern | Trigger |
|-------|---------|---------|
| Loading | `LOADING_SKELETON` (grid) | Fetching |
| Error | `ERROR_FULLPAGE` | API error |
| Empty | `EMPTY_GENERIC` | No projects |
| No Results | `EMPTY_SEARCH` | Search/filter yields nothing |
| Default | Show grid | Projects loaded |

---

### Project Detail (`/projects/:id`)

**Route:**
| Property | Value |
|----------|-------|
| Path | `/projects/:id` |
| Params | `id` - Project identifier |
| Page Title | `{{project.name}} - {{Project Name}}` |

**Route Guards:**
| Check | Value |
|-------|-------|
| Requires Auth | Yes |
| Roles Required | None (ownership checked by API) |
| On Auth Fail | Redirect to `/login` |

**Layout:** App

**Components:**
| Component | Location | Props |
|-----------|----------|-------|
| Breadcrumb | Top | `items[]` |
| ProjectHeader | Top | `project`, `onEdit`, `onDelete` |
| ProjectTabs | Below header | `activeTab`, `onTabChange` |
| ProjectOverview | Tab content | `project` |
| ProjectSettings | Tab content | `project`, `onSave` |

**Data Requirements:**
| Data | Source | When Loaded |
|------|--------|-------------|
| Project | GET /api/projects/:id | Mount |

**API Calls:**
| Endpoint | When | On Success | On Error |
|----------|------|------------|----------|
| GET /api/projects/:id | Mount | Set project | `ERROR_FULLPAGE` (404 = not found page) |
| PUT /api/projects/:id | Save settings | `SUCCESS_TOAST` | `ERROR_TOAST` |
| DELETE /api/projects/:id | Confirm delete | Redirect to `/projects` | `ERROR_TOAST` |

**User Actions:**
| Action | Trigger | Result |
|--------|---------|--------|
| Edit | Edit button | Open edit mode / modal |
| Delete | Delete button | Confirm modal → delete |
| Change tab | Tab click | Show tab content |
| Back | Breadcrumb / back | Navigate to `/projects` |

**States:**
| State | Pattern | Trigger |
|-------|---------|---------|
| Loading | `LOADING_SKELETON` | Fetching |
| Not Found | `ERROR_FULLPAGE` (404 variant) | Project doesn't exist |
| Error | `ERROR_FULLPAGE` | API error |
| Default | Show project | Loaded |

---

## Error Pages

### Not Found (`/404`)

**Route:**
| Property | Value |
|----------|-------|
| Path | `/404` (or catch-all `*`) |
| Page Title | `Page Not Found - {{Project Name}}` |

**Route Guards:** None

**Layout:** Error (minimal)

**Components:**
| Component | Location | Props |
|-----------|----------|-------|
| ErrorDisplay | Center | `code: 404`, `message`, `cta` |

**User Actions:**
| Action | Trigger | Result |
|--------|---------|--------|
| Go home | CTA button | Navigate to `/` |
| Go back | Back link | `history.back()` |

---

## Page Index

| Page | Components Count | API Calls | Has Auth |
|------|------------------|-----------|----------|
| Home | 4 | 0 | No |
| Login | 3 | 1 | No |
| Dashboard | 4 | 3 | Yes |
| Project List | 5 | 1 | Yes |
| Project Detail | 5 | 3 | Yes |
| Not Found | 1 | 0 | No |

**Total Pages:** {{n}}
**Total Unique Components:** {{n}}
**Total API Calls:** {{n}}

---

## Notes

{{Any additional notes about routing, navigation, or page behaviour}}

---
Generated: {{timestamp}}
Phase: 39 (Page Specs)
---
