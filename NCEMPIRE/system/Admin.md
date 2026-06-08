# system — Admin Notes

## Ownership
- Primary owner: (human — TBD)
- Secondary reviewers: (TBD)

## Current Status
- Maturity: Draft (Pass 1 grounding)
- Implementation status: Not started (architecture phase). Logger + Writer specced (Pass 2/3 approved); Archivist, Builders, GarbageCollector, Parsers at Pass 0.
- Documentation status: Pass 0 ✅ (verdict was CANNOT → **cleared 2026-06-08** by FailureHandler relocation + Builders mechanical-confirm); Pass 1 `System.md` drafted — **Awaiting Review**
- Test coverage: N/A

## Known Gaps
- **Retention-policy authority — RULED 2026-06-08 → config.** The policy *value* lives in config; `Archivist` + `GarbageCollector` enforce it. Closes the unsourced-policy hole. (A reactive "purge now" would be resilience *dispatching* to GC; the policy value still lives in config.)
- **`Builders.md` placeholder is mislabeled** — describes content-orchestration (documents/emails/social; calls TemplateEngine) instead of its real mechanical identity. Fix the description at Builders' Pass 2; the orchestration *concern* maps to the dropped content-pipeline and belongs in `orchestration/`.
- **`Parsers` depends on `sharp`** — a native Node binary that cannot run on Workers. Image parsing needs a Workers-compatible path (Cloudflare Images / WASM). Flagged, not redesigned.

## Known Risks
- Mechanical-only is the system's load-bearing invariant. Re-test it if `Writer.validatePath` ever consults dynamic/per-caller permissions (→ `access/PolicyEngine`), or if any subsystem accretes a policy it authors.
- (Retention-policy hole closed — ruled to config 2026-06-08.)

## Open Questions
- `Builders.md`: just fix the label to mechanical, or is the orchestration text a deliberate note pointing at a real `orchestration/` need?
- Does the document/email/social orchestration concern need a home in `orchestration/` (it overlaps the dropped content-pipeline modules)?
- **Parsers image processing — framing for its Pass 3 (where before how):** does image work run at *generation-time* (may not be a Workers concern at all → `sharp`-replacement question moot) or *serve-time on Workers* (→ Cloudflare Images native; Photon WASM fallback)? Answer *where* before choosing the replacement.

## Deferred Decisions
- Local-FS → R2/D1/KV platform remaps (Pass 3 mechanism) for Archivist, Builders, GarbageCollector. `sharp` replacement for Parsers — gated on the where-before-how framing (see Open Questions; Parsers Pass 3).
- Retention-policy authority — **ruled 2026-06-08: config** (no longer deferred).
- `Builders.md` correction (Pass 2).

## Related Systems
- `resilience/` (FailureHandler now lives here; candidate retention-policy authority), `access/PolicyEngine` (permissions vs Writer's static allowlist), `orchestration/` (home for the Builders orchestration concern), `content/` + schema/library authorities (Archivist validates against their schemas), storage R2/D1/KV.

## Change Log
| Date | Change | Reason |
|------|--------|--------|
| 2026-06-08 | Pass 1 `System.md` + `Admin.md` drafted | Opening Pass 1 on system (clears the second CANNOT-source) |
| 2026-06-08 | Mechanical-only audit of all 6 subs — **all stay**; Builders + GarbageCollector "move" verdicts overturned on adversarial verify | system's defining test = executes-never-decides |
| 2026-06-08 | Pass-0 CANNOT cleared | FailureHandler relocated to resilience/ + Builders confirmed mechanical (stay-fix-label) |
| 2026-06-08 | Retention-policy authority ruled → **config** (Archivist/GC enforce; reactive purge = resilience dispatches, policy value stays config) | Closes the unsourced-policy gap |

**Status:** Draft Complete – Awaiting Review
