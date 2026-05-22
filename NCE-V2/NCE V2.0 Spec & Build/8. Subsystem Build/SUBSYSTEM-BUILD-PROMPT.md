# PHASE 8: SUBSYSTEM BUILD

---
Phase: 8
Name: Subsystem Build (materialise SUBSYSTEM-NOTES.md per subsystem)
Location: NCE-V2/NCE V2.0 Spec & Build/8. Subsystem Build/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are materialising the approved subsystems for each parent system into per-subsystem folders + SUBSYSTEM-NOTES.md.

You are NOT designing internals, choosing technologies, or adding new subsystems.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../../FileTree-v2.md)
5. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md)
6. `NCE-V2/specs/<parent>/SYSTEM-NOTES.md`, `SUBSYSTEMS-CLARIFICATION.md`, `SUBSYSTEMS-PREBUILD-CHECK.md` (Phases 5–7 outputs)

---

## RUNTIME CONTEXT

- **2000 LOC band** per subsystem runtime file
- TypeScript 1.3–1.5× verbosity over Python

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

Each SUBSYSTEM-NOTES.md must declare:
- **Output Form** (JSON / rendered / library entry / metadata)
- **Output Destination** (parent / sibling / external system / Astro / external provider / human)
- **Storage Touch** (none / D1 / R2 / KV / DO / Queue)
- **D1 Binding (if library-touching)** — and confirm access goes through `library/Librarian`

---

## TASK

For each parent system (alphabetical A→Z), and for each of its approved subsystems:

1. Create folder at `NCE-V2/specs/<parent>/<subsystem-name>/` (subsystem name matches FileTree-v2 filename without `.ts`)
2. Create `SUBSYSTEM-NOTES.md` inside that folder
3. Complete all sections based on:
   - The subsystem's entry in `SUBSYSTEMS-CLARIFICATION.md`
   - FileTree-v2.md context (parent system, sibling subsystems)
   - Parent's `SYSTEM-NOTES.md` for scope context

> **Logging rule:** TBD markers and any inferred content go in `PASS-DECISION-NOTES.md`.

---

## COMPLETING SUBSYSTEM-NOTES.md

For each section:

### Parent System / Purpose / Intent / Justification
- From `SUBSYSTEMS-CLARIFICATION.md`

### Output Form / Destination (NCE-V2-specific)
- Per output-boundary rule

### Storage Touch + D1 Binding (if applicable)
- For library-touching subsystems (most `services/`, `library/`, library-owning system internals)

### Role Within Parent
- How this subsystem contributes to the parent's purpose

### In Scope / Out of Scope / Non-Goals
- Derived from the subsystem's specific responsibility

### Inputs / Outputs / Dependencies
- With binding types (service / D1 / R2 / KV / DO / Queue / in-Worker)
- TBD allowed where Pass 0 needs to determine

---

## RULES

1. Use **ONLY** approved subsystem names from FileTree-v2.md — no inventions
2. Do **NOT** rename
3. Do **NOT** reference technologies beyond STACK-AND-RUNTIME.md
4. Respect the **2000 LOC band**
5. Mark unknowns as TBD; log them
6. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{parent}}/{{subsystem}}/
└── SUBSYSTEM-NOTES.md
```

Per-subsystem foundation artefact. Sits alongside future Pass 1+ outputs (SUBSYSTEM.md, CONTRACT.md).

---

## END CONDITION

- [ ] All subsystems for all 27 systems have `SUBSYSTEM-NOTES.md`
- [ ] Each declares Output Form + Destination + Storage Touch + D1 Binding (where applicable)
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 9 (External Integration Provider Build) — or skip to Phase 12 if no external integrations

For NCE-V2: `integrations/` has 15 provider subsystems — Phase 9 applies.

---

## TEMPLATES (enriched for NCE-V2)

- [SUBSYSTEM-NOTES-TEMPLATE.md](./SUBSYSTEM-NOTES-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/specs/<parent>/SYSTEM-NOTES.md`
- `NCE-V2/specs/<parent>/SUBSYSTEMS-CLARIFICATION.md`
- `NCE-V2/specs/<parent>/SUBSYSTEMS-PREBUILD-CHECK.md`
- `NCE-V2/FileTree-v2.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Output location nested under parent (`NCE-V2/specs/<parent>/<subsystem>/`) — confirm?
- For `brand/`'s nested groupings (`brand/core/<sub>`, `brand/identity/<sub>`, etc.), should the path be 3 levels deep, or flatten to `NCE-V2/specs/brand/<sub>/`?
- May this draft be promoted to "Approved"?
