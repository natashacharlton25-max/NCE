# resilience — Admin Notes

## Ownership
- Primary owner: (human — TBD)
- Secondary reviewers: (TBD)

## Current Status
- Maturity: Draft (Pass 1 grounding)
- Implementation status: Not started (architecture phase)
- Documentation status: Pass 0 ✅ CAN; Pass 1 `System.md` drafted — **Awaiting Review**
- Test coverage: N/A

## Known Gaps
- Circuit-breaker subsystem is conceptually homed (DO) but not yet a named subsystem in the list — decide at Pass 2 whether it is its own subsystem or state owned by FailureClassifier/FailureHandler.
- ERROR-CODES level (1–4) ↔ severity (low/medium/high/critical) mapping must be written explicitly (clean 1:1 or documented) before Pass 2.
- **content/ has no salvage executor** — `ContentSalvageModule` is a phantom (Model-A, not carried into the 31; verified). `resilience/SalvageCoordinator` decides + coordinates, but content/ must **create** the executor at its Pass 1, or salvage dispatches to an empty target.

## Known Risks
- As a global constraint system, an unspecified boundary lets failure logic leak into system/, audit/, content/, review/, ai/. **All four cross-system boundaries are now ruled** (A, C this session; D restates the earlier provider-fallback ruling; B confirmed 2026-06-08 with the content-executor gap recorded).
- FailureHandler's prior content (`system/FailureHandler/FailureHandler.md`) over-claims ("owns retry/backoff for entire system"); re-grounded here as the decision-owning orchestrator. The doc itself is re-homed by the move but its *content* is re-scoped in Pass 2/3, not Pass 1.
- DO adoption is now stack-level; if not reflected in STACK-PROPOSAL, the stack record drifts.

## Open Questions
- (Boundaries A–D all ruled — see System.md. The only carried obligation: content/ creates its salvage executor at content/ Pass 1.)
- Circuit-breaker substrate confirmed as **Durable Object** (ruled) — open sub-question: one DO per provider/service, or one breaker DO with namespaced counters? (Pass 2.)

## Deferred Decisions
- Circuit-breaker DO design (granularity, counter windows) — Pass 2/3.
- Queue-retry parameterization detail (DEFAULT_RULES → Queue config) — Pass 3 mechanism.
- ERROR-CODES level↔severity mapping table — Pass 2.

## Related Systems
- `state/` (canonical state), `system/Logger` (records dispatch), `services/DatabaseHandler` (D1 persistence), `ai/` incl. ModelManager (dispatch + fallback chain), `audit/` (records boundary A), `content/` (salvage boundary B), `review/` + `orchestration/Notification` (handoff boundary C), `orchestration/` (Queues, RateLimiter — shares the DO primitive).

## Change Log
| Date | Change | Reason |
|------|--------|--------|
| 2026-06-08 | Pass 1 `System.md` + `Admin.md` drafted | Pass 0 CAN; opening Pass 1 on resilience first |
| 2026-06-08 | FailureHandler folded in as decision-owning orchestrating core | Pass 0 ruling (cleared system's CANNOT) |
| 2026-06-08 | Circuit-breaker: DO for live state, D1 for records; **Durable Objects adopted at stack level** | Ruling on stateful-coordination substrate (STACK-PROPOSAL §1.3) |
| 2026-06-08 | Severity made a *derived* property (FailureClassifier: code→level→severity) | Single-authority — remove caller-supplied severity field |
| 2026-06-08 | Boundaries A, C ruled; B, D spine-applied with inferred counterpart (awaiting confirm) | Cross-system ownership |
| 2026-06-08 | B & D flipped to **ruled** (B confirmed; D restates earlier provider-fallback ruling); verified `ContentSalvageModule` is a phantom → content/ executor to be created at content/ Pass 1 | User confirmation + phantom check |

**Status:** Draft Complete – Awaiting Review
