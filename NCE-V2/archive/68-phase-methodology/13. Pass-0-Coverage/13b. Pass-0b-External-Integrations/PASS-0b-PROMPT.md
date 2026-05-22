# PHASE 13b: PASS 0b — EXTERNAL INTEGRATIONS & SERVICES COVERAGE

---
Phase: 13b
Pass: 0b
Name: External Integrations & Services Coverage
Location: NCE-V2/NCE V2.0 Spec & Build/13. Pass-0-Coverage/13b. Pass-0b-External-Integrations/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are acting as a senior systems architect performing PASS 0b coverage reviews.

This is an **ASSESSMENT-ONLY** phase. You are NOT designing, refactoring, or proposing solutions.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../../Project-Intent.md) — Project intention, design principles, non-goals
2. [CLAUDE.md](../../../../CLAUDE.md) — AI collaboration contract, status vocabulary
3. [STACK-AND-RUNTIME.md](../../../STACK-AND-RUNTIME.md) — Runtime, Worker boundaries
4. [REFINEMENTS.md](../../../REFINEMENTS.md) — Active architectural refinements
5. [FileTree-v2.md](../../../FileTree-v2.md) — Canonical subsystem list (includes `integrations/` system with 15 subsystems)

---

## RUNTIME CONTEXT

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Storage primitives:** D1, R2, KV, Durable Objects, Vectorize
- **Per-system organisation:** one Worker per system
- **External constraints:** Workers paid plan (5-min CPU max, 128 MB memory)
- **Outbound integrations:** Worker `fetch()` to provider APIs; rate limits and credential management apply

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**

External providers map to one of three directions:

- **Outbound receivers** of rendered NCE-V2 artefacts (e.g. Emailit, Canva, Google Docs/Sheets/Slides). NCE-V2 rendering applies before send.
- **Inbound sources** supplying raw inputs to NCE-V2 (e.g. Pexels, Unsplash, Recraft, Phosphor). Assets land in `assets/` (R2); metadata lands in the relevant D1 library via `library/`.
- **Downstream renderers** receiving JSON from NCE-V2 (Astro for web pages). NCE-V2 does NOT render web HTML.

Flag any provider/service review that gets the direction wrong.

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 13b as **ACTIVE**

> **Logging rule:** Note decisions and observations in `NCE-V2/admin/PASS-DECISION-NOTES.md` as they happen.

### Step 2 — Execute PASS 0b in Strict Order

#### 2a. PROVIDER COVERAGE
- Process providers **alphabetically (A–Z)**
- For each provider, generate a Provider Coverage Review using **PROVIDER-COVERAGE-TEMPLATE.md** only
- Providers map to `integrations/<Provider>Integration.ts` subsystems in FileTree-v2.md

#### 2b. SERVICE COVERAGE
- For each provider (same order), process its services
- One coverage document per service using **SERVICE-COVERAGE-TEMPLATE.md** only

---

## AFTER EACH GROUP

- Pause for user approval
- If approved: update Status to APPROVED, update Version, Last Updated
- If blocked: record reason in `PASS-DECISION-NOTES.md`; do not proceed until resolved

---

## MANDATORY RULES

- Do **NOT** invent providers or services
- Do **NOT** propose fixes or designs
- Do **NOT** rename anything
- Do **NOT** make architectural decisions; flag observations only
- If information is missing, state it explicitly
- Treat file size as a **design signal**
- Apply the output-boundary rule to provider/service direction
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../../CLAUDE.md) §7

---

## BUILD SIZE CONSTRAINT

- **Target:** ~2000 LOC per integration wrapper
- TypeScript 1.3–1.5× more verbose than Python — apply when porting prior estimates
- Excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments

### File Size Risk Bands
- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC

---

## OUTPUT LOCATION

```
NCE-V2/pass-0/integrations/
├── PROVIDER-COVERAGE-{{provider}}.md
├── SERVICE-COVERAGE-{{provider}}-{{service}}.md
└── ...
```

All Pass 0 work lives under `NCE-V2/pass-0/`.

---

## SKIP CONDITION

If no external integrations exist:
- Skip PASS 0b entirely
- Mark as "N/A — No external integrations" in `PASS-DECISION-NOTES.md`
- Proceed directly to Phase 13c

For NCE-V2 this is unlikely — `integrations/` system has 15 provider subsystems.

---

## END CONDITION

- [ ] All provider coverage docs approved
- [ ] All service coverage docs approved
- [ ] `PASS-PROGRESS.md` updated: Phase 13b marked **COMPLETE**

**Next:** Proceed to Phase 13c (Pass 0 Checklist)

---

## PRE-SUBMISSION SELF-CHECK

- [ ] Did NOT invent any providers/services
- [ ] All providers from FileTree-v2.md `integrations/` reviewed
- [ ] Output-boundary direction (inbound / outbound / downstream renderer) correctly identified
- [ ] LOC estimates use ~2000 limit with TS verbosity multiplier
- [ ] Anything noteworthy logged in `PASS-DECISION-NOTES.md`
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")

---

## TEMPLATES (enriched with project-specific columns)

- [PROVIDER-COVERAGE-TEMPLATE.md](./PROVIDER-COVERAGE-TEMPLATE.md)
- [SERVICE-COVERAGE-TEMPLATE.md](./SERVICE-COVERAGE-TEMPLATE.md)

The templates carry columns for direction, LOC band, and credential handling — fill per the rules in this prompt.

---

## INPUTS

Phases 9–11 must run first to produce:
- EXTERNAL-INTEGRATIONS-REGISTER.md
- All PROVIDER-NOTES.md, SERVICE-SCOPE.md, SERVICE-NOTES.md

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- All prior TODOs resolved per user decisions on 2026-05-22.
- May this draft be promoted to "Approved"?
