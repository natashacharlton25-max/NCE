# System Coverage Review — verification

## System Role Summary

The verification system performs higher-order, post-creation validation of content quality, coherence, and safety — confirming that finished content holds together as a whole and is safe to publish in a therapeutic/peer-support context. It sits above the mechanical, per-artifact checks system: where `checks/` asks "is each piece correct?" (spelling, grammar, layout, values), verification asks "does the whole piece cohere, stay in scope, and signpost safely?".

---

## Coverage Assessment

### Covered Responsibilities

| Responsibility | Covered By |
|----------------|------------|
| WCAG / accessibility compliance validation | AccessibilityValidator |
| Readability scoring | AccessibilityValidator |
| Alt-text presence and quality | AccessibilityValidator |
| Narrative coherence across sections | ContentIntegrityValidator |
| Contradiction / conflicting-guidance detection | ContentIntegrityValidator |
| Logical flow / transitions between sections | SemanticCoherenceValidator |
| Crisis signposting presence | SignpostingValidator |
| Professional-support / regional helpline reference checks | SignpostingValidator |
| Educational/peer-support scope enforcement | ScopeValidator |
| Clinical/diagnostic-advice language flagging | ScopeValidator |

### Partially Covered Responsibilities

- **Cross-section coherence as a single responsibility.** ContentIntegrityValidator ("coherence validation logic", "integrity scores") and SemanticCoherenceValidator ("flow validation logic", "coherence scoring") both claim coherence. The intended split (contradiction-detection vs logical-flow) is plausible but is not pinned down in either placeholder, so the boundary is only partially specified.
- **Verification result aggregation / pass-fail decision.** All five validators emit a local signal (score/flags). Nothing in the system aggregates the five into one verification verdict, or decides publish-blocking vs flag-for-review. This is the same gap the `checks/` Pass 0 flagged for itself (orchestration + quality-decision uncovered); for verification it is wholly uncovered (see below).
- **Therapeutic-safety severity / escalation.** Signposting and Scope are safety-critical, but whether a failed safety check is a hard block or an advisory flag is undefined. ScopeValidator explicitly states "flags only, does not block" — so safety enforcement is currently advisory by default, which may understate intent.

### Uncovered Responsibilities

- **Verification orchestration.** No subsystem runs the five validators, collects results, or sequences them. Every neighbouring validator in `checks/`, `content/`, `brand/governance/`, and `visual/` declares it "runs as part of PostCreationCheckManager", but the verification validators (dated 2026-01-21) make no such claim and PostCreationCheckManager has no folder/file anywhere in the repo. Verification therefore has no declared orchestration home.
- **Verification verdict / decision logic.** No equivalent of a "PostCreationQualityDecider" exists (the name appears only in the review brief, not in the repo). Deciding overall pass/fail/escalate from validator outputs is unassigned.
- **Failure routing.** Per CLAUDE.md §4, failure decisions belong to `resilience/`. How a verification failure (esp. a safety failure) is routed to resilience/escalation is unspecified.

---

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| AccessibilityValidator | Sufficient (scope query) | WCAG/readability/alt-text is bounded and well understood; likely leans on a library. But it is the most *mechanical* of the five and sits oddly against the system's "higher-order than mechanical checks" intent — possible misclassification vs `checks/`. | ~350–500 | Low |
| ContentIntegrityValidator | Partially sufficient | Contradiction detection across sections is genuinely higher-order and non-trivial (likely AI-assisted). Overlaps SemanticCoherenceValidator on "coherence"; needs a hard boundary before spec. | ~400–600 | Low–Med |
| SemanticCoherenceValidator | Partially sufficient | Logical-flow checking is real and distinct in principle, but its "coherence scoring" collides with ContentIntegrityValidator. Risk of two subsystems re-deriving the same signal. | ~350–550 | Low |
| SignpostingValidator | Sufficient | Crisis-signposting + regional helpline verification is a clear, bounded, safety-critical responsibility unique to this domain. Mostly rule/lookup based. | ~250–400 | Low |
| ScopeValidator | Sufficient | Clinical-language flagging / educational-scope enforcement is distinctive and clear. "Flags only" stance is explicit. Detection quality (clinical vs educational) may grow. | ~300–450 | Low |

No subsystem is projected near the 1500-LOC High-risk threshold; all are Low to Low–Medium.

---

## Missing Capability Areas

- **Verification orchestrator** — runs the five validators, gathers results. Unassigned; no home in `verification/`, and the assumed shared orchestrator (PostCreationCheckManager) does not exist in the repo. Impact: High (without it the system is five disconnected validators). Est. ~200–350 LOC.
- **Verification verdict / decision logic** — aggregates validator outputs into pass / flag / block, with safety checks weighted. Impact: High. Est. ~150–300 LOC.
- **Safety-failure escalation contract** — explicit routing of Signposting/Scope failures into `resilience/` per the global rule that failure decisions live there. Impact: Medium–High (safety-critical). Est. small, but a required cross-system contract.

---

## Boundary & Classification Issues

- **verification ↔ checks (primary).** The intended split — checks = mechanical/per-artifact (spelling, grammar, layout, values); verification = higher-order whole-content coherence + therapeutic safety — is defensible and the two should stay separate. BUT the orchestration story is shared and unbuilt: `checks/`'s own Pass 0 lists "check orchestration" and "quality decision logic" as missing, and every checker repo-wide points at the non-existent **PostCreationCheckManager**. Open question for review: does one orchestrator (PostCreationCheckManager) run BOTH checks and verification validators, or does verification get its own orchestrator? This must be decided before either system specs, or both will assume the other owns it.
- **AccessibilityValidator classification.** Accessibility/readability is more mechanical than "higher-order coherence/safety." It could arguably belong to `checks/` (which already houses LayoutCheck and a now-superseded LanguageCheck that claimed readability). Flag for review: confirm Accessibility belongs in verification and not checks. Note a readability double-home with the superseded `checks/LanguageCheck`.
- **Internal coherence overlap.** ContentIntegrityValidator vs SemanticCoherenceValidator both own "coherence scoring." Not a duplicate to merge blindly (contradiction-detection ≠ flow-analysis), but the line must be drawn explicitly. Candidate MERGE if the spec cannot cleanly separate them.
- **ScopeValidator ↔ brand values.** ScopeValidator enforces therapeutic *scope* (educational vs clinical-advice), a safety axis; `checks/ValuesCheck` and `brand/governance/BrandCheck` enforce *brand* values/voice. Different axes — keep separate — but both judge "is this content acceptable," so the responsibility statements should name the axis to avoid future drift. Per CLAUDE.md §4, brand decisions stay in `brand/`; verification must not re-decide brand values.
- **content/ overlap (adjacent).** `content/ContentValidator` (SUPERSEDED) and `content/ContentAlignmentCheck` already produce a "coherence score (0–1)". Verification's coherence validators must not duplicate content-system coherence scoring; clarify which layer owns coherence.

---

## Overall Build Size Estimate

- **Validators (5):** ~1650–2500 LOC combined.
- **Missing orchestration + decision (if homed here):** ~350–650 LOC.
- **Estimated total system LOC:** ~2000–3150 LOC.
- **Largest expected file:** ContentIntegrityValidator, ~400–600 LOC.
- **Overall size risk:** Low (no file approaches the 1500-LOC High-risk line; verbosity from TS noted but not threshold-breaching at subsystem level).

---

## Risk Assessment

- **Overall risk level: Medium.** Sizing is comfortably Low. The risk is structural/ownership, not size.
- The five validators are individually sound and the therapeutic-safety pair (Signposting/Scope) is a genuine, well-targeted strength of this domain.
- The dominant risks are: (1) no orchestrator or verdict owner for verification, compounded by reliance on a phantom (PostCreationCheckManager) that exists nowhere in the repo; (2) unresolved checks↔verification orchestration ownership; (3) internal coherence overlap; (4) safety-failure escalation not yet contracted to `resilience/`. Items (1), (2), and (4) are decisions, not Pass-0 fixes — surfaced here, not resolved.

---

## Verdict

**CAN** fulfil its role as a set of validators — the five subsystems cover the intended higher-order coherence and therapeutic-safety responsibilities, and sizing is safe. **CANNOT** yet operate as a coherent system without resolving the following conditions (decisions required from the dev team, not made here):

1. Decide where verification orchestration lives — a verification-owned orchestrator vs the shared (and currently non-existent) PostCreationCheckManager — and reconcile with the identical gap in `checks/`.
2. Assign the verification verdict/decision owner (aggregate → pass/flag/block).
3. Draw an explicit boundary between ContentIntegrityValidator and SemanticCoherenceValidator (coherence vs flow), and confirm neither duplicates `content/`'s coherence scoring.
4. Confirm AccessibilityValidator's placement in verification (vs checks).
5. Define the escalation contract for Signposting/Scope safety failures into `resilience/`, including whether safety checks block or only flag.

---

### Review & Clarification Needed
- Does this align with project intent and system boundaries?
- Are there any corrections or refinements required?
- May I proceed, or should I revise this first?

---

**Status:** Draft Complete – Awaiting Review
