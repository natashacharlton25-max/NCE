# PHASE 14: PASS 1 — GROUNDING

---
Phase: 14
Pass: 1
Name: Grounding
Location: NCE-V2/NCE V2.0 Spec & Build/14. Pass-1-Grounding/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Supersedes (merged into this file):
  - C:/Users/NCE/prompts/PASS1PROMPT.MD (A's Pass-1 skeleton — content absorbed)
  - C:/Users/NCE/prompts/SYSTEMDOCPROMPT.MD (A's SYSTEM.md writing prompt — absorbed)
  - C:/Users/NCE/prompts/SUBSYSTEMPROMPT.MD (A's SUBSYSTEM.md writing prompt — absorbed)
  - C:/Users/NCE/prompts/ADMINDOCPROMPT.MD (A's ADMIN.md writing prompt — absorbed)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You are establishing clear boundaries and ownership for each NCE-V2 component.

This phase takes the *-NOTES.md files (from build phases) and the coverage reviews (from Pass 0) and produces formal grounding documents.

You are NOT designing implementation or writing code.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md) — Project intention, design principles, non-goals
2. [CLAUDE.md](../../../CLAUDE.md) — AI collaboration contract; pass methodology; status vocabulary
3. [STACK-AND-RUNTIME.md](../../STACK-AND-RUNTIME.md) — Runtime, Worker boundaries
4. [REFINEMENTS.md](../../REFINEMENTS.md) — Active architectural refinements
5. [FileTree-v2.md](../../FileTree-v2.md) — Canonical 27-system list
6. [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md) — D1-based library template (for library-owning systems)
7. Pass 0 coverage reviews under `NCE-V2/pass-0/<system>/`

---

## RUNTIME CONTEXT

- **Language:** TypeScript (strict mode)
- **Runtime:** Cloudflare Workers (V8 isolates)
- **Storage primitives:** D1, R2, KV, Durable Objects, Vectorize
- **Per-system organisation:** one Worker per system; `services`/`system`/`state`/`library` grouped into a `platform` Worker
- **External constraints:** Workers paid plan (5-min CPU max, 128 MB memory)

---

## OUTPUT-BOUNDARY RULE (CANONICAL)

> **NCE-V2 owns content authoring + the rendering of any artefact it itself ships to a final consumer.**
>
> - Web content shipped as JSON to Astro (web rendering is NOT NCE-V2's responsibility)
> - PDFs, DOCX, emails, embedded mark assets shipped in final rendered form (their rendering IS NCE-V2's responsibility)

Grounding documents (SYSTEM.md, SUBSYSTEM.md) must explicitly state:
- What output form the component produces (JSON / rendered artefact / library entry / metadata)
- Where the rendered/JSON form is consumed (internal system / Astro / external provider / human)

---

## STORAGE MODEL CONTEXT

Per [LIBRARY-TEMPLATE.md v2.0.0](../../docs/templates/LIBRARY-TEMPLATE.md):

- Libraries hold **text and JSON only** — stored in **D1**, accessed by Worker binding
- Binary assets handled by `assets/` system (R2)

For library-owning systems (`brand`, `content`, `ai`, `template`, `social`, `assets`, etc.), grounding must declare:
- Which library/libraries the system owns
- Which libraries it reads (via `library/Librarian`)
- The D1 binding name(s) for each owned library

---

## TASK

### Step 1 — Update Tracking
- Update `NCE-V2/admin/PASS-PROGRESS.md`: mark Phase 14 as **ACTIVE**

> **Logging rule:** Significant decisions go in `NCE-V2/admin/PASS-DECISION-NOTES.md` as they happen.

### Step 2 — Execute Grounding in Strict Order

#### 2a. SYSTEM GROUNDING
- Process systems **alphabetically (A–Z)** from FileTree-v2.md (27 systems)
- For each system, create:
  - **SYSTEM.md** — purpose, scope, non-goals, responsibilities, guarantees, boundaries, lifecycle, output form, library ownership
  - **ADMIN.md** — status, risks, open questions, deferred decisions, change log
- Use **SYSTEM-TEMPLATE.md** and **ADMIN-TEMPLATE.md**

#### 2b. SUBSYSTEM GROUNDING
- For each system (same A–Z order), process its subsystems
- For each subsystem, create:
  - **SUBSYSTEM.md** — what it owns within parent, owns/does-not-own, output form
  - **ADMIN.md** — status, risks, open questions
- Use **SUBSYSTEM-TEMPLATE.md** and **ADMIN-TEMPLATE.md**

#### 2c. EXTERNAL INTEGRATION GROUNDING
- For each provider (alphabetically), process its services
- For each service, create:
  - **EXTERNAL-INTEGRATION.md** — boundary with external service; inbound/outbound direction; credentials reference
  - **ADMIN.md** — status, vendor risks, credentials reference
- Use **EXTERNAL-INTEGRATION-TEMPLATE.md** and **ADMIN-TEMPLATE.md**

---

## WHAT GROUNDING DOCUMENTS ESTABLISH

| Question | Document Section |
|----------|------------------|
| Why does this exist? | Purpose |
| What does it own? | Scope / Owns |
| What does it NOT own? | Explicit Non-Goals / Does NOT Own |
| What can others rely on? | Guarantees |
| What must it never depend on? | Forbidden Dependencies |
| What output form does it produce? | Output Form (JSON / rendered / library entry / metadata) |
| Where does the output go? | Output Destination |
| Which libraries does it own? | Library Ownership (if applicable) |
| What are the known risks? | ADMIN.md — Risks |
| What's still unknown? | ADMIN.md — Open Questions |

---

## AFTER EACH GROUP

- Pause for user approval
- If approved: update Status to APPROVED, update Version, Last Updated, add significant decisions to `PASS-DECISION-NOTES.md`
- If blocked: log in `PASS-DECISION-NOTES.md`, consult `PASS-RESTART-RULES.md`

---

## MANDATORY RULES

- Do **NOT** invent new systems, subsystems, or integrations
- Do **NOT** add implementation details (no code, no schemas, no APIs)
- Do **NOT** contradict the *-NOTES.md files or Pass 0 coverage reviews
- If information is missing, state it explicitly in ADMIN.md and log it
- Use the templates exactly
- Apply the output-boundary rule when stating output form/destination
- Apply the storage model context when stating library ownership
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## BUILD SIZE CONSTRAINT

- **Target:** ~2000 LOC per runtime file (not enforced at grounding stage, but flag if grounding implies a file >2000 LOC)
- Excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments

### File Size Risk Bands
- **Low:** <1500 LOC
- **Medium:** 1500–2000 LOC
- **High:** >2000 LOC (design smell — flag and log)

---

## GROUNDING TEST

A component is properly grounded when:
> "Could another developer understand what this owns and doesn't own in 60 seconds?"

If no, the grounding is incomplete.

---

## OUTPUT LOCATION

```
NCE-V2/specs/{{system}}/
├── SYSTEM.md                          ← NEW
├── ADMIN.md                           ← NEW
└── {{subsystem}}/
    ├── SUBSYSTEM.md                   ← NEW
    └── ADMIN.md                       ← NEW

NCE-V2/specs/integrations/{{provider}}/{{service}}/
├── EXTERNAL-INTEGRATION.md            ← NEW
└── ADMIN.md                           ← NEW
```

Pass 1+ outputs live in `NCE-V2/specs/<system>/`. Pass 0 work stays in `NCE-V2/pass-0/`.

---

## SKIP CONDITION

**External Integrations:** If Pass 0b was N/A, skip section 2c.

---

## END CONDITION

PASS 1 is COMPLETE only when:
- [ ] All system SYSTEM.md + ADMIN.md created and approved (27 systems)
- [ ] All subsystem SUBSYSTEM.md + ADMIN.md created and approved
- [ ] All external integration EXTERNAL-INTEGRATION.md + ADMIN.md created and approved (or N/A)
- [ ] Every component's grounding states output form + destination
- [ ] Library-owning systems declare their library ownership and D1 binding names
- [ ] `PASS-PROGRESS.md` updated: Phase 14 marked **COMPLETE**
- [ ] Significant decisions logged in `PASS-DECISION-NOTES.md`

**Next:** Proceed to Phase 15 (Pass 2 — Contracts)

---

## PASS 1 CHECKLIST

### Prerequisite Check
- [ ] All Pass 0 coverage docs APPROVED
- [ ] `PASS-0-CHECKLIST.md` APPROVED

### Purpose & Scope
- [ ] System purpose stated in 1–2 sentences
- [ ] Scope lists what it owns, not how it works
- [ ] Explicit non-goals written
- [ ] Output form declared (JSON / rendered / library entry / metadata)

### Boundaries
- [ ] Allowed dependencies listed
- [ ] Forbidden dependencies explicitly stated
- [ ] Library ownership declared (if applicable, with D1 binding names)
- [ ] Output-boundary direction correct

### Maturity & Reality
- [ ] Status set (Concept / Draft / Active / Stable)
- [ ] Known risks noted
- [ ] Open questions parked (not solved)

### Sanity Test
- [ ] Could another developer understand what this owns in 60 seconds?

### DO NOT
- Define schemas
- Name error codes
- Design internals
- Add implementation details

---

## PRE-SUBMISSION SELF-CHECK

- [ ] Did NOT invent systems, subsystems, or integrations
- [ ] All Pass 0 outputs consulted
- [ ] Output form and destination stated for every component
- [ ] Library ownership declared where applicable
- [ ] Output-boundary rule applied
- [ ] Anything noteworthy logged in `PASS-DECISION-NOTES.md`
- [ ] Status marked "Draft Complete – Awaiting Review" (not "Approved")

---

## TEMPLATES (enriched with project-specific sections)

- [SYSTEM-TEMPLATE.md](./SYSTEM-TEMPLATE.md)
- [SUBSYSTEM-TEMPLATE.md](./SUBSYSTEM-TEMPLATE.md)
- [EXTERNAL-INTEGRATION-TEMPLATE.md](./EXTERNAL-INTEGRATION-TEMPLATE.md)
- [ADMIN-TEMPLATE.md](./ADMIN-TEMPLATE.md)

The templates carry sections for Output Form and Library Ownership — fill per the rules in this prompt.

---

## INPUTS

Build Phases 1–12 must run first to produce: All SYSTEM-NOTES.md, SUBSYSTEM-NOTES.md, PROVIDER-NOTES.md, SERVICE-NOTES.md
From Pass 0: All coverage reviews (under `NCE-V2/pass-0/`), `PASS-0-CHECKLIST.md`

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- All prior TODOs resolved per user decisions on 2026-05-22.
- May this draft be promoted to "Approved"?
