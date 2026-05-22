# Subsystems Clarification

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Parent System
{{System Name}}

## System Intent Reminder
(One short paragraph restating the parent system's responsibility from SYSTEM-NOTES.md.)

## NCE-V2 Source Note
Subsystem list is canonical in `NCE-V2/FileTree-v2.md`. This document transforms FileTree-v2's listing for `{{System Name}}/` into per-subsystem clarification.

---

## Proposed Subsystems

### {{Subsystem Name}}

| Field | Value |
|-------|-------|
| **FileTree-v2 Source** | `{{System}}/{{Subsystem}}.ts` |
| **Output Form** | JSON / rendered / library entry / metadata |
| **Storage Touch** | none / D1 / R2 / KV / DO / Queue |
| **D1 Binding (if library-touching)** | N/A / {{BINDING_NAME}} |

**Purpose:** (One sentence describing the internal responsibility this subsystem owns.)

**Justification:**
- Why this responsibility must be separated from sibling subsystems
- How this separation helps stay under ~2000 LOC

---

### {{Next Subsystem Name}}

(repeat for each subsystem in FileTree-v2.md for this parent)

---

## Subsystem Count Summary

| Parent System | Subsystem Count | Expected LOC per Subsystem | Notes |
|---------------|------------------|------------------------------|-------|
| {{System Name}} | {{n}} | ~{{estimate}} | TS verbosity 1.3–1.5× over Python |

---

## Nested Groupings (if applicable)

For systems like `brand/` that have nested subsystem categories in FileTree-v2 (core/, identity/, audience/, voice/, values/, governance/, alignment/, tracking/, colour/, typography/, visual/):

| Grouping | Subsystem Count | Notes |
|----------|-----------------|-------|
| | | |

---

## Dependency
Requires approved: **SYSTEM-NOTES.md** for `{{System Name}}` (Phase 5 output)

## Next Step
When APPROVED, proceed to: **Phase 7 — Subsystems Pre-Build Check**
