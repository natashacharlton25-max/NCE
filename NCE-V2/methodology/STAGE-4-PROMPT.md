# STAGE 4: IMPLEMENTATION

---
Stage: 4 of 5
Name: Implementation (code + tests, per Worker)
Methodology: NCE-V2 Reduced Methodology (see `NCE-V2/docs/REDUCED-METHODOLOGY.md`)
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Implement NCE-V2 against the approved specs. Per-Worker work proceeds in dependency order (foundation first). Each Worker: scaffold → implement subsystems → write tests → verify → code review → merge.

This stage produces code, not docs. Specs from Stages 2–3 are the source of truth; this stage does not modify them.

---

## PREREQUISITE

Stage 3 must be COMPLETE with `PROJECT-SPEC.md` Status: APPROVED and verdict GO FOR IMPLEMENTATION.

---

## LOCKED CONTEXT (Required Reading per Worker)

Per [CLAUDE.md](../../CLAUDE.md) §10:

1. [Project-Intent.md](../Project-Intent.md)
2. `NCE-V2/specs/PROJECT-SPEC.md` (Stage 3)
3. `NCE-V2/specs/platform/WORKER.md` (Stage 3, only for platform implementation)
4. Per-Worker: `NCE-V2/specs/<system>/SPEC.md` + every subsystem's `SPEC.md` + library `.library.md` files
5. Per-Worker: `NCE-V2/specs/<system>/SPEC.md` Worker section
6. `NCE-V2/admin/PROJECT-FRAME.md` (Stage 1)

---

## TASK

### Per Worker (in dependency order from PROJECT-SPEC.md §11 Dependency Tiers → Stage 4 Worker Build Order)

For each Worker (starting with `platform`, then dependency-ordered):

1. **Scaffold**
   - Create per-Worker folder `src/<system>/` (or `src/platform/<grouped-systems>/`)
   - Initialise `wrangler/<worker>.toml` with bindings + secrets per spec
   - Create `src/<system>/index.ts` (Worker fetch handler entry)
   - Create test directory `tests/<system>/`

2. **Implement subsystems** (in spec order — alphabetical within system)
   For each subsystem in this Worker:
   - Implement per `SUBSYSTEM-SPEC.md` (sections 13 Public Functions + 14 Types + 12 Flow/Behaviour + 15 Errors + 16 Config)
   - TypeScript signatures match SPEC verbatim
   - Types extracted to `<Subsystem>.types.ts`
   - Apply CODING-STANDARDS (TS strict, Result types, no `any`)
   - Apply per-subsystem Rules / Non-negotiables (section 18)

3. **Wire bindings**
   - D1 access via `library/Librarian` for non-library Workers (service-bound to `platform`)
   - R2 access via `assets/` system for non-`assets/` Workers
   - resilience/ patterns for external integration calls
   - Worker secrets via `env.<NAME>` per spec

4. **Apply D1 migrations** (libraries first, before dependent Workers)
   - Per library: `wrangler d1 migrations apply <library>-{env}`
   - Verify with smoke query

5. **Write tests alongside implementation**
   - Unit tests for pure functions (vitest)
   - Integration tests for Worker fetch handlers + D1 query layer (vitest + miniflare)
   - Test happy path + each error code + edge cases per spec Examples (section 17)
   - Cover all spec Rules / Non-negotiables with at least one assertion each

6. **Verify per Worker**
   - `pnpm vitest run --project=<worker>` — all green
   - `wrangler dev --env=local` — Worker boots, smoke endpoint responds
   - Output Form check: JSON-emitters return JSON; renderers return final artefacts
   - Library access check: no direct D1 outside `platform/library/`

7. **Code review gate**
   - PR to `main`
   - CI must pass (typecheck, lint, unit, integration)
   - Human reviewer approves
   - Merge

8. **Log progress** in `NCE-V2/admin/IMPLEMENTATION-LOG.md`:
   - Worker name + commit SHA
   - Subsystems implemented
   - Tests passing
   - Any spec deviations (must be logged in `PASS-DECISION-NOTES.md` first)

---

## ORDER

Per PROJECT-SPEC.md §11 Dependency Tiers → Stage 4 Worker Build Order:

1. **M0 Foundation**: `platform` Worker first (contains Tier 1: services, system, state, library)
2. **M1 Cross-cutting infrastructure**: Tier 2 — `access`, `audit`, `observability`, `resilience`, `versioning` (each its own Worker)
3. **M2 Data-owning systems**: Tier 3 — `ai`, `assets`, `brand`, `content`, `social`, `template` (each its own Worker)
4. **M3 Production / output systems**: Tier 4 — `checks`, `document-templates`, `documents`, `email`, `marks`, `orchestration`, `publishing`, `renderers`, `review`, `verification`, `website` (each its own Worker)
5. **M4 Integrations**: Tier 5 — `integrations` providers + services

Within a tier, parallel-safe (deploy multiple Workers concurrently). Across tiers, sequential (each tier completes before the next begins).

Adjust per actual dependency map.

---

## MANDATORY RULES

- Do **NOT** deviate from specs without logged exception in `NCE-V2/admin/PASS-DECISION-NOTES.md`
- Do **NOT** introduce new public APIs not in `PROJECT-SPEC.md` API Surface
- Do **NOT** introduce new error codes not in `PROJECT-SPEC.md` Error Codes Registry
- Apply output-boundary rule: JSON-emitters never render HTML; renderers consume JSON, produce final artefacts
- Apply library access rule: all D1 reads via `platform/library/Librarian`
- TypeScript strict: no `any` without justified comment
- Tests alongside implementation (not after)
- ~2000 LOC band per runtime file; flag exceptions
- Do **NOT** self-assign code review approval — per [CLAUDE.md](../../CLAUDE.md) §7

---

## END CONDITION

Stage 4 is COMPLETE when:
- [ ] All 24 Workers implemented (platform + 23 own-Workers)
- [ ] All subsystems implemented per spec
- [ ] All D1 migrations applied across environments
- [ ] All tests pass (`pnpm vitest run` clean)
- [ ] All Workers boot locally (`wrangler dev`)
- [ ] All code reviewed and merged
- [ ] `IMPLEMENTATION-LOG.md` shows every Worker + every subsystem + every milestone
- [ ] Output-boundary audit clean (no JSON system emits HTML; no renderer takes non-JSON)
- [ ] Library access audit clean (no direct D1 from outside `platform/library/`)
- [ ] No flags in `PASS-DECISION-NOTES.md` left unaddressed

**Next:** Stage 5 (Release)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
