# PHASE 5: SYSTEMS BUILD

---
Phase: 5
Name: Systems Build (materialise SYSTEM-NOTES.md per system)
Location: NCE-V2/NCE V2.0 Spec & Build/5. Systems Build/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are materialising the 27 approved NCE-V2 systems into per-system folders + SYSTEM-NOTES.md files.

You are NOT designing internals, choosing technologies, or adding new systems.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md)
4. [REFINEMENTS.md](../../REFINEMENTS.md)
5. [FileTree-v2.md](../../FileTree-v2.md) — canonical system list
6. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) — for library-owning systems
7. `NCE-V2/admin/SYSTEMS-CLARIFICATION.md` and `SYSTEMS-PREBUILD-CHECK.md` (Phases 3+4 outputs)

---

## RUNTIME CONTEXT

- **2000 LOC band** per runtime file
- TypeScript 1.3–1.5× more verbose than Python
- Per-system Worker (except `services`/`system`/`state`/`library` grouped into `platform`)

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**

Each SYSTEM-NOTES.md must declare:
- **Output Form** (JSON / rendered / library entry / metadata / mixed)
- **Output Destination** (internal system / Astro / external provider / human / library)

---

## TASK

For each of the 27 approved systems from `SYSTEMS-PREBUILD-CHECK.md`:

1. Create a folder at `NCE-V2/specs/<system-name>/` (already exists from initial setup — just verify)
2. Create `SYSTEM-NOTES.md` inside that folder
3. Complete all sections based on:
   - The system's entry in `SYSTEMS-CLARIFICATION.md`
   - The system's listing in `FileTree-v2.md`
   - Project-Intent.md and STACK-AND-RUNTIME.md context

> **Logging rule:** "TBD" markers go in `PASS-DECISION-NOTES.md` so they don't get silently filled later.

---

## COMPLETING SYSTEM-NOTES.md

For each section, derive content from approved sources only:

### Purpose
- Pull from `SYSTEMS-CLARIFICATION.md`
- Keep it to one sentence

### Intent
- Expand slightly on purpose to describe the desired outcome
- Still high-level, no mechanics

### Justification
- Pull from `SYSTEMS-CLARIFICATION.md`

### Output Form / Destination (NCE-V2-specific)
- Per the output-boundary rule

### Library Ownership (if applicable)
- For library-owning systems (brand, content, ai, template, social, assets, etc.)
- Declare each library + D1 binding name (per LIBRARY-TEMPLATE.md)

### Worker Grouping
- own Worker / `platform` Worker (per STACK-AND-RUNTIME.md)

### In Scope / Out of Scope / Non-Goals
- Derive from the system's purpose
- "Out of Scope" should reference other systems where appropriate ("Belongs to X system")

### Inputs / Outputs / Dependencies
- Use system names from FileTree-v2.md
- Include binding type for each (service / D1 / R2 / KV / DO / Queue)
- If unknown, write "TBD — to be determined in Pass 0" and log

---

## RULES

1. **Use ONLY the approved system names** from FileTree-v2.md — no new systems, no renames
2. Do **NOT** reference technologies beyond what STACK-AND-RUNTIME.md states
3. Do **NOT** invent scope — derive only from approved documents
4. Respect the **2000 LOC band** — if scope seems larger, note it and log
5. Mark unknowns as TBD; log them
6. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{system}}/
└── SYSTEM-NOTES.md
```

Per-system foundation artefact. Sits alongside future Pass 1+ outputs (SYSTEM.md, CONTRACT.md, etc.).

---

## END CONDITION

- [ ] All 27 systems have `NCE-V2/specs/<system>/SYSTEM-NOTES.md`
- [ ] Each declares Output Form + Output Destination
- [ ] Library-owning systems declare libraries + D1 bindings
- [ ] Worker grouping declared
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Proceed to Phase 6 (Subsystems Identification)

---

## TEMPLATES (enriched for NCE-V2)

- [SYSTEMS-NOTE-TEMPLATE.md](./SYSTEMS-NOTE-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/admin/SYSTEMS-CLARIFICATION.md`
- `NCE-V2/admin/SYSTEMS-PREBUILD-CHECK.md`
- `NCE-V2/FileTree-v2.md`
- `NCE-V2/Project-Intent.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Output to `NCE-V2/specs/<system>/SYSTEM-NOTES.md` (alongside future Pass 1+ SYSTEM.md) — confirm?
- May this draft be promoted to "Approved"?
