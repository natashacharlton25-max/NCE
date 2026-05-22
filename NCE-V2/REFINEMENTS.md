# REFINEMENTS.md

**Status:** Draft Complete – Awaiting Review
**Type:** Architectural refinements identified after initial system scoping; not a Pass document
**Generated:** 2026-05-18
**Scope:** Four refinements to NCEMPIRE's architecture identified during stack work

---

## Purpose of this Document

During the stack and runtime work, four architectural observations emerged that refine — but do not invalidate — NCEMPIRE's existing architecture. They fall into three categories:

1. **Overlap with the Astro website platform** — systems originally scoped before the Astro platform took on broader scope. Where genuine duplication exists, those systems can be narrowed or partially deferred.
2. **Integration gaps** — specific external services (Recraft, Pexels, Unsplash) that weren't named in the original scoping but fit cleanly into the existing `integrations/` system.
3. **Factual grounding gap** — a missing capability for evidence-based content generation that fits into the `ai/` system as new subsystems.

Each refinement is documented below with the chosen action. Where the affected system's Pass 0 is complete, the refinement is an **amendment** for that Pass 0 owner to review. Where Pass 0 is outstanding, the refinement is a **gap flag** for the eventual Pass 0 to address.

This document is **not a decision-making artefact**. It records observations and the chosen action paths for each. Each affected system's Pass document remains the final authority on its own scope.

---

## Confirmed Decisions

These decisions shape the document's recommendations:

| Decision | Value |
|---|---|
| How refinements are actioned | Documented in this REFINEMENTS.md for review |
| Integration additions location | Existing `integrations/` system (Pass 0 done) |
| Factual grounding placement | New subsystems within `ai/` (Pass 0 done) |

---

# REFINEMENT 1: System Overlap with Astro Website Platform

## Context

NCEMPIRE was originally scoped to handle the full content production pipeline including some concerns the Astro website platform now owns. The Astro platform's confirmed boundary (set during stack work):

> NCEMPIRE ends at validated content in storage; the Astro website platform consumes separately.

This means concerns the Astro platform owns at the *delivery* layer should not be duplicated in NCEMPIRE.

## Systems with potential overlap

### 1.1 — `colours/` system

**NCEMPIRE subsystems:** ColourMaker, ColourMix, ColourToken

**Astro platform capability:** OKLCH-first colour engine, 114 themes, 1,852+ WCAG-audited variants, dual contrast auditing (WCAG 2.x + perceptual ΔE), CVD-safe variants, gamut-clamp hue preservation.

**Honest overlap assessment:**
- The Astro platform's theme engine is more sophisticated than `colours/` would otherwise need to be
- NCEMPIRE generates *brand intent* (colour decisions); Astro materialises it across the full theme matrix
- `ColourMaker` and `ColourMix` largely duplicate Astro's theme engine
- `ColourToken` could remain if NCEMPIRE needs to express token-level colour decisions for cross-system use

**Recommended narrowing:**
- Drop or significantly narrow `colours/` — keep only what's needed to express brand colour *intent* for Astro to consume
- Do not duplicate accessibility computation or theme generation

**Pass 0 status:** ✅ Done. **Action:** Amendment to colours Pass 0 for the owner's review.

---

### 1.2 — `typography/` system

**NCEMPIRE subsystems:** FontManager, Typography, TypographyManager

**Astro platform capability:** rem-based composable type system with multipliers, comfort settings, render-mode-aware sizing.

**Honest overlap assessment:**
- Web typography (display, scaling, comfort settings) is fully owned by Astro
- Document/PDF typography (metrics for `documents/`, `marks/`, mark generation) is *not* owned by Astro and remains valid in NCEMPIRE
- FontManager has dual purpose — font asset management for marks vs font config for web — only the second overlaps

**Recommended narrowing:**
- Keep `typography/` but narrow its scope to: font metrics for document layout, mark generation, type-on-path operations
- Drop or move web-typography concerns to Astro's responsibility

**Pass 0 status:** ☐ Outstanding. **Action:** Flag as a Pass 0 gap to address.

---

### 1.3 — `website/` system

**NCEMPIRE subsystems:** PageComposer, SectionBuilder, SEOManager, MetadataBuilder, WebConstraints, SitemapManager, WebsiteLifecycle, WebPersonalizer, WebAnalyticsAdapter

**Astro platform capability:** The Astro platform *is* the website system. JSON-as-page composition, render modes, brand templates, accessibility-by-construction.

**Honest overlap assessment:**
- This is the largest overlap in the architecture
- PageComposer and SectionBuilder duplicate Astro's composer
- SEOManager, MetadataBuilder, SitemapManager are Astro concerns (the page is built there, metadata belongs with the build)
- WebsiteLifecycle, WebPersonalizer, WebAnalyticsAdapter overlap entirely with Astro responsibilities

**Recommended narrowing:**
- Reconsider whether `website/` belongs in NCEMPIRE at all
- If retained, narrow to: writing validated content JSON to R2 in the shape Astro expects, plus any metadata Astro needs at build time
- Most of the listed subsystems are candidates for removal

**Pass 0 status:** ☐ Outstanding. **Action:** Flag as a Pass 0 gap; recommend Pass 0 considers significant reduction or removal.

---

### 1.4 — `visual/` system

**NCEMPIRE subsystems:** VisualBrand, VisualCheck, VisualManager, VisualValueAlignment

**Astro platform capability:** Platform validator runs accessibility and visual-rule checks at the render layer (contrast, comfort settings, CVD safety).

**Honest overlap assessment:**
- Visual-as-render-validation (does this page render accessibly?) is Astro's concern
- Visual-as-brand-decision (does this brand image express the brand?) is NCEMPIRE's concern
- VisualBrand stays; VisualCheck partially overlaps with Astro's validator; VisualManager and VisualValueAlignment need clarification

**Recommended narrowing:**
- Keep brand-decision concerns (VisualBrand, VisualValueAlignment as brand-alignment check)
- Narrow VisualCheck to brand-visual-rules only, not accessibility validation
- Clarify VisualManager scope vs the Astro platform validator

**Pass 0 status:** ☐ Outstanding. **Action:** Flag as a Pass 0 gap; recommend Pass 0 explicitly separates brand-decision concerns from render-validation concerns.

---

### 1.5 — `verification/` system

**NCEMPIRE subsystems:** AccessibilityValidator, ContentIntegrityValidator, SemanticCoherenceValidator, SignpostingValidator, ScopeValidator

**Astro platform capability:** 34-rule validator gates every change. Enforces accessibility-by-construction, banned patterns, structural correctness.

**Honest overlap assessment:**
- Accessibility-of-render (does the page meet contrast, comfort settings, semantic HTML correctness?) is Astro's concern
- Accessibility-of-content (reading level, alt-text intent, signposting correctness in the content shape) is NCEMPIRE's concern
- ContentIntegrityValidator, SemanticCoherenceValidator, SignpostingValidator, ScopeValidator validate content-level correctness — stay in NCEMPIRE
- AccessibilityValidator partially overlaps — Astro validates rendered output; NCEMPIRE should validate content-level accessibility intent (reading level, alt-text presence, structure-as-data)

**Recommended narrowing:**
- Keep most of `verification/` for content-level validation
- Narrow AccessibilityValidator to content-level concerns; let Astro own render-validation
- Both can coexist as different validation layers if scope is clear

**Pass 0 status:** ☐ Outstanding. **Action:** Flag as a Pass 0 gap; recommend Pass 0 explicitly states content-level vs render-level boundary.

---

## Summary of Refinement 1

| System | Recommended action | Pass 0 status | Action type |
|---|---|---|---|
| `colours/` | Significantly narrow or drop | ✅ Done | Amendment |
| `typography/` | Narrow to document/mark typography | ☐ Outstanding | Gap flag |
| `website/` | Consider removal; narrow heavily if retained | ☐ Outstanding | Gap flag |
| `visual/` | Separate brand-decision from render-validation | ☐ Outstanding | Gap flag |
| `verification/` | Narrow to content-level validation | ☐ Outstanding | Gap flag |

None of these are decisions this document makes. They are observations for the relevant Pass 0 owners.

---

# REFINEMENT 2: Recraft Integration for AI-Generated SVG

## Context

The `svg/` system contains programmatic SVG operations (SVGDrawer, SVGPathBuilder, SVGTransformEngine, SVGBooleanOps, SVGTextParser, SVGGeometryUtils, SVGOptimizer, SVGExporter). These are pure JS/TS operations used by `marks/` for logo composition, icon generation, and geometric construction.

**AI-generated SVG via Recraft is a different concern.** Recraft produces complex illustrated vectors via an external API. This was not explicitly scoped in the original architecture.

## Decision

**Add Recraft to the existing `integrations/` system** (Pass 0 done, verdict ✅ SAFE).

## Recommended subsystem addition

`integrations/RecraftIntegration`

**Responsibility:** API caller for Recraft. Handles auth, rate limits, error translation, attribution metadata.

**Owns:**
- Recraft API contract
- Recraft-specific auth and key handling
- Recraft response parsing and SVG normalisation
- Recraft attribution metadata (if required by their TOS)

**Does NOT own:**
- Decision to use Recraft (consuming systems decide)
- SVG manipulation post-retrieval (`svg/` system)
- Mark composition or layout (`marks/` system)

**Dependencies:**
- `services/APIKeyManager` (Recraft API key)
- `orchestration/RateLimiter` (Recraft API limits)
- `resilience/FailureClassifier` (API errors)
- `audit/AIUsageAttribution` (Recraft is paid AI usage)

**Consumed by:**
- `marks/` subsystems when complex illustrated vectors are needed
- `ai-image/AIVizCaller` could route Recraft calls if illustrated SVG output is wanted
- `documents/` if illustrated vector elements are required in documents

## SVG path summary after refinement

| Path | Purpose | Owned by |
|---|---|---|
| Programmatic SVG | Logos, icons, type-on-path, geometric construction | `svg/` + `marks/` |
| AI-generated SVG | Complex illustrated vectors | `integrations/RecraftIntegration` |

Both paths produce SVG output that downstream consumers handle uniformly.

## Pass 0 implication

`integrations/` Pass 0 is complete. The addition is an **amendment** to that Pass 0 for the owner to review. Estimated LOC addition: ~250–400 LOC for the integration adapter.

This brings `integrations/` subsystem count from 6 → 7.

---

# REFINEMENT 3: Stock Image Integrations (Pexels, Unsplash)

## Context

The `image/` system has ImageBrowser as a subsystem but its scope is ambiguous regarding sourcing. The architecture has `AIImageCreation` for AI-generated images and `ImageAssetLibrary` for catalogued assets, but no explicit handler for **free stock image APIs** (Pexels, Unsplash) that are commonly needed for therapeutic content where licensed stock photography is useful but budget is limited.

## Decision

**Add Pexels and Unsplash to the existing `integrations/` system** (Pass 0 done, verdict ✅ SAFE).

## Recommended subsystem additions

### `integrations/PexelsIntegration`

**Responsibility:** API caller for Pexels stock photo and video API.

**Owns:**
- Pexels API contract
- Pexels-specific search query construction
- Pexels response normalisation (consistent with Unsplash response shape for ImageBrowser)
- Pexels attribution metadata (their TOS requires attribution)

**Does NOT own:**
- Decision to use Pexels vs Unsplash vs AI (ImageBrowser decides)
- Image storage or caching (`image/` system)
- Image optimisation (`image/AssetOptimizer`)

**Dependencies:**
- `services/APIKeyManager`
- `orchestration/RateLimiter`
- `resilience/FailureClassifier`

**Free tier:** Pexels API is free; rate-limited at 200 requests/hour, 20,000 requests/month. No paid tier required.

### `integrations/UnsplashIntegration`

**Responsibility:** API caller for Unsplash stock photo API.

**Owns:**
- Unsplash API contract
- Unsplash-specific search query construction
- Unsplash response normalisation
- Unsplash attribution metadata and "download tracking" requirement (Unsplash TOS requires triggering a download event when an image is used)

**Free tier:** Unsplash API is free for non-commercial use at 50 requests/hour; production rate (5000/hour) requires approval but is also free.

## Updated image source flow

`image/ImageBrowser` becomes the unified frontend across four image sources:

```
ImageBrowser
   ├── AIImageCreation         (generated, paid)
   ├── PexelsIntegration       (stock, free, attribution required)
   ├── UnsplashIntegration     (stock, free, attribution + download tracking)
   └── ImageAssetLibrary       (uploaded brand assets, owned)
```

ImageBrowser:
- Presents a unified search across all sources
- Tracks which source any retrieved image came from (for attribution and licensing)
- Applies brand alignment filtering (e.g. excluding inappropriate stock results)

## Pass 0 implication

`integrations/` Pass 0 is complete. Two amendments for the integrations Pass 0 owner:
- `PexelsIntegration` (~200–300 LOC)
- `UnsplashIntegration` (~200–300 LOC)

Plus a follow-on consideration for `image/ImageBrowser`'s scope to ensure it can frontend the four sources cleanly. Image system Pass 0 is complete; this is an amendment for that Pass 0 owner.

`integrations/` subsystem count: 7 → 9 (with Recraft from Refinement 2 also added).

---

# REFINEMENT 4: Factual Grounding for Evidence-Based Content

## Context

The architecture does not currently address **factual grounding** — pulling verified facts into prompts to prevent the LLM from hallucinating statistics, citations, definitions, or claims.

For therapeutic content this is significant:
- Hallucinated statistics in mental health content is a real harm risk
- Therapeutic claims should be evidence-based with retrievable sources
- "The NHS recommends X for Y" needs to be true, not generated plausibly

The current architecture has AICaller, PromptBuilder, ContentValidator, but no layer that grounds prompts in verified external facts. ContentValidator can check output against known facts only if those facts are themselves retrievable and stored.

## Decision

**Add four new subsystems to the existing `ai/` system** (Pass 0 done).

## Recommended subsystem additions

### `ai/FactCaller`

**Responsibility:** Call free factual APIs and return structured fact data.

**Owns:**
- Factual API contracts (one adapter per source: NHS API, OpenAlex, Wikipedia, Wikidata, World Bank, etc.)
- Source-specific query construction
- Response normalisation to a shared fact schema (statement, source, citation, retrieval timestamp)
- API attribution metadata per source

**Does NOT own:**
- Decision about which facts to retrieve (consuming systems decide)
- Fact storage (`ai/FactCache`)
- Injection into prompts (`ai/FactInjector`)

**Dependencies:**
- `services/APIKeyManager` (for any APIs requiring keys; most factual APIs are key-free)
- `orchestration/RateLimiter` (per-source rate limits)
- `resilience/FailureClassifier`

**Free APIs initially supported:**

| API | Purpose for NCEMPIRE | Notes |
|---|---|---|
| Wikipedia REST API | Definitions, biographical facts, historical context | No key, generous rate limits |
| Wikidata SPARQL/REST | Structured facts (dates, relationships, statistics) | No key, free |
| NHS API (UK) | Health conditions, treatments, NHS-endorsed information | Free for non-commercial; perfect for Mind the Box's therapeutic content |
| OpenAlex | Academic citations, peer-reviewed evidence base | Free, no key required, generous rate limits |
| Crossref | Citation lookup, DOI resolution | Free, no key |
| World Bank Open Data | Country-level statistics | Free, no key |

Additional sources can be added via the same adapter pattern.

---

### `ai/FactCache`

**Responsibility:** Cache retrieved facts to prevent repeat API calls within and across content generation batches.

**Owns:**
- Cache key construction (source + query + retrieval-date)
- TTL policy per source (NHS info changes rarely; news-like sources expire faster)
- Cache invalidation on detected source updates
- Cache hit/miss telemetry

**Does NOT own:**
- Fact retrieval (`ai/FactCaller`)
- Cache infrastructure (uses `orchestration/CacheHandler` or KV directly)

**Dependencies:**
- `orchestration/CacheHandler` or direct KV binding
- `ai/FactCaller` (cache miss triggers retrieval)

**Storage:** Cloudflare KV with TTL. Cache keys structured for invalidation.

---

### `ai/FactInjector`

**Responsibility:** Inject retrieved facts into prompts at the appropriate position with appropriate attribution.

**Owns:**
- Fact injection format (how a fact is presented in a prompt: claim, source, citation)
- Fact prioritisation when multiple sources have relevant facts
- Token budget allocation for facts within prompt construction (interacts with `services/PromptBuilder` and `ai/TokenManager`)
- Fact placement in prompt structure (per the prompt's documented sections)

**Does NOT own:**
- Fact retrieval (`ai/FactCaller`)
- Prompt construction itself (`services/PromptBuilder` — FactInjector is called by PromptBuilder)
- Decision to inject facts (PromptBuilder + template decide)

**Dependencies:**
- `ai/FactCaller` (or `ai/FactCache`)
- `ai/TokenManager` (fact insertion must respect token budgets)
- `services/PromptBuilder` (FactInjector is invoked during prompt construction)

**Field-level metadata:** Following SCHEMA-TEMPLATE.md conventions, injected facts carry their own prompt role and token priority. Facts are **Context** (background info) or **Instruction** (explicit constraint, e.g. "the NHS states X — do not contradict this") depending on use.

---

### `ai/FactVerifier`

**Responsibility:** Post-generation, verify that any factual claims in output match retrieved facts; flag deviations.

**Owns:**
- Factual claim extraction from generated content (which claims are factual vs interpretive?)
- Comparison against retrieved facts (was the claim supported by the injected facts?)
- Drift detection (LLM said "30% of adults"; injected fact said "25%")
- Verification report generation

**Does NOT own:**
- Decision to retry or salvage (`checks/PostCreationQualityDecider` decides what to do with failures)
- Source authority (which sources are trusted is a brand/governance decision)

**Dependencies:**
- `ai/FactCache` (retrieve facts that were injected into this content's prompt)
- `content/ContentValidator` (FactVerifier is a specialised validator)
- `audit/DecisionTimeline` (verification outcomes are auditable)

---

## Pass 0 implication

`ai/` Pass 0 is complete (verdict ✅ CAN, ~3700–4900 LOC across 9 subsystems).

**Amendment proposed:** Add four subsystems (FactCaller, FactCache, FactInjector, FactVerifier).

**Estimated LOC addition:** ~1500–2500 LOC across the four subsystems.

**Updated ai/ subsystem count:** 9 → 13.

**Updated ai/ total LOC estimate:** ~5200–7400 LOC. Still well within the system's coverage verdict.

The amendment requires ai system Pass 0 owner's review and re-confirmation of the CAN verdict.

---

# Cross-Cutting Implications

## Updated subsystem counts

| System | Before | After | Change |
|---|---|---|---|
| `integrations/` | 7 | 10 | +Recraft, +Pexels, +Unsplash |
| `ai/` | 9 | 13 | +FactCaller, +FactCache, +FactInjector, +FactVerifier |
| `colours/` | 3 | 0–3 | Pending Pass 0 narrowing decision |
| `typography/` | 3 | TBD | Pending Pass 0 |
| `website/` | 9 | 0–3 | Pending Pass 0 narrowing decision |
| `visual/` | 4 | 2–4 | Pending Pass 0 narrowing |
| `verification/` | 5 | 4–5 | Pending Pass 0 narrowing |

Net change: addition of 7 well-bounded subsystems with clear contracts; potential removal of 10–20 subsystems where overlap with Astro warrants reduction. The architecture gets **smaller and more focused**, not larger.

## Implications for STACK-AND-RUNTIME.md

No structural changes required. The added subsystems fit the existing storage model, runtime, and Worker grouping. Specifically:

- **Recraft, Pexels, Unsplash** sit naturally in the `integrations` Worker
- **FactCaller, FactCache, FactInjector, FactVerifier** sit naturally in the `ai` Worker
- **Free factual APIs** add to the existing pattern of external HTTP calls; AI Gateway can front the AI-related ones
- **No new Cloudflare services required** beyond what STACK-AND-RUNTIME already specifies

## Implications for the Astro website project

The narrowing of NCEMPIRE's overlap with Astro **does not add work to Astro** — the Astro platform already does these things. It just confirms that NCEMPIRE should not duplicate them. The boundary set during stack work (NCEMPIRE ends at validated content in storage) is the determining contract.

## Implications for grant proposal

The factual grounding additions strengthen the grant proposal's evidence-based-content story:

- Therapeutic content is grounded in NHS-endorsed information and academic citations
- Hallucinated statistics are structurally prevented, not just reviewed-against
- Sources are auditable per piece of content
- Local-AI-via-LM-Studio combined with factual grounding means: private generation that's also evidence-based

This is a stronger position than either piece alone.

---

# Open Questions

| ID | Question | Owner |
|---|---|---|
| OQ-REF-1 | Final narrowing decision for `colours/` (amendment to existing Pass 0) | colours Pass 0 owner |
| OQ-REF-2 | Pass 0 scope for `typography/`, `website/`, `visual/`, `verification/` — should they explicitly address Astro overlap? | per-system Pass 0 |
| OQ-REF-3 | Source priority for factual APIs (which to call first for what kind of fact?) | ai Pass 1 |
| OQ-REF-4 | Fact-injection failure policy (if NHS API is down during generation, fall through to other source, fail, or proceed without?) | ai + resilience Pass 1 |
| OQ-REF-5 | Token budget allocation for facts within prompts (interacts with existing token-priority schema) | ai Pass 2 |
| OQ-REF-6 | NHS API non-commercial licensing — confirm CIC use qualifies | Governance / legal review |
| OQ-REF-7 | Unsplash production-rate approval — apply when needed or now? | Operational |

---

# What This Document Does Not Decide

To honour the CLAUDE.md contract:

- Final scope of `colours/`, `typography/`, `website/`, `visual/`, `verification/` (Pass 0 per system)
- Exact factual API source list (Pass 1)
- Fact-injection formatting and prompt-position rules (Pass 2)
- Cost ceilings for any new integration (CostBudgetManager / Pass 0)
- Licensing decisions for external APIs (governance + legal)
- Whether Recraft is the right AI-vector provider vs alternatives (Pass 1)

These remain Pass-managed concerns. This document records the observation and the action path.

---

# Summary

Four refinements identified during stack work:

1. **System overlap with Astro** — five systems (`colours/`, `typography/`, `website/`, `visual/`, `verification/`) have varying degrees of overlap with the Astro platform's responsibilities. Recommended actions range from significant narrowing to potential removal, deferred to per-system Pass 0.

2. **Recraft integration** — new subsystem `integrations/RecraftIntegration` for AI-generated illustrated vectors. Amendment to integrations Pass 0.

3. **Stock image integrations** — new subsystems `integrations/PexelsIntegration` and `integrations/UnsplashIntegration` for free stock photography sources. Amendment to integrations Pass 0.

4. **Factual grounding** — four new subsystems in `ai/` (FactCaller, FactCache, FactInjector, FactVerifier) to ground content in verified external facts. Amendment to ai Pass 0.

Net architectural effect: **smaller, more focused, evidence-stronger**. The architecture gains capabilities it needs and sheds duplication it doesn't.

---

### Review & Clarification Needed
- Are the five overlap observations (Refinement 1) accurate to how you see the boundary with Astro?
- Are the integration additions (Refinement 2 + 3) in the right system, with the right scope?
- Is the four-subsystem factual grounding structure (Refinement 4) the right shape, or would you prefer a different breakdown?
- Are there refinements observed but not captured in this document that should be added?
- Should this document be routed to the affected Pass 0 owners as amendments now, or held pending further review?

---

*Created: 2026-05-18*
*Type: Architectural refinements review document*
*Status: Draft Complete – Awaiting Review*
