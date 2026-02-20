# System Coverage Review — Access

## System Role Summary

The Access system is responsible for making deterministic, auditable decisions about whether a requested action is allowed, denied, or conditional, based on role, capability, and brand context. It is the single authority for permissioning and must fail closed on ambiguity.

---

## Coverage Assessment

### Covered Responsibilities

- Definition of user roles and role inheritance
- Definition of action-level capabilities
- Resolution of effective permissions
- Application of brand-scoped permission constraints
- Restrictive precedence in permission conflicts
- Enforcement of brand isolation in access decisions

---

### Partially Covered Responsibilities

- Explainability of access decisions
  (Decision rationale is implied across subsystems but not clearly owned end-to-end.)

- Escalation on ambiguous access states
  (Escalation is referenced but ownership boundary with external systems is not fully explicit.)

---

### Uncovered Responsibilities

- None identified within the stated system role and explicit non-goals.

---

## Subsystem Sufficiency Review

### RoleManager
- **Sufficiency:** Sufficient
- **Reasoning:** Role definition and inheritance form a coherent, bounded responsibility distinct from capability enforcement and brand context.
- **Estimated LOC:** ~400–600
- **File Size Risk:** Low

### CapabilityManager
- **Sufficiency:** Sufficient
- **Reasoning:** Capability definition and final permission enforcement are cohesive but central, resulting in higher internal density.
- **Estimated LOC:** ~900–1100
- **File Size Risk:** Medium

### BrandPermissionResolver
- **Sufficiency:** Sufficient
- **Reasoning:** Brand-scoped permission constraints and lifecycle awareness are clearly separated from roles and capabilities.
- **Estimated LOC:** ~600–800
- **File Size Risk:** Low

---

## Missing Capability Areas

- **Access decision explainability aggregation**
  - **Impact:** Medium
  - **Estimated LOC:** ~200–300

- **Access decision audit emission**
  - **Impact:** Medium
  - **Estimated LOC:** ~150–250

---

## Boundary & Classification Issues

- The boundary between access decision semantics and escalation handling is not fully explicit.
- There is potential for downstream systems to infer permissions instead of consuming access decisions.

---

## Overall Build Size Estimate

- **Estimated total system LOC:** ~2000–2300
- **Largest expected file:** ~1100 LOC
- **Overall size risk:** Medium

---

## Risk Assessment

- **Risk level:** Medium
- **Reasoning:** The system is structurally coherent and bounded, but centralisation of final enforcement increases density and integration sensitivity.

---

## Verdict

This system **CAN** reasonably fulfil its role with the current subsystem set and size constraints.

---

**Status:** Approved
