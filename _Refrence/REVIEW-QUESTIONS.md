# REVIEW-QUESTIONS.md

**Status:** Draft Complete – Awaiting Review
**Type:** Consolidated clarification questions across STACK-PROPOSAL, STACK-AND-RUNTIME, REFINEMENTS, and prior conversation threads
**Generated:** 2026-05-18
**Purpose:** Single source of decisions outstanding before architecture work continues

---

## How to use this document

Each question carries a stable code (e.g. `Q-INTENT-1`). Answer in-line below the question or via separate disposition note. Codes can be referenced in Pass docs and PR descriptions for traceability. Questions are organised by **decision owner** and **blocking dependency**, not by topic.

---

## Confirmed Issues (REFINEMENTS.md, accepted)

These are bugs/errors confirmed in [REFINEMENTS.md](REFINEMENTS.md) that REFINEMENTS v1.1 must address:

1. **Math errors.** §2 line 217 reads `integrations 6 → 7` — should be `7 → 8` (Recraft only adds 1). §3 line 291 reads `7 → 9` — should be `8 → 10` (adding Pexels + Unsplash to post-Recraft total of 8). Cross-cutting table at line 437 (`7 → 10` net) is correct.
2. **§1.4 internal contradiction.** `VisualValueAlignment` is described as "needs clarification" then "keep" within the same section. Resolve.
3. **Invented subsystem.** Line 406 references `checks/PostCreationQualityDecider`, which does not exist in [NCEMPIRE/checks/](NCEMPIRE/checks/). Per PASS0PROMPT.md ("Do NOT invent new subsystems"), this reference must be removed, reattached to an existing subsystem (e.g. `content/ContentValidator`), or surfaced as a gap flag for `checks/` Pass 0.
4. **ai/ LOC overshoot.** Pass 0 verdict CAN was given at ~3700–4900 LOC. Adding 1500–2500 LOC across 4 new subsystems is a 36–67% increase. This is not an amendment — it warrants Pass 0 re-run for ai/.
5. **Unverified Astro capability claims.** REFINEMENTS §1.1–1.5 treats specific Astro capabilities as established fact. See *Astro Claims Requiring Verification* below.

---

## Astro Claims Requiring Verification (blocks REFINEMENTS sticking)

Each item below is an assertion REFINEMENTS.md makes about the Astro platform. The overlap-analysis-and-narrowing recommendations in §1.1–1.5 depend on these being **current** state, not aspirational.

**Colour system (§1.1)**
1. OKLCH-first colour engine
2. 114 themes built
3. 1,852+ WCAG-audited theme variants
4. Dual contrast auditing (WCAG 2.x + perceptual ΔE)
5. CVD-safe variant generation
6. Gamut-clamp hue preservation

**Typography (§1.2)**
7. rem-based composable type system with multipliers
8. Comfort settings (per-user reading-comfort configuration)
9. Render-mode-aware sizing

**Website composition (§1.3)**
10. JSON-as-page composition system
11. Multiple render modes
12. Brand templates as build-time primitives
13. Accessibility-by-construction at component level

**Visual validator (§1.4)**
14. Render-layer validator running accessibility + visual-rule checks
15. CVD safety enforced at render

**Verification (§1.5)**
16. 34-rule validator that gates every change to the Astro repo
17. Banned-pattern enforcement
18. Structural-correctness enforcement

If any are aspirational, the narrowing recommendation for the affected system must be withdrawn or downgraded.

---

## Critical Path (revised)

```
Q-REFINE-5 (Astro state)
    │ if claims unverified, REFINEMENTS.md narrowing recommendations don't apply
    │ → fresh Pass 0 cycle proceeds against original scope, not refined scope
    ▼
Q-INTENT-1 (LOC flex)
    │ → determines PASS0PROMPT.md content + ESLint config
    ▼
Q-INTENT-3 (17-doc disposition)
    │ → required before clean-slate proceeds
    ▼
Q-CYCLE-1 (PASS0PROMPT.md v2)
    │ → substrate every new Pass 0 reads from
    ▼
Q-CYCLE-2 (FileTree regeneration)
    │ → second substrate input
    ▼
Q-CYCLE-3 (order of redo for the 31 Pass 0s)
    │
    ▼
Begin new Pass 0 cycle.
```

Everything else (Sections D–G) can run in parallel or after.

---

# Section A — Project-Intent / CLAUDE.md changes (block everything)

### Q-INTENT-1 — LOC-flex disposition
Project-Intent.md line 65 mandates ~1500 LOC. Earlier conversation: "we can flex on file size, and allow some exceptions, pass 0 could scope splitting." Pick:
- (a) Update Project-Intent.md to say "soft guidance, exceptions per-system on Pass 0 verdict"
- (b) Keep the cap but add an explicit exception clause
- (c) No change — Pass 0 reviewers judge case-by-case using existing "design signal, not optimisation target" phrasing

**Blocks:** ESLint config (STACK-AND-RUNTIME §4.3 / §5.9), all new Pass 0 verdicts, PASS0PROMPT.md update.

### Q-INTENT-2 — "Operational reference" precedence in CLAUDE.md §10
STACK-AND-RUNTIME.md and REFINEMENTS.md both call themselves "not a Pass document." CLAUDE.md §10's priority order has no slot for that. Pick:
- (a) Add §0 to both docs placing them below Pass artefacts
- (b) Add an explicit non-Pass tier to CLAUDE.md §10
- (c) Treat them as ephemeral working docs that get absorbed into Pass docs over time

**Blocks:** whether STACK-AND-RUNTIME and REFINEMENTS answers persist or are overridable by later Pass work.

### Q-INTENT-3 — Provenance of existing 17 Pass 0 docs after clean-slate
You chose "clean slate — redo all 31." Existing 17 files need disposition:
- (a) Delete
- (b) Archive to `legacy/pass-0-python-era/` *(your stated preference — preserves audit trail per Project-Intent.md's truthfulness-over-time principle)*
- (c) Overwrite in place (no history preserved)
- (d) Move to git tag/branch then delete from working tree

**Blocks:** file disposition step in Pass 0 cycle restart.

---

# Section B — Pass 0 cycle mechanics (block new Pass 0 work)

### Q-CYCLE-1 — PASS0PROMPT.md v2
[prompts/PASS0PROMPT.md](prompts/PASS0PROMPT.md) currently hardcodes `Target system: access` (line 6) and references the Python-era FileTree. New cycle needs:
- Parameterised target system
- TS-on-Workers context input
- Alignment with Q-INTENT-1 LOC stance
- Reference to a TS-aware tree (or note that `.py` extensions are legacy)

Draft prepared by me or by you?

### Q-CYCLE-2 — FileTree.md regeneration
Currently all `.py`. Pick:
- (a) Regenerate as `.ts` with current tree structure
- (b) Keep `.py` with a top-of-file note "legacy extensions — actual implementation is TS"
- (c) Strip extensions entirely (subsystem names only)
- (d) Restructure tree to reflect Worker boundaries (one folder per Worker, subsystems inside)

**Blocks:** the substrate every new Pass 0 reads from.

### Q-CYCLE-3 — Order of redo for the 31 Pass 0s
- (a) Original A–Z order
- (b) Foundational-first (services, system, state, then access, audit, ai, then dependents)
- (c) Highest-risk-first (access medium-risk first per prior verdicts)
- (d) Touch only what STACK or REFINEMENTS changes affect — this conflicts with clean-slate decision unless explicitly reconciled

### Q-CYCLE-4 — Pass 1/2/3 prompts
You only decided to clean-slate Pass 0. Pass 1/2/3 prompts likely carry Python-era assumptions. Touch now or after Pass 0 cycle completes?

---

# Section C — STACK-PROPOSAL.md v1.1

### Q-STACK-1 — PythonRunner disposition
Your earlier instruction ("keep intent, refresh to JS") resolves this to **Option C: absorb into TS equivalents**. Confirm? If yes, the doc records this rather than carrying OQ-PY-1 forward.

### Q-STACK-2 — Path forward for STACK-PROPOSAL.md
Given clean-slate cycle, the three options I originally offered (A/B/C) no longer fit. New options:
- (a) STACK-PROPOSAL.md becomes historical — superseded by STACK-AND-RUNTIME + REFINEMENTS *(your stated preference — archive, don't delete)*
- (b) STACK-PROPOSAL.md is revised to v1.1 reflecting post-conversation state
- (c) Delete entirely

### Q-STACK-3 — RateLimiter ownership
§1.3 says Durable Object; §2.4 says KV. Pick primary.

### Q-STACK-4 — Brand bundle size
26 subsystems / ~8500–11000 LOC in one Worker. Acceptable, or split into 8 group-Workers (alignment / audience / core / governance / identity / tracking / values / voice)?

### Q-STACK-5 — Brand isolation at storage layer
Single shared D1 with row-level brand keys, application-layer enforcement, or per-brand D1?

---

# Section D — STACK-AND-RUNTIME.md v1.1

### Q-RUNTIME-1 — `state/` location
§4.1 puts state inside `platform` Worker. §9.3 lists state as deploy step 2 (separate). Pick one.

### Q-RUNTIME-2 — Day-one queues
§3.6 says "day-one has general + audit-events queues." §10.2 omits queues. Pick one.

### Q-RUNTIME-3 — D1 name
§3.3 text says `ncempire-core`; §5.4 and §10.2 use `ncempire-registry-dev`. Pick one.

### Q-RUNTIME-4 — Service Binding direction in §5.4 example
Example shows `platform` binding outward to `AI_WORKER` and `BRAND_WORKER`. If platform is foundational, this is backwards. Fix the example or clarify intent.

### Q-RUNTIME-5 — `specs/` folder content type
§4.1 line 388 says "`.ts` spec deliverables." Existing methodology produces `SPEC.md`. Which?

### Q-RUNTIME-6 — Migration plan from current repo
Current files at `C:\Users\NCE\*`. Day-one scaffold is empty `ncempire/`. Pick:
- (a) Create `ncempire/` as new subdir alongside
- (b) Move everything in
- (c) Replace current root structure entirely

### Q-RUNTIME-7 — `/health` endpoint convention
Day-one Worker returns `{"status":"ok"}` from `/`. Add `/health` as separate endpoint, or accept conflation?

### Q-RUNTIME-8 — Service Binding pipe-test
Day-one is one Worker — no bindings tested. Add a "day-two" scaffold with a second Worker to prove bindings work end-to-end?

### Q-RUNTIME-9 — `vars` vs KV for feature flags
§7.1 lists feature flags as `vars` use case; §3.5 says KV. Pick one.

### Q-RUNTIME-10 — Pass-routing corrections
§3.2 routes Durable Object assignment to "Pass 1." §3.3 routes D1 splits to "Pass 0." §3.6 routes queue topology to "orchestration Pass 1." Per CLAUDE.md §3, these are Pass 3 (or Pass 2 for pre-specs). Accept corrections?

### Q-RUNTIME-11 — Close vs keep three "effectively-resolved" OQs
OQ-PY-1 (resolved by Q-STACK-1), OQ-DEV-2 (resolved by §8.3), OQ-COST-1 (resolved by §12.2). Close in v1.1 or keep deferred for safety?

### Q-RUNTIME-12 — Cost picture completeness
§12 shows £5–15/month Cloudflare. AI provider costs (Anthropic/OpenAI) are not included and are likely the **dominant** monthly cost. The £5–15 number is misleading without that context. Make explicit in §12, or accept as out-of-scope?

### Q-RUNTIME-13 — Missing operational items
Add or defer the seven I flagged: inbound auth, custom domain, Worker version tagging, alerting, R2 lifecycle, CF API token scope, migration plan (Q-RUNTIME-6 covers the last)?

### Q-RUNTIME-14 — System isolation enforcement
No rule preventing Worker A from importing Worker B's subsystems. Pick:
- (a) Lint rule
- (b) Convention only
- (c) Stricter (per-Worker tsconfig with restricted paths)

---

# Section E — REFINEMENTS.md v1.1

### Q-REFINE-1 — Math errors fix
See *Confirmed Issues* §1 above. §2 line 217 `6 → 7` → `7 → 8`. §3 line 291 `7 → 9` → `8 → 10`. Cross-cutting table correct as-is.

### Q-REFINE-2 — VisualValueAlignment status (§1.4)
"Needs clarification" then "keep" within same section. Pick one.

### Q-REFINE-3 — PostCreationQualityDecider reference (line 406)
Doesn't exist in [checks/](NCEMPIRE/checks/). Pick:
- (a) Replace with existing subsystem (e.g. `content/ContentValidator`)
- (b) Flag as gap for checks Pass 0 (new subsystem if needed)
- (c) Remove the reference

### Q-REFINE-4 — ai/ amendment vs Pass 0 re-run
Adding 4 subsystems + 1500–2500 LOC to ai/ is a 36–67% LOC increase. Pick:
- (a) Pass 0 amendment (current framing)
- (b) Pass 0 re-run because LOC envelope materially changes *(recommended)*
- (c) Defer to clean-slate cycle — ai/ is in the 31 being redone anyway, so this resolves naturally

### Q-REFINE-5 — Astro capability confirmation *(promoted to top blocker)*
See *Astro Claims Requiring Verification* above. Until those 18 claims are confirmed as current state, REFINEMENTS.md's overlap analysis in §1.1–1.5 is on uncertain ground.

**This question gates whether REFINEMENTS sticks in its current form.**

### Q-REFINE-6 — Brand-voice + factual-claim boundary
FactInjector inserts NHS-style claims; brand voice is therapeutic/warm. Who owns rephrasing claims into brand voice while preserving truth value? Not in any current subsystem.

### Q-REFINE-7 — Free API resilience policy
If NHS API is down during generation, fall through to other source, fail, or proceed without facts? Currently routed to "ai + resilience Pass 1" (OQ-REF-4). Confirm this isn't blocking before FactCaller is built.

### Q-REFINE-8 — Order of refinements vs clean-slate Pass 0 cycle
The 7 affected systems (`colours`, `typography`, `website`, `visual`, `verification`, `ai`, `integrations`) are all in the 31 being redone. Apply refinements:
- (a) Before fresh Pass 0 (so Pass 0 reviews refined scope)
- (b) After fresh Pass 0 (Pass 0 reviews original scope; refinement = amendment)

Compounds with Q-REFINE-5: if Astro claims unverified, this question becomes moot for §1.1–1.5 refinements.

---

# Section F — Per-Pass routing (non-blocking; address in their pass)

| Code | Question | Owner |
|---|---|---|
| Q-PASS-1 | Audit retention policy | audit Pass 0 (already flagged) |
| Q-PASS-2 | Audit boundary with `resilience/FailureAuditRecorder` | audit + resilience Pass 0 |
| Q-PASS-3 | Queue topology details | orchestration Pass 3 |
| Q-PASS-4 | Storage splits per library | per-system Pass 3 |
| Q-PASS-5 | Durable Object assignments per subsystem | per-system Pass 2/3 |
| Q-PASS-6 | D1 schema migration strategy at scale | Pass 2 |
| Q-PASS-7 | D1 backup / DR / RPO / RTO | services Pass 0 + ops |
| Q-PASS-8 | Secret rotation policy | services Pass 0 |
| Q-PASS-9 | Continuous background processes per subsystem | per-system Pass 0 |
| Q-PASS-10 | Cron Trigger limits per subsystem | per-system Pass 1 |
| Q-PASS-11 | Fact source priority + token budget | ai Pass 1 + Pass 2 |
| Q-PASS-12 | AI Gateway adoption | ai Pass 1 |
| Q-PASS-13 | **Local LLM routing for privacy-sensitive content.** Includes LM Studio + RX 9060 XT + Vulkan path discussed earlier in conversation as a viable privacy-preserving deployment. Combines with Refinement 4 factual grounding to make a stronger "private + evidence-based" position. Routing reaches Workers via Cloudflare Tunnel. | future / deferred (architectural placement: ai/AICaller as a provider) |
| Q-PASS-14 | **MCP server / tenant-supplied AI** (phase 3). Earlier conversation discussed letting tenants connect their own AI via MCP. Genuinely deferred — not blocking — but worth recording so the thread isn't lost. | future / deferred / phase 3 |

---

# Section G — Governance / Legal / External (outside engineering)

### Q-GOV-1 — NHS API non-commercial licensing for CIC use
REFINEMENTS OQ-REF-6. Blocks FactCaller's NHS adapter. Confirm with legal before building.

### Q-GOV-2 — Unsplash production-rate approval timing
REFINEMENTS OQ-REF-7. Apply now or when needed?

### Q-GOV-3 — Recraft attribution / TOS review
Their TOS may dictate metadata-passing requirements; needs confirmation before RecraftIntegration is built.

### Q-GOV-4 — Pexels attribution + Unsplash download-tracking obligations
Both have specific TOS requirements; needs operational ownership.

### Q-GOV-5 — Workers paid plan + AI provider budget approval
£5–15/month platform + AI provider costs (the larger line item). Budget owner confirmation needed before account upgrade.

---

## Dependency Map (revised)

```
Q-REFINE-5 (Astro state confirmation)
    │
    ├── claims confirmed current ──► REFINEMENTS narrowing applies; continue
    │
    └── claims aspirational/partial ──► REFINEMENTS §1.1–1.5 narrowing withdrawn
                                         │
                                         ▼
                                    fresh Pass 0 cycle uses original scope
                                         │
Q-INTENT-1 (LOC flex) ───┬──────────────┘
                         │
                         ├─► Q-RUNTIME (ESLint config)
                         │
                         └─► Q-CYCLE-1 (PASS0PROMPT)
                                │
                                ├─► Q-CYCLE-2 (FileTree regen)
                                ├─► Q-CYCLE-3 (Pass 0 order)
                                └─► All 31 new Pass 0s
                                     │
Q-INTENT-3 (17-doc disposition) ─────┘

Q-INTENT-2 (operational ref priority) ──► Q-RUNTIME-10, Q-REFINE-8 (precedence)

Q-STACK-1 (PythonRunner) ──► Q-RUNTIME-11 (close OQ-PY-1)

Q-GOV-1 (NHS licensing) ──► blocks FactCaller NHS adapter build (depends on Q-REFINE-5 first)
```

---

## Stated Preferences (preliminary, not final)

From conversation:
- **Q-INTENT-3:** lean toward (b) archive to `legacy/pass-0-python-era/`
- **Q-STACK-2:** lean toward (a) historical, superseded — but archive, not delete
- **Q-REFINE-4:** recommendation (b) Pass 0 re-run, but (c) defer to clean-slate may resolve naturally

These need explicit confirmation before being treated as decided.

---

### Review & Clarification Needed
- Are any of the 38+ questions missing from this consolidation, or wrongly categorised?
- Has the critical-path order been correctly revised with Q-REFINE-5 at the top?
- Are the Astro claims enumerated above complete, or are there others REFINEMENTS.md treats as fact that I missed?
- Should the "Stated Preferences" section be promoted to decided answers in v1.1, or stay preliminary?
- Is this document fit to be the standing reference for the next round of decisions?

---

*Created: 2026-05-18*
*Type: Consolidated review questions*
*Status: Draft Complete – Awaiting Review*
