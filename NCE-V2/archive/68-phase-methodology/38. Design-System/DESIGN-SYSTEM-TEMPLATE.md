# Design System — {{Project Name}}

---
Status: DRAFT | IN REVIEW | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
---

## Overview

This document captures the visual design system extracted from mockups. It is implementation-agnostic and serves as the source of truth for UI consistency.

**What this includes:** Design tokens, component inventory, layout patterns, global UX patterns
**What this excludes:** CSS, framework code, component logic

---

## Design Tokens

### Colors

#### Brand Colors

| Token | Value | Usage |
|-------|-------|-------|
| `color-primary` | {{#hexcode}} | Primary actions, links |
| `color-primary-hover` | {{#hexcode}} | Primary hover state |
| `color-secondary` | {{#hexcode}} | Secondary actions |
| `color-accent` | {{#hexcode}} | Highlights, badges |

#### Neutral Colors

| Token | Value | Usage |
|-------|-------|-------|
| `color-background` | {{#hexcode}} | Page background |
| `color-surface` | {{#hexcode}} | Cards, panels |
| `color-border` | {{#hexcode}} | Borders, dividers |
| `color-text-primary` | {{#hexcode}} | Main text |
| `color-text-secondary` | {{#hexcode}} | Secondary text |
| `color-text-muted` | {{#hexcode}} | Disabled, hints |

#### Status Colors

| Token | Value | Usage |
|-------|-------|-------|
| `color-success` | {{#hexcode}} | Success states |
| `color-warning` | {{#hexcode}} | Warning states |
| `color-error` | {{#hexcode}} | Error states |
| `color-info` | {{#hexcode}} | Info states |

---

### Typography

#### Font Families

| Token | Value | Usage |
|-------|-------|-------|
| `font-primary` | {{font name}} | Body text |
| `font-heading` | {{font name}} | Headings |
| `font-mono` | {{font name}} | Code |

#### Font Sizes

| Token | Size | Line Height | Usage |
|-------|------|-------------|-------|
| `text-xs` | {{size}} | {{lh}} | Captions, labels |
| `text-sm` | {{size}} | {{lh}} | Small text |
| `text-base` | {{size}} | {{lh}} | Body text |
| `text-lg` | {{size}} | {{lh}} | Large body |
| `text-xl` | {{size}} | {{lh}} | H4 |
| `text-2xl` | {{size}} | {{lh}} | H3 |
| `text-3xl` | {{size}} | {{lh}} | H2 |
| `text-4xl` | {{size}} | {{lh}} | H1 |

#### Font Weights

| Token | Value | Usage |
|-------|-------|-------|
| `font-normal` | 400 | Body text |
| `font-medium` | 500 | Emphasis |
| `font-semibold` | 600 | Subheadings |
| `font-bold` | 700 | Headings |

---

### Spacing

Base unit: {{4px / 8px}}

| Token | Value | Usage |
|-------|-------|-------|
| `space-1` | {{value}} | Minimal spacing |
| `space-2` | {{value}} | Tight spacing |
| `space-3` | {{value}} | Default spacing |
| `space-4` | {{value}} | Medium spacing |
| `space-6` | {{value}} | Large spacing |
| `space-8` | {{value}} | Section spacing |
| `space-12` | {{value}} | Page spacing |

---

### Borders

#### Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `radius-none` | 0 | Sharp corners |
| `radius-sm` | {{value}} | Subtle rounding |
| `radius-md` | {{value}} | Default rounding |
| `radius-lg` | {{value}} | Cards, modals |
| `radius-full` | 9999px | Pills, avatars |

#### Border Width

| Token | Value |
|-------|-------|
| `border-1` | 1px |
| `border-2` | 2px |

---

### Shadows

| Token | Value | Usage |
|-------|-------|-------|
| `shadow-sm` | {{value}} | Subtle elevation |
| `shadow-md` | {{value}} | Cards |
| `shadow-lg` | {{value}} | Dropdowns, modals |

---

## Component Inventory

### Atomic Components

| Component | Description | Variants |
|-----------|-------------|----------|
| Button | Clickable action | primary, secondary, ghost, danger |
| Input | Text input field | text, email, password, search |
| Checkbox | Boolean toggle | default, indeterminate |
| Radio | Single selection | default |
| Select | Dropdown selection | single, multi |
| Badge | Status indicator | success, warning, error, info |
| Avatar | User image | sizes: sm, md, lg |
| Icon | SVG icon | sizes: sm, md, lg |

### Composite Components

| Component | Description | Contains |
|-----------|-------------|----------|
| Card | Content container | Header, Body, Footer |
| Modal | Overlay dialog | Header, Body, Actions |
| Dropdown | Menu overlay | Trigger, Menu, Items |
| Toast | Notification | Icon, Message, Dismiss |
| Table | Data display | Header, Rows, Pagination |
| Form | Input collection | Fields, Validation, Submit |

### Layout Components

| Component | Description | Usage |
|-----------|-------------|-------|
| Header | Top navigation | All pages |
| Sidebar | Side navigation | Dashboard pages |
| Footer | Bottom content | Public pages |
| Container | Width constraint | Page content |
| Grid | Column layout | Content arrangement |

---

## Layout Logical Groups

### Page: {{Page Name}}

```
{{PageName}}
├── Header (full width, sticky)
│   ├── Left: Logo
│   ├── Center: Navigation
│   └── Right: UserMenu
│
├── Main (below header)
│   ├── Sidebar (left, fixed width: {{width}})
│   │   ├── Top: {{component}}
│   │   ├── Middle: {{component}} (scrollable)
│   │   └── Bottom: {{component}} (sticky)
│   │
│   └── Content (right of sidebar, flexible)
│       ├── Top: PageHeader
│       ├── Middle: {{main content}}
│       └── Bottom: {{footer content}}
│
└── Footer (optional)
```

### Page: {{Another Page}}

```
{{layout structure}}
```

(Repeat for each page in mockups)

---

## Global UX Patterns

### Loading Patterns

| ID | Name | Visual | When to Use |
|----|------|--------|-------------|
| `LOADING_SKELETON` | Skeleton | Gray shapes mimicking content | Initial page/section load |
| `LOADING_SPINNER` | Spinner | Centered rotating indicator | Button actions, small areas |
| `LOADING_PROGRESS` | Progress Bar | Horizontal progress indicator | File uploads, known duration |
| `LOADING_DOTS` | Dots | Animated dots | Inline loading |

### Error Patterns

| ID | Name | Visual | When to Use |
|----|------|--------|-------------|
| `ERROR_TOAST` | Toast | Brief notification (top-right) | Non-blocking errors |
| `ERROR_INLINE` | Inline | Red text below field | Form validation |
| `ERROR_BANNER` | Banner | Full-width alert bar | Page-level warnings |
| `ERROR_FULLPAGE` | Full Page | Centered error + retry CTA | Page load failures |

### Empty State Patterns

| ID | Name | Visual | When to Use |
|----|------|--------|-------------|
| `EMPTY_GENERIC` | Generic | Icon + message + CTA | No data yet |
| `EMPTY_SEARCH` | No Results | Search icon + "No results" | Search with no matches |
| `EMPTY_FILTERED` | No Matches | Filter icon + "No matches" + Clear | Filters exclude all |

### Success Patterns

| ID | Name | Visual | When to Use |
|----|------|--------|-------------|
| `SUCCESS_TOAST` | Toast | Green notification | Action completed |
| `SUCCESS_INLINE` | Inline | Green checkmark + message | Form submission |

---

## Assets

### Icons

| Source | Location | Usage |
|--------|----------|-------|
| {{Library name}} | {{install command or path}} | UI icons |
| Custom icons | `{{path in repo}}` | Brand-specific icons |

### Images

| Type | Location | Format |
|------|----------|--------|
| Logo | `{{path}}` | SVG |
| Illustrations | `{{path}}` | SVG / PNG |
| Placeholders | `{{path}}` | PNG |

### Fonts

| Font | Source | Weights |
|------|--------|---------|
| {{font name}} | {{Google Fonts / local path}} | {{weights}} |

---

## Responsive Breakpoints

| Name | Min Width | Columns | Notes |
|------|-----------|---------|-------|
| `mobile` | 0 | 1 | Stack everything |
| `tablet` | {{640px}} | 2 | Sidebar collapses to drawer |
| `desktop` | {{1024px}} | 3+ | Full layout |
| `wide` | {{1280px}} | 3+ | Max content width |

### Responsive Behaviours

| Component | Mobile | Tablet | Desktop |
|-----------|--------|--------|---------|
| Header | Hamburger menu | Hamburger menu | Full nav |
| Sidebar | Hidden (drawer) | Hidden (drawer) | Visible |
| Cards | Full width | 2 columns | 3 columns |

---

## Accessibility Baseline

### Contrast

| Element | Minimum Ratio |
|---------|---------------|
| Normal text | 4.5:1 |
| Large text (18px+) | 3:1 |
| UI components | 3:1 |

### Focus States

- All interactive elements have visible focus indicator
- Focus ring: `{{description}}`
- Tab order follows visual order

### Keyboard Navigation

| Key | Action |
|-----|--------|
| Tab | Move to next focusable element |
| Shift+Tab | Move to previous element |
| Enter | Activate buttons, links |
| Space | Toggle checkboxes, select options |
| Escape | Close modals, dropdowns |
| Arrow keys | Navigate within menus, tabs |

### Screen Reader

- Semantic HTML elements used
- Images have alt text
- Form fields have labels
- ARIA used where semantic HTML insufficient

---

## Performance Constraints

### Bundle Boundaries

| Boundary | Contents | Load Strategy |
|----------|----------|---------------|
| Critical | {{components}} | Inline / Immediate |
| Main | {{components}} | Eager |
| Deferred | {{components}} | Lazy load |

### Hydration Strategy

| Strategy | Description | When to Use |
|----------|-------------|-------------|
| Full hydration | Entire page interactive | SPA, highly interactive |
| Partial hydration | Only interactive islands | Content-heavy, Astro-style |
| Progressive | Hydrate on interaction | Performance-critical |

**Project Choice:** {{Full / Partial / Progressive / N/A (CSR only)}}

### Critical Rendering Path

| Priority | Assets | Notes |
|----------|--------|-------|
| 1 (Blocking) | {{fonts, critical CSS}} | Must load before render |
| 2 (High) | {{hero images, above-fold}} | Preload |
| 3 (Normal) | {{below-fold content}} | Standard loading |
| 4 (Low) | {{analytics, non-essential}} | Defer |

### Performance Targets

| Metric | Target | Hard Limit |
|--------|--------|------------|
| First Contentful Paint | {{target}} | {{limit}} |
| Largest Contentful Paint | {{target}} | {{limit}} |
| Time to Interactive | {{target}} | {{limit}} |
| Cumulative Layout Shift | {{target}} | {{limit}} |

**Note:** These are SPECIFICATIONS, not measurements. Actual performance is verified in 0g Testing.

---

## Notes

{{Any additional design notes, decisions, or clarifications}}

---
Generated: {{timestamp}}
Phase: 38 (Design System)
Source: User-provided mockups
---
