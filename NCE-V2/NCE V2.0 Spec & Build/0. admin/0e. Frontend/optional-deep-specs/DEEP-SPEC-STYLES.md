# {{Component Name}} — Styles Specification

---
Component: {{component_name}}
Spec Type: Styling API
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Styling API and CSS variables for {{component_name}}.

---

## CSS Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `--{{component}}-{{property}}` | `{{value}}` | {{description}} |

---

## CSS Variable Details

### `--{{component}}-{{property}}`

**Default:** `{{value}}`
**Applies To:** {{what elements}}
**Description:** {{what it controls}}

**Example Override:**
```css
.my-theme {
  --{{component}}-{{property}}: {{new_value}};
}
```

---

## Theming

### Light Theme (Default)

| Variable | Value |
|----------|-------|
| `--{{component}}-bg` | `#ffffff` |
| `--{{component}}-text` | `#1a1a1a` |
| `--{{component}}-border` | `#e5e5e5` |

### Dark Theme

| Variable | Value |
|----------|-------|
| `--{{component}}-bg` | `#1a1a1a` |
| `--{{component}}-text` | `#ffffff` |
| `--{{component}}-border` | `#333333` |

---

## CSS Classes

### Public Classes (Safe to Override)

| Class | Purpose |
|-------|---------|
| `.{{component}}` | Root element |
| `.{{component}}__{{element}}` | Child element |
| `.{{component}}--{{modifier}}` | Modifier state |

### Internal Classes (Do Not Override)

| Class | Why Internal |
|-------|--------------|
| `.{{component}}-internal` | Implementation detail |

---

## Responsive Styles

| Breakpoint | Changes |
|------------|---------|
| Mobile (<640px) | {{changes}} |
| Tablet (640-1023px) | {{changes}} |
| Desktop (1024px+) | {{changes}} |

---

## Animation Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `--{{component}}-duration` | `200ms` | Animation duration |
| `--{{component}}-easing` | `ease-out` | Animation easing |

---

## Style Variants

| Variant | How to Apply | Visual Difference |
|---------|--------------|-------------------|
| default | No class | {{description}} |
| `primary` | `variant="primary"` | {{description}} |
| `secondary` | `variant="secondary"` | {{description}} |

---

## Notes

{{Additional notes about styling}}

---
Template: DEEP-SPEC-STYLES.md
Pack: Optional Deep Specs
---
