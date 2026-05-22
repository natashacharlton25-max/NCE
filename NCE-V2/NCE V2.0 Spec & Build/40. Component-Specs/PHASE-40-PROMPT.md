# PHASE 40: COMPONENT SPECS

---
Phase: 40
Name: Component Specs
Section: 0e. Frontend
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are specifying every reusable UI component in the frontend application. Each component spec defines props, state, events, variants, and accessibility requirements.

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| DESIGN-SYSTEM.md | `frontend/` | Component inventory, global patterns |
| PAGES.md | `frontend/` | Components used by pages |
| FRONTEND-BACKEND-CONTRACT.md | `frontend/` | API for data-fetching components |

---

## TASK

### Step 1: Collect All Components

Gather from:
1. DESIGN-SYSTEM.md component inventory
2. All components referenced in PAGES.md
3. Any "NEW: ComponentName" noted in pages

Create master list:

| Component | Type | Used By |
|-----------|------|---------|
| Button | Atomic | Multiple pages |
| Card | Atomic | Dashboard, Projects |
| LoginForm | Composite | Login page |
| ProjectGrid | Composite | Project List |
| etc. | | |

### Step 2: Verify No Orphans

Every component MUST be used by at least one page.

If a component isn't used → Remove from list (or verify it's truly needed)

### Step 3: Specify Each Component

For each component, define:

1. **Identity**
   - Name
   - Type (Atomic / Composite / Layout)
   - Purpose (one sentence)

2. **Props** (inputs from parent)
   - Name, type, required, default, description

3. **Internal State** (if any)
   - What state does it manage?
   - Initial values

4. **Events** (outputs to parent)
   - Name, payload, when fired

5. **Variants**
   - Size variants (sm, md, lg)
   - Style variants (primary, secondary, ghost)
   - State variants (disabled, loading)

6. **Children** (for composite components)
   - What child components does it contain?

7. **API Calls** (if component fetches data)
   - Which endpoints?
   - When called?

8. **States** (reference global patterns)
   - Loading: Which pattern ID?
   - Error: Which pattern ID?
   - Empty: Which pattern ID?

9. **Accessibility**
   - ARIA attributes
   - Keyboard interactions
   - Focus management

### Step 4: Reference Global Patterns

**DO NOT duplicate loading/error/empty definitions.**

Instead, reference by ID:

```
States:
- Loading: LOADING_SKELETON
- Error: ERROR_INLINE
- Empty: N/A
```

### Step 5: Note API-Calling Components

Some components fetch their own data. For these:

| Component | Endpoint | Trigger |
|-----------|----------|---------|
| UserMenu | GET /api/users/me | Mount |
| ProjectList | GET /api/projects | Mount + refresh |

Ensure every endpoint exists in FRONTEND-BACKEND-CONTRACT.md.

### Step 6: Verify Completeness

- [ ] Every component from pages is specified
- [ ] Every component has props defined
- [ ] Every interactive component has events
- [ ] Every data-fetching component maps to contract
- [ ] No orphaned components (all used somewhere)
- [ ] Accessibility defined for interactive components

### Step 7: Identify Complex Components (Optional Deep Specs)

Flag components that need detailed specifications beyond standard COMPONENTS.md coverage:

| Component | Complexity | Deep Spec Needed? |
|-----------|------------|-------------------|
| Button | Low | No |
| DataTable | High | ✅ Yes |
| Modal | Medium | ✅ Yes |
| FormWizard | High | ✅ Yes |

**Criteria for deep spec:**
- Many variants, states, or edge cases
- Critical to user experience
- Significant accessibility requirements
- Complex keyboard/focus behaviour

**For flagged components:**
1. Create `deep-spec/` folder inside the component folder
2. Use templates from `0. Admin/0e. Frontend/optional-deep-specs/`
3. Fill only the templates relevant to that component
4. Reference from COMPONENTS.md: `**Deep Spec:** See components/{{name}}/deep-spec/`

**Deep Spec Files Available:**
- DEEP-SPEC-INDEX.md (always start here)
- DEEP-SPEC-PROPS.md
- DEEP-SPEC-EVENTS.md
- DEEP-SPEC-STATE.md
- DEEP-SPEC-SLOTS.md
- DEEP-SPEC-STYLES.md
- DEEP-SPEC-A11Y.md
- DEEP-SPEC-EXAMPLES.md

---

## FILE SPLIT RULES

| Condition | Action |
|-----------|--------|
| Default | Single COMPONENTS.md file |
| File > ~500 LOC | Split into `components/COMPONENT-{name}.md` |
| Large component library | Split by category (atomic, composite, layout) |

---

## OUTPUT

Create: `frontend/COMPONENTS.md`

Or if split:
```
frontend/
└── components/
    ├── COMPONENT-button.md
    ├── COMPONENT-card.md
    ├── COMPONENT-login-form.md
    └── ...
```

Use template: `COMPONENTS-TEMPLATE.md`

---

## APPROVAL

Present components to user.

Verify:
- [ ] All components from pages are defined
- [ ] Props are complete and typed
- [ ] Events are documented
- [ ] States reference global patterns
- [ ] Accessibility is addressed

**If user says APPROVE:** Proceed to Phase 41
**If user says REVISE:** Make changes, re-present

---
Generated: {{timestamp}}
Phase: 40 (Component Specs)
---
