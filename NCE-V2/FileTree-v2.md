# FileTree-v2.md

**Status:** Finalised
**Date:** 2026-05-18
**Supersedes:** FileTree.md (v1, Python-era)
**Basis:** ARCHITECTURE-DECISIONS.md (2026-05-18)

---

## Notes on This Version

- File extensions are `.ts` (TypeScript on Cloudflare Workers). v1's `.py` extensions are legacy.
- Changes from v1 are marked: **[NEW]**, **[MOVED]**, **[REMOVED]**, **[ABSORBED]**.
- Three v1 systems collapsed into `brand/`: `colours/`, `typography/`, `visual/`.
- Two v1 systems collapsed into `assets/`: `image/`, `ai-image/`.
- `svg/` became library utilities (`lib/svg/`), not a top-level system.
- **`library/` added as a system** — the data-access layer (Librarian, FileManager, Writer, Archivist, CacheHandler), previously scattered across `services/`, `system/`, and `orchestration/`. Confirmed against SPEC-BUILDING-RULES.md's data-management layer.
- **Storage model:** library data lives in D1 (SQLite, accessed by binding — not by `.sqlite` file paths) and R2 (objects). The uploaded LIBRARY-TEMPLATE.md / REPOS-TEMPLATE.md schemas carry over to D1; their filesystem framing (`/data/*.sqlite`, `/repos/`, filename lookups) is superseded. FileManager and Writer keep their names for continuity; their specs are reframed to D1+R2 internally.
- This is the system/subsystem tree only. Worker grouping, repo layout, and config files are in STACK-AND-RUNTIME.md.

---

## System Count

**v1:** 31 systems
**v2:** 27 systems + `lib/` utilities

- Removed as top-level systems: `colours/`, `typography/`, `visual/`, `image/`, `ai-image/`, `svg/` (6 removed).
- Added as top-level systems: `assets/`, `library/` (2 added).
- Net: 31 − 6 + 2 = 27 systems, plus `lib/` holds former `svg/`.

---

## The Tree

```
/
├─ access/
│  ├─ BrandPermissionResolver.ts
│  ├─ CapabilityManager.ts
│  └─ RoleManager.ts
│
├─ ai/
│  ├─ AICaller.ts
│  ├─ AISystemManager.ts
│  ├─ AIWebSearch.ts
│  ├─ CostBudgetManager.ts
│  ├─ CostTracker.ts
│  ├─ EmbeddingService.ts
│  ├─ ModelManager.ts
│  ├─ OptimizationLoop.ts
│  ├─ TokenManager.ts
│  ├─ ImageGeneration.ts        # [NEW] absorbed from ai-image; AI raster/vector generation
│  ├─ FactCaller.ts             # [NEW] factual grounding — calls free factual APIs
│  ├─ FactCache.ts              # [NEW] factual grounding — caches retrieved facts
│  ├─ FactInjector.ts           # [NEW] factual grounding — injects facts into prompts
│  └─ FactVerifier.ts           # [NEW] factual grounding — verifies output against facts
│
├─ assets/                       # [NEW SYSTEM] replaces image/ + ai-image/
│  ├─ AssetCatalog.ts            # registry of all assets (acquired, generated, uploaded)
│  ├─ AssetBrowser.ts            # unified search/select across all asset sources
│  ├─ AssetMetadata.ts           # source, license, attribution, dimensions, format
│  ├─ AssetSelector.ts           # given a brief, choose source and acquire
│  ├─ AssetOptimizer.ts          # [MOVED] from image/
│  ├─ AssetCollections.ts        # [MOVED] from image/ (was ImageCollections)
│  └─ AssetTemplate.ts           # [MOVED] from image/ (was ImageTemplate)
│
├─ audit/
│  ├─ AIUsageAttribution.ts
│  ├─ DecisionTimeline.ts
│  └─ HumanOverrideLedger.ts
│
├─ brand/
│  ├─ core/
│  │  ├─ BrandManager.ts
│  │  ├─ BrandVersionManager.ts
│  │  ├─ BrandConfidenceTracker.ts
│  │  └─ BrandStateResolver.ts
│  │
│  ├─ identity/
│  │  ├─ BrandIdentityResolver.ts
│  │  ├─ BrandDifferentiationEngine.ts
│  │  └─ BrandNarrativeEngine.ts
│  │
│  ├─ audience/
│  │  ├─ AudienceManager.ts
│  │  ├─ AudienceSegmentResolver.ts
│  │  ├─ PersonaSynthesiser.ts
│  │  └─ AudienceConfidenceScorer.ts
│  │
│  ├─ voice/
│  │  ├─ BrandVoiceManager.ts
│  │  ├─ VocabularyGovernor.ts
│  │  └─ ToneVariationEngine.ts
│  │
│  ├─ values/
│  │  ├─ BrandValuesManager.ts
│  │  ├─ EthicalBoundaryResolver.ts
│  │  └─ DecisionPrinciplesEngine.ts
│  │
│  ├─ governance/
│  │  ├─ BrandRulesEngine.ts
│  │  ├─ BrandCheck.ts
│  │  └─ BrandExceptionHandler.ts
│  │
│  ├─ alignment/
│  │  ├─ BrandAlignmentEngine.ts
│  │  ├─ BrandDriftDetector.ts
│  │  └─ BrandConsistencyScorer.ts
│  │
│  ├─ tracking/
│  │  ├─ BrandTracker.ts
│  │  ├─ BrandChangeLog.ts
│  │  └─ BrandImpactAnalyzer.ts
│  │
│  ├─ colour/                    # [ABSORBED] from colours/ — colour intent, not a colour engine
│  │  ├─ ColourIntentResolver.ts # brand colour decisions as intent (was ColourMaker)
│  │  └─ ColourTokenDefiner.ts   # named colour tokens for JSON reference (was ColourToken)
│  │
│  ├─ typography/                # [ABSORBED] from typography/ — typography intent
│  │  ├─ TypographyIntentResolver.ts  # brand type decisions as intent
│  │  └─ FontIntentManager.ts    # font choices as brand config (was FontManager, narrowed)
│  │
│  └─ visual/                    # [ABSORBED] from visual/ — visual-as-brand-decision only
│     ├─ VisualBrand.ts          # [MOVED] from visual/
│     └─ VisualValueAlignment.ts # [MOVED] from visual/
│
├─ checks/
│  ├─ GrammarCheck.ts
│  ├─ LanguageCheck.ts
│  ├─ LayoutCheck.ts
│  ├─ SpellGrammarCheck.ts
│  ├─ SpellingCheck.ts
│  ├─ TransformationCheck.ts
│  └─ ValuesCheck.ts
│
├─ content/
│  ├─ Content.ts
│  ├─ ContentAdapterEngine.ts
│  ├─ ContentAlignment.ts
│  ├─ ContentAlignmentCheck.ts
│  ├─ ContentLifecycleManager.ts
│  ├─ ContentManager.ts
│  ├─ ContentPersonalizer.ts
│  ├─ ContentTextAssessor.ts
│  ├─ ContentValidator.ts
│  ├─ VariantGenerator.ts
│  └─ LegalDocCreator.ts
│
├─ library/                       # [NEW SYSTEM] data-access layer; data lives in D1 + R2
│  ├─ Librarian.ts                # [MOVED] from services/ — discovery/search across libraries
│  ├─ FileManager.ts              # [MOVED] from services/ — read access (reframed: D1 query / R2 get)
│  ├─ Writer.ts                   # [MOVED] from system/ — write access (reframed: D1 write / R2 put)
│  ├─ Archivist.ts                # [MOVED] from system/ — validation workflow, archival
│  └─ CacheHandler.ts             # [MOVED] from orchestration/ — caching layer over library reads
│
├─ website/                       # generates website page JSON — does NOT render
│  ├─ PageComposer.ts             # composes page structure as JSON
│  ├─ SectionBuilder.ts           # defines section JSON shapes
│  ├─ SEOManager.ts               # generates SEO metadata as JSON fields
│  ├─ MetadataBuilder.ts          # generates page metadata as JSON
│  ├─ WebConstraints.ts           # content-level constraint checks on generated JSON
│  ├─ SitemapManager.ts           # generates sitemap data as JSON
│  ├─ WebsiteLifecycle.ts         # tracks pages through draft/published states
│  ├─ WebPersonalizer.ts          # generates personalised page JSON variants
│  └─ WebAnalyticsAdapter.ts      # analytics data handling
│
├─ email/                         # composes email HTML; sends via Emailit
│  ├─ EmailComposer.ts            # composes email HTML directly
│  ├─ EmailSequenceManager.ts
│  ├─ EmailConstraints.ts
│  ├─ EmailPersonalizer.ts
│  ├─ DeliverabilityGuard.ts
│  ├─ EmailLifecycle.ts
│  ├─ EmailAnalytics.ts
│  └─ EmailProviderAdapter.ts     # adapter to Emailit send path
│
├─ social/                        # direct platform API posting + analytics
│  ├─ SocialMediaPlanner.ts
│  ├─ SocialMediaManager.ts
│  ├─ SocialMediaCollator.ts
│  └─ SocialMediaAnalytics.ts
│
├─ document-templates/
│  ├─ TemplateRegistry.ts
│  ├─ DocumentTemplate.ts
│  ├─ TemplateSchema.ts
│  ├─ TemplateLayoutStrategy.ts
│  ├─ TemplateTypographyRules.ts
│  ├─ TemplateVisualRules.ts
│  ├─ TemplatePaginationRules.ts
│  ├─ TemplateVariantRules.ts
│  ├─ TemplateConstraintValidator.ts
│  ├─ TemplateVersionManager.ts
│  └─ TemplatePreviewBuilder.ts
│
├─ documents/
│  ├─ DocumentBuilder.ts
│  ├─ DocumentSchema.ts
│  ├─ PageLayoutEngine.ts
│  ├─ PaginationEngine.ts
│  ├─ MasterPageManager.ts
│  ├─ TypographyLayoutEngine.ts
│  ├─ VisualPlacementEngine.ts
│  ├─ TableBuilder.ts
│  ├─ ChartBuilder.ts
│  ├─ DocumentRules.ts
│  ├─ DocumentVariantGenerator.ts
│  └─ DocumentExportCoordinator.ts
│
├─ renderers/                      # narrowed — PDF stays; Google renderers moved to integrations
│  ├─ PDFRenderer.ts               # JSON/HTML → PDF
│  ├─ DOCXRenderer.ts              # JSON → DOCX
│  ├─ HTMLRenderer.ts              # JSON → HTML (for non-Astro HTML needs)
│  └─ MarkdownRenderer.ts          # JSON → Markdown
│  # [MOVED to integrations/] GoogleDocsRenderer, GoogleSheetsRenderer,
│  #   GoogleSlidesRenderer, CanvaRenderer
│
├─ marks/
│  ├─ LogoComposer.ts
│  ├─ LogoVariantGenerator.ts
│  ├─ LogoLayoutEngine.ts
│  ├─ LogoRules.ts
│  ├─ TypographicLogoGenerator.ts
│  ├─ GlyphExtractor.ts
│  ├─ FontToPathConverter.ts
│  ├─ KerningGeometryEngine.ts
│  ├─ IconGenerator.ts
│  ├─ IconSetBuilder.ts
│  ├─ IconConstraints.ts
│  ├─ GeometryValidator.ts
│  ├─ VisualComplexityScorer.ts
│  ├─ MarkSimilarityDetector.ts
│  ├─ MarkCatalog.ts
│  ├─ MarkVersioning.ts
│  └─ MarkMetadata.ts
│
├─ integrations/
│  ├─ CanvaIntegration.ts
│  ├─ GoogleIntegration.ts
│  ├─ IntegrationAdapter.ts
│  ├─ WebhookReceiver.ts
│  ├─ WebScraper.ts
│  ├─ YouTubeAPI.ts
│  ├─ RecraftIntegration.ts        # [NEW] AI-generated illustrated vectors
│  ├─ PexelsIntegration.ts         # [NEW] stock photo API
│  ├─ UnsplashIntegration.ts       # [NEW] stock photo API
│  ├─ PhosphorIntegration.ts       # [NEW] icon library caller
│  ├─ EmailitIntegration.ts        # [NEW] email send API
│  ├─ GoogleDocsRenderer.ts        # [MOVED] from renderers/
│  ├─ GoogleSheetsRenderer.ts      # [MOVED] from renderers/
│  ├─ GoogleSlidesRenderer.ts      # [MOVED] from renderers/
│  └─ CanvaRenderer.ts             # [MOVED] from renderers/
│
├─ observability/
│  ├─ AIQualityMonitor.ts
│  ├─ CostMonitor.ts
│  ├─ MetricsCollector.ts
│  └─ TraceLogger.ts
│
├─ orchestration/                  # CacheHandler moved out to library/
│  ├─ AnalyticsHandler.ts
│  ├─ BatchProcessor.ts
│  ├─ FeatureFlags.ts
│  ├─ GlobalRules.ts
│  ├─ JobSimulator.ts
│  ├─ MigrationHandler.ts
│  ├─ Notification.ts
│  ├─ Orchestrator.ts
│  ├─ PerformanceOptimizer.ts
│  ├─ PolicyEngine.ts
│  ├─ QueueHandler.ts
│  ├─ RateLimiter.ts
│  ├─ RequestHandler.ts
│  ├─ Scheduler.ts
│  ├─ StatusHandler.ts
│  └─ TestHandler.ts
│
├─ publishing/
│  ├─ DistributionManager.ts
│  └─ PublishController.ts
│
├─ resilience/
│  ├─ FailureClassifier.ts
│  ├─ RetryPolicyEngine.ts
│  ├─ FallbackStrategyResolver.ts
│  ├─ SalvageCoordinator.ts
│  ├─ RecoveryStateBuilder.ts
│  ├─ ResumePointManager.ts
│  ├─ FailureNarrator.ts
│  ├─ EscalationRouter.ts
│  ├─ HumanHandoffManager.ts
│  └─ FailureAuditRecorder.ts
│
├─ review/
│  ├─ ApprovalManager.ts
│  ├─ ManualOverride.ts
│  └─ ReviewQueue.ts
│
├─ services/                       # Librarian + FileManager moved out to library/
│  ├─ APIKeyManager.ts
│  ├─ AuthHandler.ts
│  ├─ ContentMetadataManager.ts
│  # DatabaseHandler.ts removed 2026-05-24 — see OQ-DB-1 in §Open Flags below
│  ├─ DataSanitizer.ts
│  ├─ GeneratedContentCatalog.ts   # stays in services/ (confirmed)
│  ├─ IngestionEngine.ts           # stays in services/ (confirmed)
│  ├─ PromptBuilder.ts
│  ├─ PromptCondenser.ts
│  # PythonRunner.ts removed 2026-05-24 — see OQ-PY-1 in §Open Flags below
│  ├─ ResearchTools.ts
│  └─ VectorStore.ts
│
├─ state/
│  ├─ ContextAssembler.ts
│  ├─ StateManager.ts
│  └─ StateTransitionValidator.ts
│
├─ template/
│  ├─ OutlineGenerator.ts
│  ├─ TemplateEngine.ts
│  ├─ TemplateFactory.ts
│  ├─ TemplateLibraryManager.ts
│  └─ ThemeGenerator.ts
│
├─ verification/                   # content-level validation only — not render validation
│  ├─ AccessibilityValidator.ts    # content-level: alt-text presence, reading level, structure
│  ├─ ContentIntegrityValidator.ts
│  ├─ SemanticCoherenceValidator.ts
│  ├─ SignpostingValidator.ts
│  └─ ScopeValidator.ts
│
├─ versioning/
│  ├─ ChangeLog.ts
│  └─ VersionManager.ts
│
├─ system/                         # Writer + Archivist moved out to library/
│  # FailureHandler.ts removed 2026-05-24 — see OQ-FH-1 in §Open Flags below
│  ├─ Logger.ts
│  ├─ Parsers.ts
│  ├─ Builders.ts
│  └─ GarbageCollector.ts
│
└─ lib/                            # [NEW] shared utilities — not a system, no Pass 0
   └─ svg/                         # [ABSORBED] former svg/ system as pure utilities
      ├─ SVGDrawer.ts
      ├─ SVGPathBuilder.ts
      ├─ SVGTransformEngine.ts
      ├─ SVGBooleanOps.ts
      ├─ SVGTextParser.ts
      ├─ SVGGeometryUtils.ts
      ├─ SVGOptimizer.ts
      └─ SVGExporter.ts
```

---

## Change Summary

### Removed as top-level systems (6)

| v1 system | Disposition |
|---|---|
| `colours/` | Absorbed into `brand/colour/` — colour intent, not a colour engine |
| `typography/` | Absorbed into `brand/typography/` — typography intent |
| `visual/` | Absorbed into `brand/visual/` — visual-as-brand-decision only |
| `image/` | Absorbed into `assets/` |
| `ai-image/` | Absorbed into `assets/` + `ai/ImageGeneration` |
| `svg/` | Moved to `lib/svg/` as utilities |

### Added (2 systems, plus subsystems)

| Addition | Location |
|---|---|
| `assets/` system | New top-level system, 7 subsystems |
| `library/` system | New top-level system, 5 subsystems (data-access layer) |
| `ai/ImageGeneration` | New subsystem in `ai/` |
| `ai/FactCaller`, `FactCache`, `FactInjector`, `FactVerifier` | 4 new factual-grounding subsystems in `ai/` |
| `integrations/RecraftIntegration` | New |
| `integrations/PexelsIntegration` | New |
| `integrations/UnsplashIntegration` | New |
| `integrations/PhosphorIntegration` | New |
| `integrations/EmailitIntegration` | New |

### Moved

| Subsystem | From | To |
|---|---|---|
| Librarian | `services/` | `library/` |
| FileManager | `services/` | `library/` |
| Writer | `system/` | `library/` |
| Archivist | `system/` | `library/` |
| CacheHandler | `orchestration/` | `library/` |
| GoogleDocsRenderer | `renderers/` | `integrations/` |
| GoogleSheetsRenderer | `renderers/` | `integrations/` |
| GoogleSlidesRenderer | `renderers/` | `integrations/` |
| CanvaRenderer | `renderers/` | `integrations/` |
| AssetOptimizer, ImageCollections→AssetCollections, ImageTemplate→AssetTemplate | `image/` | `assets/` |

### Subsystem counts (v2)

access 3 · ai 14 · assets 7 · audit 3 · brand 31 · checks 7 · content 11 · library 5 · website 9 · email 8 · social 4 · document-templates 11 · documents 12 · renderers 4 · marks 17 · integrations 15 · observability 4 · orchestration 16 · publishing 2 · resilience 10 · review 3 · services 10 · state 3 · template 5 · verification 5 · versioning 2 · system 4 · lib/svg 8

---

## Storage Model Note

The uploaded LIBRARY-TEMPLATE.md and REPOS-TEMPLATE.md describe libraries using local SQLite files (`/data/{library}.sqlite`) and `/repos/` JSON folders. On Cloudflare:

- **D1 is SQLite** — accessed by Worker binding, not by file path. The table schemas in LIBRARY-TEMPLATE.md (`_index`, versioned `{items}`, `_latest` view, FTS5) all run on D1 unchanged.
- **R2** holds objects (draft staging, large artefacts) — replacing the `/repos/` JSON-files-on-disk staging model.
- **What survives:** entry schemas, versioning model, status enums, library types (core/derived/generated/reference), validation rules, write policy, migration rules.
- **What is superseded:** `.sqlite` file paths, `/repos/` directories, the ID-to-filename contract, `_index.json` files (the `_index` becomes a D1 table).
- FileManager and Writer keep their names; their specs are reframed to D1 query / R2 object access internally.

The library/repo templates should be updated to this storage model before they drive Pass 0 for the `library/` system or any data-owning system.

---

## Open Flags

**OQ-PY-1 — `services/PythonRunner.ts`. RESOLVED 2026-05-24.** Removed from `services/`. Was a "just in case we ever need Python" placeholder from the v1 Python-era architecture (NCEMPIRE/services/PythonRunner/PythonRunner.md: status "Placeholder", version 0.0.0, no specific task or library ever named). Cloudflare Workers run V8 isolates which execute TypeScript/JavaScript, not Python — so an in-Worker Python runner cannot exist as written. The original decision to move from Python to TypeScript-on-Workers already implied this resolution; this entry completes it. If a Python-only library is ever genuinely needed in future, it must be an external service the Worker calls over HTTP (wrapper goes in `integrations/`, never inside the Worker). services/ subsystem count: 12 → 11.

**OQ-DB-1 — `services/DatabaseHandler.ts`. RESOLVED 2026-05-24.** Removed from `services/`. v1 role was the "own-database SQLite primitive": owning modules opened their own `.sqlite` files via `DatabaseHandler.open('/data/{library}.db')` (per `DOCS - StructureDefined/reference/MODULE-SPEC-CHECKLIST.md` access pattern; `DOCS - Outlines/REPOSITORY-STRUCTURE.md` call trace lines 431–442). The v2 substrate eliminates that role: D1 bindings are pre-bound at Worker boot (nothing to "open"), and `Project-Intent.md` locks all library data access via `library/Librarian` (no direct database access from outside library/). The v1 role didn't translate — there is no v2 step that corresponds to opening a SQLite file. `PLATFORM-GAP-ANALYSIS.md` §7 (2026-02-20) had already independently classified DatabaseHandler as SUPERSEDED; v2 ratifies that verdict (same pattern as OQ-PY-1). If a uniform D1 utility (shared error normalization, retry, query metrics) is needed by `library/FileManager` + `library/Writer` during Stage 2, it lands in `lib/d1/` as a small utility decided on its own merits — not as a resurrection of this slot. services/ subsystem count: 11 → 10.

**OQ-FH-1 — `system/FailureHandler.ts`. RESOLVED 2026-05-24.** Removed from `system/`. v1 FailureHandler was the entire failure-intelligence module: 8 functions all doing judgement work (`classifyError`, `attemptRetry`, `findAlternative`, `triggerSelfHealing`, `escalateToHuman`, `logFailure`, `checkCircuitBreaker`, `handleFailure`) per `DOCS - StructureDefined/reference/ROLE-MATRIX.md` lines 669–684. v2 decomposed this wholesale into the `resilience/` system; every v1 FailureHandler function maps to a `resilience/` subsystem (FailureClassifier, RetryPolicyEngine, FallbackStrategyResolver, SalvageCoordinator, EscalationRouter, HumanHandoffManager, FailureAuditRecorder). The "mechanical only" annotation on v2's slot labelled an emptied husk with no documented job — no v1 mechanical precedent (v1's one genuinely mechanical error function, `normalizeError`, lived in `RequestHandler`, ROLE-MATRIX line 717, not in FailureHandler), and no v2 spec for a mechanical FailureHandler. Structured-error shape comes from the mandated Result pattern (`PROJECT-SPEC-TEMPLATE.md`); failure-intelligence is owned by `resilience/`. No distinct v2 role remains. Same shape as OQ-PY-1 and OQ-DB-1: v1 slot whose role the v2 architecture absorbed elsewhere. system/ subsystem count: 5 → 4. **Pattern flag:** third husk drop in foundation tier; two of the three are in `system/`. `system/`-coherence question logged in `NCE-V2/admin/TIER-2-PREP-NOTES.md` — Parsers and Builders boundary reviews must explicitly carry the question "does `system/` remain a system?" before any `system/` spec is written.

**v1 subsystems intentionally dropped during collapse.** When `colours/` collapsed, `ColourMix.ts` was not carried into `brand/colour/` — colour mixing is a rendering concern (Astro's job), not brand intent. When `image/` collapsed, `ImageAssetLibrary`, `ImageBrowser`, `ImageFieldRenderer`, `ImageRenderer` were not carried as-is: `ImageBrowser` became `assets/AssetBrowser`, `ImageAssetLibrary` became `assets/AssetCatalog`, and the two renderers (`ImageFieldRenderer`, `ImageRenderer`) were dropped as rendering concerns. When `ai-image/` collapsed, `AIImageCreation` became `ai/ImageGeneration`; `AIVizCaller`, `AIVizManager`, `AIVizPrompter`, `AIVizTemplate` are flagged for per-system Pass 0 to place (likely fold into `ai/` or `template/`). These drops are noted explicitly so Pass 0 can confirm or restore.

**Library storage reframing.** LIBRARY-TEMPLATE.md and REPOS-TEMPLATE.md need updating from the SQLite-file / `/repos/` model to D1 + R2 before they drive Pass 0. Flagged for the `library/` system Pass 0.

---

*Created: 2026-05-18*
*Status: Finalised*
