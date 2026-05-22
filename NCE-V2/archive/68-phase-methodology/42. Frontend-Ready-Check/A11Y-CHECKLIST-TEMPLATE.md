# Accessibility Checklist — {{Project Name}}

---
Status: DRAFT | IN REVIEW | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
WCAG Target: Level AA
Phase: 42 (Frontend Ready Check)
---

## Purpose

Verify all frontend components meet accessibility requirements before implementation begins.

---

## Quick Summary

| Category | Pass | Fail | N/A |
|----------|------|------|-----|
| Keyboard Navigation | /{{ }} | /{{ }} | /{{ }} |
| Screen Reader | /{{ }} | /{{ }} | /{{ }} |
| Colour & Contrast | /{{ }} | /{{ }} | /{{ }} |
| Focus Management | /{{ }} | /{{ }} | /{{ }} |
| Forms & Errors | /{{ }} | /{{ }} | /{{ }} |
| Motion & Animation | /{{ }} | /{{ }} | /{{ }} |
| **Total** | /{{ }} | /{{ }} | /{{ }} |

---

## Keyboard Navigation

Every interactive element must be usable without a mouse.

### Global Navigation

| Check | Status | Notes |
|-------|--------|-------|
| All interactive elements reachable via Tab | ✅ / ❌ | |
| Tab order follows visual layout (left→right, top→bottom) | ✅ / ❌ | |
| No keyboard traps (can always Tab away) | ✅ / ❌ | |
| Skip link to main content exists | ✅ / ❌ | |
| Focus never disappears unexpectedly | ✅ / ❌ | |

### Component-Specific Keyboard

| Component | Keys Required | Status |
|-----------|---------------|--------|
| Buttons | Enter, Space to activate | ✅ / ❌ |
| Links | Enter to follow | ✅ / ❌ |
| Modals/Dialogs | Escape to close, focus trapped inside | ✅ / ❌ |
| Dropdown menus | Arrow keys to navigate, Escape to close | ✅ / ❌ |
| Tabs | Arrow keys to switch, Enter to select | ✅ / ❌ |
| Accordions | Enter/Space to toggle | ✅ / ❌ |
| Sliders | Arrow keys to adjust | ✅ / ❌ |
| Date pickers | Arrow keys to navigate dates | ✅ / ❌ |

---

## Screen Reader Compatibility

Content must be understandable when read aloud.

### Page Structure

| Check | Status | Notes |
|-------|--------|-------|
| Single `<h1>` per page | ✅ / ❌ | |
| Heading hierarchy logical (no skipped levels) | ✅ / ❌ | |
| Landmark regions used (`<main>`, `<nav>`, `<aside>`) | ✅ / ❌ | |
| Page title describes content | ✅ / ❌ | |
| Language attribute set on `<html>` | ✅ / ❌ | |

### Images & Media

| Check | Status | Notes |
|-------|--------|-------|
| All images have `alt` text | ✅ / ❌ | |
| Decorative images have `alt=""` | ✅ / ❌ | |
| Complex images have extended description | ✅ / ❌ | |
| Videos have captions | ✅ / ❌ / N/A | |
| Audio has transcript | ✅ / ❌ / N/A | |

### ARIA Usage

| Check | Status | Notes |
|-------|--------|-------|
| ARIA roles used correctly | ✅ / ❌ | |
| `aria-label` provided where no visible label | ✅ / ❌ | |
| `aria-describedby` links to descriptions | ✅ / ❌ | |
| Dynamic content uses `aria-live` regions | ✅ / ❌ | |
| `aria-expanded` on expandable elements | ✅ / ❌ | |
| `aria-selected` on selectable items | ✅ / ❌ | |
| `aria-disabled` (not just `disabled` attribute) | ✅ / ❌ | |

### Announcements

| Event | What's Announced | Status |
|-------|------------------|--------|
| Page load | Page title | ✅ / ❌ |
| Loading starts | "Loading..." | ✅ / ❌ |
| Loading ends | "Content loaded" or new content | ✅ / ❌ |
| Error occurs | Error message | ✅ / ❌ |
| Success action | Confirmation message | ✅ / ❌ |
| Item added/removed | Change description | ✅ / ❌ |

---

## Colour & Contrast

Information must not rely on colour alone.

### Contrast Ratios

| Element | Required Ratio | Actual | Status |
|---------|----------------|--------|--------|
| Body text | 4.5:1 | {{ }} | ✅ / ❌ |
| Large text (18px+ or 14px bold) | 3:1 | {{ }} | ✅ / ❌ |
| UI components (buttons, inputs) | 3:1 | {{ }} | ✅ / ❌ |
| Focus indicators | 3:1 | {{ }} | ✅ / ❌ |
| Icons (meaningful) | 3:1 | {{ }} | ✅ / ❌ |
| Placeholder text | 4.5:1 | {{ }} | ✅ / ❌ |

### Colour Independence

| Information | Colour Used | Non-Colour Indicator | Status |
|-------------|-------------|----------------------|--------|
| Error state | Red | Icon + text + border | ✅ / ❌ |
| Success state | Green | Icon + text | ✅ / ❌ |
| Warning state | Amber | Icon + text | ✅ / ❌ |
| Required fields | Red asterisk | Text label + `aria-required` | ✅ / ❌ |
| Links in text | Blue | Underline | ✅ / ❌ |
| Selected state | Highlight | Border/icon change | ✅ / ❌ |

---

## Focus Management

Users must always know where they are.

### Focus Visibility

| Check | Status | Notes |
|-------|--------|-------|
| Focus indicator visible on all interactive elements | ✅ / ❌ | |
| Focus indicator has sufficient contrast (3:1) | ✅ / ❌ | |
| Focus ring at least 2px | ✅ / ❌ | |
| `:focus-visible` used (hide for mouse, show for keyboard) | ✅ / ❌ | |

### Focus Movement

| Scenario | Expected Behaviour | Status |
|----------|-------------------|--------|
| Modal opens | Focus moves to modal (first focusable or title) | ✅ / ❌ |
| Modal closes | Focus returns to trigger element | ✅ / ❌ |
| Dropdown opens | Focus moves to first item | ✅ / ❌ |
| Dropdown closes | Focus returns to trigger | ✅ / ❌ |
| Item deleted | Focus moves to next/previous item | ✅ / ❌ |
| Form error | Focus moves to first error field | ✅ / ❌ |
| Page navigation | Focus moves to main content or `<h1>` | ✅ / ❌ |

### Focus Trapping

| Component | Trap Focus? | Status |
|-----------|-------------|--------|
| Modal/Dialog | Yes — can't Tab outside | ✅ / ❌ |
| Dropdown menu | Yes — while open | ✅ / ❌ |
| Toast/Notification | No — informational only | ✅ / ❌ |
| Sidebar | No — unless overlay on mobile | ✅ / ❌ |

---

## Forms & Errors

Forms must be usable by everyone.

### Labels & Instructions

| Check | Status | Notes |
|-------|--------|-------|
| Every input has a visible label | ✅ / ❌ | |
| Labels use `<label for="id">` or `aria-labelledby` | ✅ / ❌ | |
| Required fields indicated visually AND via `aria-required` | ✅ / ❌ | |
| Input format hints provided (e.g., "DD/MM/YYYY") | ✅ / ❌ | |
| Fieldsets used for related inputs (radio groups, etc.) | ✅ / ❌ | |

### Error Handling

| Check | Status | Notes |
|-------|--------|-------|
| Errors appear near the relevant field | ✅ / ❌ | |
| Errors use `aria-describedby` to link to field | ✅ / ❌ | |
| Error summary at top of form (for multiple errors) | ✅ / ❌ | |
| `aria-invalid="true"` set on invalid fields | ✅ / ❌ | |
| Errors announced via live region | ✅ / ❌ | |
| Error messages explain how to fix | ✅ / ❌ | |

### Autocomplete

| Field Type | `autocomplete` Value | Status |
|------------|---------------------|--------|
| Name | `name` | ✅ / ❌ |
| Email | `email` | ✅ / ❌ |
| Password | `current-password` / `new-password` | ✅ / ❌ |
| Phone | `tel` | ✅ / ❌ |
| Address | `street-address`, `postal-code`, etc. | ✅ / ❌ |

---

## Motion & Animation

Animations must not cause harm.

### Reduced Motion

| Check | Status | Notes |
|-------|--------|-------|
| `prefers-reduced-motion` media query respected | ✅ / ❌ | |
| Essential animations still work (just instant) | ✅ / ❌ | |
| No animations longer than 5 seconds (or can be stopped) | ✅ / ❌ | |

### Auto-Playing Content

| Content Type | Behaviour | Status |
|--------------|-----------|--------|
| Videos | Do NOT autoplay (or muted + controls) | ✅ / ❌ |
| Carousels | Do NOT auto-advance (or has pause) | ✅ / ❌ |
| Background animations | Stop after 5 seconds (or has stop button) | ✅ / ❌ |

### Flashing Content

| Check | Status | Notes |
|-------|--------|-------|
| No content flashes more than 3 times per second | ✅ / ❌ | |

---

## Component-by-Component Checklist

### {{Component Name}}

| Requirement | Status | Notes |
|-------------|--------|-------|
| Keyboard accessible | ✅ / ❌ | |
| Screen reader announces purpose | ✅ / ❌ | |
| Focus visible | ✅ / ❌ | |
| Colour contrast passes | ✅ / ❌ | |
| Works with reduced motion | ✅ / ❌ | |

_(Repeat for each key component)_

---

## Testing Tools

### Automated Testing

| Tool | What It Checks | Run? |
|------|----------------|------|
| axe DevTools | ARIA, contrast, structure | ☐ |
| Lighthouse | Overall accessibility score | ☐ |
| WAVE | Visual error highlighting | ☐ |
| eslint-plugin-jsx-a11y | Code-level issues (React) | ☐ |

### Manual Testing

| Test | How | Done? |
|------|-----|-------|
| Keyboard only | Unplug mouse, navigate entire site | ☐ |
| Screen reader | VoiceOver (Mac), NVDA (Windows) | ☐ |
| Zoom 200% | Browser zoom, check layout | ☐ |
| High contrast mode | Windows setting | ☐ |
| Reduced motion | OS setting | ☐ |
| Mobile screen reader | TalkBack (Android), VoiceOver (iOS) | ☐ |

---

## WCAG 2.1 Level AA Compliance

### Perceivable

| Criterion | Description | Status |
|-----------|-------------|--------|
| 1.1.1 | Non-text content has text alternatives | ✅ / ❌ |
| 1.2.1 | Audio/video has alternatives | ✅ / ❌ / N/A |
| 1.3.1 | Info and relationships programmatically determined | ✅ / ❌ |
| 1.3.2 | Meaningful sequence preserved | ✅ / ❌ |
| 1.3.3 | Instructions don't rely on sensory characteristics | ✅ / ❌ |
| 1.4.1 | Colour not sole means of conveying info | ✅ / ❌ |
| 1.4.3 | Contrast ratio at least 4.5:1 (text) | ✅ / ❌ |
| 1.4.4 | Text resizable to 200% without loss | ✅ / ❌ |
| 1.4.10 | Content reflows at 320px width | ✅ / ❌ |
| 1.4.11 | Non-text contrast at least 3:1 | ✅ / ❌ |

### Operable

| Criterion | Description | Status |
|-----------|-------------|--------|
| 2.1.1 | All functionality keyboard accessible | ✅ / ❌ |
| 2.1.2 | No keyboard traps | ✅ / ❌ |
| 2.4.1 | Skip links / bypass blocks | ✅ / ❌ |
| 2.4.2 | Pages have descriptive titles | ✅ / ❌ |
| 2.4.3 | Focus order logical | ✅ / ❌ |
| 2.4.4 | Link purpose clear from text | ✅ / ❌ |
| 2.4.6 | Headings and labels descriptive | ✅ / ❌ |
| 2.4.7 | Focus visible | ✅ / ❌ |

### Understandable

| Criterion | Description | Status |
|-----------|-------------|--------|
| 3.1.1 | Language of page identified | ✅ / ❌ |
| 3.2.1 | No unexpected context changes on focus | ✅ / ❌ |
| 3.2.2 | No unexpected context changes on input | ✅ / ❌ |
| 3.3.1 | Input errors identified and described | ✅ / ❌ |
| 3.3.2 | Labels or instructions for input | ✅ / ❌ |

### Robust

| Criterion | Description | Status |
|-----------|-------------|--------|
| 4.1.1 | Valid HTML (no parsing errors) | ✅ / ❌ |
| 4.1.2 | Name, role, value for all UI components | ✅ / ❌ |
| 4.1.3 | Status messages announced without focus | ✅ / ❌ |

---

## Sign-Off

| Check | Status | Verified By | Date |
|-------|--------|-------------|------|
| Automated tests pass | ✅ / ❌ | | |
| Manual keyboard test pass | ✅ / ❌ | | |
| Screen reader test pass | ✅ / ❌ | | |
| Colour contrast verified | ✅ / ❌ | | |
| **Final A11Y Approval** | ✅ / ❌ | | |

---

## Notes

{{Additional accessibility notes or known issues}}

---
Generated: {{timestamp}}
Phase: 42 (Frontend Ready Check)
Template: A11Y-CHECKLIST-TEMPLATE.md
---
