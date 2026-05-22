# Phase 50: Component Implementation

---
Phase: 50
Section: 0f. Implementation
Name: Component Implementation
Purpose: Build all UI components per specification
---

## Role

You are implementing UI components. Your job is to create every component specified in COMPONENTS.md using the framework and patterns from TECH-STACK.md.

---

## Inputs

| Document | Location | What to Extract |
|----------|----------|-----------------|
| COMPONENTS.md | 0e outputs | All components, props, events, states |
| DESIGN-SYSTEM.md | 0e outputs | Styling tokens (reference) |
| A11Y-CHECKLIST.md | 0e outputs | Accessibility requirements |
| TECH-STACK.md | 0d outputs | Framework, component patterns |
| shared-types/ | From Phase 43 | Type definitions |
| optional-deep-specs/ | 0e (if used) | Complex component specs |
| Design tokens | From Phase 49 | Implemented tokens |

---

## Process

### Step 1: Extract Component List

From COMPONENTS.md, create a complete inventory:

| Component | Category | Props | Events | Variants | Complexity |
|-----------|----------|-------|--------|----------|------------|
| {{name}} | {{category}} | {{count}} | {{count}} | {{count}} | Low/Med/High |

### Step 2: Check for Deep Specs

Review `optional-deep-specs/` for complex components. If a component has deep specs, read all relevant files before implementing:

- INDEX.md — Overview
- PROPS.md — Detailed prop specs
- EVENTS.md — Event contracts
- STATE.md — Internal state
- A11Y.md — Accessibility details
- etc.

### Step 3: Implement Each Component

For each component in COMPONENTS.md:

1. **Create component file** per framework conventions
2. **Implement all props** per spec (typed from shared-types/)
3. **Implement all events** per spec
4. **Implement all variants** per spec
5. **Implement all states** (hover, focus, disabled, loading, error)
6. **Add accessibility attributes** per A11Y-CHECKLIST.md
7. **Apply design tokens** from Phase 49

### Step 4: Component Organization

Organize per REPO-STRUCTURE.md or TECH-STACK.md conventions.

### Step 5: Verify Each Component

For each component, verify:

- [ ] All props from spec
- [ ] All events from spec
- [ ] All variants work
- [ ] All states render
- [ ] Accessibility requirements met
- [ ] Design tokens used (no hardcoded values)
- [ ] Types correct

---

## Output

| Output | Location | Status |
|--------|----------|--------|
| All components | Per REPO-STRUCTURE.md | ☐ Created |

---

## Checklist

Before moving to Phase 51:

- [ ] Every component in COMPONENTS.md implemented
- [ ] Props match spec exactly
- [ ] Events match spec exactly
- [ ] All variants work
- [ ] All states work
- [ ] Accessibility attributes present
- [ ] Deep spec requirements met (where applicable)
- [ ] Design tokens used correctly
- [ ] No type errors

---

## Implementation Log Entry

```markdown
## Phase 50: Component Implementation

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Implemented {{count}} components per COMPONENTS.md.

### Components

| Category | Count | Status |
|----------|-------|--------|
| {{category}} | {{count}} | ✅ |

### Deep Specs Applied
- {{component}}: {{specs used}}

### Deviations
- None

### Notes
{{observations}}
```

---

## Rules

1. **Implement ALL components** — Nothing missing from COMPONENTS.md
2. **Use TECH-STACK.md framework** — Don't assume specific framework
3. **Use shared-types/** — Type all props from shared source
4. **Use design tokens** — From Phase 49
5. **Check accessibility** — Every interactive component

---

## Next Phase

**Phase 51: Page Implementation**

---
