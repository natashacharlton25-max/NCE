# PHASE 17: PASS 4 — IMPLEMENTATION PLANNING

---
Phase: 17
Pass: 4
Name: Implementation Planning
Location: NCE-V2/NCE V2.0 Spec & Build/17. Pass-4-Implementation-Planning/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASSLIST.md Pass-4 section (relevant content absorbed; NOTE: B's Phase 17 is implementation planning, not hardening; B has a separate Phase 27 for Hardening)
  - C:/Users/NCE/prompts/SYSTEMHARDENINGPROMPT.MD (sizing/structure parts absorbed; balance redirected to B's Phase 27 Hardening when that phase is adapted)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are planning the actual NCE-V2 build — what files, what order, what size.

This phase takes the dependency map (from Pass 3) and produces a concrete implementation plan developers can follow.

This is the **last pass phase** before moving to PreCode (Phase 36 onwards).

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md) — confirms ~2000 LOC target for TS-on-Workers
2. [CLAUDE.md](../../../CLAUDE.md) — pass methodology
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Worker grouping; deployment topology
4. [FileTree-v2.md](../../FileTree-v2.md) — canonical file structure
5. `NCE-V2/specs/DEPENDENCY-MAP.md` — Pass 3 output (primary input)
6. `NCE-V2/specs/<system>/CONTRACT.md` + `SYSTEM.md` + `SUBSYSTEM.md` (Pass 1 + Pass 2 outputs)

---

## RUNTIME CONTEXT

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Worker topology:** one Worker per system in the general case; `platform` Worker groups `services`/`system`/`state`/`library`
- **Storage primitives:** D1, R2, KV, DO, Vectorize
- **External constraints:** 5-min CPU max per invocation, 128 MB memory
- **Deployment:** `wrangler` per Worker; D1 migrations under `migrations/{library}/`

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

Implementation plan must reflect:
- `website/` system files emit JSON only; no HTML rendering code in this Worker
- `renderers/` system has PDFRenderer, DOCXRenderer, HTMLRenderer (for non-Astro), MarkdownRenderer — final rendered output goes to consumers (PDF stream, DOCX blob, etc.)
- `email/` system composes email HTML, hands off to `integrations/EmailitIntegration` for send
- Library access in every Worker goes through `library/Librarian` — no direct D1 bindings outside `library/`

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 17 as **ACTIVE**

> **Logging rule:** Any sizing exceptions (file >2000 LOC accepted with justification) get logged in `NCE-V2/admin/PASS-DECISION-NOTES.md`.

### Step 2 — Define File Structure
- For each of the 27 systems + `lib/` + `migrations/`:
  - List the `.ts` files that will be created
  - Define each file's purpose (one sentence)
  - Note whether each file is runtime, test, debug, or type-only

### Step 3 — Estimate Size

#### What counts as runtime LOC
- Production `.ts` source files
- Excludes: comments, blank lines

#### EXCLUDED from runtime LOC count
- `*.debug.ts` (development-only code)
- `*.test.ts` (test code)
- Files under `__debug__/` directories
- `*.types.ts` containing type-only declarations
- Comments and JSDoc

#### Estimating LOC
- Conservative, order-of-magnitude estimates
- TypeScript ~1.3–1.5× more verbose than Python — apply multiplier when porting old estimates
- Each subsystem typically 200–1500 LOC; flag any subsystem implying >2000 LOC

### Step 4 — Verify Size Constraints

| Signal | Action |
|--------|--------|
| File <1500 LOC | OK (Low risk) |
| File 1500–2000 LOC | Monitor (Medium risk) |
| File >2000 LOC | **STOP** — propose split, return to Pass 0 or Pass 1; log exception in `PASS-DECISION-NOTES.md` |

### Step 5 — Break Into Tasks
- Define discrete, implementable tasks
- Each task should be completable in one session (1–4 hours of focused work)
- Tasks should follow dependency order from `DEPENDENCY-MAP.md`
- Each task: testable in isolation; clear "done" criteria

### Step 6 — Assign Order
- Use dependency map to sequence tasks
- `platform` Worker (`services`/`system`/`state`/`library`) typically first
- Identify parallel work streams
- Define milestones

---

## WHAT THE IMPLEMENTATION PLAN SHOWS

| Question | Plan Section |
|----------|--------------|
| What files will exist? | File Structure (per system) |
| How big will each file be? | LOC Estimates (with exclusion rules applied) |
| What are the discrete tasks? | Task Breakdown |
| In what order? | Task Sequence (from DEPENDENCY-MAP.md) |
| What can be parallelised? | Parallel Streams |
| What are the milestones? | Milestones |
| What's the platform-Worker first-build slice? | Foundation Milestone |

---

## MANDATORY RULES

- Do **NOT** exceed ~2000 LOC per file — flag, split, or log explicit exception
- Do **NOT** define tasks that violate dependency order
- Do **NOT** start implementation — only plan
- Tasks must be specific (file name + verb + outcome)
- Apply the output-boundary rule when planning system files
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## TASK GRANULARITY

**Good task:**
> "Implement BrandManager.ts: brand CRUD via library/Librarian, version bump on update, ref_status cascade on archive."

**Bad task:**
> "Build brand/ system" (too big — covers ~31 subsystems)
> "Write line 42 of BrandManager.ts" (too small)

Each task should be:
- Completable in 1–4 hours
- Testable in isolation
- Clear "done" criteria

---

## OUTPUT LOCATION

```
NCE-V2/specs/IMPLEMENTATION-PLAN.md
```

Single project-wide document. Pass 4 outputs live in `NCE-V2/specs/` (Pass 0 work stays in `NCE-V2/pass-0/`).

---

## END CONDITION

- [ ] File structure defined for all 27 systems + `lib/` + `migrations/`
- [ ] LOC estimates for all files (none >2000 LOC, exclusions applied)
- [ ] Tasks broken down per the granularity rule
- [ ] Task sequence follows `DEPENDENCY-MAP.md` ordering
- [ ] `platform` Worker foundation milestone identified
- [ ] Milestones defined
- [ ] Output-boundary respected in every system's file plan
- [ ] `IMPLEMENTATION-PLAN.md` approved
- [ ] `PASS-PROGRESS.md` updated: Phase 17 marked **COMPLETE**

**Next:** Proceed to Phase 18 (Post-Pass-Checklist), then onward to Pre-Spec (Phase 20), Specs (Phases 21–26), Hardening (Phase 27), PreCode (Phase 36), Implementation (Phases 43–52), Testing (Phases 54–60), Release (Phases 61–68).

---

## PRE-SUBMISSION SELF-CHECK

- [ ] All 27 systems have file structure
- [ ] LOC estimates use ~2000 limit with TS verbosity multiplier
- [ ] Runtime LOC excludes debug, test, and type-only files
- [ ] No file estimated >2000 LOC without explicit exception note in log
- [ ] `platform` Worker foundation milestone present
- [ ] Output-boundary respected
- [ ] Library access goes through `library/Librarian` in every plan
- [ ] Anything noteworthy logged in `PASS-DECISION-NOTES.md`
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")

---

## TEMPLATES (updated for project-specific constraints)

- [IMPLEMENTATION-PLAN-TEMPLATE.md](./IMPLEMENTATION-PLAN-TEMPLATE.md)

The template uses the 2000 LOC ceiling with TS verbosity multiplier and file exclusions — fill per the rules in this prompt.

---

## INPUTS

From Pass 3: `NCE-V2/specs/DEPENDENCY-MAP.md`
From Pass 1 & 2: All grounding docs, all contracts (under `NCE-V2/specs/`)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- All prior TODOs resolved per user decisions on 2026-05-22.
- May this draft be promoted to "Approved"?
