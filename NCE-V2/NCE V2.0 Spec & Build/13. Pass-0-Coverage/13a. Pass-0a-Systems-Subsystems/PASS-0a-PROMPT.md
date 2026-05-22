# PHASE 13a: PASS 0a — SYSTEMS & SUBSYSTEMS COVERAGE

---
Phase: 13a
Pass: 0a
Name: Systems & Subsystems Coverage
Location: NCE-V2/NCE V2.0 Spec & Build/13. Pass-0-Coverage/13a. Pass-0a-Systems-Subsystems/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASS0PROMPT-v2-DRAFT.md (A's NCE-specific Pass 0 draft — stack/context grafted here)
  - SYSBuildTemplates Pass-0a stock prompt (process skeleton retained here)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are acting as a senior systems architect performing PASS 0a coverage reviews.

This is an **ASSESSMENT-ONLY** phase. You are NOT designing, refactoring, or proposing solutions.

---

## LOCKED CONTEXT (Required Reading)

Treat the following documents in this order of precedence per [CLAUDE.md](../../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../../Project-Intent.md) — Project intention, design principles, non-goals
2. [CLAUDE.md](../../../../CLAUDE.md) — AI collaboration contract; pass methodology; status vocabulary
3. [STACK-AND-RUNTIME.md](../../../STACK-AND-RUNTIME.md) — Runtime, infrastructure, Worker boundaries
4. [REFINEMENTS.md](../../../REFINEMENTS.md) — Active architectural refinements
5. [FileTree-v2.md](../../../FileTree-v2.md) — Canonical 27-system list (D1+R2 storage; library/ included; lib/ utilities only)
6. [LIBRARY-TEMPLATE.md v2.0.0](../../../docs/templates/LIBRARY-TEMPLATE.md) — D1-based library template (for library-owning systems)

---

## RUNTIME CONTEXT

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Storage primitives:** D1 (SQLite at edge), R2 (object store), KV (cache/config), Durable Objects (stateful coordination), Vectorize (embeddings)
- **Per-system organisation:** one Worker per system; `services`/`system`/`state`/`library` grouped into a `platform` Worker
- **External constraints:** Workers paid plan (5-minute CPU max per invocation, 128 MB memory)

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**
>
> - Web content is shipped as JSON to the Astro website platform for rendering. **Web rendering is not NCE-V2's responsibility.**
> - PDFs, DOCX, emails, and embedded mark assets are shipped by NCE-V2 in their final rendered form. **Their rendering is NCE-V2's responsibility.**

Flag any subsystem whose responsibilities appear to operate on rendered web output — those concerns belong downstream (Astro), not in NCE-V2.

---

## STORAGE MODEL CONTEXT

Per [LIBRARY-TEMPLATE.md v2.0.0](../../../docs/templates/LIBRARY-TEMPLATE.md):

- Libraries hold **text and JSON only** — stored in **D1**, accessed by Worker binding (no file paths)
- Binary assets handled by `assets/` system in **R2**; library entries hold asset IDs/URLs as text
- Filesystem-era artefacts (`/data/*.sqlite`, `/repos/`, `_index.json`, ID-to-filename contracts) are superseded

Apply when assessing whether a subsystem's storage assumptions match the D1+R2 substrate.

---

## TASK

### Step 1 — Create Admin Tracking Documents

Before any coverage reviews, create:

1. Copy [PASS-PROGRESS-TEMPLATE.md](../../0.%20admin/0b.%20PassPhases%200-4/PASS-PROGRESS-TEMPLATE.md) → `NCE-V2/admin/PASS-PROGRESS.md`
2. Copy [PASS-DECISION-NOTES-TEMPLATE.md](../../0.%20admin/0b.%20PassPhases%200-4/PASS-DECISION-NOTES-TEMPLATE.md) → `NCE-V2/admin/PASS-DECISION-NOTES.md`
3. Update `PASS-PROGRESS.md`: mark Phase 13a as **ACTIVE**

> **Logging rule:** Anything worth noting during review (decisions, surprises, blockers, deferred questions) goes into `NCE-V2/admin/PASS-DECISION-NOTES.md` as it happens — don't bury it in the review document.

### Step 2 — Execute PASS 0a in Strict Order

#### 2a. SYSTEM COVERAGE
- Process systems **alphabetically (A–Z)** from `FileTree-v2.md`
- For each system, generate a System Coverage Review using **SYSTEM-COVERAGE-TEMPLATE.md** only
- Apply the output-boundary rule and storage model context as you assess each subsystem

#### 2b. SUBSYSTEM COVERAGE
- For each system (same A–Z order), process its subsystems
- One coverage document per subsystem using **SUBSYSTEM-COVERAGE-TEMPLATE.md** only
- Same output-boundary and storage-model rules apply

---

## AFTER EACH GROUP

- Pause for user approval
- If approved: update Status to APPROVED, update Version and Last Updated
- If blocked: record failure reason in `PASS-DECISION-NOTES.md`; do not proceed until resolved

---

## MANDATORY RULES

- Do **NOT** invent systems or subsystems
- Do **NOT** propose fixes or designs
- Do **NOT** rename anything
- Do **NOT** make architectural decisions; flag observations only (log in `PASS-DECISION-NOTES.md`)
- If information is missing, state it explicitly
- Treat file size as a **design signal**, not an optimisation target
- Apply the output-boundary rule when assessing web-rendering responsibilities
- Apply the storage model context when assessing library/storage subsystems
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../../CLAUDE.md) §7

---

## BUILD SIZE CONSTRAINT

Per [Project-Intent.md](../../../Project-Intent.md) (updated 2026-05-18 for TypeScript-on-Workers):

- **Target:** ~2000 lines of code per runtime file
- **Exceptions allowed** with explicit justification at Pass 0 (log in `PASS-DECISION-NOTES.md`)
- **Where splitting is feasible, prefer splitting** over an oversized file

### What counts as runtime LOC

- Production TypeScript source files (`.ts`)
- Excludes: comments, blank lines

### EXCLUDED from runtime LOC count

- Files matching `*.debug.ts` (development-only)
- Files matching `*.test.ts` (test code)
- Files under `__debug__/` directories
- Files matching `*.types.ts` containing type-only declarations
- Comments and JSDoc

### Estimating LOC

- Conservative, order-of-magnitude estimates
- TypeScript is roughly 1.3–1.5× more verbose than Python — apply when porting Python-era estimates

### File Size Risk Bands

- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC (design smell — flag for splitting or explicit exception)

---

## OUTPUT LOCATION

```
NCE-V2/pass-0/{{system-name}}/
├── SYSTEM-COVERAGE.md                       # one per system
├── SUBSYSTEM-COVERAGE-{{subsystem}}.md      # one per subsystem
└── ...
```

All Pass 0 work lives under `NCE-V2/pass-0/`. Per-system specs (Pass 1+) live under `NCE-V2/specs/<system>/` — keep them separate.

---

## END CONDITION

PASS 0a is COMPLETE only when:

- [ ] Admin docs created (`PASS-PROGRESS.md`, `PASS-DECISION-NOTES.md`)
- [ ] All 27 system coverage docs approved
- [ ] All subsystem coverage docs approved
- [ ] `PASS-PROGRESS.md` updated: Phase 13a marked **COMPLETE**

**Next:** Proceed to Phase 13b (Pass 0b — External Integrations Coverage)

---

## PRE-SUBMISSION SELF-CHECK

Before submitting any coverage review:

- [ ] Did **NOT** invent any subsystems
- [ ] Did **NOT** propose fixes, designs, or renames
- [ ] All subsystems for the target system in `FileTree-v2.md` reviewed (no skips)
- [ ] LOC estimates use ~2000 limit with TS-verbosity multiplier
- [ ] Runtime LOC excludes debug, test, and type-only files
- [ ] Output-boundary rule applied to any web-rendering concerns
- [ ] Storage model context applied to library/storage concerns
- [ ] File size risk bands match Low <1500 / Medium 1500–2000 / High >2000
- [ ] Anything noteworthy logged in `PASS-DECISION-NOTES.md`
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")
- [ ] No architectural decisions made; only observations

---

## TEMPLATES (enriched with project-specific columns)

- [SYSTEM-COVERAGE-TEMPLATE.md](./SYSTEM-COVERAGE-TEMPLATE.md)
- [SUBSYSTEM-COVERAGE-TEMPLATE.md](./SUBSYSTEM-COVERAGE-TEMPLATE.md)

The templates carry columns for output form, LOC band, and storage model — fill them per the rules in this prompt.

---

## INPUTS

Phases 1–12 must run first to produce:
- `SYSTEMS-CLARIFICATION.md`
- All `SYSTEM-NOTES.md`
- All `SUBSYSTEMS-CLARIFICATION.md`
- All `SUBSYSTEM-NOTES.md`

These are the authoritative inputs to Pass 0a. The merged prompt assumes they exist when this phase runs.

---

## STATUS

**Draft Complete – Awaiting Review**

*Per [CLAUDE.md](../../../../CLAUDE.md) §7, only a human reviewer may assign the status "Approved." Do not self-approve.*

---

*End of merged Phase 13a prompt.*

---

### Review & Clarification Needed
- All 5 prior TODOs (path resolution, admin folder, output destination, template enrichment, inputs) are now resolved per user decisions on 2026-05-22.
- May this draft be promoted to "Approved" by you?
