# PHASE 38: DESIGN SYSTEM

---
Phase: 38
Name: Design System
Section: 0e. Frontend
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are capturing the visual design from user-provided mockups into a structured, implementation-agnostic design system document.

---

## PRE-REQUISITE: Phase 38a

Before running Phase 38, ensure Phase 38a (Design Request) has been completed:

- [ ] `frontend/DESIGN-REQUEST.md` exists
- [ ] Design team has delivered mockups to `/mockups/` folder
- [ ] Filenames match those specified in DESIGN-REQUEST.md

**If mockups aren't ready:** Run Phase 38a first to generate the design brief.

---

## INPUTS REQUIRED

### From Design Team

Mockups should already be in `/mockups/` folder with filenames from DESIGN-REQUEST.md:

| File Pattern | Contains |
|--------------|----------|
| `page-*.png` | Screen designs |
| `component-*.png` | Reusable element designs |
| `state-*.png` | Loading, error, empty states |
| `tokens.css` | Colour and style tokens (optional) |

**If `/mockups/` is empty or incomplete:** Go back to Phase 38a.

**Example component breakdown format:**

```
Page: Dashboard

Layout:
- Header (full width)
  - Logo (left)
  - Navigation: Home, Projects, Settings (center)
  - UserMenu (right): avatar + dropdown

- Sidebar (left, 250px)
  - ProjectList: list of project cards
  - CreateProjectButton (bottom)

- MainContent (right of sidebar)
  - StatsRow: 3 StatCards in a row
  - RecentActivity: table with columns [Date, Action, User]
```

### If No Mockups Available

**Fallback Option:** If user cannot provide visual mockups:

1. **Ask for detailed text descriptions:**
   - Page list with purpose
   - Component inventory with relationships
   - Color preferences (or "use standard")
   - Layout style (sidebar? header-only? etc.)

2. **Use standard design patterns:**
   - Default to TECH-STACK.md UI library defaults
   - Standard spacing/sizing from library (e.g., Tailwind defaults)
   - Common layout patterns (header + sidebar + main)

3. **Document assumptions explicitly:**
   - "No mockup provided — using standard {{library}} patterns"
   - "Color scheme: {{library}} default / user specified X"
   - "Layout: standard dashboard pattern"

**Warning:** Without mockups, design decisions are more likely to need revision. Encourage user to provide at least rough sketches.

### From Previous Phases

| Input | Location | Purpose |
|-------|----------|---------|
| TECH-STACK.md | `precode/` | Know the framework context |
| REPO-STRUCTURE.md | `precode/` | Know where assets live |

---

## TASK

### Step 1: Request Mockups

Ask user:

> Please provide:
> 1. Mockup images (screenshots or design files)
> 2. Component breakdown for each page (text describing the structure)
>
> Example format for component breakdown:
> ```
> Page: [Page Name]
> Layout:
> - [Component] (position)
>   - [Child component] (position/role)
> ```

### Step 2: Extract Design Tokens

From the mockups, identify:

**Colors:**
- Primary, secondary, accent
- Background colors
- Text colors
- Border colors
- Status colors (success, warning, error, info)

**Typography:**
- Font families
- Font sizes (heading 1-6, body, small, caption)
- Font weights
- Line heights

**Spacing:**
- Base unit (e.g., 4px or 8px)
- Common spacings (xs, sm, md, lg, xl)

**Borders:**
- Border radii
- Border widths

**Shadows:**
- Elevation levels

### Step 3: Create Component Inventory

List ALL components visible in mockups:

| Component | Type | Appears On |
|-----------|------|------------|
| Button | Atomic | Multiple pages |
| Card | Atomic | Dashboard, Projects |
| Header | Layout | All pages |
| etc. | | |

### Step 4: Define Layout Logical Groups

For each page, describe spatial relationships in text:

```
Header:
├── Left: Logo
├── Center: Navigation (Home | Projects | Settings)
└── Right: UserMenu (Avatar + Name + Dropdown)

Sidebar:
├── Top: SearchInput
├── Middle: ProjectList (scrollable)
└── Bottom: CreateButton (sticky)

MainContent:
├── Top: PageTitle + ActionButtons
├── Middle: ContentArea
└── Bottom: Pagination
```

**This is critical for AI code generation.** Text descriptions > pixel coordinates.

### Step 5: Define Global UX Patterns

Create reusable patterns with IDs:

**Loading Patterns:**
| ID | Name | Description | Use When |
|----|------|-------------|----------|
| LOADING_SKELETON | Skeleton | Gray placeholders mimicking content shape | Content loading |
| LOADING_SPINNER | Spinner | Centered spinning indicator | Actions processing |
| LOADING_PROGRESS | Progress Bar | Horizontal progress indicator | Known duration |

**Error Patterns:**
| ID | Name | Description | Use When |
|----|------|-------------|----------|
| ERROR_TOAST | Toast | Brief notification, auto-dismiss | Non-blocking errors |
| ERROR_INLINE | Inline | Red text below input | Validation errors |
| ERROR_FULLPAGE | Full Page | Centered error with retry | Page load failures |

**Empty State Patterns:**
| ID | Name | Description |
|----|------|-------------|
| EMPTY_GENERIC | Generic Empty | Icon + message + optional CTA |
| EMPTY_SEARCH | No Results | Search-specific empty state |

### Step 6: Document Assets

| Asset Type | Location | Examples |
|------------|----------|----------|
| Icons | `{{path from REPO-STRUCTURE}}` | Lucide, Heroicons, custom |
| Images | `{{path}}` | Logos, illustrations |
| Fonts | `{{path or CDN}}` | Font files or Google Fonts |

### Step 7: Define Responsive Breakpoints

| Name | Min Width | Max Width | Notes |
|------|-----------|-----------|-------|
| mobile | 0 | 639px | Single column |
| tablet | 640px | 1023px | Two column |
| desktop | 1024px | ∞ | Full layout |

### Step 8: Accessibility Baseline

- Minimum contrast ratio: {{4.5:1 for text}}
- Focus indicators: {{visible on all interactive elements}}
- Keyboard navigation: {{required}}
- Screen reader: {{semantic HTML, ARIA where needed}}

---

## WHAT DESIGN SYSTEM IS NOT

**Explicitly exclude:**
- ❌ Pixel-perfect CSS (`padding: 16px`)
- ❌ Framework-specific class names (`className="flex"`)
- ❌ Implementation code
- ❌ Component logic or state
- ❌ Animation keyframes

**Keep it abstract and implementation-agnostic.**

---

## OUTPUT

Create: `frontend/DESIGN-SYSTEM.md`

Use template: `DESIGN-SYSTEM-TEMPLATE.md`

---

## APPROVAL

Present design system to user.

Verify:
- [ ] All mockup pages are represented
- [ ] All visible components are inventoried
- [ ] Layout logical groups make sense
- [ ] Global patterns are defined
- [ ] Asset locations are correct

**If user says APPROVE:** Proceed to Phase 39
**If user says REVISE:** Make changes, re-present

---
Generated: {{timestamp}}
Phase: 38 (Design System)
---
