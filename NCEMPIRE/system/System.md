# system

## Purpose
The core **mechanical infrastructure** layer — the executors. system/ constructs paths/folders, validates conformance, parses/transforms data, archives, cleans up, logs, and writes — strictly **on instruction**. Its defining test: **every module executes, never decides.** All judgment lives in the owning authority (resilience = failure, brand = brand, content = content, ai = model/provider, access = permissions, orchestration = job flow).

## Scope (What This System Owns)
*6 mechanical subsystems (FailureHandler relocated to `resilience/` 2026-06-08 — failure decisions are not here):*
- **Archivist** — validate incoming data against *externally-supplied* category schemas (conformance pass/fail), catalog metadata, sequence the write flow (Builders→Writer), execute parameter-driven temp cleanup. Decides nothing (paths→Builders, recovery→resilience).
- **Builders** — construct file/folder paths, IDs, and scaffolding; maintain the path registry; resolve paths on instruction against fixed structure templates.
- **GarbageCollector** — execute cleanup of temp/stale data by **enforcing** a retention policy supplied from elsewhere.
- **Logger** — record system events/errors (dual-write Workers Logs + D1, silent-degrade); records exactly what it is told. *(Already specced — Pass 2/3.)*
- **Parsers** — pure transforms: parse/normalize/conformance-check JSON, CSV, Markdown, AI responses, images. Data in → data out, no side effects.
- **Writer** — the **only** module that writes system data; writes exactly what it is given, atomically and safely. *(Already specced — Pass 2/3.)*

## Explicit Non-Goals
- **Makes no decisions** — the single defining non-goal. Any strategy choice, rule-authoring, policy ownership, or judgment belongs to the owning authority, never to system/.
- Does **not** decide paths (callers supply intent; Builders constructs per fixed patterns).
- Does **not** decide failure recovery (`resilience/`).
- Does **not** own retention policy — the policy *value* lives in **config**; GarbageCollector/Archivist *enforce* it (ruled 2026-06-08).
- Does **not** validate as judgment — conformance pass/fail only; schemas are external data it checks against.
- Does **not** own permissions (`access/PolicyEngine`); Writer enforces only a static path allowlist (defense-in-depth).
- Does **not** orchestrate content creation — the document/email/social orchestration text in the `Builders.md` stub belongs in `orchestration/`, not here.

## Core Responsibilities
- Execute construction / validation-conformance / transformation / archival / cleanup / logging / writing **on instruction**, deterministically.
- Be the **single mechanical layer**: one Writer (sole writer of system data), centralized logging, centralized path construction.
- Delegate every decision point to its owning authority and report outcomes without acting on judgment.

## System Guarantees
- Deterministic execution: identical instruction + input → identical output; no hidden state-driven choices.
- No system/ module owns a decision — every piece of "logic" present is a conformance check or a safety mechanism (atomic write, lock, allowlist), not an authority.
- Exactly one writer of system data (Writer); no other module writes.

## System Boundaries

### Allowed Dependencies
- Storage primitives (on Workers): **R2** (objects), **D1** (queryable/metadata/logs), **KV** (temp/lock state) — reached by Writer/Logger/Archivist/GarbageCollector as the persistence layer.
- Callers (any system) pass instructions; system/ executes them.

### Forbidden Dependencies
- Must not author policy, choose a strategy, or make a domain decision (that would mislabel the module — see FailureHandler precedent).
- Must not depend on the deciding systems in a way that pulls decision logic inward.

### Cross-System Boundary Notes (flagged for Pass 2)
- **Retention-policy authority (RULED 2026-06-08 → config):** the retention policy *value* (e.g. "temp 24h, exports 30d") lives in **config**; `Archivist` + `GarbageCollector` **enforce** it. resilience owns failure recovery, not data lifecycle, so it does not own retention. A *reactive* purge ("purge now" under storage pressure) would be resilience **dispatching** to GarbageCollector — but the policy value stays in config (the act "purge now" ≠ the policy "keep 24h").
- **Builders ↔ orchestration/:** the stray content-orchestration description in `Builders.md` (documents/emails/social; calls TemplateEngine) maps to the dropped content-pipeline orchestrators and belongs in `orchestration/`, not `system/Builders`.
- **Writer.validatePath ↔ access/PolicyEngine:** Writer enforces a **static** allowlist; dynamic per-caller permissions are PolicyEngine's. Re-test the mechanical-only verdict if validatePath ever grows dynamic.
- **Archivist validation ↔ schema/library authority:** schemas are external data Archivist checks against, not logic it owns.

## Lifecycle / Maturity
- Status: **Draft** (Pass 1 grounding)
- Expected evolution: Logger + Writer already through Pass 2/3; Archivist, Builders, GarbageCollector, Parsers carry Pass 0 → need Pass 2/3. Retention-policy authority is ruled (→ config); the one remaining boundary item — the `Builders.md` label — resolves at Builders' Pass 2.

## Notes
- **Mechanical-only audit (2026-06-08):** all 6 subsystems pass the executes-never-decides test. Two initial "move" verdicts — **Builders** and **GarbageCollector** — were **overturned on adversarial verify**: both *enforce/execute*, neither *owns* the decision. No evictions from system/.
- **Builders = stay (fix label):** canonical identity is path/folder/ID construction + scaffolding (Index.md, Pass0-Coverage, BUILDERS-PLAN all agree). The placeholder `Builders.md` over-claims content-orchestration — a mislabel to correct at Builders' Pass 2; the orchestration *concern* itself belongs in `orchestration/`.
- **system's Pass-0 CANNOT is cleared:** FailureHandler relocated to resilience/ + Builders confirmed mechanical → nothing in system/ decides.
- **Platform gaps (→ R2/D1/KV; flag-only, remap at Pass 3):** Archivist (`/temp`, catalog.json → D1), Builders (local-FS registry → R2 + D1/KV), GarbageCollector (`/temp`,`/exports` → R2 lifecycle / D1 TTL / KV expiry), Writer (POSIX/atomic-rename/locks — already remapped in its spec). **Sharpest: `Parsers` depends on `sharp`, a native Node binary that cannot run on Workers** → image parsing needs a Workers-compatible path (Cloudflare Images / WASM). Logger + Writer specs already remapped their gaps.

**Status:** Draft Complete – Awaiting Review
