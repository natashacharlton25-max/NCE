# STAGE 2: COMPONENT SPECS

---
Stage: 2 of 5
Name: Component Specs (per system + per subsystem + per library + per integration service)
Methodology: NCE-V2 Reduced Methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
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
