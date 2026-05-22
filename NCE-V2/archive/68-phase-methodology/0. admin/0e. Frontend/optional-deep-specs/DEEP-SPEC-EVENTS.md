# {{Component Name}} — Events Specification

---
Component: {{component_name}}
Spec Type: Events
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Event contracts for {{component_name}}.

---

## Events Table

| Event | Payload | When Fired | Bubbles |
|-------|---------|------------|---------|
| `{{event}}` | `{{type}}` | {{when}} | Yes/No |

---

## Event Details

### `{{event_name}}`

**Payload Type:**
```typescript
interface {{EventName}}Payload {
  {{field}}: {{type}};
}
```

**When Fired:**
{{describe exactly when this event fires}}

**Example Handler:**
```svelte
<script>
  function handle{{EventName}}(event) {
    const { {{field}} } = event.detail;
    // Handle event
  }
</script>

<Component on:{{event_name}}={handle{{EventName}}} />
```

---

## Event Sequence

For complex interactions, events fire in this order:

1. `{{event1}}` — {{description}}
2. `{{event2}}` — {{description}}
3. `{{event3}}` — {{description}}

---

## Cancellable Events

| Event | Cancellable | How to Cancel |
|-------|-------------|---------------|
| `{{event}}` | Yes/No | `event.preventDefault()` |

---

## Event vs Callback Props

| Use Event When | Use Callback Prop When |
|----------------|------------------------|
| Parent needs to react | Parent needs to control |
| Multiple listeners possible | Single handler expected |
| Fire and forget | Return value needed |

---

## Notes

{{Additional notes about events}}

---
Template: DEEP-SPEC-EVENTS.md
Pack: Optional Deep Specs
---
