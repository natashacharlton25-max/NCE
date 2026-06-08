# System Coverage Review — template

## System Role Summary
The `template` system manages content templates, outlines, and theme generation for structured content creation: it turns a brand-aligned theme (the WHAT) and a template structure (the HOW) into section-by-section outlines, then orchestrates AI section-filling and hand-off to renderers. It owns content templating (section-assembly instructions for generated content), not document layout/pagination (that is `document-templates`).

## Coverage Assessment

### Covered Responsibilities

| Responsibility | Covered By |
|----------------|------------|
| Generate themes from brand + audience (concept, message, angle, keywords, truths, strategic/transformation framework) | ThemeGenerator (mature spec, ~1869 LOC of markdown) |
| Theme CRUD, scoped/derived themes, query, FTS, usage/rating tracking | ThemeGenerator |
| Generate section-by-section outlines from theme + template (truth distribution, narrative arc, plan depth) | OutlineGenerator (mature spec, ~1438 LOC) |
| Outline CRUD, regenerate (full / per-section), drift snapshots, usage/rating tracking | OutlineGenerator |
| Execute templates → fill sections via AI → assemble in order → hand to renderer | TemplateEngine (mature spec, ~914 LOC) |
| Section-spec loading, context pass-through (brand/audience/topic), variants, parallel fill, partial-success policy | TemplateEngine |
| Create template instances / extract template structure from one-off output | TemplateFactory (placeholder only) |
| Template version storage, evolution tracking, rollback | TemplateLibraryManager (placeholder only) |

### Partially Covered Responsibilities
- **Template write/CRUD authority (create/update/delete/list/search).** TemplateFactory's overview claims ownership of *all* template write operations, but the architecture doc narrows it to `createFromOutput / detectSections / save`. Only a 29-line placeholder exists — the actual surface (does it own delete/update/list, or just creation?) is unspecified.
- **Template version archiving.** Split across two owners: TemplateFactory ("must archive previous version when sections added/removed/reordered") and TemplateLibraryManager ("store v1→v2→v3, archive old templates, enable rollback"). Ownership boundary between the two is undecided.
- **Template drift detection.** OutlineGenerator *consumes* drift signals (templateSnapshot: version/sectionCount/structureHash) and TemplateLibraryManager claims to *detect* drift, but no spec defines who computes/raises the drift signal authoritatively.
- **Template persistence location.** TemplateEngine reads from `/data/templates.db` (SQLite); the architecture doc shows TemplateFactory saving to `/repos/templates/*.json`. The store-of-record for templates is inconsistent across docs and unresolved.

### Uncovered Responsibilities
- **Template usage tracking** ("which content used this template?") is claimed by TemplateLibraryManager but has no spec, no schema, and no defined writer (compare OutlineGenerator/ThemeGenerator which each define `incrementUsage`).
- **Template definition schema / validation** — no subsystem defines the canonical template object shape or validates it on write. (Note: `document-templates/TemplateSchema` and `TemplateConstraintValidator` exist but serve document templating, a different boundary.)
- These are coverage *gaps in the spec record*, not necessarily missing subsystems — flagged for the human to decide, not resolved here.

## Subsystem Sufficiency Review

| Subsystem | Sufficiency | Reasoning | Est. LOC | Size Risk |
|-----------|-------------|-----------|----------|-----------|
| OutlineGenerator | Sufficient (spec mature) | Full public surface, drift snapshots, truth strategy, per-section regen. Spec already large; behaviour well bounded. | ~1300–1800 (TS, w/ verbosity multiplier on a 1438-line spec) | **High** (likely >1500 LOC; candidate for internal split: generation vs CRUD/query) |
| TemplateEngine | Sufficient (spec mature) | Clear orchestration role, logic-free renderer hand-off respected, parallel-fill + partial-success policy defined. Renderers are separate logic-free modules. | ~900–1400 | Medium–High (watch fill/assemble + variant routing growth) |
| TemplateFactory | Insufficient (placeholder) | 29-line stub. Write-authority scope contradicts itself across docs; version-archiving ownership unresolved; persistence target unclear. Function real, but slot under-specified. | ~300–600 (once scoped) | Low–Medium |
| TemplateLibraryManager | Insufficient (placeholder) | 34-line stub. Core claims (versioning, usage tracking, drift, rollback) overlap VersionManager and TemplateFactory. Needs a decision on whether it survives as a distinct subsystem. | ~300–700 (if retained) | Low–Medium |
| ThemeGenerator | Sufficient (spec mature) | Generates themes from brand+audience; correctly delegates brand storage to BrandManager (reads brand context, owns no brand decisions). Stays in `template` per user despite standalone `theme` repo. | ~1500–2200 (TS, from 1869-line spec) | **High** (likely >1500 LOC; largest expected file in the system) |

## Missing Capability Areas
- **Single defined owner for template versioning + rollback + usage tracking.** Currently claimed by both TemplateLibraryManager and `versioning/VersionManager`, with archiving also claimed by TemplateFactory. No spec resolves who is authoritative. (Capability is *claimed*, not *covered*.)
- **Authoritative template definition schema + write-validation.** No subsystem in `template` currently specs the canonical content-template object or validates it.
- (Both are surfaced as questions for human decision per PASS 0 rules — no resolution proposed here.)

## Boundary & Classification Issues
- **TemplateLibraryManager ↔ versioning/VersionManager (overlap — needs decision).** VersionManager explicitly "manages versions of templates... owns version graphs, compatibility rules"; TemplateLibraryManager claims "store versions, drift, rollback." Direct functional overlap. Both are placeholders, so neither is load-bearing yet — the human must decide whether template versioning lives in `template` (local) or `versioning` (global), or splits (e.g., generic version graph in `versioning`, template-semantic drift in `template`). Audit-style flag only; not resolved.
- **TemplateFactory ↔ TemplateLibraryManager (archiving overlap).** "Archive previous version on structure change" is claimed by both. Boundary undecided.
- **document-templates/ vs template/ (audit's Low-severity boundary — confirmed Low).** `document-templates` owns document layout/pagination/typography/visual-rules and has its own `TemplateSchema`, `TemplateRegistry`, `TemplateVersionManager`, `TemplateVariantRules`. Naming collision is high (both use "Template…"), but functional separation holds: `template` = content/section-assembly instructions; `document-templates` = rendered-document layout rules. Risk is naming confusion, not responsibility overlap. Note that `document-templates/TemplateVersionManager` is a *third* "template version manager" in the repo — naming hygiene flag.
- **brand/ (global constraint — respected).** ThemeGenerator derives themes from brand+audience but stores no brand data ("BrandManager does that") and defers brand-governed behaviour (e.g., `truthApprovalMode`, `reviewRequired`) to brand. Consistent with the global rule that brand decisions belong to `brand/`. No violation.
- **services/Librarian (template discovery — respected).** Librarian owns cross-library discovery/search/recommend and lists `templates` as a managed library with cascade rules ("templates: block archive if active outlines"). Template discovery correctly sits in `services/Librarian`, not in `template`. No overlap, but TemplateFactory's claimed `search`/`list` must be reconciled against Librarian's discovery ownership (flag).
- **renderers (logic-free — respected).** TemplateEngine selects/invokes renderers and hands off assembled sections; renderers only map section types to layouts. Logic-free renderer rule respected.
- **resilience/ (failure decisions — not yet exercised).** Mature specs reference error codes/partial-success policy locally; per global rule, final failure *decisions* (escalation/retry policy) belong to `resilience/`. Not a coverage gap at PASS 0, but a PASS 3 contract to honour.

## Overall Build Size Estimate
- Mature/large: ThemeGenerator ~1500–2200, OutlineGenerator ~1300–1800, TemplateEngine ~900–1400 (TS, applying the 1.3–1.5× verbosity multiplier to existing Python/JS-style specs).
- Placeholders (once scoped): TemplateFactory ~300–600, TemplateLibraryManager ~300–700 (if retained as a distinct subsystem).
- **System total (excluding renderers, which are separate logic-free modules): ~4300–6700 LOC.**
- **Largest expected file:** ThemeGenerator (likely >1500 LOC — High risk).

## Risk Assessment
- **Overall risk: Medium–High.**
- **Size risk (High):** two subsystems (ThemeGenerator, OutlineGenerator) are likely to exceed the 1500 LOC signal; both are split candidates (generation vs CRUD/query) at PASS 3. TemplateEngine is borderline.
- **Boundary risk (High):** template versioning is claimed by three places (TemplateLibraryManager, VersionManager, TemplateFactory). Unresolved at PASS 0; must be decided before specs are written.
- **Spec-completeness risk (Medium):** TemplateFactory and TemplateLibraryManager are placeholders; their real surface, persistence target, and store-of-record for templates are undecided.
- **Naming risk (Medium):** three distinct "template version manager"-flavoured concepts plus the template / document-templates collision invite cross-wiring.

## Verdict
**CAN** fulfil its role as structured — the five subsystems collectively cover theme → outline → template-execution, and three of them already have mature specs. Conditional on resolving, before PASS 1/2 proceed:
1. Decide the owner of template versioning / rollback / usage tracking (TemplateLibraryManager vs `versioning/VersionManager` vs split) — and whether TemplateLibraryManager survives as a distinct subsystem.
2. Resolve TemplateFactory's write-authority scope (full CRUD vs creation-only) and the template store-of-record (`/data/templates.db` vs `/repos/templates/*.json`), and reconcile its claimed `search`/`list` against `services/Librarian`.
3. Confirm the content-template vs document-template naming/boundary so the three "template version manager" concepts do not cross-wire.
4. Plan PASS 3 file-size splits for ThemeGenerator and OutlineGenerator (both >1500 LOC risk).

These are decisions for the human, not resolutions made here.

**Status:** Draft Complete – Awaiting Review
