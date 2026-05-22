# {{Subsystem Name}} — Subsystem Spec

---
Status: DRAFT | APPROVED | BLOCKED
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Claude | Human
Methodology Stage: 2 of 5
Component Type: Subsystem
Parent System: {{System Name}}
---

## 1. Identity

| Field | Value |
|---|---|
| **Name** | {{Subsystem}} |
| **Parent System** | {{System Name}} |
| **Worker** | (inherits from parent — own Worker `{{name}}` / `platform`) |
| **FileTree-v2 reference** | `{{System}}/{{Subsystem}}.ts` (or nested category) |
| **Filename at implementation** | `src/{{system}}/{{Subsystem}}.ts` |
| **Type-only filename** | `src/{{system}}/{{Subsystem}}.types.ts` (excluded from runtime LOC) |
| **Test filename** | `tests/{{system}}/{{Subsystem}}.test.ts` (excluded from runtime LOC) |

## 2. Purpose

(One paragraph stating why this subsystem exists within the parent and what unique responsibility it owns.)

## 3. Scope — What This Subsystem Owns

- …
- …

## 4. Out of Scope / Non-Goals

What this subsystem must NOT take on:
- (Reference siblings / parent / other systems where the responsibility actually lives.)
- …

## 5. Output Form + Destination

| Field | Value |
|---|---|
| **Output Form** | JSON / rendered artefact / library entry / metadata |
| **Output Destination** | Parent system / Sibling subsystem / External system: {{name}} / Astro / External provider: {{name}} / Human |

## 6. Storage Touch

| Storage | Touched | Binding |
|---|---|---|
| D1 | Yes / No | `{{BINDING_NAME}}` (if Yes) |
| R2 | Yes / No | `{{BUCKET_BINDING}}` |
| KV | Yes / No | `{{KV_BINDING}}` |
| DO | Yes / No | `{{DO_BINDING}}` |
| Vectorize | Yes / No | `{{VECTOR_BINDING}}` |
| None | Yes / No | |

## 7. D1 Binding + Library Access (if D1-touching)

| Field | Value |
|---|---|
| **D1 Binding** | `{{BINDING_NAME}}` |
| **Library accessed** | `{{library-name}}` |
| **Access pattern** | Read (via `library/Librarian`) / Write (via `library/Writer` — only if this subsystem IS the library write-owner) / Both |
| **Access goes through `library/Librarian`?** | Yes / N/A (this IS library/) — flag if "Direct D1" |

## 8. Inputs

| Input | Source | Binding Type | TS Type | Required | Description |
|---|---|---|---|---|---|
| | Parent / Sibling / External / D1 / R2 / KV / DO / Queue | service / D1 / R2 / KV / DO / Queue / in-Worker import | | Yes / No | |

## 9. Outputs

| Output | Destination | Form | Binding Type | TS Type | Description |
|---|---|---|---|---|---|
| | Parent / Sibling / External / D1 / R2 | JSON / rendered / library entry / metadata | | | |

## 10. Dependencies

| Dependency | Type | Binding | Reason |
|---|---|---|---|
| | Hard / Soft / Build / Runtime / External | service / D1 / R2 / KV / DO / in-Worker | |

## 11. Forbidden Dependencies

What this subsystem must never depend on:
- …
- (Apply output-boundary rule: e.g. JSON-emitters MUST NOT depend on `renderers/`.)
- (No library access bypassing `library/Librarian`.)

## 12. Flow / Behaviour

(Describe the subsystem's normal flow as a sequence of steps. State machines if applicable. Async patterns. Resilience involvement.)

Example:
```
1. Receive input X (TS type {{InType}})
2. Validate via {{Validator}}
3. Query library via `library/Librarian.find({{params}})`
4. Transform result
5. Return TS type {{OutType}}
   On error: return Result.error({{ErrorCode}})
```

## 13. Public Functions

For each public function:

### {{functionName}}

```typescript
function {{functionName}}(
  param1: {{Type}},
  param2: {{Type}}
): Promise<Result<{{ReturnType}}, {{ErrorCode}}>>;
```

**Purpose:** (one sentence)

**Behaviour:** (what it does in 2–3 lines; reference Flow section if complex)

**Errors thrown / returned:** (list error codes; map to category)

---

(Repeat per function. Each function gets its own TS signature + behaviour + errors.)

## 14. Types

```typescript
// Owned by this subsystem; lives in {{Subsystem}}.types.ts

export interface {{TypeName}} {
  field1: string;
  field2: number;
}

export type {{UnionName}} = "value1" | "value2" | "value3";
```

## 15. Errors

| Error Code | Category | Recoverable | resilience/ Pattern | Cause | Consumer Action |
|---|---|---|---|---|---|
| `{{SYSTEM}}_{{CATEGORY}}_{{SPECIFIC}}` | Validation / Dependency / State / External / Worker / Unknown | Yes / No | RateLimiter / RetryPolicyEngine / FallbackStrategyResolver / N/A | | |

## 16. Config

| Field | Value |
|---|---|
| **Worker bindings used** | (list) |
| **Worker secrets used** | (list `SECRET_NAME` placeholders — never actual values) |
| **Environment variables** | (list, with defaults) |
| **Tunable knobs (e.g. rate limits, retries)** | (list with defaults) |

## 17. Examples

### Happy path

```typescript
const result = await {{functionName}}({ /* valid inputs */ });
// expect: Result.ok({ /* shape of {{ReturnType}} */ })
```

### Error case

```typescript
const result = await {{functionName}}({ /* invalid inputs */ });
// expect: Result.error("{{SYSTEM}}_VALIDATION_INVALID_X")
```

### Edge case

```typescript
const result = await {{functionName}}({ /* boundary value */ });
// expect: ...
```

## 18. Rules / Non-negotiables

Specific to this subsystem:
- …
- …

Examples:
- "Must not exceed 1500ms per request."
- "Must not write to any library other than its own."
- "Must call `resilience/RateLimiter.check()` before any outbound fetch."

## 19. LOC Estimate

| Field | Value |
|---|---|
| **Estimated LOC** | ~{{n}} |
| **Size Band** | Low (<1500) / Medium (1500–2000) / High (>2000) |
| **TS verbosity multiplier applied?** | Yes (1.3–1.5× over Python-era estimate) / N/A |
| **LOC exclusion applied** | `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments |

If High (>2000) → log exception in `NCE-V2/admin/PASS-DECISION-NOTES.md` AND consider splitting.

## 20. Open Questions / Risks

| Item | Severity | Owner | Notes |
|---|---|---|---|
| | Low / Medium / High | | |

## 21. Hardening Self-Check (mandatory before APPROVED status)

| Pass | Check | Status |
|---|---|---|
| Completeness | All 20 sections above have content | ☐ |
| Completeness | Every public function has signature + behaviour + errors | ☐ |
| Completeness | Every error has a code, category, and consumer action | ☐ |
| Ambiguity | No vague language ("should", "might", "usually") | ☐ |
| Ambiguity | All TS types fully specified (no untyped `any`) | ☐ |
| Implementability | Could a developer write this subsystem from this doc alone? | ☐ |
| Implementability | All dependencies exist (no missing source systems) | ☐ |
| Implementability | Library access route correct (via `library/Librarian` unless this IS library/) | ☐ |
| Test coverage | Happy path example present | ☐ |
| Test coverage | At least one error example present | ☐ |
| Test coverage | At least one edge case example present | ☐ |
| Output-boundary | Output Form matches what the parent system permits | ☐ |
| Output-boundary | No web HTML rendering unless this is `renderers/` or `email/` or `marks/` | ☐ |
| Size | Estimated LOC ≤2000 OR exception logged | ☐ |

If any check ☐ unchecked → revise; do not mark Status: APPROVED.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Spec author | Claude | {{date}} |
| Spec reviewer | Human | |

---
