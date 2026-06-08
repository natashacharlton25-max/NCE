# SYSTEMS-DEFINITIVE.md — Canonical System & Subsystem List

> **Status:** Draft Complete – Awaiting Review
> **Generated:** 2026-06-07
> **Purpose:** The single definitive list of systems and subsystems to drive the spec and planning passes.
> **Anchor:** This mirrors [PASS0-TRACKER.md](PASS0-TRACKER.md) — the live working file where Pass 0 was left off (17/31 complete). PASS0-TRACKER was built from [FileTree.md](FileTree.md) (subsystem list) + Project-Intent.md (roles). Keeping this list identical to PASS0-TRACKER preserves the anchor for the 17 completed reviews.

---

## Provenance (which model this is, and why)

Three architectures existed in the reference material. This list uses **Model B** and discards the others:

| Model | Source | Decision |
|-------|--------|----------|
| **A** — 33 categories / 166 modules, flat, `.py` | SYSTEMS-INDEX.md, SYSTEMS-DIRECTORY.md (2026-01-21) | **Superseded** — stale predecessor. |
| **B** — 31 systems / ~195 subs, `.py`→TypeScript | PASS0-TRACKER.md, FileTree.md, AUDIT-REPORT.md, deleted `NCEMPIRE/` spec tree | **✅ CANONICAL** — what the live work uses. |
| **C** — 26 function-based systems, Manager→Worker→Function | DOCS - StructureDefined/02-SYSTEMS.md (2026-01-22) | **Discarded** — user: "Model C looks wrong." |

**Language:** `.py` in the source trees is historical. Runtime is **TypeScript on Cloudflare Workers** ([STACK-PROPOSAL.md](STACK-PROPOSAL.md)). File extension only — not a structural difference.

---

## Counts

- **Systems:** 31
- **Subsystems:** 239 (237 in the canonical tracker set + 2 re-added checks/ modules — verified by the NCEMPIRE/ scaffold: 239 subsystem folders. The audit's "~237" and the tracker's "~195" were both estimates; 239 is the counted total.)
- **Pass 0 complete:** 31 / 31 (all reviewed 2026-06-08; verdicts + rulings in [PASS0-TRACKER.md](PASS0-TRACKER.md))

### Pass 0 → Pass 1 structural rulings (2026-06-08; applied at Pass 1, not yet physically moved)
- **FailureHandler:** moves `system/` → **`resilience/`** as its decision-owning recovery orchestrator (system 7→6, resilience 10→11). Cleared `system`'s Pass-0 CANNOT.
- **visual:** `VisualBrand` + `VisualValueAlignment` → `brand/`; `VisualCheck` → `checks/`; `visual/` likely a **DROP** (confirm remainder at Pass 1). Cleared `visual`'s Pass-0 CANNOT.

### Resolved subsystem-count decisions (per PASS0-TRACKER, user-confirmed)
- **checks/ → 9** (tracker had 7; **PostCreationCheckManager + PostCreationQualityDecider re-added** — confirmed Tier-1 blocking per `DOCS - Notes/final grade spec analysis python.txt`).
- **integrations/ → 7** (PASS0-TRACKER body includes EmaillitIntegration).
- **document-templates/ → 11** (PASS0-TRACKER value; the "10" was only in PROGRESS-TRACKER).

---

# SYSTEMS (A–Z)

> Status legend: ✅ = Pass 0 complete · ☐ = Pass 0 not started. Verdict/risk/LOC carried from PASS0-TRACKER + AUDIT-REPORT.

---

### 1. access — 3 subs — ✅ CAN (Medium risk; ~2,000–2,300 LOC)
- BrandPermissionResolver
- CapabilityManager
- RoleManager

### 2. ai — 9 subs — ✅ CAN (Low; ~3,700–4,900 LOC)
- AICaller
- AISystemManager
- AIWebSearch
- CostBudgetManager
- CostTracker
- EmbeddingService
- ModelManager
- OptimizationLoop
- TokenManager

### 3. ai-image — 5 subs — ✅ CAN (Low; ~1,700–2,400 LOC)
- AIImageCreation
- AIVizCaller
- AIVizManager
- AIVizPrompter
- AIVizTemplate

### 4. audit — 3 subs — ✅ CAN (Low; ~1,000–1,400 LOC)
- AIUsageAttribution
- DecisionTimeline
- HumanOverrideLedger

### 5. brand — 26 subs / 8 groups — ✅ CAN (Low; ~8,500–11,000 LOC)
**core/** — BrandManager · BrandVersionManager · BrandConfidenceTracker · BrandStateResolver
**identity/** — BrandIdentityResolver · BrandDifferentiationEngine · BrandNarrativeEngine
**audience/** — AudienceManager · AudienceSegmentResolver · PersonaSynthesiser · AudienceConfidenceScorer
**voice/** — BrandVoiceManager · VocabularyGovernor · ToneVariationEngine
**values/** — BrandValuesManager · EthicalBoundaryResolver · DecisionPrinciplesEngine
**governance/** — BrandRulesEngine · BrandCheck · BrandExceptionHandler
**alignment/** — BrandAlignmentEngine · BrandDriftDetector · BrandConsistencyScorer
**tracking/** — BrandTracker · BrandChangeLog · BrandImpactAnalyzer

### 6. checks — 9 subs — ✅ CAN (Low; ~2,100–2,800 LOC) — ⚠️ 2 re-added
- GrammarCheck
- LanguageCheck
- LayoutCheck
- SpellGrammarCheck
- SpellingCheck
- TransformationCheck
- ValuesCheck
- PostCreationCheckManager ⚠️ RE-ADDED (orchestrates all post-creation checks)
- PostCreationQualityDecider ⚠️ RE-ADDED (decides pass/retry/salvage/manual on results — Tier-1 blocking)

### 7. colours — 3 subs — ✅ CAN (Low; ~800–1,100 LOC) *(separate build track)*
- ColourMaker
- ColourMix
- ColourToken

### 8. content — 11 subs — ✅ CAN (Low; ~4,000–5,300 LOC)
- Content
- ContentAdapterEngine
- ContentAlignment
- ContentAlignmentCheck
- ContentLifecycleManager
- ContentManager
- ContentPersonalizer
- ContentTextAssessor
- ContentValidator
- VariantGenerator
- LegalDocCreator

### 9. document-templates — 11 subs — ✅ CAN (Low; ~3,000–4,100 LOC)
- TemplateRegistry
- DocumentTemplate
- TemplateSchema
- TemplateLayoutStrategy
- TemplateTypographyRules
- TemplateVisualRules
- TemplatePaginationRules
- TemplateVariantRules
- TemplateConstraintValidator
- TemplateVersionManager
- TemplatePreviewBuilder

### 10. documents — 12 subs — ✅ CAN (Low; ~4,500–6,100 LOC)
- DocumentBuilder
- DocumentSchema
- PageLayoutEngine
- PaginationEngine
- MasterPageManager
- TypographyLayoutEngine
- VisualPlacementEngine
- TableBuilder
- ChartBuilder
- DocumentRules
- DocumentVariantGenerator
- DocumentExportCoordinator

### 11. email — 8 subs — ✅ CAN (Low; ~2,600–3,400 LOC)
- EmailComposer
- EmailSequenceManager
- EmailConstraints
- EmailPersonalizer
- DeliverabilityGuard
- EmailLifecycle
- EmailAnalytics
- EmailProviderAdapter

### 12. image — 7 subs — ✅ CAN (Low; ~2,200–3,400 LOC)
- AssetOptimizer
- ImageAssetLibrary
- ImageBrowser
- ImageCollections
- ImageFieldRenderer
- ImageRenderer
- ImageTemplate

### 13. integrations — 7 subs — ✅ SAFE (Low; ~2,050–3,250 LOC; adapters only)
- CanvaIntegration
- GoogleIntegration
- IntegrationAdapter
- WebhookReceiver
- WebScraper
- YouTubeAPI
- EmaillitIntegration

### 14. marks — 17 subs — ✅ CAN (Low; ~5,400–8,050 LOC)
- LogoComposer
- LogoVariantGenerator
- LogoLayoutEngine
- LogoRules
- TypographicLogoGenerator
- GlyphExtractor
- FontToPathConverter
- KerningGeometryEngine
- IconGenerator
- IconSetBuilder
- IconConstraints
- GeometryValidator
- VisualComplexityScorer
- MarkSimilarityDetector
- MarkCatalog
- MarkVersioning
- MarkMetadata

### 15. observability — 4 subs — ✅ CAN (Low; ~1,550–2,300 LOC)
- AIQualityMonitor
- CostMonitor
- MetricsCollector
- TraceLogger

### 16. orchestration — 17 subs — ✅ CAN (Low; ~5,700–8,450 LOC)
- AnalyticsHandler
- BatchProcessor
- CacheHandler
- FeatureFlags
- GlobalRules
- JobSimulator
- MigrationHandler
- Notification
- Orchestrator
- PerformanceOptimizer
- PolicyEngine
- QueueHandler
- RateLimiter
- RequestHandler
- Scheduler
- StatusHandler
- TestHandler

### 17. publishing — 2 subs — ✅ CAN (Low; ~750–1,100 LOC)
- DistributionManager
- PublishController

### 18. renderers — 8 subs — ☐ NOT DONE *(Renderers must remain logic-free — CLAUDE.md §4)*
- PDFRenderer
- DOCXRenderer
- GoogleDocsRenderer
- GoogleSheetsRenderer
- GoogleSlidesRenderer
- HTMLRenderer
- MarkdownRenderer
- CanvaRenderer

### 19. resilience — 10 subs — ☐ NOT DONE *(Global constraint system — failure decisions live here, CLAUDE.md §4)*
- FailureClassifier
- RetryPolicyEngine
- FallbackStrategyResolver
- SalvageCoordinator
- RecoveryStateBuilder
- ResumePointManager
- FailureNarrator
- EscalationRouter
- HumanHandoffManager
- FailureAuditRecorder

### 20. review — 3 subs — ☐ NOT DONE
- ApprovalManager
- ManualOverride
- ReviewQueue

### 21. services — 14 subs — ☐ NOT DONE
- APIKeyManager
- AuthHandler
- ContentMetadataManager
- DatabaseHandler
- DataSanitizer
- GeneratedContentCatalog
- IngestionEngine
- Librarian
- PromptBuilder
- PromptCondenser
- PythonRunner ⚠️ (language-era name; intent = objective grading layer per DOCS-Notes — see Open Items)
- ResearchTools
- VectorStore
- FileManager

### 22. social — 4 subs — ☐ NOT DONE
- SocialMediaPlanner
- SocialMediaManager
- SocialMediaCollator
- SocialMediaAnalytics

### 23. state — 3 subs — ☐ NOT DONE *(foundational for all workflows)*
- ContextAssembler
- StateManager
- StateTransitionValidator

### 24. svg — 8 subs — ☐ NOT DONE
- SVGDrawer
- SVGPathBuilder
- SVGTransformEngine
- SVGBooleanOps
- SVGTextParser
- SVGGeometryUtils
- SVGOptimizer
- SVGExporter

### 25. system — 7 subs — ☐ NOT DONE *(core mechanical)*
- Archivist
- Builders
- FailureHandler *(mechanical only — failure decisions belong to resilience/)*
- GarbageCollector
- Logger
- Parsers
- Writer

### 26. template — 5 subs — ☐ NOT DONE
- OutlineGenerator
- TemplateEngine
- TemplateFactory
- TemplateLibraryManager
- ThemeGenerator

### 27. typography — 3 subs — ☐ NOT DONE *(separate build track)*
- FontManager
- Typography
- TypographyManager

### 28. verification — 5 subs — ☐ NOT DONE
- AccessibilityValidator
- ContentIntegrityValidator
- SemanticCoherenceValidator
- SignpostingValidator
- ScopeValidator

### 29. versioning — 2 subs — ☐ NOT DONE
- ChangeLog
- VersionManager

### 30. visual — 4 subs — ☐ NOT DONE
- VisualBrand
- VisualCheck
- VisualManager
- VisualValueAlignment

### 31. website — 9 subs — ☐ NOT DONE *(content generation; distinct from the consuming Astro platform — STACK-PROPOSAL out-of-scope boundary)*
- PageComposer
- SectionBuilder
- SEOManager
- MetadataBuilder
- WebConstraints
- SitemapManager
- WebsiteLifecycle
- WebPersonalizer
- WebAnalyticsAdapter

---

## SUMMARY TABLE

| # | System | Subs | Pass 0 | Verdict |
|---|--------|------|--------|---------|
| 1 | access | 3 | ✅ | CAN |
| 2 | ai | 9 | ✅ | CAN |
| 3 | ai-image | 5 | ✅ | CAN |
| 4 | audit | 3 | ✅ | CAN |
| 5 | brand | 26 | ✅ | CAN |
| 6 | checks | 9 ⚠️ | ✅ | CAN |
| 7 | colours | 3 | ✅ | CAN |
| 8 | content | 11 | ✅ | CAN |
| 9 | document-templates | 11 | ✅ | CAN |
| 10 | documents | 12 | ✅ | CAN |
| 11 | email | 8 | ✅ | CAN |
| 12 | image | 7 | ✅ | CAN |
| 13 | integrations | 7 | ✅ | SAFE |
| 14 | marks | 17 | ✅ | CAN |
| 15 | observability | 4 | ✅ | CAN |
| 16 | orchestration | 17 | ✅ | CAN |
| 17 | publishing | 2 | ✅ | CAN |
| 18 | renderers | 8 | ☐ | — |
| 19 | resilience | 10 | ☐ | — |
| 20 | review | 3 | ☐ | — |
| 21 | services | 14 | ☐ | — |
| 22 | social | 4 | ☐ | — |
| 23 | state | 3 | ☐ | — |
| 24 | svg | 8 | ☐ | — |
| 25 | system | 7 | ☐ | — |
| 26 | template | 5 | ☐ | — |
| 27 | typography | 3 | ☐ | — |
| 28 | verification | 5 | ☐ | — |
| 29 | versioning | 2 | ☐ | — |
| 30 | visual | 4 | ☐ | — |
| 31 | website | 9 | ☐ | — |

**31 systems · 239 subsystems · 17/31 Pass 0 complete.**

---

## OPEN ITEMS (do not block the list; resolve as you spec)

These are flagged, not decided — they don't change the 31-system structure, but each needs a ruling at the right pass:

1. **services/PythonRunner** — name predates the TS/Workers switch (no Python runtime on Workers). DOCS-Notes documents the *intent* as an objective grading layer (SpaCy / Sentence-Transformers: continuity, brand-purity, truth-coverage grading between TemplateEngine and Renderer). Decide at Pass 1/3: rename + reimplement as a Workers-compatible grader, route to an external service, or drop.
2. **Old-index extras NOT in this list** — content-pipeline, explainability, feedback, governance, insights, memory (6 systems from Model A). DOCS-Notes calls some of these intentional "ownership gap" additions. They are **excluded** from the canonical 31 for now (your answer pointed back to PASS0-TRACKER as the source of record, and these are not in it). If you later decide any belong, they re-enter as new systems. An adversarially-verified investigation of all six found each is **partially covered** by the canonical 31 → recommendation **fold-into-existing** (no reinstatement), with these unowned gaps to assign at Pass 0/1: **content-pipeline** (cross-format content-parity check; output-artifact injection — tracking pixels / unsubscribe / schema.org), **explainability** (human-readable explanation-synthesis layer), **feedback** (signal collection/normalisation ownership), **governance** (blocking unsafe/disallowed output), **insights** (StrategySignalGenerator — genuinely orphaned, no home), **memory** (validated-knowledge curation, expiry policy, confidence scoring). Notably:
   - PostCreationCheckManager / PostCreationQualityDecider (old content-pipeline-adjacent) → already folded into **checks/** above.
   - MultiFormatOrchestrator / OutputFormatterOrchestrator / PublishingOrchestrator (old content-pipeline) → candidates for **orchestration/** or **publishing/** if needed.
3. **Runtime conflict (recorded, not in scope of this list)** — Model C documented SQLite + local Node server (Express/Fastify + Svelte). STACK-PROPOSAL says Cloudflare Workers. Model C is discarded, so **Workers stands** — but if any planning doc still references the local-server stack, it descends from Model C and should be reconciled to Workers.
4. **brand count convention** — listed as 26 subsystems (8 groups). PROGRESS-TRACKER counted "8". This list uses **subsystems (26)** as the unit, consistent with PASS0-TRACKER.

---
### Review & Clarification Needed
- Is this the definitive list you want to spec against — built on PASS0-TRACKER (Model B), Model C discarded?
- checks/ now = 9 (2 re-added). integrations/ = 7, document-templates/ = 11 — all per PASS0-TRACKER. Correct?
- The 6 old-index extras are excluded (folded where they fit). Do you want any pulled back in as full systems before we start specs?
- Once confirmed, may I retire the superseded sources (SYSTEMS-INDEX.md, SYSTEMS-DIRECTORY.md, the StructureDefined 02-SYSTEMS.md) or update them to point here?
---
