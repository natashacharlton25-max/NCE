# {{Component Name}} — Usage Examples

---
Component: {{component_name}}
Spec Type: Examples
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

Usage examples for {{component_name}}.

---

## Basic Usage

### Minimal Example

```svelte
<script>
  import {{ComponentName}} from '$lib/components/{{component_name}}';
</script>

<{{ComponentName}} />
```

### With Required Props

```svelte
<{{ComponentName}}
  {{required_prop}}="{{value}}"
/>
```

---

## Common Patterns

### Pattern: {{Pattern Name}}

**Use When:** {{describe when to use this pattern}}

```svelte
<script>
  import {{ComponentName}} from '$lib/components/{{component_name}}';

  // Setup code
</script>

<{{ComponentName}}
  {{prop}}="{{value}}"
>
  {{slot content}}
</{{ComponentName}}>
```

---

### Pattern: {{Pattern Name}}

**Use When:** {{describe when to use this pattern}}

```svelte
<script>
  import {{ComponentName}} from '$lib/components/{{component_name}}';

  function handleEvent(event) {
    // Handle event
  }
</script>

<{{ComponentName}}
  on:{{event}}={handleEvent}
/>
```

---

## Variants

### Default Variant

```svelte
<{{ComponentName}} />
```

### Primary Variant

```svelte
<{{ComponentName}} variant="primary" />
```

### Secondary Variant

```svelte
<{{ComponentName}} variant="secondary" />
```

---

## States

### Loading State

```svelte
<{{ComponentName}} loading />
```

### Disabled State

```svelte
<{{ComponentName}} disabled />
```

### Error State

```svelte
<{{ComponentName}} error="{{error message}}" />
```

---

## With Slots

### Default Slot

```svelte
<{{ComponentName}}>
  <p>Content in default slot</p>
</{{ComponentName}}>
```

### Named Slots

```svelte
<{{ComponentName}}>
  <svelte:fragment slot="header">
    <h2>Header content</h2>
  </svelte:fragment>

  <p>Default slot content</p>

  <svelte:fragment slot="footer">
    <button>Footer action</button>
  </svelte:fragment>
</{{ComponentName}}>
```

---

## Event Handling

### Basic Event

```svelte
<script>
  function handleClick() {
    console.log('Clicked!');
  }
</script>

<{{ComponentName}} on:click={handleClick} />
```

### Event with Payload

```svelte
<script>
  function handleChange(event) {
    const { value } = event.detail;
    console.log('New value:', value);
  }
</script>

<{{ComponentName}} on:change={handleChange} />
```

---

## Composition

### With Other Components

```svelte
<Card>
  <{{ComponentName}} />
</Card>
```

### As Child of Form

```svelte
<form on:submit={handleSubmit}>
  <{{ComponentName}} name="field" bind:value />
  <button type="submit">Submit</button>
</form>
```

---

## Anti-Patterns

### DON'T: {{Anti-pattern description}}

```svelte
<!-- DON'T DO THIS -->
<{{ComponentName}}
  {{bad_pattern}}
/>
```

**Why:** {{explanation}}

**Instead:**

```svelte
<!-- DO THIS -->
<{{ComponentName}}
  {{good_pattern}}
/>
```

---

## Edge Cases

### Empty Data

```svelte
<{{ComponentName}} data={[]} />
<!-- Renders empty state -->
```

### Large Data Set

```svelte
<{{ComponentName}} data={largeArray} virtualize />
<!-- Enable virtualization for performance -->
```

---

## Notes

{{Additional usage notes}}

---
Template: DEEP-SPEC-EXAMPLES.md
Pack: Optional Deep Specs
---
