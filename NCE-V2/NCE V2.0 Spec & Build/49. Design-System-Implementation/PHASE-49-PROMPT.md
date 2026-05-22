# Phase 49: Design System Implementation

---
Phase: 49
Section: 0f. Implementation
Name: Design System Implementation
Purpose: Implement visual foundation from design specs
---

## Role

You are implementing the design system. Your job is to translate DESIGN-SYSTEM.md into working design tokens and base styles using whatever approach is specified in TECH-STACK.md.

---

## Inputs

| Document | Location | What to Extract |
|----------|----------|-----------------|
| DESIGN-SYSTEM.md | 0e outputs | Tokens, typography, colors, spacing |
| TECH-STACK.md | 0d outputs | Styling approach (CSS, Tailwind, CSS-in-JS, etc.) |
| Mockups | /mockups/ | Visual reference |

---

## Process

### Step 1: Identify Styling Approach

Check TECH-STACK.md for the styling method:

| Approach | Implementation |
|----------|----------------|
| CSS Variables | Create tokens file with :root variables |
| Tailwind CSS | Configure tailwind.config with theme |
| CSS-in-JS | Create theme object (styled-components, Emotion, etc.) |
| SCSS/Sass | Create variables file with token maps |
| CSS Modules | Create tokens as CSS variables or JS constants |

**Use whatever TECH-STACK.md specifies. Do not assume a specific approach.**

### Step 2: Extract Tokens from DESIGN-SYSTEM.md

Create a token inventory from the spec:

| Category | Token Name | Value | Notes |
|----------|------------|-------|-------|
| Color | primary | {{value}} | From DESIGN-SYSTEM.md |
| Color | text | {{value}} | From DESIGN-SYSTEM.md |
| Spacing | sm | {{value}} | From DESIGN-SYSTEM.md |
| Font Size | base | {{value}} | From DESIGN-SYSTEM.md |
| Radius | md | {{value}} | From DESIGN-SYSTEM.md |
| Shadow | sm | {{value}} | From DESIGN-SYSTEM.md |

**Extract ALL tokens listed in DESIGN-SYSTEM.md. Do not invent values.**

### Step 3: Implement Tokens

Based on the styling approach from TECH-STACK.md, implement tokens appropriately.

**The format depends on TECH-STACK.md.** Common patterns:

- CSS Variables → `:root { --token-name: value; }`
- Tailwind → `theme: { colors: { ... } }`
- CSS-in-JS → `export const theme = { ... }`
- SCSS → `$token-name: value;`

### Step 4: Implement Typography

From DESIGN-SYSTEM.md typography section:

- Font families
- Font sizes (scale)
- Font weights
- Line heights
- Letter spacing

### Step 5: Implement Color System

From DESIGN-SYSTEM.md colors section:

- Primary/secondary colors
- Neutral/gray scale
- Semantic colors (success, warning, error, info)
- Background/surface colors
- Border colors
- Focus colors

### Step 6: Implement Spacing System

From DESIGN-SYSTEM.md spacing section:

- Spacing scale
- Semantic spacing names (if defined)

### Step 7: Implement Additional Tokens

From DESIGN-SYSTEM.md:

- Border radii
- Shadows
- Transitions/animations
- Z-index scale
- Breakpoints

### Step 8: Implement Dark Mode (If Specified)

Only if DESIGN-SYSTEM.md specifies dark mode:

- Override color tokens for dark theme
- Use approach specified in specs (media query, class toggle, etc.)

### Step 9: Create Base Styles

Implement base/reset styles per TECH-STACK.md approach:

- Box-sizing reset
- Typography defaults
- Focus styles (accessibility)
- Any global styles from DESIGN-SYSTEM.md

### Step 10: Verify Implementation

Compare implementation against DESIGN-SYSTEM.md:

| Token | Spec Value | Implemented Value | Match |
|-------|------------|-------------------|-------|
| primary color | {{spec value}} | {{your value}} | ✅/❌ |
| spacing md | {{spec value}} | {{your value}} | ✅/❌ |

**Every token in DESIGN-SYSTEM.md must be implemented.**

---

## Output

| Output | Location | Status |
|--------|----------|--------|
| Token definitions | Per TECH-STACK.md approach | ☐ Created |
| Typography styles | Per TECH-STACK.md approach | ☐ Created |
| Color system | Per TECH-STACK.md approach | ☐ Created |
| Spacing system | Per TECH-STACK.md approach | ☐ Created |
| Base styles | Per TECH-STACK.md approach | ☐ Created |
| Dark mode (if specified) | Per DESIGN-SYSTEM.md | ☐ Created |

---

## Checklist

Before moving to Phase 50:

- [ ] All tokens from DESIGN-SYSTEM.md implemented
- [ ] Typography matches spec exactly
- [ ] Colors match spec exactly
- [ ] Spacing matches spec exactly
- [ ] Border radii match spec
- [ ] Shadows match spec
- [ ] Responsive breakpoints configured
- [ ] Dark mode implemented (if specified in DESIGN-SYSTEM.md)
- [ ] Focus styles meet accessibility requirements
- [ ] Tokens are usable in components

---

## Implementation Log Entry

```markdown
## Phase 49: Design System Implementation

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Implemented design tokens per DESIGN-SYSTEM.md using {{approach from TECH-STACK.md}}.

### Tokens Implemented

| Category | Count | Source |
|----------|-------|--------|
| Colors | {{count}} | DESIGN-SYSTEM.md |
| Typography | {{count}} | DESIGN-SYSTEM.md |
| Spacing | {{count}} | DESIGN-SYSTEM.md |
| Borders | {{count}} | DESIGN-SYSTEM.md |
| Shadows | {{count}} | DESIGN-SYSTEM.md |

### Files Created
- {{file path}} — {{purpose}}

### Performance Baseline
- Bundle size: {{size}} (target: {{target from DESIGN-SYSTEM.md if specified}})

### Deviations
- None

### Notes
{{Any observations}}
```

---

## Rules

1. **Use TECH-STACK.md approach** — Don't assume CSS/Tailwind/CSS-in-JS
2. **Match DESIGN-SYSTEM.md exactly** — All tokens, all values
3. **Don't invent tokens** — Only implement what's specified
4. **Check accessibility** — Focus styles must be visible (3:1 contrast minimum)

---

## Next Phase

After completing Phase 49, proceed to:

**Phase 50: Component Implementation**

---
