# NCE-V2 Methodology

The 5-stage reduced methodology for NCE-V2. See `NCE-V2/docs/REDUCED-METHODOLOGY.md` for the full design rationale.

---

## Where to start

| If… | Open |
|---|---|
| First time here, no Stages done yet | `STAGE-1-PROMPT.md` |
| Stage 1 complete (frame locked) — `NCE-V2/admin/PROJECT-FRAME.md` exists | `STAGE-2-PROMPT.md` |
| Stage 2 complete (~300 component specs approved) | `STAGE-3-PROMPT.md` |
| Stage 3 complete (project-wide spec approved) | `STAGE-4-PROMPT.md` |
| Stage 4 complete (all Workers implemented + tests pass) | `STAGE-5-PROMPT.md` |

Check `NCE-V2/admin/PROJECT-FRAME.md` and other `NCE-V2/admin/*` tracking docs to see which stage is current.

---

## The 5 Stages

| Stage | Prompt | Produces | Templates used |
|---|---|---|---|
| 1. Project Frame | `STAGE-1-PROMPT.md` | `NCE-V2/admin/PROJECT-FRAME.md` (validation record; FRAME LOCKED verdict) | `PROJECT-FRAME-TEMPLATE.md` |
| 2. Component Specs | `STAGE-2-PROMPT.md` | ~300 docs: per-system SPEC.md + per-subsystem SPEC.md + per-library `.library.md` + per-integration-service SPEC.md | `SYSTEM-SPEC-TEMPLATE.md`, `SUBSYSTEM-SPEC-TEMPLATE.md`, `INTEGRATION-SERVICE-SPEC-TEMPLATE.md`, `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 |
| 3. Project-Wide Spec | `STAGE-3-PROMPT.md` | `NCE-V2/specs/PROJECT-SPEC.md` (consolidated cross-cutting concerns + Worker topology) + `NCE-V2/specs/platform/WORKER.md` (multi-system Worker rules) | `PROJECT-SPEC-TEMPLATE.md`, `PLATFORM-WORKER-TEMPLATE.md` |
| 4. Implementation | `STAGE-4-PROMPT.md` | Code + tests per Worker, in dependency-tier order (foundation first) | — |
| 5. Release | `STAGE-5-PROMPT.md` | CI/CD pipeline, audits, deployed production NCE-V2, operations handoff | `STAGE-5-TEMPLATES/` (4 sub-templates: CI-PIPELINE, AUDIT, DEPLOY-CHECKLIST, RELEASE-SUMMARY) |

---

## Folder contents

```
NCE-V2/methodology/
├── README.md                              ← you are here
├── STAGE-1-PROMPT.md                      ← validation pass
├── PROJECT-FRAME-TEMPLATE.md
│
├── STAGE-2-PROMPT.md                      ← the long stage (per-component specs)
├── SYSTEM-SPEC-TEMPLATE.md
├── SUBSYSTEM-SPEC-TEMPLATE.md
├── INTEGRATION-SERVICE-SPEC-TEMPLATE.md
│   (LIBRARY-TEMPLATE.md v2.0.0 lives at NCE-V2/docs/templates/)
│
├── STAGE-3-PROMPT.md                      ← project-wide consolidation
├── PROJECT-SPEC-TEMPLATE.md               ← §11 is the canonical Dependency Tier map
├── PLATFORM-WORKER-TEMPLATE.md
│
├── STAGE-4-PROMPT.md                      ← per-Worker implementation
│
├── STAGE-5-PROMPT.md                      ← release
└── STAGE-5-TEMPLATES/
    ├── CI-PIPELINE-TEMPLATE.md
    ├── AUDIT-TEMPLATE.md
    ├── DEPLOY-CHECKLIST-TEMPLATE.md
    └── RELEASE-SUMMARY-TEMPLATE.md
```

---

## Canonical source of truth — Dependency Tiers

`PROJECT-SPEC-TEMPLATE.md §11` defines the canonical tier list driving both Stage 2 spec order AND Stage 4 Worker build order:

- **Tier 1** — Foundation: `system`, `services`, `state`, `library`
- **Tier 2** — Cross-cutting infrastructure: `access`, `audit`, `observability`, `resilience`, `versioning`
- **Tier 3** — Data-owning systems: `ai`, `assets`, `brand`, `content`, `social`, `template`
- **Tier 4** — Production / output: `checks`, `document-templates`, `documents`, `email`, `marks`, `orchestration`, `publishing`, `renderers`, `review`, `verification`, `website`
- **Tier 5** — External integrations: `integrations` providers + services

Within a tier: alphabetical. Across tiers: sequential (each tier completes before the next begins).

Do not "tidy" this order back to alphabetical — every data-owning system's spec references `library/Librarian`, and downstream specs reference upstream contracts. Foundation-first is dependency-driven, not stylistic.

If §11 ever changes, every prompt that defers to it needs a sanity-check for stale references.

---

## NCE-V2 rules — apply throughout every stage

These appear in every prompt and template. Recapping in one place so the fresh session knows what's locked:

- **Output-boundary:** NCE-V2 emits JSON for web (Astro renders); NCE-V2 emits final rendered artefacts for PDF/DOCX/email/embedded marks.
- **Library access:** all reads via `library/Librarian`; only library write-owner module writes its library.
- **Storage:** D1 for libraries (text + JSON only), R2 for binaries (via `assets/`), KV for cache/config, DO for stateful coordination, Vectorize for embeddings.
- **2000 LOC band** per runtime `.ts` file. Excludes `*.debug.ts`, `*.test.ts`, `__debug__/`, `*.types.ts`, comments. TS verbosity 1.3–1.5× over Python.
- **TypeScript strict.** No `any` without justification. ESM only.
- **Worker secrets:** never in repo; always via `wrangler secret`.

Plus the universal contract from `CLAUDE.md`: no decisions without human approval; ambiguity = stop; status vocabulary; "Approved" is human-only.

---

## Related docs (outside this folder)

| Doc | Where | What for |
|---|---|---|
| Reduced Methodology design | `NCE-V2/docs/REDUCED-METHODOLOGY.md` | Why the 5 stages exist; full rationale |
| Stage 1 output | `NCE-V2/admin/PROJECT-FRAME.md` | What was validated; FRAME LOCKED verdict |
| Foundation docs | `NCE-V2/Project-Intent.md`, `FileTree-v2.md`, `STACK-AND-RUNTIME.md`, `REFINEMENTS.md` (historical) | The locked frame Stage 2+ build on |
| D1 library template | `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 | For Stage 2 library specs |
| 68-phase methodology (deleted 2026-05-24) | — (recoverable from git commit 678d2d6 / local copy) | Superseded by 5-stage Reduced Methodology; see `admin/PROJECT-FRAME.md` Frame Amendment |
| AI collaboration contract | `C:/Users/NCE/CLAUDE.md` | Universal rules; §3 supersession note explains methodology choice |
