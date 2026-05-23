# STAGE 3: PROJECT-WIDE SPEC

---
Stage: 3 of 5
Name: Project-Wide Spec (cross-cutting concerns + Worker topology)
Methodology: NCE-V2 Reduced Methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Consolidate cross-cutting concerns from Stage 2 component specs into project-wide artefacts that lock the interfaces, error codes, schemas, conventions, and Worker topology before implementation.

This is **consolidation**, not invention. Inputs come from approved Stage 2 specs.

---

## PREREQUISITE

Stage 2 must be COMPLETE:
- All ~300 component specs approved
- `NCE-V2/admin/STAGE-2-AUDIT.md` shows PASS verdict

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../CLAUDE.md) §10:

1. [Project-Intent.md](../Project-Intent.md)
2. [STACK-AND-RUNTIME.md](../STACK-AND-RUNTIME.md)
3. [FileTree-v2.md](../FileTree-v2.md)
4. [LIBRARY-TEMPLATE.md v2.0.0](../docs/templates/LIBRARY-TEMPLATE.md)
5. All Stage 2 component specs under `NCE-V2/specs/`
6. `NCE-V2/admin/STAGE-2-AUDIT.md`

---

## TASK

Produce two artefacts:

### 3a. `NCE-V2/specs/PROJECT-SPEC.md`

Single project-wide doc using [PROJECT-SPEC-TEMPLATE.md](./PROJECT-SPEC-TEMPLATE.md). Eleven sections:

1. **API Surface** — every Worker fetch handler + service binding interface, consolidated from per-system SPEC FUNCTIONS sections
2. **Error Codes Registry** — every error code from every component spec; conflicts resolved here
3. **Project-Wide Schemas** — shared TS types (extracted from component TYPES sections); candidates for `src/lib/types/`
4. **Conventions** — TS style, async patterns, naming, file organization (consolidated; should already be locked in CODING-STANDARDS section of project intent)
5. **Security Standards** — Worker secret bindings, D1 query safety, R2 access patterns, input validation, output sanitisation, access/ system patterns
6. **Tech Stack (locked)** — concrete pinned choices
7. **Repo Structure** — concrete folder layout matching FileTree-v2 + lib/ + migrations/ + tests/
8. **Environment** — dev/staging/prod tiers; Worker secrets inventory; per-environment D1/R2/KV
9. **Dependency Map** — system-to-system flows; binding types per edge; no circular deps
10. **Worker Topology** — all 24 Workers (`platform` + 23 own-Workers); cross-Worker call graph; cold-start ordering; cross-Worker rules
11. **Implementation Plan** — canonical Dependency Tiers (drives both Stage 2 spec order AND Stage 4 Worker build order), Foundation Milestone, Subsequent Milestones (M0–M4), LOC Estimate Summary

### 3b. `NCE-V2/specs/platform/WORKER.md`

Multi-system Worker rules using [PLATFORM-WORKER-TEMPLATE.md](./PLATFORM-WORKER-TEMPLATE.md). Covers:

- Why these 4 systems share a Worker (foundation tier; in-Worker imports cheaper than service bindings)
- Module structure: `src/platform/services/`, `src/platform/system/`, `src/platform/state/`, `src/platform/library/`
- Shared D1 bindings (all library D1 databases bound here)
- Shared R2/KV/DO/Vectorize bindings
- Worker secrets shared
- Routes exposed by this Worker
- Cold-start considerations
- How the 4 systems coordinate without service bindings (in-Worker imports)

---

## MANDATORY RULES

- Do **NOT** invent new endpoints, types, error codes, or rules — only consolidate from Stage 2
- Resolve conflicts (e.g. two systems claiming same error code) by logging in `NCE-V2/admin/PASS-DECISION-NOTES.md` and choosing a canonical owner
- Apply output-boundary rule to API Surface (web endpoints emit JSON; rendered-output endpoints emit binary)
- Apply library access rule to Worker Topology (cross-Worker D1 access goes through platform Worker's library system)
- Apply 2000 LOC band to Implementation Plan
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../CLAUDE.md) §7

---

## END CONDITION

- [ ] `PROJECT-SPEC.md` complete (all 11 sections)
- [ ] `platform/WORKER.md` complete
- [ ] No unresolved conflicts
- [ ] Cross-cutting rules locked
- [ ] Human approves "GO to implementation"
- [ ] Status: Draft Complete – Awaiting Review

**Next (if GO):** Stage 4 (Implementation)

---

## TEMPLATES

- [PROJECT-SPEC-TEMPLATE.md](./PROJECT-SPEC-TEMPLATE.md)
- [PLATFORM-WORKER-TEMPLATE.md](./PLATFORM-WORKER-TEMPLATE.md)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
