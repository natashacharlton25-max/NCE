# BrandPermissionResolver

## Purpose

BrandPermissionResolver applies **brand-scoped permission rules** to role- and capability-based access, ensuring that all access decisions respect brand boundaries, lifecycle state, and explicit brand governance.

It contextualises access decisions to a specific brand without redefining roles or capabilities themselves.

---

## Role Within Access System

This subsystem is responsible for **translating brand context into enforceable permission constraints**.

It sits between role-based potential and final capability resolution, ensuring that no action is permitted outside the rules of the active brand.

---

## Owns

- Brand-scoped permission rules
- Brand lifecycle–aware permission constraints
- Permission override resolution (restrictive, conditional)
- Brand isolation guarantees

---

## Does NOT Own

- Role definitions or hierarchies
- Global capability definitions
- Authentication or identity verification
- Business or content logic
- Workflow orchestration

---

## Inputs

- Role assignments
- Requested capability
- Brand context (brand ID, lifecycle state)
- Optional permission overrides

---

## Outputs

- Brand-scoped permission decision inputs
- Resolved constraints to be applied by CapabilityManager
- Decision rationale metadata (for audit)

---

## Dependencies

### System Dependencies
- `brand/` — brand identity and lifecycle state
- `access/RoleManager` — role context
- `access/CapabilityManager` — final enforcement
- `resilience/` — escalation on ambiguity

---

## Error Categories

- Missing brand context
- Invalid brand state
- Conflicting permission rules
- Unsupported override type

---

## Notes

Detailed behaviour, examples, and rationale are documented in `NOTES.md`.
Formal enforcement rules are documented in `SPEC.md`.
