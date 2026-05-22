# {{System Name}}

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD (Phase 5)
---

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **Output Form** | JSON / rendered artefact / library entry / metadata / mixed |
| **Output Destination** | internal system / Astro / external provider / human / library |
| **Worker Grouping** | own Worker / `platform` Worker |
| **Library Ownership** | None / {{D1_BINDING_NAME(s)}} |
| **Storage Touch** | none / D1 / R2 / KV / DO / Queue / Vectorize |

---

## Purpose
(One clear sentence describing **why this system exists**.)

## Intent
(What this system is trying to achieve at a high level.)

## Justification
- Why this responsibility is required for NCE-V2
- Why it deserves its own system
- Expected LOC band (Low <1500 / Medium 1500–2000 / High >2000)

---

## In Scope
- …

## Out of Scope
- …
- (For renderer-adjacent systems: state explicitly if web rendering is excluded — per output-boundary rule)

## Non-Goals
- …

---

## Inputs

| Input | Source System | Binding Type | Description |
|-------|----------------|--------------|-------------|
| | | service / D1 / R2 / KV / DO / Queue / in-Worker | |

## Outputs

| Output | Consuming System | Form | Binding Type | Description |
|--------|-------------------|------|--------------|-------------|
| | | JSON / rendered / library / metadata | | |

## Dependencies

| Dependency | Type | Binding | Reason |
|------------|------|---------|--------|
| | Hard / Soft / Build / Runtime / External | service / D1 / etc. | |

---

## Library Ownership (if applicable)

| Library | D1 Binding | Type (core / derived / generated / reference) | Notes |
|---------|-----------|------------------------------------------------|-------|
| | `{{BINDING_NAME}}` | | |

---

## Size Constraint
This system is intended to remain under **~2000 LOC** of runtime TypeScript (excluding `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments).

If estimated >2000 LOC: log exception in `NCE-V2/admin/PASS-DECISION-NOTES.md` and consider Pass 0 split.

---

## Notes

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Phase 5 Build | COMPLETE | Claude | {{timestamp}} |
| Phase 6 Subsystem ID | PENDING | — | — |
| Phase 13 Pass 0 | PENDING | — | — |
| Phase 14 Pass 1 | PENDING | — | — |
| Phase 15 Pass 2 | PENDING | — | — |
| Phase 16 Pass 3 | PENDING | — | — |
| Phase 17 Pass 4 | PENDING | — | — |

## Pass Notes
