# {{Component Name}} — Props Specification

---
Component: {{component_name}}
Spec Type: Props
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Detailed prop specifications for {{component_name}}.

---

## Props Table

| Prop | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {{prop}} | `{{type}}` | Yes/No | `{{default}}` | {{description}} |

---

## Prop Details

### `{{prop_name}}`

**Type:** `{{type}}`
**Required:** Yes / No
**Default:** `{{default}}`

**Description:**
{{detailed description of what this prop does}}

**Validation:**
- {{validation rule 1}}
- {{validation rule 2}}

**Example:**
```svelte
<Component {{prop_name}}="{{value}}" />
```

---

## Type Definitions

```typescript
interface {{ComponentName}}Props {
  {{prop}}: {{type}};
}
```

---

## Prop Combinations

### Valid Combinations

| Combination | Result |
|-------------|--------|
| `prop1` + `prop2` | {{what happens}} |

### Invalid Combinations

| Combination | Why Invalid |
|-------------|-------------|
| `prop1` + `prop3` | {{reason}} |

---

## Notes

{{Additional notes about props}}

---
Template: DEEP-SPEC-PROPS.md
Pack: Optional Deep Specs
---
