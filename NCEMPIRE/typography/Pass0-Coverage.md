# System Coverage Review — typography

> **Separate-repo status (read first):** The authoritative typography build lives in a **separate repository** (built on its own track, per project memory: Typography/Theme/Colours are separate build tracks). This NCEMPIRE folder is **not** the build of record. This Pass 0 therefore assesses **only** whether the NCEMPIRE subsystem *decomposition* is sufficient and coherent as a representation/integration surface — it does not assess the external build, and any "build size" figures below are decomposition-shape estimates for that integration surface, not authority over the real implementation.

## System Role Summary

The typography system manages fonts, typography rules, and text styling so that text presentation stays consistent with brand. Within NCEMPIRE its job is to expose a coherent font + type-rule + apply-config surface that other systems (documents, document-templates, marks, renderers, brand) can depend on; the authoritative engine itself is built in a separate repo.

## Coverage Assessment

The system's stated intent decomposes into three responsibility clusters: **font asset management** (assets, pairings, loading, recommendations), **type-rule definition** (sizes, line heights, spacing, hierarchy), and **configuration management / application** (assembling and applying typography across content). The three declared subsystems map onto these clusters.

Note: all NCEMPIRE-side docs (System.md, Index.md, Notes.md, all subsystem Spec/Subsystem/Schema files) are **unpopulated templates**. The only substantive content is four one-line `*Module.md` purpose statements. Coverage is therefore assessed from the declared intent + subsystem names, not from written behaviour. This is acceptable for a separate-repo system at Pass 0 but is itself a coverage gap (see below).

### Covered Responsibilities

| Responsibility | Covered By |
|----------------|------------|
| Manage font assets and font pairings | FontManager |
| Font loading, fallbacks, availability | FontManager |
| Font recommendations | FontManager |
| Define typography rules (font sizes) | Typography |
| Define line heights and spacing | Typography |
| Text hierarchy / styling rules | Typography |
| Manage typography configurations | TypographyManager |
| Apply typography across content | TypographyManager |
| Coordinate across FontManager + Typography | TypographyManager |

### Partially Covered Responsibilities

- **Brand consistency enforcement** — the intent says "for brand consistency," but brand decisions belong to `brand/` (global constraint). Typography is the *consumer/applier* of brand type direction; whether it merely reads a brand contract or makes any type-selection judgement is unstated. The boundary is implied, not defined.
- **Font asset persistence / sourcing** — "manages font assets" is declared, but where font files live, how they are loaded in a Workers runtime, and licensing/availability constraints are unwritten (Repositorys.md is an empty template).
- **Recommendations vs. rules** — FontManager "recommendations" and Typography "rules" both touch selection; the line between a recommendation and an authoritative rule is not drawn.

### Uncovered Responsibilities

- **Failure / degradation behaviour** — no decision on what happens when a font is unavailable, a pairing is invalid, or a config cannot be applied. Per CLAUDE.md, failure *decisions* belong to `resilience/`, but typography must still declare its failure *surface*. Currently undeclared (all Error Handling tables are empty templates).
- **Versioning of type rules / configs** — Versioning sections are empty; how a changed rule set propagates to already-styled content is unaddressed.
- **Observability** — events/logs/metrics sections are empty templates.

(These are flagged as Pass-0 gaps, not defects; for a separate-repo system the authoritative answers may already exist externally.)

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| FontManager | Sufficient (distinct) | Font assets, pairings, loading, fallbacks, recommendations is a bounded, cohesive responsibility distinct from rule definition. | ~300–500 | Low |
| Typography | Sufficient *if* boundary vs. TypographyManager is drawn | Pure rule/value definitions (sizes, line heights, spacing, hierarchy) — small, declarative. Risk is not size but overlap (see below). | ~200–350 | Low |
| TypographyManager | Sufficient (distinct *role*), overlap to resolve | Config assembly + apply-across-content + coordination is a real orchestration job distinct from rule *definition*. But "manage typography configurations" vs. Typography's "define typography rules" needs an explicit owns/does-not-own line to avoid an artificial split. | ~300–500 | Low |

**On the Typography vs. TypographyManager question:** This reads as a legitimate **definition (data) vs. application/coordination (behaviour)** split — analogous to ColourToken (definitions) vs. a manager elsewhere — rather than an artificial one. It is defensible *provided* Pass 2 writes explicit owns/does-not-own. As currently documented (both empty), the split is *asserted but unproven*. Flag, do not drop, pending the owns/does-not-own line.

## Missing Capability Areas

- **No declared font-loading/runtime model** for Cloudflare Workers (font binary access, edge availability) — may live in the external repo, but the integration contract NCEMPIRE depends on is absent.
- **No declared failure surface** (font-unavailable, invalid pairing, apply-failure) for hand-off to `resilience/`.
- **No brand contract reference** — the dependency on `brand/` for authoritative type direction is implied by intent but not declared anywhere.
- (If these are all owned by the external build, the missing item collapses to one: **a documented integration/boundary contract between the external typography repo and NCEMPIRE consumers.**)

## Boundary & Classification Issues

- **marks/FontToPathConverter + marks/GlyphExtractor** — font-to-path / glyph extraction for **logos**. Correctly classified under `marks/`, not typography: these produce *vector outlines for marks*, a different output domain than *styled text for content*. Shared input (font files) is the only overlap; no boundary violation, but FontManager and these marks modules likely share font-asset access and should reference a single font-asset source to avoid divergent loaders.
- **documents/TypographyLayoutEngine** — *layout-time* typography (text flow, hyphenation, spacing in a document). Correctly outside typography: it *consumes* type rules and applies them at layout time. Boundary is clean **if** TypographyManager owns "apply config" at the style level and TypographyLayoutEngine owns "lay out flowed text" at the document level. Risk of overlap on "spacing" — needs one owner for which spacing decision lives where.
- **document-templates/TemplateTypographyRules** — *template-level* type rules. This is the sharpest overlap: it "defines typography rules, font selections, sizes, line heights, hierarchy" — nearly identical wording to typography's **Typography** subsystem. Must be resolved as **scope difference** (template-scoped overrides/instances vs. system-level canonical rules) and not duplicated rule logic. Flag for an explicit "who owns the canonical rule, who owns the template override" decision.
- **brand/ (global constraint)** — brand owns authoritative brand decisions; typography must read, not redefine, brand type direction. No violation observed, but the consuming relationship is undocumented.
- **renderers (logic-free)** — typography must not push styling *logic* into renderers; renderers receive resolved styles only. No violation observed; note as a constraint to honour at Pass 3.

## Overall Build Size Estimate

- **NCEMPIRE-side decomposition estimate:** ~800–1,350 LOC across three subsystems (integration/representation surface only).
- **Largest expected file:** FontManager or TypographyManager, ~300–500 LOC.
- **No file projected above ~500 LOC** → no High-risk (>1500 LOC) file from the current decomposition.
- **Caveat:** authoritative implementation lives in the separate repo; the real build size is governed there and is out of scope for this review.

## Risk Assessment

- **Overall risk level: Low.**
- No oversized-file (>1500 LOC) signal; three small, cohesive subsystems.
- Primary risks are **boundary/coherence**, not size: (1) Typography vs. TypographyManager split unproven until owns/does-not-own is written; (2) Typography vs. document-templates/TemplateTypographyRules near-duplicate rule wording; (3) entirely template-state documentation means coverage is inferred, not verified.
- **Separate-repo risk:** the authoritative build being external means NCEMPIRE could drift from it unless an integration/boundary contract is recorded; this is the main thing to watch, but it is a documentation/coherence risk, not a decomposition defect.

## Verdict

**CAN** — the three-subsystem decomposition is sufficient and coherent to represent the typography system's role at NCEMPIRE level, with **Low** size risk.

Conditions (none block the verdict; all are Pass 2 / Pass 3 documentation items, not redesigns):
1. Write explicit owns / does-not-own for **Typography** (canonical rule definition) vs. **TypographyManager** (config assembly + apply + coordination) to prove the split is real, not artificial.
2. Record the scope boundary with **document-templates/TemplateTypographyRules** (template-scoped override) vs. Typography (system canonical rule) — avoid duplicated rule logic.
3. Record the **brand/** consuming relationship (typography reads brand type direction; does not redefine it) and the **resilience/** hand-off for font-unavailable / apply-failure.
4. Capture the **separate-repo integration/boundary contract** so the NCEMPIRE folder cannot silently drift from the authoritative external build.

---

**Status:** Draft Complete – Awaiting Review
