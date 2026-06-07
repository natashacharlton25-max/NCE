# FileTree.ts.md — TypeScript / Cloudflare translation of FileTree.md

**Status:** Draft Complete – Awaiting Review
**Type:** Mechanical translation artefact (no design changes)
**Source:** `../FileTree.md` (≡ `../NCEMPIREDIRECTORY.md`)
**Generated:** 2026-06-07

---

## What this is

A 1:1 translation of the `.py` module tree in `FileTree.md` into TypeScript
files for Cloudflare Workers, scaffolded under `src/`.

**Translation rules applied (as instructed — no evaluation of the design):**

- Every `.py` module → a `.ts` file of the same name (PascalCase preserved).
- Directory structure preserved exactly, including `brand/`'s 8 subdirectories.
- Added one parent the platform requires: `src/` (Workers convention). The
  source tree's root `/` becomes `src/`.
- `.ts` files are created **empty** — there was no Python *logic* to translate,
  only the file/module structure. (Confirmed: 0 `.py` files exist in the repo;
  `PLATFORM-GAP-ANALYSIS.md` records the `.py` names as "outdated/aspirational".)

**Counts:** 236 `.py` names in source → 236 `.ts` files scaffolded. Diff verified
clean in both directions (no missing, no extra).

---

## Platform binding map

These bindings are **transcribed from `../STACK-PROPOSAL.md`** (which is itself
"Draft Complete – Awaiting Review"). They are not new decisions. Where
STACK-PROPOSAL names a specific subsystem, it is cited inline.

| Directory | Primary Cloudflare primitive(s) | Source in STACK-PROPOSAL |
|---|---|---|
| `access/` | Workers (stateless resolvers); brand-permission boundary feeds D1 partitioning | §2.1, §2.2 |
| `ai/` | Workers; `OptimizationLoop` flagged for long-running (OQ-RT-1) | §1.3, OQ-RT-1 |
| `ai-image/` | Workers + Workers AI / external image gen | §1.1 |
| `audit/` | **D1** (decision timeline, override ledger — durable, not logs) | §2.1, OQ-OBS-1 |
| `brand/**` | **D1** (brand/audience/voice records = tenant config) | §6 ("Tenant config lives in D1") |
| `checks/` | Workers (stateless validators) | §1.3 |
| `colours/` | Workers (pure transforms) | §1.3 |
| `content/` | Workers; `ContentLifecycleManager` flagged long-running (OQ-RT-1); artefacts → **R2** | OQ-RT-1, §2.1 |
| `website/` | Workers; output artefacts → **R2** (consumed by Astro, no egress) | §2.3 |
| `email/` | Workers + Queues for sequence/send; provider via external adapter | §3 |
| `social/` | Workers + Queues | §3 |
| `document-templates/` | Workers; template data → **D1** | §2.1 |
| `documents/` | Workers; multi-stage build = **Durable Object orchestrator + Queues** | §3.2 |
| `renderers/` | Workers; **PDFRenderer → Browser Rendering API** (paid); see §8.1 caveat | §8.1 |
| `marks/` | Workers (SVG/geometry compute, CPU-bound — Paid plan if >limits) | §1.1 |
| `svg/` | Workers (pure compute) | §1.1 |
| `image/` | Workers; assets → **R2** | §2.1 |
| `visual/` | Workers (validators) | §1.3 |
| `integrations/` | Workers; `WebhookReceiver` = fetch handler; external APIs | §1.1 |
| `observability/` | Workers Logs + **D1** for business metrics; optional Logpush→R2 | §5 |
| `orchestration/` | Workers + **Queues** + **Durable Objects**; `RateLimiter`/`FeatureFlags` → **KV** | §1.3, §2.4, §3 |
| `publishing/` | Workers + Queues; published artefacts → **R2** | §3 |
| `resilience/` | Workers; failure decisions own this domain (per CLAUDE.md §4) | §3 |
| `review/` | **Durable Object** (`ReviewQueue` = stateful per-reviewer) | §1.3 |
| `services/` | Mixed — see per-module notes below | §2, §6 |
| `state/` | Workers; per-job WIP → **Durable Object SQLite storage** | §2.1 |
| `template/` | Workers; template library → **D1** | §2.1 |
| `typography/` | Workers (font metrics compute) | §1.1 |
| `verification/` | Workers (stateless validators) | §1.3 |
| `versioning/` | **D1** (changelog/version records) | §2.1 |
| `system/` | Workers; mechanical primitives | (general) |

### `services/` per-module platform notes (from STACK-PROPOSAL)

| Module | Binding | Source |
|---|---|---|
| `APIKeyManager.ts` | **Workers Secrets** (never D1/KV) | §6 |
| `DatabaseHandler.ts` | **D1** client wrapper (async — not sync better-sqlite3) | §2.2 |
| `VectorStore.ts` | **Cloudflare Vectorize** recommended (OQ-STORE-2 open) | OQ-STORE-2 |
| `FileManager.ts` | **R2** with key-namespacing (replaces filesystem paths) | §2.1, OQ-STORE-1 |

---

## Translation notes / flags (non-binding — for your review)

1. **`services/PythonRunner.ts`** — The source has a `PythonRunner.py`. Its
   *name* translated mechanically to `PythonRunner.ts`, but a "Python runner"
   has no native equivalent on Workers (no Python subprocess in the runtime).
   This is a structural flag only — I did **not** rename it, remove it, or decide
   what it becomes. STACK-PROPOSAL's "Workers as much as possible, external as
   last resort" (§1) and §8 ("What Does Not Fit Workers Cleanly") are the right
   place for that call. Flagged, not resolved.

2. **`src/` root vs `packages/` layout** — This scaffold mirrors `FileTree.md`
   (module-internal layout). STACK-PROPOSAL §7.2 proposes a *deployment* layout
   (`packages/<system>/` monorepo). These are different concerns and not in
   conflict; the scaffold does **not** yet encode the `packages/` grouping.
   §7.2 itself says it "is a starting structure, not a decision" pending Pass 0.

3. **Empty files** — Every `.ts` is empty. No stubs, no `export {}`, no
   `class` skeletons were invented (that would be inventing design).

---
### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed, or should I revise this first?
---
