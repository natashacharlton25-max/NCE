# Components — {{Project Name}}

---
Status: DRAFT | IN REVIEW | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
---

## Component Index

### Atomic Components

| Component | Purpose | Used By |
|-----------|---------|---------|
| Button | Trigger actions | Multiple |
| Input | Text entry | Forms |
| Checkbox | Boolean toggle | Forms |
| Badge | Status indicator | Cards, Lists |
| Avatar | User image | Header, Cards |

### Composite Components

| Component | Purpose | Used By |
|-----------|---------|---------|
| Card | Content container | Dashboard, Lists |
| Modal | Overlay dialog | Multiple |
| Form | Input collection | Auth, Settings |
| Table | Data display | Lists, Admin |
| Dropdown | Menu selection | Header, Filters |

### Layout Components

| Component | Purpose | Used By |
|-----------|---------|---------|
| Header | Top navigation | All pages |
| Sidebar | Side navigation | App pages |
| PageHeader | Page title + actions | Most pages |

### Data Components

| Component | Purpose | API Endpoint |
|-----------|---------|--------------|
| UserMenu | Current user display | GET /api/users/me |
| ProjectGrid | Project listing | GET /api/projects |

---

## Atomic Components

### Button

**Purpose:** Trigger actions or navigation

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `variant` | `"primary" \| "secondary" \| "ghost" \| "danger"` | No | `"primary"` | Visual style |
| `size` | `"sm" \| "md" \| "lg"` | No | `"md"` | Button size |
| `disabled` | `boolean` | No | `false` | Disable interaction |
| `loading` | `boolean` | No | `false` | Show loading state |
| `type` | `"button" \| "submit" \| "reset"` | No | `"button"` | HTML type |
| `icon` | `IconName` | No | — | Optional icon |
| `iconPosition` | `"left" \| "right"` | No | `"left"` | Icon placement |
| `children` | `ReactNode` | Yes | — | Button label |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onClick` | `MouseEvent` | Button clicked (not when disabled/loading) |

**Variants:**

| Variant | Use Case |
|---------|----------|
| `primary` | Primary actions (Save, Submit) |
| `secondary` | Secondary actions (Cancel, Back) |
| `ghost` | Tertiary actions, inline actions |
| `danger` | Destructive actions (Delete) |

**States:**

| State | Visual |
|-------|--------|
| Default | Normal appearance |
| Hover | Slightly darker/lighter |
| Active | Pressed appearance |
| Disabled | Muted, no pointer |
| Loading | Spinner replaces text, disabled |

**Accessibility:**

| Requirement | Implementation |
|-------------|----------------|
| Focus visible | Ring on focus |
| Keyboard | Enter/Space activates |
| Disabled | `aria-disabled="true"` |
| Loading | `aria-busy="true"` |

---

### Input

**Purpose:** Accept text input from user

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `type` | `"text" \| "email" \| "password" \| "search" \| "number"` | No | `"text"` | Input type |
| `value` | `string` | Yes | — | Controlled value |
| `placeholder` | `string` | No | — | Placeholder text |
| `disabled` | `boolean` | No | `false` | Disable input |
| `error` | `string` | No | — | Error message |
| `label` | `string` | No | — | Label text |
| `required` | `boolean` | No | `false` | Required field |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onChange` | `string` (new value) | Value changes |
| `onBlur` | `FocusEvent` | Input loses focus |
| `onFocus` | `FocusEvent` | Input gains focus |

**States:**

| State | Visual | Pattern |
|-------|--------|---------|
| Default | Normal border | — |
| Focus | Highlighted border | — |
| Error | Red border + message | `ERROR_INLINE` |
| Disabled | Muted background | — |

**Accessibility:**

| Requirement | Implementation |
|-------------|----------------|
| Label | `<label>` element linked |
| Error | `aria-invalid="true"`, `aria-describedby` |
| Required | `aria-required="true"` |

---

### Badge

**Purpose:** Display status or count

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `variant` | `"default" \| "success" \| "warning" \| "error" \| "info"` | No | `"default"` | Color variant |
| `size` | `"sm" \| "md"` | No | `"md"` | Badge size |
| `children` | `ReactNode` | Yes | — | Badge content |

**States:** None (presentational)

**Accessibility:**

| Requirement | Implementation |
|-------------|----------------|
| Color contrast | 4.5:1 minimum |
| Not interactive | No role needed |

---

## Composite Components

### Card

**Purpose:** Container for related content

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `variant` | `"default" \| "elevated" \| "outlined"` | No | `"default"` | Visual style |
| `clickable` | `boolean` | No | `false` | Enable click interaction |
| `selected` | `boolean` | No | `false` | Selected state |
| `children` | `ReactNode` | Yes | — | Card content |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onClick` | `MouseEvent` | Card clicked (if clickable) |

**Children (slots):**

| Slot | Component | Purpose |
|------|-----------|---------|
| Header | `Card.Header` | Title, actions |
| Body | `Card.Body` | Main content |
| Footer | `Card.Footer` | Actions, metadata |

**Accessibility:**

| Requirement | Implementation |
|-------------|----------------|
| Clickable | `role="button"`, `tabIndex={0}` |
| Focus | Focus ring when clickable |
| Keyboard | Enter activates if clickable |

---

### Modal

**Purpose:** Overlay dialog for focused interactions

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `open` | `boolean` | Yes | — | Control visibility |
| `title` | `string` | Yes | — | Modal title |
| `size` | `"sm" \| "md" \| "lg"` | No | `"md"` | Modal width |
| `closable` | `boolean` | No | `true` | Show close button |
| `children` | `ReactNode` | Yes | — | Modal content |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onClose` | — | Close requested (X, Escape, backdrop) |

**Children (slots):**

| Slot | Component | Purpose |
|------|-----------|---------|
| Body | `Modal.Body` | Main content |
| Actions | `Modal.Actions` | Buttons |

**Accessibility:**

| Requirement | Implementation |
|-------------|----------------|
| Focus trap | Focus stays in modal |
| Escape | Closes modal |
| `role` | `dialog` |
| `aria-modal` | `true` |
| `aria-labelledby` | Title ID |

---

### LoginForm

**Purpose:** User authentication form

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `onSubmit` | `(credentials: Credentials) => Promise<void>` | Yes | — | Submit handler |
| `loading` | `boolean` | No | `false` | Submitting state |
| `error` | `string` | No | — | Form-level error |

**Internal State:**

| State | Type | Initial | Description |
|-------|------|---------|-------------|
| `email` | `string` | `""` | Email input value |
| `password` | `string` | `""` | Password input value |
| `fieldErrors` | `Record<string, string>` | `{}` | Per-field errors |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onSubmit` | `{ email, password }` | Form submitted |

**Children:**
- Input (email)
- Input (password)
- Button (submit)
- Link (forgot password)

**States:**

| State | Pattern | Trigger |
|-------|---------|---------|
| Default | Show form | Initial |
| Submitting | `LOADING_SPINNER` on button | Submit |
| Error | `ERROR_INLINE` below form | API error |
| Field Error | `ERROR_INLINE` on field | Validation |

**Validation:**
- Email: Required, valid format
- Password: Required, min 8 chars

---

## Data Components

### UserMenu

**Purpose:** Display current user with dropdown menu

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `onLogout` | `() => void` | Yes | — | Logout handler |

**Internal State:**

| State | Type | Initial | Description |
|-------|------|---------|-------------|
| `open` | `boolean` | `false` | Dropdown open |

**API Call:**

| Endpoint | When | Caching |
|----------|------|---------|
| GET /api/users/me | Mount | Cache for session |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onLogout` | — | Logout clicked |

**States:**

| State | Pattern | Trigger |
|-------|---------|---------|
| Loading | `LOADING_SKELETON` (avatar shape) | Fetching user |
| Error | Show fallback avatar | API error |
| Default | Show avatar + name | Loaded |

---

### ProjectGrid

**Purpose:** Display grid of project cards

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `projects` | `Project[]` | Yes | — | Projects to display |
| `loading` | `boolean` | No | `false` | Loading state |
| `onSelect` | `(id: string) => void` | Yes | — | Project selected |
| `onCreateNew` | `() => void` | No | — | Create new clicked |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onSelect` | `projectId` | Card clicked |
| `onCreateNew` | — | "New" card clicked |

**Children:**
- ProjectCard (repeated)
- CreateProjectCard (optional)

**States:**

| State | Pattern | Trigger |
|-------|---------|---------|
| Loading | `LOADING_SKELETON` (card grid) | `loading=true` |
| Empty | `EMPTY_GENERIC` | `projects.length === 0` |
| Default | Show grid | Has projects |

---

## Layout Components

### Header

**Purpose:** Top navigation bar

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `variant` | `"public" \| "app"` | No | `"app"` | Header style |
| `user` | `User \| null` | No | `null` | Current user |

**Children:**
- Logo
- Navigation
- UserMenu (if user)
- AuthButtons (if no user)

**Responsive:**

| Breakpoint | Behaviour |
|------------|-----------|
| Mobile | Hamburger menu |
| Desktop | Full navigation |

---

### Sidebar

**Purpose:** Side navigation for app pages

**Props:**

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `collapsed` | `boolean` | No | `false` | Collapsed state |
| `items` | `NavItem[]` | Yes | — | Navigation items |

**Internal State:**

| State | Type | Initial | Description |
|-------|------|---------|-------------|
| `activeItem` | `string` | — | Currently active item |

**Events:**

| Event | Payload | When Fired |
|-------|---------|------------|
| `onToggle` | — | Collapse toggle |
| `onNavigate` | `path` | Item clicked |

**Responsive:**

| Breakpoint | Behaviour |
|------------|-----------|
| Mobile | Drawer (hidden by default) |
| Desktop | Visible |

---

## Component Checklist

| Component | Props | Events | States | A11y | API |
|-----------|-------|--------|--------|------|-----|
| Button | ✅ | ✅ | ✅ | ✅ | — |
| Input | ✅ | ✅ | ✅ | ✅ | — |
| Badge | ✅ | — | — | ✅ | — |
| Card | ✅ | ✅ | — | ✅ | — |
| Modal | ✅ | ✅ | — | ✅ | — |
| LoginForm | ✅ | ✅ | ✅ | ✅ | — |
| UserMenu | ✅ | ✅ | ✅ | ✅ | ✅ |
| ProjectGrid | ✅ | ✅ | ✅ | — | — |
| Header | ✅ | — | — | — | — |
| Sidebar | ✅ | ✅ | — | — | — |

**Total Components:** {{n}}
**With API Calls:** {{n}}
**Orphaned (unused):** 0

---

## Frontend Utilities (Non-Component)

> Shared utility functions used across components. These are NOT components but are documented here for completeness.

| Utility | Purpose | Used By |
|---------|---------|---------|
| `formatDate(date)` | Format dates consistently | Multiple components |
| `formatCurrency(amount)` | Currency display | Pricing, stats |
| `truncateText(text, max)` | Truncate with ellipsis | Cards, lists |
| `debounce(fn, ms)` | Debounce function calls | Search input |
| `classNames(...args)` | Conditional class merging | All components |

**Note:** These utilities emerge during implementation. Document known needs here; implementation details are 0f.

---

## Notes

{{Any additional notes about components, design decisions, or patterns}}

---
Generated: {{timestamp}}
Phase: 40 (Component Specs)
---
