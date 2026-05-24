# NCE-V2 Project Frame

---
Status: FRAME LOCKED
Version: v1.0.0
Stage: 1 of 5 (Reduced Methodology — see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Locked on: 2026-05-23
Reviewed by: Human (Natasha)
Drafted by: Claude
---

## In plain words

Stage 1 is a check, not a creation. Its job is to make sure the foundation documents NCE-V2 is built on are current, consistent, and approved before any system work begins. If the foundation is wrong, every later spec inherits the mistake.

This frame is now locked. The five foundation documents agree on what NCE-V2 is, what runs it, what's in it, and how it produces output. Stage 2 (writing the per-component specs) can begin.

If anything significant in those foundation documents changes — the stack, the system list, the output rules, the LOC band — Stage 1 must re-run before Stage 2 continues.

---

## What's in the frame

| Doc | Where | Locked content |
|---|---|---|
| Project-Intent.md | `NCE-V2/Project-Intent.md` | Why the project exists, design principles (now with technical substrate locked in) |
| FileTree-v2.md | `NCE-V2/FileTree-v2.md` | The 27 systems + `lib/` utilities |
| STACK-AND-RUNTIME.md | `NCE-V2/STACK-AND-RUNTIME.md` | The Cloudflare setup, configs, Worker grouping |
| REFINEMENTS.md | `NCE-V2/REFINEMENTS.md` | Historical record of design refinements (now archived as reference) |
| LIBRARY-TEMPLATE.md v2.0.0 | `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` | How each D1 content library is shaped |
| CLAUDE.md | `C:/Users/NCE/CLAUDE.md` | AI collaboration contract (with NCE-V2 supersession note for the methodology section) |

---

## Validation results

### Check 1 — Per-doc validity: PASS

| Doc | Status | Last updated | Owner |
|---|---|---|---|
| Project-Intent.md | Approved | 2026-05-23 | Human |
| FileTree-v2.md | Finalised | 2026-05-18 | Human |
| STACK-AND-RUNTIME.md | Approved (post-alignment) | 2026-05-23 | Human |
| REFINEMENTS.md | Historical (proposals adopted) | 2026-05-23 | — |
| LIBRARY-TEMPLATE.md v2.0.0 | Approved | 2026-05-18 | Human |

### Check 2 — Cross-doc consistency: PASS (after alignment)

| Check | Status |
|---|---|
| System count uniform (27 + lib/) | ✓ |
| Runtime + storage substrate stated (TS on Cloudflare Workers; D1/R2/KV/DO/Vectorize) | ✓ |
| Output-boundary rule stated (web = JSON via Astro; PDF/DOCX/email/marks = rendered by NCE-V2) | ✓ |
| LOC band uniform (2000 with TS verbosity + file exclusions) | ✓ |
| Library access via `library/Librarian` stated | ✓ |
| `lib/svg/` classified as utilities (not a Pass-target system) | ✓ |
| Methodology referenced consistently (5-stage Reduced; pass-based archived) | ✓ |

### Check 3 — Methodology readiness: PASS

| Resource | Status |
|---|---|
| `NCE-V2/methodology/` with Stage 1–5 prompts + templates | ✓ |
| `NCE-V2/admin/` directory | ✓ (this doc is its first content) |
| `NCE-V2/specs/<27 system folders>/` skeleton | ✓ |
| `NCE-V2/docs/templates/LIBRARY-TEMPLATE.md` v2.0.0 | ✓ |
| 68-phase methodology archived at `NCE-V2/archive/68-phase-methodology/` | ✓ |

---

## What changed to lock the frame

Stage 1 found inconsistencies and surfaced them honestly. To reach FRAME LOCKED, these were fixed:

1. **Project-Intent.md** — added a Status block; updated "Pass-based development" → "Stage-based development (5 stages)"; updated LOC `1500` → `2000` with file exclusions and TS verbosity multiplier; added explicit library-access rule and output-boundary rule; added a Technical Substrate section locking TypeScript + Cloudflare Workers + D1/R2/KV/DO/Vectorize + 27 systems + `platform` Worker grouping.

2. **STACK-AND-RUNTIME.md** — added a 2026-05-23 alignment banner at the top; replaced every "31 systems" with "27 systems"; replaced "1500 LOC" with "2000 LOC" plus exclusion patterns; ESLint rule values raised; Status promoted to Approved.

3. **REFINEMENTS.md** — added a banner at the top marking it as historical reference (its proposals have been adopted into FileTree-v2.md; subsystem counts in its before/after tables are stale).

4. **CLAUDE.md** — added an NCE-V2 supersession note in §3: the 5-stage Reduced Methodology supersedes the pass-based workflow for NCE-V2; the rest of CLAUDE.md still applies. Updated §4 LOC reference to 2000 for NCE-V2 (1500 retained as default for other projects in this workspace).

5. **No changes to FileTree-v2.md or LIBRARY-TEMPLATE.md v2.0.0** — they were already aligned.

---

## NCE-V2 locked constraints (recap — the things every later spec must respect)

- TypeScript on Cloudflare Workers; paid plan (5-min CPU, 128 MB memory per invocation)
- D1 for libraries (text + JSON only, accessed by Worker binding)
- R2 for binary assets (handled by `assets/` system)
- KV for cache/config, DO for stateful coordination, Vectorize for embeddings
- 27 top-level systems + `lib/svg/` utilities (per FileTree-v2.md)
- One Worker per system except `services`/`system`/`state`/`library` grouped into `platform` Worker
- Output-boundary: NCE-V2 owns content authoring + final rendered artefacts; Astro renders web
- Library access mediated by `library/Librarian` (no direct D1 from outside library/)
- 2000 LOC per runtime `.ts` file (with exclusions; TS verbosity 1.3–1.5×)
- 5-stage Reduced Methodology (`NCE-V2/methodology/`)
- 68-phase methodology archived; not for active use

---

## What's next

Stage 2 (Component Specs) can now begin. The next step produces one comprehensive spec document per system, per subsystem, per library, and per integration service — around 300 documents total. Each is approved individually before moving on. Stage 2 ends with a cross-cutting audit confirming no overlapping responsibilities, no circular dependencies, and no boundary violations.

The Stage 2 prompt + four templates are ready at `NCE-V2/methodology/`. To begin, run `STAGE-2-PROMPT.md` starting with the alphabetically-first system.

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| Frame author | Claude (Opus 4.7) | 2026-05-23 |
| Frame reviewer / approver | Human (Natasha) | 2026-05-23 |

---

## Notes

- This is the first artefact under the Reduced Methodology, produced at the close of Stage 1.
- Any future change to the foundation docs that affects the locked constraints above requires re-running Stage 1 before Stage 2 work continues — to confirm the frame still holds.
- The 68-phase methodology and its 200+ phase prompts remain at `NCE-V2/archive/68-phase-methodology/` as historical reference. They are not for active use.

---

## Frame Amendments (post-lock)

Amendments below are small, deliberate changes after FRAME LOCKED that do NOT require re-running Stage 1. Each is logged with date, reason, and impact.

### 2026-05-24 — PythonRunner removed from services/

| Field | Value |
|---|---|
| **Change** | Removed `services/PythonRunner.ts` from FileTree-v2.md + PLATFORM-WORKER-TEMPLATE.md module structure |
| **Reason** | OQ-PY-1 resolved. Was a "just in case we need Python" placeholder from the v1 Python-era architecture. Cloudflare Workers run V8 isolates — no in-Worker Python. The v1 placeholder doc (NCEMPIRE/services/PythonRunner/PythonRunner.md) was status "Placeholder", version 0.0.0, no specific task or library ever named. The runtime decision (TypeScript on Workers) implied this resolution; this amendment completes it. |
| **Future Python need** | If a Python-only library is ever genuinely needed, it must be an external service the Worker calls over HTTP (wrapper in `integrations/`, never inside the Worker). |
| **Subsystem count impact** | `services/`: 12 → 11. System count unchanged: 27. |
| **Files updated** | FileTree-v2.md (subsystem list + Open Flags + count summary); STACK-AND-RUNTIME.md (OQ-PY-1 marked resolved); PLATFORM-WORKER-TEMPLATE.md (module structure); PROJECT-FRAME.md (this amendment record) |
| **Frame still LOCKED?** | Yes. Small substantive amendment, does not invalidate Stage 1 validation. Sign-off: Human. |
