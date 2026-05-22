# Deep Component Specs — Optional Drop-In Pack

---
Type: Optional Extension
For: Complex components requiring detailed specification
When: Drop in when a component needs more than standard 0e coverage
---

## When to Use This Pack

Use deep specs when a component is:

- **Complex** — Multiple variants, states, and edge cases
- **Critical** — Core to user experience, can't afford ambiguity
- **Shared** — Used across multiple features/teams
- **Accessible** — Has significant a11y requirements
- **Interactive** — Complex keyboard/focus behaviour

## When NOT to Use

Skip deep specs for:

- Simple, single-purpose components
- Wrappers around existing libraries
- Components with obvious behaviour
- Prototypes or MVPs

---

## What's in This Pack

| Template | Purpose | Use When |
|----------|---------|----------|
| DEEP-SPEC-INDEX.md | Quick reference hub | Always (if using pack) |
| DEEP-SPEC-PROPS.md | Detailed prop specifications | Many props, complex types |
| DEEP-SPEC-EVENTS.md | Event contracts | Custom events, callbacks |
| DEEP-SPEC-STATE.md | Internal state machine | Complex state logic |
| DEEP-SPEC-SLOTS.md | Content projection | Multiple slots, slot props |
| DEEP-SPEC-STYLES.md | Styling API | Themeable, CSS variables |
| DEEP-SPEC-A11Y.md | Accessibility spec | Interactive, form elements |
| DEEP-SPEC-EXAMPLES.md | Usage examples | Complex usage patterns |

---

## How to Use

### Step 1: Identify Complex Components

During Phase 40 (Component Architecture), flag components needing deep specs:

```markdown
## Components

| Component | Complexity | Deep Spec? |
|-----------|------------|------------|
| Button | Low | No |
| DataTable | High | ✅ Yes |
| Modal | Medium | ✅ Yes |
| Icon | Low | No |
| FormWizard | High | ✅ Yes |
```

### Step 2: Create Deep Spec Folder

For each complex component:

```
frontend/
└── components/
    └── data-table/
        └── deep-spec/           ← Add this folder
            ├── INDEX.md
            ├── PROPS.md
            ├── EVENTS.md
            ├── STATE.md
            ├── SLOTS.md
            ├── STYLES.md
            ├── A11Y.md
            └── EXAMPLES.md
```

### Step 3: Fill Relevant Templates

Not every component needs every file. Pick what's relevant:

| Component Type | Likely Needs |
|----------------|--------------|
| Form inputs | PROPS, EVENTS, A11Y |
| Containers | SLOTS, STYLES |
| Interactive widgets | STATE, A11Y, EVENTS |
| Data display | PROPS, SLOTS, STYLES |

### Step 4: Reference from COMPONENTS.md

In your Phase 40 COMPONENTS.md, link to deep specs:

```markdown
### DataTable

**Deep Spec:** See `components/data-table/deep-spec/`

Brief summary here...
```

---

## Integration with 0e Phases

| Phase | How Deep Specs Fit |
|-------|-------------------|
| Phase 38 (Design System) | Deep STYLES.md supplements DESIGN-SYSTEM.md |
| Phase 40 (Components) | Deep specs expand on COMPONENTS.md entries |
| Phase 41 (State) | Deep STATE.md details component-level state |
| Phase 42 (Verification) | Deep A11Y.md feeds into A11Y-CHECKLIST.md |

---

## File Naming

When using deep specs, use this structure:

```
{{component}}/
└── deep-spec/
    ├── INDEX.md          ← Always start here
    ├── PROPS.md          ← If needed
    ├── EVENTS.md         ← If needed
    ├── STATE.md          ← If needed
    ├── SLOTS.md          ← If needed
    ├── STYLES.md         ← If needed
    ├── A11Y.md           ← If needed
    └── EXAMPLES.md       ← If needed
```

---

## Checklist

When adding deep specs to a project:

- [ ] Identified which components need deep specs
- [ ] Created `deep-spec/` folder for each
- [ ] Filled INDEX.md (always required)
- [ ] Filled other templates as needed
- [ ] Referenced from COMPONENTS.md
- [ ] Added to Phase 42 verification checklist

---

## Notes

These templates are adapted from Phase 29 (Full Specs) but streamlined for drop-in use with 0e Frontend phases. They provide more detail than standard 0e documents but less than a full Phase 29 specification.

---
Pack: Deep Component Specs
Version: 1.0.0
Compatible with: 0e Frontend (Phases 37-42)
---
