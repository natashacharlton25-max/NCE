# {{Component Name}} — Accessibility Specification

---
Component: {{component_name}}
Spec Type: Accessibility (A11Y)
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Component-level accessibility requirements for {{component_name}}.

---

## ARIA Attributes

| Attribute | Value | When Applied |
|-----------|-------|--------------|
| `role` | `{{role}}` | Always |
| `aria-label` | `{{value}}` | When no visible label |
| `aria-labelledby` | `{{id}}` | When labelled by element |
| `aria-describedby` | `{{id}}` | When has description |
| `aria-expanded` | `true/false` | When expandable |
| `aria-selected` | `true/false` | When selectable |
| `aria-disabled` | `true/false` | When disabled |

---

## Keyboard Navigation

| Key | Action | Context |
|-----|--------|---------|
| `Tab` | Move to next focusable | Always |
| `Shift+Tab` | Move to previous focusable | Always |
| `Enter` | {{action}} | When focused |
| `Space` | {{action}} | When focused |
| `Escape` | {{action}} | When open/active |
| `Arrow Up/Down` | {{action}} | When navigating list |
| `Home/End` | {{action}} | When navigating list |

---

## Focus Management

### Focus Order

1. {{first focusable element}}
2. {{second focusable element}}
3. {{etc}}

### Focus Trap

| Scenario | Trap Focus? | Escape Key |
|----------|-------------|------------|
| {{scenario}} | Yes/No | {{behavior}} |

### Focus Restoration

| Scenario | Restore Focus To |
|----------|------------------|
| Modal closes | Trigger element |
| Dropdown closes | Trigger element |
| Item deleted | Next/previous item |

---

## Screen Reader

### Announcements

| Event | Announcement | ARIA Method |
|-------|--------------|-------------|
| Component loads | `{{text}}` | `aria-live="polite"` |
| Error occurs | `{{text}}` | `aria-live="assertive"` |
| State changes | `{{text}}` | `aria-live="polite"` |

### Hidden Content

| Element | Hide From SR? | Method |
|---------|---------------|--------|
| Decorative icon | Yes | `aria-hidden="true"` |
| Helper text | No | Visible or `aria-describedby` |

---

## Colour & Contrast

| Element | Foreground | Background | Ratio Required |
|---------|------------|------------|----------------|
| Text | `{{color}}` | `{{color}}` | 4.5:1 |
| Large text | `{{color}}` | `{{color}}` | 3:1 |
| UI component | `{{color}}` | `{{color}}` | 3:1 |
| Focus ring | `{{color}}` | `{{color}}` | 3:1 |

---

## Focus Indicator

| State | Visual | Minimum |
|-------|--------|---------|
| Focus visible | {{description}} | 2px outline, 3:1 contrast |
| Focus within | {{description}} | — |

---

## Reduced Motion

| Animation | Normal | Reduced Motion |
|-----------|--------|----------------|
| {{animation}} | {{duration}} | Instant / disabled |

---

## Testing Checklist

- [ ] Keyboard-only navigation works
- [ ] Screen reader announces correctly
- [ ] Focus visible on all interactive elements
- [ ] Colour contrast passes
- [ ] Works with reduced motion
- [ ] No ARIA errors (axe DevTools)

---

## Notes

{{Additional accessibility notes}}

---
Template: DEEP-SPEC-A11Y.md
Pack: Optional Deep Specs
---
