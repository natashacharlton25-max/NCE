# STAGE 2: COMPONENT SPECS

---
Stage: 2 of 5
Name: Component Specs (per system + per subsystem + per library + per integration service)
Methodology: NCE-V2 Reduced Methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## BLOCKING OPEN QUESTION — must resolve BEFORE speccing `content/` or `template/` (both in Tier 3)

**Where does the therapeutic frameworks library live?**

`C:/Users/NCE/LibraryJsonFilled/` contains **30 evidence-based + proprietary therapeutic frameworks** (CBT, polyvagal, attachment theory, stages of change, COM-B, habit loop, etc.) — ~140 KB of structured JSON across 12 files. Each framework has professional + peer-led descriptions, background, structure (stages/components/steps), usage guidance, and content application rules. This IS real library content that must populate an NCE-V2 library.

**The decision is which library** — and that determines which system's library list owns it (and therefore which system's spec writes the `.library.md`). Three candidates are NOT equivalent; they reflect different models of what a therapeutic framework IS:

| Candidate | Model | Owner system | Risk |
|---|---|---|---|
| **New `frameworks` library** | Frameworks are their own thing — methodology with stages/components AND content application rules. | `content/` (most likely owner) | Adds a library not in v1 `_LIBRARIES.md`; needs explicit subsystem mapping |
| **Expand `content/themes` library** | Frameworks are themes — content topics that drive workbook generation. | `content/` (ContentManager owns themes) | A theme in v1 was a *generated content topic*; folding 30 structured frameworks into the same schema risks fitting neither well |
| **`cognitive-types` library under `template/`** | Frameworks are structural patterns content follows. | `template/` (TemplateEngine owns cognitive-types per v1 `_LIBRARIES.md`) | "Cognitive-types" and "therapeutic frameworks" may be different concepts wearing similar words |

**Why this is blocking:** LibraryJsonFilled's structure (stages, components, usage guidance, content application rules) has BOTH methodology content AND structural pattern — meaning frameworks may warrant their own library, not be folded into either themes or cognitive-types. The "right answer" is an architecture decision, not a labelling decision. Defer it past Tier 3 and the system whose `.library.md` should have included it gets specced without it.

**How to resolve:**
1. When approaching Tier 3 speccing (`content/` and `template/` are both Tier 3), pause.
2. Open the 12 LibraryJsonFilled JSON files. Read at least 2 frameworks end-to-end.
3. Decide: content, template, or its own thing?
4. **Log the decision + rationale in `NCE-V2/admin/PASS-DECISION-NOTES.md`** before continuing.
5. Proceed with the affected system's spec including the frameworks library in its ownership list.

Do NOT spec `content/` or `template/` past their system-level SPEC.md without this resolved.

---

## ROLE

You produce a comprehensive spec doc for every NCE-V2 component:
- Each of the 27 systems (system-level SPEC.md)
- Each subsystem within each system (subsystem SPEC.md)
- Each D1 content library (`.library.md` per LIBRARY-TEMPLATE v2.0.0)
- Each integration service (per provider, per service)

Every component gets its own doc covering rules, boundaries, flow, Worker, scope, contracts, types, errors, examples — implementation-ready in one file.

---

## PREREQUISITE

Stage 1 must be COMPLETE with `NCE-V2/admin/PROJECT-FRAME.md` showing **FRAME LOCKED**.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../CLAUDE.md) §10:

1. [Project-Intent.md](../Project-Intent.md)
2. [CLAUDE.md](../../CLAUDE.md)
3. [STACK-AND-RUNTIME.md](../STACK-AND-RUNTIME.md)
4. [FileTree-v2.md](../FileTree-v2.md) — canonical 27-system list + subsystems
5. [REFINEMENTS.md](../REFINEMENTS.md)
6. [LIBRARY-TEMPLATE.md v2.0.0](../docs/templates/LIBRARY-TEMPLATE.md)
7. `NCE-V2/admin/PROJECT-FRAME.md` (Stage 1)

---

## NCE-V2 RULES (apply throughout every component spec)

- **Output-boundary:** NCE-V2 emits JSON for web (Astro renders); NCE-V2 emits final rendered artefacts for PDF/DOCX/email/marks.
- **Library access:** all reads via `library/Librarian`; only library write-owner module writes its library.
- **Storage:** D1 (libraries, text+JSON only), R2 (binaries), KV (cache/config), DO (stateful coordination), Vectorize (embeddings).
- **2000 LOC band** per runtime `.ts` file. Excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments. TS verbosity 1.3–1.5× Python.
- **TypeScript strict:** no `any` without justification. ESM only.
- **Worker secrets:** never in repo; always via `wrangler secret`.

---

## TASK

### Order

Process systems **in dependency order** per the canonical Dependency Tiers in [PROJECT-SPEC-TEMPLATE.md §11](./PROJECT-SPEC-TEMPLATE.md) (Tier 1 → Tier 2 → Tier 3 → Tier 4 → Tier 5). Within a tier, alphabetical.

**Why dependency order, not alphabetical:** every data-owning system's spec references `library/Librarian` for D1 reads, and every system spec references foundation services (`services/DatabaseHandler`, `system/Logger`, `state/StateManager`). If those are not yet specced, downstream specs stand on undefined contracts. Foundational systems (`system`, `services`, `state`, `library`) are therefore specced first. The dependency map is the single source of truth — do not "tidy" this order back to alphabetical.

Within each system:

1. **System-level SPEC.md** (using `SYSTEM-SPEC-TEMPLATE.md`)
2. **Each subsystem's SPEC.md** (using `SUBSYSTEM-SPEC-TEMPLATE.md`) — alphabetical within parent
3. **Each library this system owns** (using existing `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0) — produces `<system>/libraries/<library>.library.md`

After all 27 systems done, process **integrations** (Tier 5 — last):

4. For each integration provider (alphabetical):
   - Each service's SPEC.md (using `INTEGRATION-SERVICE-SPEC-TEMPLATE.md`)

### Per-Component Input Sources

In addition to the LOCKED CONTEXT above, the following **per-component external inputs** apply. Read these before drafting the relevant component's spec.

**`brand/` (Tier 3) — substantial existing work:**
- `C:/Users/Business/BrandKit Workflow/` — actual TypeScript code (`tsconfig.json`, `package.json`, `lib/`, `scripts/`, `steps/`, `node_modules/`); closest thing to working v2-compatible code in your workspace; informs brand/ TS implementation patterns
- `C:/Users/Business/BrandLib/` — library content organised by category (compliance, content, ops, outcomes, safety, voice); informs brand library schemas + content
- `C:/Users/Business/BrandData/test-brand/` — actual brand data for one test brand; informs brand library schema validation

**`ai/` (Tier 3) — partial existing work:**
- `C:/Users/Business/BrandKit AIGen/` — AI generation workflows (prompts, sections, `_config/`); may inform ai/ subsystems, especially around prompt building and generation patterns. Verify before treating as authoritative; may be more orientation than canonical input.

**For the therapeutic frameworks library (home TBD — see BLOCKING OPEN QUESTION at top):**
- `C:/Users/NCE/LibraryJsonFilled/` — 30 frameworks across 12 JSON files (~140 KB); informs the library's schema AND populates it with actual entries. The library's owner system gets decided per the BLOCKING OPEN QUESTION; once resolved, this content goes into that library.

**All other systems (Tiers 1, 2, 4, 5):**
- **Greenfield draft** from FileTree-v2 + foundation docs (PROJECT-FRAME, Project-Intent, STACK-AND-RUNTIME, LIBRARY-TEMPLATE v2.0.0). No authoritative external input.

**Explicitly DO NOT port from `C:/Users/NCE/NCEMPIRE/`:**
- 2,276 markdown files but **zero code** (per `C:/Users/NCE/AUDIT-REPORT.md` 2026-02-20)
- All architecture/design docs from earlier pre-implementation phase
- The project owner has explicitly chosen fresh draft over porting
- `NCEMPIRE/<system>/Pass0-Coverage.md` (exists for 17 of 31 v1 systems) may be read as **orientation only** — to see what was already thought through — but the v2 spec is fresh-drafted from NCE-V2 templates, not derived from this content.
- Note: v1 had 31 systems; v2 has 27 systems plus `lib/` (see FileTree-v2.md for collapses: colours/typography/visual → brand; image/ai-image → assets; svg → lib/svg/; library/ is new). Do not assume v1 → v2 1:1 mapping for any system.

### Output locations

```
NCE-V2/specs/<system>/SPEC.md                          # system-level (27 total)
NCE-V2/specs/<system>/<subsystem>/SPEC.md              # subsystem-level (~226 total)
NCE-V2/specs/<system>/libraries/<library>.library.md   # library-level (~16 total)
NCE-V2/specs/integrations/<provider>/<service>/SPEC.md # integration-service-level (~30 total)
```

### Per-component approval gate

After drafting each component's spec:
1. Run the **Hardening Self-Check** at the bottom of the template (completeness, ambiguity, implementability, test coverage)
2. Present for review
3. Get human approval before moving to next component
4. Mark Status: APPROVED in the spec's metadata

### End-of-stage cross-cutting audit

After all components are approved, produce `NCE-V2/admin/STAGE-2-AUDIT.md` checking:

- [ ] No overlapping subsystem responsibilities (within or across systems)
- [ ] No circular dependencies (Worker-level or subsystem-level)
- [ ] Output-boundary respected everywhere (no JSON-emitter has rendered output; no renderer has JSON-only output)
- [ ] Library access goes through `library/Librarian` everywhere (no direct D1 from outside `library/`)
- [ ] All TS types consistent across boundaries (output type = consumer's input type)
- [ ] All error codes unique and registered
- [ ] All library D1 binding names unique
- [ ] All Worker secret binding names unique

If audit fails → revise the affected component spec(s) → re-audit.

---

## MANDATORY RULES

- Do **NOT** invent functionality not implied by FileTree-v2.md
- Do **NOT** rename systems or subsystems
- Do **NOT** skip subsystems — every one in FileTree-v2 gets its own doc
- TypeScript signatures throughout (not Python pseudocode)
- Apply output-boundary rule to every Output Form declaration
- Apply library access rule (via `library/Librarian`) to every D1 touch
- Each spec ends with Hardening Self-Check; failed checks → log in `NCE-V2/admin/PASS-DECISION-NOTES.md`
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../CLAUDE.md) §7

---

## END CONDITION

- [ ] All 27 system SPEC.md complete and approved
- [ ] All subsystem SPEC.md complete and approved (~226)
- [ ] All library `.library.md` complete and approved (~16)
- [ ] All integration service SPEC.md complete and approved (~30)
- [ ] `NCE-V2/admin/STAGE-2-AUDIT.md` complete with PASS verdict
- [ ] Human approves Stage 2 → 3 transition

**Next:** Stage 3 (Project-Wide Spec)

---

## TEMPLATES

- [SYSTEM-SPEC-TEMPLATE.md](./SYSTEM-SPEC-TEMPLATE.md)
- [SUBSYSTEM-SPEC-TEMPLATE.md](./SUBSYSTEM-SPEC-TEMPLATE.md)
- [LIBRARY-TEMPLATE.md v2.0.0](../docs/templates/LIBRARY-TEMPLATE.md) (existing)
- [INTEGRATION-SERVICE-SPEC-TEMPLATE.md](./INTEGRATION-SERVICE-SPEC-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
