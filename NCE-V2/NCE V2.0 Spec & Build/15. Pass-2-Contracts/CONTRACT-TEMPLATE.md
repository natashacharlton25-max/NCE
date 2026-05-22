# {{Component Name}} — Contract

## Component
- **Name:** {{name}}
- **Type:** System / Subsystem / External Integration
- **Parent:** {{parent or N/A}}

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered artefact / library entry / metadata |
| **Library D1 Binding (if library boundary)** | N/A / {{BINDING_NAME}} |
| **Library Read/Write Side** | N/A / Read (via Librarian) / Write (owner only) |
| **TypeScript Interface Module** | `src/types/{{name}}.ts` (or N/A at Pass 2) |

---

## Inputs

### Input: {{Input Name}}

| Field | Value |
|-------|-------|
| **Source** | {{where it comes from}} |
| **Binding Type** | service binding / D1 / R2 / KV / DO / Queue / in-Worker import |
| **Required** | Yes / No |
| **Description** | {{what it is}} |
| **Form** | JSON / rendered artefact / library entry / metadata |

**Data Shape (TypeScript types):**
```ts
{{interface or type alias}}
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
| **Binding Type** | service binding / D1 / R2 / KV / DO / Queue / in-Worker import |
| **Guaranteed** | Yes / No |
| **Form** | JSON / rendered artefact / library entry / metadata |
| **Description** | {{what it is}} |

**Data Shape (TypeScript types):**
```ts
{{interface or type alias}}
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
| **Category** | Validation / Dependency / State / External / Unknown |
| **resilience/ Involvement** | Yes / No |
| **Consumer Action** | {{what the consumer should do}} |

---

### Error Categories (categories only — no codes yet; codes come in full spec)

| Category | Examples | Handling |
|----------|----------|----------|
| Validation | Invalid input, missing field | Reject, return error details |
| Dependency | Upstream unavailable, D1 timeout | Retry / Fail gracefully |
| State | Invalid state for operation | Return error, no side effects |
| External | Third-party failure | Retry / Circuit break (via resilience/) |
| Worker | CPU limit / memory limit | Log, return generic error |
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
| Worker CPU/memory exhaustion | {{what happens}} |
| Timeout | {{what happens}} |

---

## Contract Consistency

### This Component Consumes From:

| Source | Their Output Type | Our Input Type | Consistent? |
|--------|-------------------|----------------|-------------|
| {{component}} | {{TS type}} | {{TS type}} | Yes / No |

### This Component Provides To:

| Destination | Our Output Type | Their Input Type | Consistent? |
|-------------|------------------|------------------|-------------|
| {{component}} | {{TS type}} | {{TS type}} | Yes / No |

---

## Output-Boundary Check

- If this component is in `website/`: outputs MUST be JSON, never rendered HTML
- If this component is in `renderers/`: inputs MUST be JSON, outputs are final rendered artefacts
- If this component is in `email/`: composes rendered HTML for `integrations/EmailitIntegration`
- If this component touches a library: read access goes through `library/Librarian` (not direct D1 unless this IS `library/`)

Confirm: Yes / Flag

---

## Notes
(Clarifications, edge cases, or assumptions.)

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Phase: 15 (Pass 2)
---
