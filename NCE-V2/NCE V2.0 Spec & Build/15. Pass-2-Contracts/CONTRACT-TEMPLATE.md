# {{Component Name}} — Contract

## Component
- **Name:** {{name}}
- **Type:** System / Subsystem / External Integration
- **Parent:** {{parent or N/A}}

---

## Inputs

### Input: {{Input Name}}

| Field | Value |
|-------|-------|
| **Source** | {{where it comes from}} |
| **Required** | Yes / No |
| **Description** | {{what it is}} |

**Data Shape:**
```
{{data structure — pseudocode or simple schema}}
```

**Validation:**
- {{validation rule}}
- {{validation rule}}

---

(Repeat for each input)

---

## Outputs

### Output: {{Output Name}}

| Field | Value |
|-------|-------|
| **Destination** | {{where it goes}} |
| **Guaranteed** | Yes / No |
| **Description** | {{what it is}} |

**Data Shape:**
```
{{data structure — pseudocode or simple schema}}
```

**Guarantees:**
- {{what consumers can rely on}}

---

(Repeat for each output)

---

## Error Contract

### Error: {{Error Type}}

| Field | Value |
|-------|-------|
| **Cause** | {{what triggers this error}} |
| **Recoverable** | Yes / No |
| **Consumer Action** | {{what the consumer should do}} |

---

### Error Categories

| Category | Examples | Handling |
|----------|----------|----------|
| Validation | Invalid input, missing field | Reject, return error details |
| Dependency | Upstream unavailable | Retry / Fail gracefully |
| State | Invalid state for operation | Return error, no side effects |
| External | Third-party failure | Retry / Circuit break |
| Unknown | Unexpected error | Log, return generic error |

---

## Validation Responsibility

| Boundary | Who Validates | What |
|----------|---------------|------|
| Input from {{source}} | This component / Source | {{what}} |
| Output to {{destination}} | This component / Destination | {{what}} |

---

## Failure Behaviour

| Scenario | Behaviour |
|----------|-----------|
| Invalid input | {{what happens}} |
| Dependency failure | {{what happens}} |
| Partial success | {{what happens}} |
| Timeout | {{what happens}} |

---

## Contract Consistency

### This Component Consumes From:

| Source | Their Output | Our Input | Consistent? |
|--------|--------------|-----------|-------------|
| {{component}} | {{output name}} | {{input name}} | Yes / No |

### This Component Provides To:

| Destination | Our Output | Their Input | Consistent? |
|-------------|------------|-------------|-------------|
| {{component}} | {{output name}} | {{input name}} | Yes / No |

---

## Notes
(Clarifications, edge cases, or assumptions.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 15 (Pass 2)
---
