# PHASE 38a: GENERATE DESIGN REQUEST

---
Phase: 38a (Pre-requisite to 38)
Name: Generate Design Request
Section: 0e. Frontend
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are creating a design brief for the design team. They are designers, not developers — write in plain English, avoid technical jargon, and explain the *why* behind what you're asking for.

---

## WHEN TO RUN

Run this **before** the design team starts creating mockups.

**Trigger:** User says something like:
- "What designs do we need?"
- "Generate design request"
- "Brief the design team"
- "Start Phase 38"
- "What should the designers create?"

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| PAGES.md | `frontend/` | List of screens needed (if exists) |
| COMPONENTS.md | `frontend/` | Key elements needed (if exists) |
| User stories / requirements | `specs/` | Understand what users do |
| TECH-STACK.md | `precode/` | Any design system constraints |

**If PAGES.md doesn't exist yet:** Ask for a quick list of what the app does, who uses it, and what screens they'll need.

---

## LANGUAGE RULES

### ✅ DO say:
- "Screen" not "page" or "route"
- "Element" or "piece" not "component"
- "What users do here" not "user actions"
- "Save as" not "filename"
- "Colour" not "color token"
- "When things go wrong" not "error states"
- "While waiting" not "loading states"

### ❌ DON'T say:
- Route, endpoint, API
- Props, state, events
- Mount, render, hydrate
- Any code examples in the brief itself
- Technical implementation details

### Describe by PURPOSE not FUNCTION:
- ❌ "This component fetches user data and displays it"
- ✅ "This shows the logged-in user's name and profile picture"

---

## TASK

### Step 1: Understand the Product

Before listing screens, write a brief intro:
- What is this product?
- Who uses it?
- What's the overall feel/mood?

### Step 2: List All Screens

For each screen, describe:
- **What it's called** (simple name)
- **What users do here** (in plain English)
- **What's on this screen** (describe visually, not technically)

**Example:**

```markdown
#### Dashboard
**Save as:** `page-dashboard.png`

**What users do here:** 
This is the home base after logging in. Users get an overview of their projects and recent activity.

**What's on this screen:**
- A header bar across the top with the logo, main navigation, and user's profile picture
- A sidebar on the left showing their project list
- The main area showing:
  - Welcome message with user's name
  - 3-4 summary cards (total projects, tasks due, team members)
  - A list of recent activity (who did what, when)
```

### Step 3: List Key Elements

Elements are reusable pieces that appear on multiple screens.

For each element, describe:
- **What it is** (in simple terms)
- **Where it appears** (which screens)
- **What variations are needed** (different states, sizes)

**Example:**

```markdown
#### Project Card
**Save as:** `component-card-project.png`

**What it is:**
A card showing one project — users click it to open that project.

**Where it appears:**
Dashboard sidebar, Projects screen (as a grid)

**What to show:**
- Project thumbnail or colour
- Project name
- Brief description (1-2 lines)
- Status indicator (active, paused, completed)
- When it was last updated
- Show both: how it looks normally AND how it looks when hovering
```

### Step 4: Explain States

Designers need to know about:
- **Loading** — what users see while waiting
- **Empty** — what users see when there's nothing yet
- **Errors** — what users see when something goes wrong
- **Success** — how the app confirms something worked

**Explain each in plain terms:**

```markdown
### While Waiting (Loading States)

When content is loading, we don't want users staring at a blank screen. 
Please design:

1. **Skeleton screen** (`state-loading-skeleton.png`)
   Faded grey shapes that mimic where real content will appear. 
   Like a ghost version of the page.

2. **Spinner** (`state-loading-spinner.png`)
   A simple spinning indicator for buttons or smaller areas.
```

### Step 5: Style Questions

Ask about visual preferences in accessible terms:
- Colours (provide a simple table to fill in)
- Fonts (ask for names, not CSS)
- Overall mood (ask them to describe in their own words)
- Corners, shadows, spacing (give visual options, not values)

### Step 6: Delivery Instructions

Explain:
- What file format (PNG)
- What filenames to use (exact names for each)
- Where to put them (`/mockups/` folder)
- What to do when they're done

---

## OUTPUT

Create: `frontend/DESIGN-REQUEST.md`

Use template: `MOCKUP-REQUEST-TEMPLATE.md`

Also create: `mockups/README.md` with a friendly note:

```markdown
# Mockups Folder

👋 Hi Design Team!

Drop your design files here using the filenames from `DESIGN-REQUEST.md`.

When everything's ready, let the dev team know and we'll take it from there.

Thanks!
```

---

## WHAT TO TELL THE USER (to pass to design team)

After generating DESIGN-REQUEST.md:

> I've created a design brief at `frontend/DESIGN-REQUEST.md`.
>
> **It's written for designers, not developers** — plain English, no jargon.
>
> **What it includes:**
> - {{n}} screens to design
> - {{n}} reusable elements
> - Loading, empty, and error states
> - Style/colour questions to answer
> - Exact filenames for each deliverable
> - A checklist to track progress
>
> **You can send this directly to your design team.**
>
> When designs are ready in `/mockups/`, let me know and I'll process them automatically.

---

## TONE CHECK

Before finalising, re-read the document and ask:
- Would a designer with no coding knowledge understand this?
- Have I explained *why* we need each thing?
- Have I avoided all technical jargon?
- Is the tone friendly and collaborative, not demanding?

If any answer is "no" — rewrite that section.

---

## NEXT STEP

When user confirms designs are ready → Run Phase 38 (Design System extraction)

---
Generated: {{timestamp}}
Phase: 38a (Generate Design Request)
---
