# {{Library Name}} — Functions

---
Component: lib-{{name}}
Type: Library
Version: {{version}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Overview

This is the PRIMARY specification for the library. All public functions are documented here.

| Category | Count | Description |
|----------|-------|-------------|
| {{Category}} | {{n}} | {{description}} |
| {{Category}} | {{n}} | {{description}} |
| **Total** | {{n}} | |

---

## Function Index

| Function | Category | Description | Pure |
|----------|----------|-------------|------|
| `{{function}}()` | {{category}} | {{description}} | Yes/No |
| `{{function}}()` | {{category}} | {{description}} | Yes/No |
| `{{function}}()` | {{category}} | {{description}} | Yes/No |

---

## {{Category}} Functions

### {{functionName}}()

{{One sentence describing what this function does}}

**Signature:**
```typescript
{{functionName}}({{params}}): {{ReturnType}}
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {{param}} | {{type}} | Yes/No | {{default}} | {{description}} |

**Returns:**

| Type | Description |
|------|-------------|
| {{type}} | {{description}} |

**Behaviour:**
1. {{Step 1}}
2. {{Step 2}}
3. {{Step 3}}

**Errors:**

| Code | Condition | Message |
|------|-----------|---------|
| `{{PREFIX}}_XXX` | {{when}} | {{message}} |

**Examples:**

```typescript
// Basic usage
{{functionName}}({{args}})
// → {{result}}

// With options
{{functionName}}({{args}}, { {{options}} })
// → {{result}}

// Edge case: empty input
{{functionName}}({{emptyInput}})
// → {{result}}

// Edge case: null input
{{functionName}}(null)
// → throws {{PREFIX}}_XXX

// Edge case: boundary value
{{functionName}}({{boundaryValue}})
// → {{result}}
```

**Notes:**
- {{Note about usage}}
- {{Performance consideration}}

---

### {{functionName2}}()

{{Description}}

**Signature:**
```typescript
{{functionName2}}({{params}}): {{ReturnType}}
```

**Parameters:**

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {{param}} | {{type}} | Yes/No | {{default}} | {{description}} |

**Returns:**

| Type | Description |
|------|-------------|
| {{type}} | {{description}} |

**Behaviour:**
1. {{Step 1}}
2. {{Step 2}}

**Errors:**

| Code | Condition |
|------|-----------|
| `{{PREFIX}}_XXX` | {{when}} |

**Examples:**

```typescript
// Basic usage
{{functionName2}}({{args}})
// → {{result}}
```

---

## {{Category 2}} Functions

### {{functionName3}}()

(Repeat pattern for each function)

---

## Utility Functions

### {{utilityFunction}}()

Small helper functions.

**Signature:**
```typescript
{{utilityFunction}}({{params}}): {{ReturnType}}
```

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| {{param}} | {{type}} | Yes/No | {{description}} |

**Returns:**

| Type | Description |
|------|-------------|
| {{type}} | {{description}} |

**Example:**
```typescript
{{utilityFunction}}({{args}})  // → {{result}}
```

---

## Type Guard Functions

### is{{TypeName}}()

Check if value is of type {{TypeName}}.

**Signature:**
```typescript
is{{TypeName}}(value: unknown): value is {{TypeName}}
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| value | unknown | Value to check |

**Returns:**

| Type | Description |
|------|-------------|
| boolean | True if value is {{TypeName}} |

**Example:**
```typescript
is{{TypeName}}(someValue)  // → true or false
```

---

## Conversion Functions

### {{from}}To{{To}}()

Convert from {{from}} to {{to}}.

**Signature:**
```typescript
{{from}}To{{To}}(input: {{FromType}}): {{ToType}}
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| input | {{FromType}} | Value to convert |

**Returns:**

| Type | Description |
|------|-------------|
| {{ToType}} | Converted value |

**Example:**
```typescript
{{from}}To{{To}}({{input}})  // → {{output}}
```

**Inverse:** `{{to}}To{{From}}()`

---

## Validation Functions

### validate{{Thing}}()

Validate {{thing}} and return result.

**Signature:**
```typescript
validate{{Thing}}(value: {{Type}}): ValidationResult<{{Type}}>
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| value | {{Type}} | Value to validate |

**Returns:**

| Type | Description |
|------|-------------|
| ValidationResult<{{Type}}> | Valid value or errors |

**ValidationResult Type:**
```typescript
type ValidationResult<T> = 
  | { valid: true; value: T }
  | { valid: false; errors: ValidationError[] }
```

**Example:**
```typescript
// Valid
validate{{Thing}}({{validValue}})
// → { valid: true, value: {{validValue}} }

// Invalid
validate{{Thing}}({{invalidValue}})
// → { valid: false, errors: [{ field: '{{field}}', message: '{{message}}' }] }
```

---

## Function Dependencies

### Internal Call Graph

```
{{functionA}}()
  └─> {{helperFunction}}()

{{functionB}}()
  └─> {{helperFunction}}()
  └─> {{anotherHelper}}()
```

### No External Dependencies

This library has NO external dependencies beyond:
- Language built-ins
- Other `lib-*` libraries: {{list or "none"}}

---

## Performance Notes

| Function | Time Complexity | Space Complexity | Notes |
|----------|-----------------|------------------|-------|
| `{{function}}()` | O(n) | O(1) | {{notes}} |
| `{{function}}()` | O(1) | O(1) | {{notes}} |
| `{{function}}()` | O(n log n) | O(n) | {{notes}} |

---

## Thread Safety

| Function | Thread Safe | Notes |
|----------|-------------|-------|
| All functions | Yes | No shared state |

---

## Notes

- All functions are pure unless explicitly noted
- All functions validate inputs before processing
- See TYPES.md for type definitions
- See ERRORS.md for error codes
- See EXAMPLES.md for comprehensive usage examples

---
Source: PRE-SPEC-NOTES.md sections 3, 4
Phase: 23 (Library Specs)
Generated: {{timestamp}}
---
