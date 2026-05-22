# {{Component Name}} — Deep Spec Index

---
Component: {{component_name}}
Type: UI Component / Feature Component / Layout / Page
Category: {{category}}
Framework: Svelte / Astro / React / Vue
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

{{One sentence — what this component does and why it needs a deep spec}}

---

## Quick Reference

### Props

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {{prop}} | `{{type}}` | Yes/No | `{{default}}` | {{description}} |

### Events

| Event | Payload | When Fired |
|-------|---------|------------|
| `{{event}}` | `{{type}}` | {{when}} |

### Slots

| Slot | Purpose |
|------|---------|
| default | {{description}} |
| {{slot}} | {{description}} |

---

## Spec Files

| File | Purpose | Status |
|------|---------|--------|
| INDEX.md | This file — quick reference | ✅ |
| PROPS.md | All props in detail | ✅ / ❌ / N/A |
| EVENTS.md | Events and callbacks | ✅ / ❌ / N/A |
| STATE.md | Internal state management | ✅ / ❌ / N/A |
| SLOTS.md | Content projection | ✅ / ❌ / N/A |
| STYLES.md | CSS variables, theming | ✅ / ❌ / N/A |
| A11Y.md | Accessibility requirements | ✅ / ❌ / N/A |
| EXAMPLES.md | Usage examples | ✅ / ❌ / N/A |

---

## Why Deep Spec?

This component needs detailed specification because:

- [ ] Complex — many variants/states/edge cases
- [ ] Critical — core to user experience
- [ ] Shared — used across multiple features
- [ ] Accessible — significant a11y requirements
- [ ] Interactive — complex keyboard/focus behaviour
- [ ] Other: {{reason}}

---

## Variants

| Variant | Description | Key Differences |
|---------|-------------|-----------------|
| default | {{description}} | — |
| {{variant}} | {{description}} | {{differences}} |

---

## States

| State | When | Visual Change |
|-------|------|---------------|
| default | Resting | — |
| hover | Mouse over | {{change}} |
| focus | Keyboard focus | {{change}} |
| active | Being clicked | {{change}} |
| disabled | Cannot interact | {{change}} |
| loading | Operation pending | {{change}} |
| error | Something wrong | {{change}} |

---

## Component Relationships

### Uses

| Component | How |
|-----------|-----|
| {{component}} | {{relationship}} |

### Used By

| Component | Context |
|-----------|---------|
| {{component}} | {{where}} |

---

## Design Reference

| Resource | Link |
|----------|------|
| Figma mockup | {{link}} |
| Design system | {{link}} |
| Related component | {{link}} |

---

## Notes

{{Any additional context, decisions, or gotchas}}

---
Template: DEEP-SPEC-INDEX.md
Pack: Optional Deep Specs
---
