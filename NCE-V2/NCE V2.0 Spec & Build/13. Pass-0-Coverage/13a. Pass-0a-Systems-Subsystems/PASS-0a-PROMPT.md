# PHASE 13a: PASS 0a — SYSTEMS & SUBSYSTEMS COVERAGE

---
Phase: 13a
Pass: 0a
Name: Systems & Subsystems Coverage
Location: NCE-V2/NCE V2.0 Spec & Build/13. Pass-0-Coverage/13a. Pass-0a-Systems-Subsystems/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASS0PROMPT-v2-DRAFT.md (A's NCE-specific Pass 0 draft — stack/context content grafted here)
  - C:/Users/SYSBuildTemplates/13. Pass-0-Coverage/13a. Pass-0a-Systems-Subsystems/PASS-0a-PROMPT.md (B's generic Pass-0a — process skeleton retained here)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are acting as a senior systems architect performing PASS 0a coverage reviews.

This is an **ASSESSMENT-ONLY** phase. You are NOT designing, refactoring, or proposing solutions.

---

## LOCKED CONTEXT (Required Reading)

Treat the following documents in this order of precedence per [CLAUDE.md](../../../../CLAUDE.md) §10:

1. **Project-Intent.md** — Project intention, design principles, non-goals
2. **CLAUDE.md** — AI collaboration contract; pass methodology; status vocabulary
3. **STACK-AND-RUNTIME.md** — Runtime, infrastructure, Worker boundaries
4. **REFINEMENTS.md** — Active architectural refinements affecting scope (where applicable to target system)
5. **FileTree-v2.md** — Canonical subsystem list (D1 + R2 storage; includes `library/` and `assets/` systems; reflects output-boundary rule)
6. **NCE-V2/docs/templates/LIBRARY-TEMPLATE.md v2.0.0** — D1-based library template (for systems that own libraries)

> **TODO (Path resolution):** The first five locked-context files currently live at `C:/Users/NCE/` root, outside `NCE-V2/`. Decide whether to (a) copy them into `NCE-V2/docs/locked-context/`, (b) keep them at root with relative paths back up (`../../../../`), or (c) treat root as the project anchor with `NCE-V2/` as a subdirectory. Resolve before this prompt is used in anger.

---

## RUNTIME CONTEXT

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Storage primitives:** D1 (SQLite at edge), R2 (object store), KV (cache/config), Durable Objects (stateful coordination), Vectorize (embeddings)
- **Per-system organisation:** one Worker per system, with foundational systems (`services`, `system`, `state`, `library`) grouped into a `platform` Worker
- **External constraints:** Workers paid plan (5-minute CPU max per invocation, 128 MB memory)

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

When assessing whether a subsystem belongs in NCE-V2 or is a downstream-platform concern, apply this rule:

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**
>
> - Web content is shipped as JSON to the Astro website platform for rendering. **Web rendering is not NCE-V2's responsibility.**
> - PDFs, DOCX, emails, and embedded mark assets are shipped by NCE-V2 in their final rendered form. **Their rendering is NCE-V2's responsibility.**

Flag any subsystem whose responsibilities appear to operate on rendered web output — those concerns belong downstream (Astro), not in NCE-V2.

---

## STORAGE MODEL CONTEXT (for library-owning systems)

Per [LIBRARY-TEMPLATE.md v2.0.0](../../../docs/templates/LIBRARY-TEMPLATE.md):

- Libraries hold **text and JSON only** — stored in **D1**, accessed by Worker binding (no file paths).
- Binary assets (images, PDFs) are handled by the `assets/` system and stored in **R2**. A library entry that needs an image holds the asset's ID/URL as a text field — never the binary itself.
- Filesystem-era artefacts (`/data/*.sqlite`, `/repos/`, `_index.json`, ID-to-filename contracts) are superseded.

Apply this when assessing whether a subsystem's storage assumptions match the D1+R2 substrate.

---

## TASK

### Step 1 — Create Admin Tracking Documents

Before any coverage reviews, create:

1. `NCE-V2/admin/PASS-PROGRESS.md` — copy from `PASS-PROGRESS-TEMPLATE.md`
2. `NCE-V2/admin/PASS-DECISION-NOTES.md` — copy from `PASS-DECISION-NOTES-TEMPLATE.md`
3. Update `PASS-PROGRESS.md`: mark Phase 13a as **ACTIVE**

> **TODO (Admin path):** `NCE-V2/admin/` does not yet exist. Confirm the directory and template source paths before this step runs. The templates likely live under `NCE-V2/NCE V2.0 Spec & Build/0. admin/` — verify.

### Step 2 — Execute PASS 0a in Strict Order

#### 2a. SYSTEM COVERAGE
- Process systems **alphabetically (A–Z)** from `FileTree-v2.md`
- For each system, generate a System Coverage Review using **SYSTEM-COVERAGE-TEMPLATE.md** only
- Apply the **output-boundary rule** when assessing any subsystem with web-rendering responsibilities
- Apply the **storage model context** when assessing any subsystem that owns or accesses libraries

#### 2b. SUBSYSTEM COVERAGE
- For each system (same A–Z order), process its subsystems
- One coverage document per subsystem, using **SUBSYSTEM-COVERAGE-TEMPLATE.md** only
- Same output-boundary and storage-model rules apply

---

## AFTER EACH GROUP

- **Pause for user approval**
- If approved:
  - Update Status to APPROVED
  - Update Version and Last Updated
- If blocked:
  - Record failure reason in `PASS-DECISION-NOTES.md`
  - Do not proceed until resolved

---

## MANDATORY RULES

- Do **NOT** invent systems or subsystems
- Do **NOT** propose fixes or designs
- Do **NOT** rename anything
- Do **NOT** make architectural decisions; flag observations only
- If information is missing, state it explicitly
- Treat file size as a **design signal**, not an optimisation target
- Apply the output-boundary rule when assessing any subsystem with web-rendering responsibilities
- Apply the storage model context when assessing library/storage subsystems
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../../CLAUDE.md) §7, approval is human-only

---

## BUILD SIZE CONSTRAINT

Per Project-Intent.md (updated 2026-05-18 for TypeScript-on-Workers):

- **Target:** ~2000 lines of code per runtime file
- **Exceptions allowed** with explicit justification at Pass 0
- **Where splitting is feasible, prefer splitting** over an oversized file

### What counts as runtime LOC

- Production TypeScript source files (`.ts`)
- Excludes: comments, blank lines

### What is EXCLUDED from the runtime LOC count

- Files matching `*.debug.ts` (development-only code)
- Files matching `*.test.ts` (test code)
- Files under `__debug__/` directories (debug utilities)
- Files matching `*.types.ts` containing type-only declarations (no runtime impact)
- Comments and JSDoc

### Estimating LOC

- Estimate conservatively (order-of-magnitude is sufficient)
- TypeScript is roughly 1.3–1.5× more verbose than Python for equivalent logic
- When porting Python-era LOC estimates from prior Pass 0 docs, apply this multiplier

### File Size Risk Bands

- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC (design smell — flag for splitting or explicit exception)

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{system-name}}/
├── SYSTEM-COVERAGE.md                       # one per system
├── SUBSYSTEM-COVERAGE-{{subsystem}}.md      # one per subsystem
└── ...
```

> **TODO (Output destination):** Confirm coverage docs land in `NCE-V2/specs/<system>/` (alongside future SPEC docs) or in a parallel `NCE-V2/pass-0/` structure. Either is defensible; pick one.

---

## END CONDITION

PASS 0a is COMPLETE only when:

- [ ] Admin docs created (`PASS-PROGRESS.md`, `PASS-DECISION-NOTES.md`)
- [ ] All system coverage docs are approved
- [ ] All subsystem coverage docs are approved
- [ ] `PASS-PROGRESS.md` updated: Phase 13a marked **COMPLETE**

**Next:** Proceed to Phase 13b (Pass 0b — External Integrations Coverage)

---

## PRE-SUBMISSION SELF-CHECK

Before submitting any coverage review, verify:

- [ ] Did **NOT** invent any subsystems
- [ ] Did **NOT** propose fixes, designs, or renames
- [ ] All subsystems for the target system in `FileTree-v2.md` were reviewed (no skips)
- [ ] LOC estimates use the ~2000 limit and TS-verbosity multiplier
- [ ] Runtime LOC excludes debug, test, and type-only files per the convention
- [ ] Output-boundary rule was applied to any web-rendering concerns
- [ ] Storage model context was applied to any library/storage concerns
- [ ] File size risk bands match Low <1500 / Medium 1500–2000 / High >2000
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")
- [ ] Did not make architectural decisions; only observations

If any item is unchecked, stop and either complete it or flag what's missing as an open question.

---

## TEMPLATES

- [SYSTEM-COVERAGE-TEMPLATE.md](./SYSTEM-COVERAGE-TEMPLATE.md)
- [SUBSYSTEM-COVERAGE-TEMPLATE.md](./SUBSYSTEM-COVERAGE-TEMPLATE.md)

> **TODO (Template enrichment):** B's `SYSTEM-COVERAGE-TEMPLATE.md` and `SUBSYSTEM-COVERAGE-TEMPLATE.md` are table-only and do not yet carry an output-boundary check column, an LOC-band column tuned to <1500 / 1500–2000 / >2000, or a storage-model column. Decide whether to enrich the templates or rely on this prompt to enforce those checks during review.

---

## INPUTS

From phases 1–12 (per B's chain):
- `SYSTEMS-CLARIFICATION.md`
- All `SYSTEM-NOTES.md`
- All `SUBSYSTEMS-CLARIFICATION.md`
- All `SUBSYSTEM-NOTES.md`

> **TODO (Inputs from phases 1–12):** None of phases 1–12 outputs exist yet in `NCE-V2/`. For this project, the equivalent inputs are:
>   - **System identity / scope:** Project-Intent.md
>   - **Canonical system list:** FileTree-v2.md (supersedes the SYSTEMS-CLARIFICATION step)
>   - **Per-system notes:** none currently captured — to be produced under the relevant phase 1–12 templates if needed
>
> Confirm: run phases 1–12 first to produce these inputs, or accept FileTree-v2.md + Project-Intent.md as substitute inputs for 13a?

---

## STATUS

**Draft Complete – Awaiting Review**

*Per [CLAUDE.md](../../../../CLAUDE.md) §7, only a human reviewer may assign the status "Approved." Do not self-approve.*

---

*End of merged Phase 13a prompt.*

---

### Review & Clarification Needed (about this merged prompt)

- Locked-context path resolution (root vs `NCE-V2/docs/locked-context/`) — see TODO above
- Admin directory path (`NCE-V2/admin/`) and template source — see TODO above
- Output destination (`NCE-V2/specs/<system>/` vs `NCE-V2/pass-0/`) — see TODO above
- Coverage template enrichment (output-boundary + LOC-band + storage-model columns) — see TODO above
- Phase 1–12 inputs (run them, or substitute FileTree-v2 + Project-Intent) — see TODO above
- Does the merge preserve everything you wanted from A's draft and B's process skeleton, or are there sections you'd add/cut?
- May I proceed with the same merge pattern for 13b (External Integrations), 13c (Checklist), 13d (Internal Integrations), and then phases 14–17 (Passes 1–4)?
