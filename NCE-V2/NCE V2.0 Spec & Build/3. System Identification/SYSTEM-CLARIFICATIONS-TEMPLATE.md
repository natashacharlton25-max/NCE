# Systems Clarification

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Claude | Human
---

## Project Context
(One short paragraph summarising NCE-V2's intent. Pull from Project-Intent.md.)

## NCE-V2 Source Note
For NCE-V2, the canonical system list is `NCE-V2/FileTree-v2.md` — 27 top-level systems (plus `lib/` utilities, which is not a Pass 0 target). This document **transforms** that list into per-system clarification, not discovers it from scratch.

---

## Proposed Systems (27 total)

### {{System Name}}

| Field | Value |
|-------|-------|
| **Purpose** | Owns responsibility for [doing what]. (One sentence.) |
| **Output Form** | JSON / rendered artefact / library entry / metadata / mixed |
| **Worker Grouping** | own Worker / `platform` Worker |
| **Library Ownership** | None / {{D1_BINDING_NAME(s)}} |

**Justification:**
- Why this responsibility must exist as a separate system
- Why this scope is expected to stay under ~2000 LOC (TS, runtime only, with file exclusions applied)

---

### {{Next System Name}}

(repeat for each of the 27 systems — alphabetical order)

---

## System Count Summary

| Total Systems | TS Verbosity Multiplier | Expected Avg LOC | Notes |
|---------------|--------------------------|-------------------|-------|
| 27 | 1.3–1.5× over Python | ~1000–1500 per system | Some larger (brand/ has 31 subsystems; orchestration has 16) |

---

## Cross-Cutting Concerns

| Concern | Owning System | Notes |
|---------|----------------|-------|
| Brand decisions | `brand/` | global constraint per CLAUDE.md §4 |
| Access / permissions | `access/` | global |
| Resilience / failure | `resilience/` | global |
| Library data access | `library/` | all library reads via `library/Librarian` |
| Asset (binary) storage | `assets/` | R2-backed |

---

## Dependency
This document requires approved: **Project Overview** + **Project Intention**

## Next Step
When APPROVED, proceed to: **Phase 4 — Systems Pre-Build Check**
