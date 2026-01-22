# BrandPermissionResolver — Specification

## Overview

BrandPermissionResolver evaluates brand-scoped permission rules and produces constraints that must be applied during final capability resolution.

It does not grant capabilities directly; it restricts or conditions them based on brand governance.

---

## Resolution Order

Permission resolution MUST occur in the following order:

1. Validate brand context presence
2. Validate brand lifecycle state
3. Apply restrictive brand rules
4. Apply conditional brand rules
5. Reject expansive overrides unless explicitly allowed
6. Produce decision constraints and rationale

If any step fails, resolution stops and the request is denied or escalated.

---

## Supported Override Types

| Type | Description |
|----|------------|
| Restrictive | Removes or limits a capability |
| Conditional | Requires additional checks or approval |
| Expansive | Disabled by default; requires explicit enablement |

---

## Failure Behaviour

| Condition | Behaviour |
|--------|----------|
| Missing brand context | Deny |
| Unknown brand | Deny |
| Invalid lifecycle state | Deny |
| Conflicting rules | Restrictive wins |
| Ambiguous resolution | Escalate via resilience |

---

## Audit & Explainability

Every resolution MUST emit:

- Brand identifier
- Lifecycle state
- Applied rules
- Final constraint outcome
- Human-readable rationale

---

## Escalation

Unresolvable or ambiguous states MUST be escalated to `resilience/` and MUST NOT default to allow.

---

## Versioning

- Brand rule changes are versioned
- Resolution semantics are stable within a major version
- Backward compatibility is preferred over silent behaviour change
