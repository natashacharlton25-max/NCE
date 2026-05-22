# {{Component Name}} — Slots Specification

---
Component: {{component_name}}
Spec Type: Slots (Content Projection)
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Content projection (slots) for {{component_name}}.

---

## Slots Table

| Slot | Purpose | Required | Fallback |
|------|---------|----------|----------|
| default | {{purpose}} | Yes/No | {{fallback content}} |
| `{{slot_name}}` | {{purpose}} | Yes/No | {{fallback content}} |

---

## Slot Details

### Default Slot

**Purpose:** {{what content goes here}}

**Expected Content:**
- {{content type 1}}
- {{content type 2}}

**Example:**
```svelte
<Component>
  <p>This goes in the default slot</p>
</Component>
```

---

### `{{slot_name}}`

**Purpose:** {{what content goes here}}

**Expected Content:**
- {{content type}}

**Slot Props:**
| Prop | Type | Description |
|------|------|-------------|
| `{{prop}}` | `{{type}}` | {{description}} |

**Example:**
```svelte
<Component>
  <svelte:fragment slot="{{slot_name}}" let:{{prop}}>
    <span>{{{prop}}}</span>
  </svelte:fragment>
</Component>
```

---

## Slot Props (let: bindings)

| Slot | Prop | Type | Description |
|------|------|------|-------------|
| `{{slot}}` | `{{prop}}` | `{{type}}` | {{description}} |

---

## Conditional Slots

| Slot | Render Condition |
|------|------------------|
| `{{slot}}` | Only when `{{condition}}` |

---

## Slot Validation

| Check | Requirement |
|-------|-------------|
| Default slot | Must contain at least one child |
| `{{slot}}` | Must be a specific element type |

---

## Notes

{{Additional notes about slots}}

---
Template: DEEP-SPEC-SLOTS.md
Pack: Optional Deep Specs
---
