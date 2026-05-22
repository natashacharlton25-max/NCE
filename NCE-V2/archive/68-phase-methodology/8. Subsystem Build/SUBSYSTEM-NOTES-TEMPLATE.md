# {{Subsystem Name}}

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
Pass: BUILD (Phase 8)
Parent System: {{System Name}}
---

## NCE-V2 Project-Specific Fields

| Field | Value |
|-------|-------|
| **FileTree-v2 source** | `{{System}}/{{Subsystem}}.ts` |
| **Output Form** | JSON / rendered / library entry / metadata |
| **Output Destination** | parent / sibling / external system / Astro / external provider / human |
| **Storage Touch** | none / D1 / R2 / KV / DO / Queue |
| **D1 Binding (if library-touching)** | N/A / {{BINDING_NAME}} |
| **Library Access via Librarian?** | Yes / N/A / Direct D1 (flag if outside `library/`) |

---

## Purpose
(One clear sentence describing why this subsystem exists within the parent.)

## Intent
(What this subsystem is trying to achieve at a high level.)

## Justification
- Why this responsibility must be separated from siblings
- Expected LOC band (Low / Medium / High)

---

## Role Within Parent
(How this subsystem contributes to the parent system's purpose.)

## Responsibilities
(What this subsystem does.)

---

## In Scope
- …

## Out of Scope
- …

## Non-Goals
- …

---

## Inputs

| Input | Source | Binding Type | Description |
|-------|--------|--------------|-------------|
| | Parent / Sibling / External | service / D1 / R2 / KV / DO / Queue / in-Worker | |

## Outputs

| Output | Destination | Form | Binding Type | Description |
|--------|-------------|------|--------------|-------------|
| | Parent / Sibling / External | JSON / rendered / library / metadata | | |

## Dependencies

| Dependency | Type | Binding | Reason |
|------------|------|---------|--------|
| | Sibling / Parent / External | service / D1 / etc. | |

## Forbidden Dependencies
- …

---

## Size Constraint
This subsystem is intended to remain under **~2000 LOC** of runtime TypeScript (with file exclusions applied).

If estimated >2000 LOC: log exception in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

---

## Notes

---

## Pass Tracking

| Pass | Status | Owner | Date |
|------|--------|-------|------|
| Phase 8 Build | COMPLETE | Claude | {{timestamp}} |
| Phase 13 Pass 0 | PENDING | — | — |
| Phase 14 Pass 1 | PENDING | — | — |
| Phase 15 Pass 2 | PENDING | — | — |
| Phase 16 Pass 3 | PENDING | — | — |
| Phase 17 Pass 4 | PENDING | — | — |

## Pass Notes
