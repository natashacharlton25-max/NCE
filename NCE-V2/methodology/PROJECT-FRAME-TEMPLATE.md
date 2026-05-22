# NCE-V2 Project Frame

---
Status: DRAFT | FRAME LOCKED | FRAME NEEDS WORK
Version: v0.1.0
Last Updated: {{timestamp}}
Owner: Human
Methodology Stage: 1 of 5
---

## Purpose

This document is the gate confirming NCE-V2's foundation is locked before any spec work begins. It validates three foundation docs and three cross-doc consistency rules.

If any check fails, this doc records the failure. The methodology pauses until the foundation is fixed.

---

## Foundation Docs

| Doc | Path | Status | Last Updated | Validity |
|---|---|---|---|---|
| Project-Intent.md | `NCE-V2/Project-Intent.md` | {{APPROVED}} | {{date}} | ☐ Pass / ☐ Fail |
| FileTree-v2.md | `NCE-V2/FileTree-v2.md` | {{APPROVED}} | {{date}} | ☐ Pass / ☐ Fail |
| STACK-AND-RUNTIME.md | `NCE-V2/STACK-AND-RUNTIME.md` | {{APPROVED}} | {{date}} | ☐ Pass / ☐ Fail |
| REFINEMENTS.md | `NCE-V2/REFINEMENTS.md` | {{APPROVED}} | {{date}} | ☐ Pass / ☐ Fail |
| LIBRARY-TEMPLATE.md v2.0.0 | `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` | {{APPROVED}} | {{date}} | ☐ Pass / ☐ Fail |

---

## Check 1 — Per-doc validity

| Doc | Exists | Approved | Current | Owner Human |
|---|---|---|---|---|
| Project-Intent.md | ☐ | ☐ | ☐ | ☐ |
| FileTree-v2.md | ☐ | ☐ | ☐ | ☐ |
| STACK-AND-RUNTIME.md | ☐ | ☐ | ☐ | ☐ |

---

## Check 2 — Cross-doc consistency

| Check | Pass / Fail | Notes |
|---|---|---|
| FileTree-v2 system list matches Project-Intent concept | ☐ Pass / ☐ Fail | |
| STACK-AND-RUNTIME primitives match FileTree-v2 assumptions (D1 for libraries, R2 for assets) | ☐ Pass / ☐ Fail | |
| Output-boundary rule (web=JSON via Astro; PDF/DOCX/email=rendered by NCE-V2) stated or implied | ☐ Pass / ☐ Fail | |
| 2000 LOC band stated in Project-Intent | ☐ Pass / ☐ Fail | |
| `library/` system access patterns match LIBRARY-TEMPLATE v2.0.0 | ☐ Pass / ☐ Fail | |
| `lib/svg/` classified as utilities (not a Pass 0 / spec target) | ☐ Pass / ☐ Fail | |

---

## Check 3 — Methodology readiness

| Resource | Present | Notes |
|---|---|---|
| `NCE-V2/methodology/` with Stage 1–5 prompts | ☐ | |
| `NCE-V2/admin/` directory | ☐ | |
| `NCE-V2/specs/<system>/` skeleton (27 folders) | ☐ | |
| `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 | ☐ | |

---

## Identified Risks / Gaps

| Risk | Source | Severity | Action |
|---|---|---|---|
| | | Low / Medium / High | |

---

## NCE-V2 Locked Constraints (recap — for the record)

- TypeScript on Cloudflare Workers (V8 isolates; paid plan; 5-min CPU, 128 MB memory)
- D1 for libraries (text + JSON only, accessed by Worker binding)
- R2 for binaries (assets/ system)
- KV for cache/config, DO for stateful coordination, Vectorize for embeddings
- One Worker per system; `services` + `system` + `state` + `library` grouped into `platform` Worker
- 27 systems + `lib/` utilities (per FileTree-v2.md)
- Output-boundary rule: NCE-V2 owns content authoring + final-rendered-artefact rendering; web rendering is Astro's
- Library access mediated by `library/Librarian`
- 2000 LOC band per runtime `.ts` file (excluding test/debug/types/comments)

---

## Verdict

**Status:** FRAME LOCKED | FRAME NEEDS WORK

If FRAME LOCKED → proceed to Stage 2 (Component Specs)
If FRAME NEEDS WORK → address the failing check(s); log in `NCE-V2/admin/PASS-DECISION-NOTES.md`; re-run Stage 1.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Project owner | | |

---

## Notes

(Any context, deferred items, or considerations.)

---
