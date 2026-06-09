# NCEMPIRE — Pass 0 Coverage Review Tracker

> Track completion of Pass 0 (Coverage & Sufficiency) reviews for all 31 systems.

**Reference files:**
- [Project-Intent.md](Project-Intent.md) — System role/purpose
- [FileTree.md](FileTree.md) — Subsystem list
- [prompts/PASS0PROMPT.md](prompts/PASS0PROMPT.md) — Review prompt template

---

## Pass 0 Checklist (A-Z)

| System | Subs | File | Status | Verdict | Notes |
|--------|------|------|--------|---------|-------|
| access | 3 | [Coverage.md](NCEMPIRE/access/Coverage.md) | ✅ | CAN | Medium risk; ~2000-2300 LOC |
| ai | 9 | [Pass0-Coverage.md](NCEMPIRE/ai/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~3700-4900 LOC |
| ai-image | 5 | [Pass0-Coverage.md](NCEMPIRE/ai-image/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~1700-2400 LOC |
| audit | 3 | [Pass0-Coverage.md](NCEMPIRE/audit/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~1000-1400 LOC |
| brand | 26 | [Pass0-Coverage.md](NCEMPIRE/brand/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~8500-11000 LOC; 8 groups |
| checks | 7 | [Pass0-Coverage.md](NCEMPIRE/checks/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~2100-2800 LOC |
| colours | 3 | [Pass0-Coverage.md](NCEMPIRE/colours/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~800-1100 LOC |
| content | 11 | [Pass0-Coverage.md](NCEMPIRE/content/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~4000-5300 LOC |
| document-templates | 11 | [Pass0-Coverage.md](NCEMPIRE/document-templates/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~3000-4100 LOC |
| documents | 12 | [Pass0-Coverage.md](NCEMPIRE/documents/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~4500-6100 LOC |
| email | 8 | [Pass0-Coverage.md](NCEMPIRE/email/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~2600-3400 LOC |
| image | 7 | [Pass0-Coverage.md](NCEMPIRE/image/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~2200-3400 LOC |
| integrations | 7 | [Pass0-Coverage.md](NCEMPIRE/integrations/Pass0-Coverage.md) | ✅ | SAFE | Low risk; ~2050-3250 LOC; adapters only |
| marks | 17 | [Pass0-Coverage.md](NCEMPIRE/marks/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~5400-8050 LOC |
| observability | 4 | [Pass0-Coverage.md](NCEMPIRE/observability/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~1550-2300 LOC |
| orchestration | 17 | [Pass0-Coverage.md](NCEMPIRE/orchestration/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~5700-8450 LOC |
| publishing | 2 | [Pass0-Coverage.md](NCEMPIRE/publishing/Pass0-Coverage.md) | ✅ | CAN | Low risk; ~750-1100 LOC |
| renderers | 8 | [Pass0-Coverage.md](NCEMPIRE/renderers/Pass0-Coverage.md) | ✅ | CAN | Low; ~2750-4050 LOC; logic-free; tracker synced (was drafted, untracked) |
| resilience | 11 | [Pass0-Coverage.md](NCEMPIRE/resilience/Pass0-Coverage.md) | ✅ | CAN | Low-Med; **RULED 2026-06-08:** `FailureHandler` folds in as resilience's orchestrating core (owns recovery strategy + cross-system dispatch) → 11 subs. Remaining boundaries (audit/content/review/ai) lock at Pass 1. |
| review | 3 | [Pass0-Coverage.md](NCEMPIRE/review/Pass0-Coverage.md) | ✅ | CAN | Medium; ~900-1600 LOC; ApprovalManager/ManualOverride overlap; ledger ownership |
| services | 14 | [Pass0-Coverage.md](NCEMPIRE/services/Pass0-Coverage.md) | ✅ | CAN* | Med-High; ~7600-11400 LOC; **PythonRunner→DROP/REMAP**; Catalog/Metadata/Librarian overlap |
| social | 4 | [Pass0-Coverage.md](NCEMPIRE/social/Pass0-Coverage.md) | ✅ | CAN* | Medium; ~1200-2150 LOC; SocialMediaManager vs publishing/DistributionManager |
| state | 3 | [Pass0-Coverage.md](NCEMPIRE/state/Pass0-Coverage.md) | ✅ | CAN* | Medium; ~1400-2350 LOC; recovery-state ↔ resilience authority |
| svg | 8 | [Pass0-Coverage.md](NCEMPIRE/svg/Pass0-Coverage.md) | ✅ | CAN* | Med-High; ~3450-6500 LOC; svg↔marks geometry; SVGExporter over-claims raster |
| system | 6 | [Pass0-Coverage.md](NCEMPIRE/system/Pass0-Coverage.md) | ✅ | CAN | **RULED 2026-06-08:** `FailureHandler` relocates → `resilience/` (decision-owning recovery orchestrator) — clears the CANNOT. `system/` keeps 6 mechanical subs. Confirm `Builders` label when `system` opens for Pass 1. |
| template | 5 | [Pass0-Coverage.md](NCEMPIRE/template/Pass0-Coverage.md) | ✅ | CAN* | Med-High; ~4300-6700 LOC; ThemeGenerator>1500; versioning claimed in 3 places |
| typography | 3 | [Pass0-Coverage.md](NCEMPIRE/typography/Pass0-Coverage.md) | ✅ | CAN | Low; ~800-1350 LOC; SEPARATE REPO (integration only); doc-templates type-rule overlap |
| verification | 5 | [Pass0-Coverage.md](NCEMPIRE/verification/Pass0-Coverage.md) | ✅ | CAN* | Medium; ~2000-3150 LOC; shared checks-orchestrator referenced but missing |
| versioning | 2 | [Pass0-Coverage.md](NCEMPIRE/versioning/Pass0-Coverage.md) | ✅ | CAN* | Medium; ~650-1100 LOC; central-vs-per-domain version managers |
| visual | 4 | [Pass0-Coverage.md](NCEMPIRE/visual/Pass0-Coverage.md) | ✅ | RESOLVED (MERGE/DROP) | **RULED 2026-06-08:** `VisualBrand` + `VisualValueAlignment` → `brand/`; `VisualCheck` → `checks/`. `visual/` likely a DROP — confirm what (if anything) remains when applied at Pass 1. |
| website | 9 | [Pass0-Coverage.md](NCEMPIRE/website/Pass0-Coverage.md) | ✅ | CAN* | Medium; ~3400-6400 LOC; SEPARATE REPO; WebsiteLifecycle↔content; Astro boundary |

**Progress: 31 / 31 Pass 0 drafts complete** — all reviewed. The 2 former CANNOTs are **RULED** (system: FailureHandler → resilience; visual: MERGE → brand/checks, likely DROP); structural moves applied at Pass 1. 8 conditional CANs carry boundary rulings into Pass 1. All *Draft Complete – Awaiting Review*.
`*` = CAN **conditional** on a Pass-1 boundary ruling.

---

## Subsystems Per System (A-Z)

### access (3)
- BrandPermissionResolver, CapabilityManager, RoleManager

### ai (9)
- AICaller, AISystemManager, AIWebSearch, CostBudgetManager, CostTracker
- EmbeddingService, ModelManager, OptimizationLoop, TokenManager

### ai-image (5)
- AIImageCreation, AIVizCaller, AIVizManager, AIVizPrompter, AIVizTemplate

### audit (3)
- AIUsageAttribution, DecisionTimeline, HumanOverrideLedger

### brand (26 across 8 groups)
- **alignment**: BrandAlignmentEngine, BrandConsistencyScorer, BrandDriftDetector
- **audience**: AudienceConfidenceScorer, AudienceManager, AudienceSegmentResolver, PersonaSynthesiser
- **core**: BrandConfidenceTracker, BrandManager, BrandStateResolver, BrandVersionManager
- **governance**: BrandCheck, BrandExceptionHandler, BrandRulesEngine
- **identity**: BrandDifferentiationEngine, BrandIdentityResolver, BrandNarrativeEngine
- **tracking**: BrandChangeLog, BrandImpactAnalyzer, BrandTracker
- **values**: BrandValuesManager, DecisionPrinciplesEngine, EthicalBoundaryResolver
- **voice**: BrandVoiceManager, ToneVariationEngine, VocabularyGovernor

### checks (7)
- GrammarCheck, LanguageCheck, LayoutCheck, SpellGrammarCheck
- SpellingCheck, TransformationCheck, ValuesCheck

### colours (3)
- ColourMaker, ColourMix, ColourToken

### content (11)
- Content, ContentAdapterEngine, ContentAlignment, ContentAlignmentCheck
- ContentLifecycleManager, ContentManager, ContentPersonalizer
- ContentTextAssessor, ContentValidator, LegalDocCreator, VariantGenerator

### document-templates (11)
- DocumentTemplate, TemplateConstraintValidator, TemplateLayoutStrategy
- TemplatePaginationRules, TemplatePreviewBuilder, TemplateRegistry
- TemplateSchema, TemplateTypographyRules, TemplateVariantRules
- TemplateVersionManager, TemplateVisualRules

### documents (12)
- ChartBuilder, DocumentBuilder, DocumentExportCoordinator, DocumentRules
- DocumentSchema, DocumentVariantGenerator, MasterPageManager
- PageLayoutEngine, PaginationEngine, TableBuilder
- TypographyLayoutEngine, VisualPlacementEngine

### email (8)
- DeliverabilityGuard, EmailAnalytics, EmailComposer, EmailConstraints
- EmailLifecycle, EmailPersonalizer, EmailProviderAdapter, EmailSequenceManager

### image (7)
- AssetOptimizer, ImageAssetLibrary, ImageBrowser, ImageCollections
- ImageFieldRenderer, ImageRenderer, ImageTemplate

### integrations (7)
- CanvaIntegration, EmaillitIntegration, GoogleIntegration, IntegrationAdapter
- WebhookReceiver, WebScraper, YouTubeAPI

### marks (17)
- FontToPathConverter, GeometryValidator, GlyphExtractor, IconConstraints
- IconGenerator, IconSetBuilder, KerningGeometryEngine, LogoComposer
- LogoLayoutEngine, LogoRules, LogoVariantGenerator, MarkCatalog
- MarkMetadata, MarkSimilarityDetector, MarkVersioning
- TypographicLogoGenerator, VisualComplexityScorer

### observability (4)
- AIQualityMonitor, CostMonitor, MetricsCollector, TraceLogger

### orchestration (17)
- AnalyticsHandler, BatchProcessor, CacheHandler, FeatureFlags, GlobalRules
- JobSimulator, MigrationHandler, Notification, Orchestrator, PerformanceOptimizer
- PolicyEngine, QueueHandler, RateLimiter, RequestHandler, Scheduler
- StatusHandler, TestHandler

### publishing (2)
- DistributionManager, PublishController

### renderers (8)
- CanvaRenderer, DOCXRenderer, GoogleDocsRenderer, GoogleSheetsRenderer
- GoogleSlidesRenderer, HTMLRenderer, MarkdownRenderer, PDFRenderer

### resilience (11)
- EscalationRouter, FailureAuditRecorder, FailureClassifier, **FailureHandler** *(orchestrating core, ex-system/)*, FailureNarrator
- FallbackStrategyResolver, HumanHandoffManager, RecoveryStateBuilder
- ResumePointManager, RetryPolicyEngine, SalvageCoordinator

### review (3)
- ApprovalManager, ManualOverride, ReviewQueue

### services (14)
- APIKeyManager, AuthHandler, ContentMetadataManager, DatabaseHandler
- DataSanitizer, FileManager, GeneratedContentCatalog, IngestionEngine
- Librarian, PromptBuilder, PromptCondenser, PythonRunner
- ResearchTools, VectorStore

### social (4)
- SocialMediaAnalytics, SocialMediaCollator, SocialMediaManager, SocialMediaPlanner

### state (3)
- ContextAssembler, StateManager, StateTransitionValidator

### svg (8)
- SVGBooleanOps, SVGDrawer, SVGExporter, SVGGeometryUtils
- SVGOptimizer, SVGPathBuilder, SVGTextParser, SVGTransformEngine

### system (6)
- Archivist, Builders, GarbageCollector, Logger
- Parsers, Writer
- *(FailureHandler relocated → resilience/ 2026-06-08)*

### template (5)
- OutlineGenerator, TemplateEngine, TemplateFactory, TemplateLibraryManager, ThemeGenerator

### typography (3)
- FontManager, Typography, TypographyManager

### verification (5)
- AccessibilityValidator, ContentIntegrityValidator, ScopeValidator
- SemanticCoherenceValidator, SignpostingValidator

### versioning (2)
- ChangeLog, VersionManager

### visual (4)
- VisualBrand, VisualCheck, VisualManager, VisualValueAlignment

### website (9)
- MetadataBuilder, PageComposer, SectionBuilder, SEOManager
- SitemapManager, WebAnalyticsAdapter, WebConstraints, WebPersonalizer, WebsiteLifecycle

---

## Session Log

| Date | System | Verdict | Notes |
|------|--------|---------|-------|
| 2026-01-22 | access | ✅ CAN | Medium risk; ~2000-2300 LOC; 2 partial responsibilities noted |
| 2026-01-22 | ai | ✅ CAN | Low risk; ~3700-4900 LOC; 9 subsystems all sufficient |
| 2026-01-22 | ai-image | ✅ CAN | Low risk; ~1700-2400 LOC; 5 subsystems all sufficient |
| 2026-01-22 | audit | ✅ CAN | Low risk; ~1000-1400 LOC; 3 subsystems all sufficient |
| 2026-01-22 | brand | ✅ CAN | Low risk; ~8500-11000 LOC; 26 subsystems across 8 groups |
| 2026-01-22 | checks | ✅ CAN | Low risk; ~2100-2800 LOC; 7 subsystems all sufficient |
| 2026-01-22 | colours | ✅ CAN | Low risk; ~800-1100 LOC; 3 subsystems all sufficient |
| 2026-01-22 | content | ✅ CAN | Low risk; ~4000-5300 LOC; 11 subsystems all sufficient |
| 2026-01-22 | document-templates | ✅ CAN | Low risk; ~3000-4100 LOC; 11 subsystems all sufficient |
| 2026-01-22 | documents | ✅ CAN | Low risk; ~4500-6100 LOC; 12 subsystems all sufficient |
| 2026-01-22 | email | ✅ CAN | Low risk; ~2600-3400 LOC; 8 subsystems all sufficient |
| 2026-01-22 | image | ✅ CAN | Low risk; ~2200-3400 LOC; 7 subsystems all sufficient |
| 2026-01-22 | integrations | ✅ SAFE | Low risk; ~2050-3250 LOC; 7 adapters, non-authoritative |
| 2026-01-22 | marks | ✅ CAN | Low risk; ~5400-8050 LOC; 17 subsystems all sufficient |
| 2026-01-22 | observability | ✅ CAN | Low risk; ~1550-2300 LOC; 4 subsystems all sufficient |
| 2026-01-22 | orchestration | ✅ CAN | Low risk; ~5700-8450 LOC; 17 subsystems all sufficient |
| 2026-01-22 | publishing | ✅ CAN | Low risk; ~750-1100 LOC; 2 subsystems all sufficient |
| 2026-06-08 | renderers | ✅ CAN | Low; logic-free; tracker synced (was drafted, untracked) |
| 2026-06-08 | resilience | ✅ CAN | Low-Med; 5 cross-system boundaries to lock at Pass 1 |
| 2026-06-08 | review | ✅ CAN | Medium; ApprovalManager/ManualOverride overlap; ledger ownership |
| 2026-06-08 | services | ✅ CAN* | Med-High; PythonRunner→DROP/REMAP; Catalog/Metadata/Librarian overlap |
| 2026-06-08 | social | ✅ CAN* | Medium; SocialMediaManager vs publishing/DistributionManager |
| 2026-06-08 | state | ✅ CAN* | Medium; recovery-state ↔ resilience authority |
| 2026-06-08 | svg | ✅ CAN* | Med-High; svg↔marks geometry; SVGExporter de-scope |
| 2026-06-08 | system | ⚠️ CANNOT | FailureHandler breaches mechanical-only; Builders mislabeled (pending ruling) |
| 2026-06-08 | template | ✅ CAN* | Med-High; ThemeGenerator>1500; versioning claimed in 3 places |
| 2026-06-08 | typography | ✅ CAN | Low; SEPARATE REPO (integration only) |
| 2026-06-08 | verification | ✅ CAN* | Medium; checks-orchestrator referenced but missing |
| 2026-06-08 | versioning | ✅ CAN* | Medium; central-vs-per-domain version managers |
| 2026-06-08 | visual | ⚠️ CANNOT | brand-authority collision (pending ruling) |
| 2026-06-08 | website | ✅ CAN* | Medium; SEPARATE REPO; WebsiteLifecycle↔content; Astro boundary |

---
## Pass 0 → Pass 1 Decision Queue (OPEN — human ruling needed; recorded as questions, not decisions)

- [x] **system → RULED (2026-06-08):** `FailureHandler` is removed from `system/` and **folds into `resilience/` as its decision-owning recovery orchestrator** — it owns the recovery paths, rules, and strategy, and dispatches to other systems to execute (resend → AICaller, rewrite → generator, log → Logger). This clears `system`'s CANNOT. *(Supersedes the earlier A/B framing — neither applied; FailureHandler is a decision owner, not a thin executor.)* `Builders` mislabel: confirm when `system` opens for Pass 1.
- [x] **visual → RULED (2026-06-08, MERGE/DROP):** `VisualBrand` + `VisualValueAlignment` → `brand/` (brand authority lives in brand, no exceptions); `VisualCheck` → `checks/`. If nothing genuinely visual-mechanical remains, `visual/` is a **DROP** — flag the remainder at Pass 1.
- [ ] **Conditional CANs (boundary ruling at Pass 1):** services (PythonRunner DROP/REMAP), social (Manager vs publishing), state (recovery-state vs resilience), svg (geometry vs marks; SVGExporter scope), template (versioning ownership), verification (shared checks-orchestrator), versioning (central vs per-domain), website (lifecycle vs content).
- Recurring rule to apply: **cross-cutting concern → one global owner** (brand / access / resilience / checks).

PASS0 NOTES TODO:
[ ] PASS 1: Colours — lock non-goal: palette generation handled by separate system








## Structural Flag Scan — 2026-06-09

Validated the stub-based Pass-0 **structural flags** (move / merge / drop / relabel / duplicate-authority) against each module's real `_plan/` content (now co-located after the doc-org). Verdicts / sufficiency / LOC ride to Pass 1 unchanged — only the structural flags were checked, and only in systems carrying one.

- **services** — PythonRunner **DROP confirmed** (Python subprocess, no Workers path; capabilities redistribute to AICaller / EmbeddingService / Cloudflare-Images / Parsers — a *drop, not a move*; `ai/EmbeddingService` confirmed real, not a phantom). `Catalog ↔ Metadata` merge **deferred** (both stub-only). `Librarian` **cleared** — it's *library/repo* discovery (tones/models/templates), not generated-content discovery.
- **social** — `SocialMediaManager` is **distinct**, not a `DistributionManager` duplicate (owns engagement + social-analytics + platform APIs). Layering boundary (DistributionManager decides+tracks; SocialMediaManager executes social delivery) draws at Pass 2; both ungrounded → no move.
- **state / review** — confirm `resilience`'s already-drawn boundaries (StateManager owns canonical state, resilience reads/reconstructs; ReviewQueue is the human queue, resilience owns the handoff). No conflict, no re-open.
- **svg / template / verification / versioning** — flagged modules stub-only and touch only *ungrounded* systems → defer to Pass 1. (verification's "missing checks-orchestrator" already resolved — PostCreationCheckManager re-added.)

**Conclusion: the only cross-system structural MOVE across all flagged systems is `visual`.** No hidden move can force re-opening grounded `resilience` / `system`. **Grounding order is unconstrained except `visual` before `brand` / `checks`.** (VisualBrand + VisualManager dispositions under final adversarial verification before applying visual.)

---

## How to Use

1. Pick a system from the checklist (A-Z)
2. Open `PASS0PROMPT.md` and set target system name
3. Run the review (assessment only - no design)
4. Fill in the system's Pass0-Coverage.md file
5. Record verdict (CAN/CANNOT) in tracker
6. Replace ☐ with ✅ when complete
