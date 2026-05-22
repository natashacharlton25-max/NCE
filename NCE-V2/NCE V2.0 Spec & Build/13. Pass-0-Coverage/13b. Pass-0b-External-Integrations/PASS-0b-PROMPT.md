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

1. **Project-Intent.md** — Project intention, design principles, non-goals
2. **CLAUDE.md** — AI collaboration contract; pass methodology; status vocabulary
3. **STACK-AND-RUNTIME.md** — Runtime, infrastructure, Worker boundaries
4. **REFINEMENTS.md** — Active architectural refinements
5. **FileTree-v2.md** — Canonical subsystem list (includes `integrations/` system with 15 subsystems)

> **TODO (Path resolution):** See 13a for the open path question. Same resolution applies here.

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

External providers (Google, Canva, Pexels, Unsplash, Recraft, Phosphor, Emailit, YouTube, etc.) sit on the *receiving* side of this boundary for some artefacts and the *sending* side for others:

- A provider that **receives a rendered artefact from NCE-V2** (e.g. Emailit receives email HTML; Canva/Google Docs receive rendered docs) is an outbound integration target. NCE-V2 rendering still applies before send.
- A provider that **supplies raw inputs to NCE-V2** (e.g. Pexels/Unsplash supplies images; Recraft supplies AI vectors; Phosphor supplies icons) is an inbound integration source. Assets land in the `assets/` system (R2) and metadata in the relevant D1 library.
- A provider that is **a downstream renderer** (Astro for web pages) receives JSON from NCE-V2 and renders it. NCE-V2 does NOT render web HTML.

Flag any provider/service review that gets the inbound/outbound side wrong.

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 13b as **ACTIVE**

### Step 2 — Execute PASS 0b in Strict Order

#### 2a. PROVIDER COVERAGE
- Process providers **alphabetically (A–Z)**
- For each provider, generate a Provider Coverage Review using **PROVIDER-COVERAGE-TEMPLATE.md** only
- Note: providers map to `integrations/<Provider>Integration.ts` subsystems in FileTree-v2.md

#### 2b. SERVICE COVERAGE
- For each provider (same order), process its services
- One coverage document per service using **SERVICE-COVERAGE-TEMPLATE.md** only

---

## AFTER EACH GROUP

- Pause for user approval
- If approved: update Status to APPROVED, update Version, Last Updated
- If blocked: record failure reason in `PASS-DECISION-NOTES.md`; do not proceed until resolved

---

## MANDATORY RULES

- Do **NOT** invent providers or services
- Do **NOT** propose fixes or designs
- Do **NOT** rename anything
- Do **NOT** make architectural decisions; flag observations only
- If information is missing, state it explicitly
- Treat file size as a **design signal**, not an optimisation target
- Apply the output-boundary rule when assessing provider/service direction
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../../CLAUDE.md) §7

---

## BUILD SIZE CONSTRAINT

- **Target:** ~2000 LOC per integration wrapper
- **Exceptions allowed** with explicit justification at Pass 0
- TypeScript is 1.3–1.5× more verbose than Python — apply when porting prior estimates
- Excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments

### File Size Risk Bands
- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC

---

## OUTPUT LOCATION

```
NCE-V2/specs/integrations/
├── PROVIDER-COVERAGE-{{provider}}.md
├── SERVICE-COVERAGE-{{provider}}-{{service}}.md
└── ...
```

> **TODO (Output destination):** Same as 13a — confirm `NCE-V2/specs/integrations/` vs parallel `NCE-V2/pass-0/`.

---

## SKIP CONDITION

If no external integrations are identified for NCE-V2:
- Skip PASS 0b entirely
- Mark as "N/A — No external integrations"
- Proceed directly to Phase 13c

For NCE-V2 this is unlikely — FileTree-v2.md `integrations/` system already lists 15 provider subsystems.

---

## END CONDITION

PASS 0b is COMPLETE only when:
- [ ] All provider coverage docs approved
- [ ] All service coverage docs approved
- [ ] PASS-PROGRESS.md updated: Phase 13b marked **COMPLETE**

**Next:** Proceed to Phase 13c (Pass 0 Checklist)

---

## PRE-SUBMISSION SELF-CHECK

- [ ] Did NOT invent any providers/services
- [ ] All providers from FileTree-v2.md `integrations/` were reviewed
- [ ] Output-boundary direction (inbound vs outbound) correctly identified per provider
- [ ] LOC estimates use ~2000 limit with TS verbosity multiplier
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")

---

## TEMPLATES

- [PROVIDER-COVERAGE-TEMPLATE.md](./PROVIDER-COVERAGE-TEMPLATE.md)
- [SERVICE-COVERAGE-TEMPLATE.md](./SERVICE-COVERAGE-TEMPLATE.md)

---

## INPUTS

From phases 9–11 (per B's chain):
- EXTERNAL-INTEGRATIONS-REGISTER.md
- All PROVIDER-NOTES.md
- All SERVICE-SCOPE.md, SERVICE-NOTES.md

> **TODO (Inputs from phases 9–11):** None of these exist yet for NCE-V2. For this project, the substitute input is `FileTree-v2.md` `integrations/` system list. Confirm.

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- Inbound/outbound framing of the output-boundary rule for integrations — does this match your model?
- Path resolution and inputs TODOs — same as 13a
- May I proceed with 13c, 13d, 14–17?
