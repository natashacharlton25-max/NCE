# External Integrations Register

---
Status: DRAFT | APPROVED
Version: v0.2.0 (enriched for NCE-V2)
Last Updated: {{timestamp}}
Owner: Human
---

## Project Context
(One short paragraph summarising NCE-V2's intent. Pull from `NCE-V2/Project-Intent.md`.)

## NCE-V2 Source Note
For NCE-V2, the canonical provider list is `integrations/<Provider>Integration.ts` subsystems in `NCE-V2/FileTree-v2.md` — 15 providers total. This document transforms that list into a formal register.

---

## Identified External Integrations

### {{Capability Name}}

| Field | Value |
|-------|-------|
| **Capability Needed** | (One sentence describing what external capability is required.) |
| **Direction** | Outbound receiver / Inbound source / Downstream renderer |
| **Provider** | {{e.g., Canva, Google, Pexels, Unsplash, Recraft, Phosphor, Emailit, YouTube}} |
| **FileTree-v2 Subsystem** | `integrations/{{Provider}}Integration.ts` |
| **Worker Secret Binding Name** | `{{SECRET_NAME}}` (placeholder; finalised in Phase 9) |

**Why Needed:**
- How this capability supports the project intent
- Which internal system(s) will use this integration

**Provider Options:** (Known alternatives, if any)
- Selected: {{Provider}}
- Alternatives: …

---

(repeat for each external integration — for NCE-V2 expect ~15 entries based on FileTree-v2 `integrations/`)

---

## Integration Summary (NCE-V2: 15 providers expected)

| # | Capability | Provider | Direction | Consuming System(s) | Status |
|---|------------|----------|-----------|---------------------|--------|
| 1 | {{capability}} | {{Provider}} | Inbound / Outbound / Renderer | {{system}} | Confirmed |

---

## No External Integrations?

If the project has no external integrations:
- Write "None identified" in the summary table
- Mark status as APPROVED
- Proceed directly — Phases 9–11 will be skipped

For NCE-V2: this is unlikely — at least 15 providers from FileTree-v2.

---

## Notes
(Assumptions, questions, or considerations about external dependencies.)

---

## Dependency
Requires approved: **Project Overview** + **Project Intention** + **FileTree-v2.md** (canonical source)

## Next Step
When approved:
- Store at: `NCE-V2/admin/EXTERNAL-INTEGRATIONS-REGISTER.md`
- External integration build (Phases 9–11) runs AFTER Systems + Subsystems build (Phases 5–8)
- Build order: Phases 1–4 (foundation + systems) → 5 (build systems) → 6–8 (subsystems) → 9–11 (integrations) → 12 (prepass check) → 13–17 (Pass 0–4)
