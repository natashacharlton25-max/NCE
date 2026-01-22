/
├─ access/
│  ├─ BrandPermissionResolver.py
│  ├─ CapabilityManager.py
│  └─ RoleManager.py
│
├─ ai/
│  ├─ AICaller.py
│  ├─ AISystemManager.py
│  ├─ AIWebSearch.py
│  ├─ CostBudgetManager.py
│  ├─ CostTracker.py
│  ├─ EmbeddingService.py
│  ├─ ModelManager.py
│  ├─ OptimizationLoop.py
│  └─ TokenManager.py
│
├─ ai-image/
│  ├─ AIImageCreation.py
│  ├─ AIVizCaller.py
│  ├─ AIVizManager.py
│  ├─ AIVizPrompter.py
│  └─ AIVizTemplate.py
│
├─ audit/
│  ├─ AIUsageAttribution.py
│  ├─ DecisionTimeline.py
│  └─ HumanOverrideLedger.py
│
├─ brand/
│  ├─ core/
│  │  ├─ BrandManager.py
│  │  ├─ BrandVersionManager.py
│  │  ├─ BrandConfidenceTracker.py
│  │  └─ BrandStateResolver.py
│  │
│  ├─ identity/
│  │  ├─ BrandIdentityResolver.py
│  │  ├─ BrandDifferentiationEngine.py
│  │  └─ BrandNarrativeEngine.py
│  │
│  ├─ audience/
│  │  ├─ AudienceManager.py
│  │  ├─ AudienceSegmentResolver.py
│  │  ├─ PersonaSynthesiser.py
│  │  └─ AudienceConfidenceScorer.py
│  │
│  ├─ voice/
│  │  ├─ BrandVoiceManager.py
│  │  ├─ VocabularyGovernor.py
│  │  └─ ToneVariationEngine.py
│  │
│  ├─ values/
│  │  ├─ BrandValuesManager.py
│  │  ├─ EthicalBoundaryResolver.py
│  │  └─ DecisionPrinciplesEngine.py
│  │
│  ├─ governance/
│  │  ├─ BrandRulesEngine.py
│  │  ├─ BrandCheck.py
│  │  └─ BrandExceptionHandler.py
│  │
│  ├─ alignment/
│  │  ├─ BrandAlignmentEngine.py
│  │  ├─ BrandDriftDetector.py
│  │  └─ BrandConsistencyScorer.py
│  │
│  └─ tracking/
│     ├─ BrandTracker.py
│     ├─ BrandChangeLog.py
│     └─ BrandImpactAnalyzer.py
│
├─ checks/
│  ├─ GrammarCheck.py
│  ├─ LanguageCheck.py
│  ├─ LayoutCheck.py
│  ├─ SpellGrammarCheck.py
│  ├─ SpellingCheck.py
│  ├─ TransformationCheck.py
│  └─ ValuesCheck.py
│
├─ colours/
│  ├─ ColourMaker.py
│  ├─ ColourMix.py
│  └─ ColourToken.py
│
├─ content/
│  ├─ Content.py
│  ├─ ContentAdapterEngine.py
│  ├─ ContentAlignment.py
│  ├─ ContentAlignmentCheck.py
│  ├─ ContentLifecycleManager.py
│  ├─ ContentManager.py
│  ├─ ContentPersonalizer.py
│  ├─ ContentTextAssessor.py
│  ├─ ContentValidator.py
│  ├─ VariantGenerator.py
│  └─ LegalDocCreator.py
│
├─ website/
│  ├─ PageComposer.py
│  ├─ SectionBuilder.py
│  ├─ SEOManager.py
│  ├─ MetadataBuilder.py
│  ├─ WebConstraints.py
│  ├─ SitemapManager.py
│  ├─ WebsiteLifecycle.py
│  ├─ WebPersonalizer.py
│  └─ WebAnalyticsAdapter.py
│
├─ email/
│  ├─ EmailComposer.py
│  ├─ EmailSequenceManager.py
│  ├─ EmailConstraints.py
│  ├─ EmailPersonalizer.py
│  ├─ DeliverabilityGuard.py
│  ├─ EmailLifecycle.py
│  ├─ EmailAnalytics.py
│  └─ EmailProviderAdapter.py
│
├─ social/
│  ├─ SocialMediaPlanner.py
│  ├─ SocialMediaManager.py
│  ├─ SocialMediaCollator.py
│  └─ SocialMediaAnalytics.py
│
├─ document-templates/
│  ├─ TemplateRegistry.py
│  ├─ DocumentTemplate.py
│  ├─ TemplateSchema.py
│  ├─ TemplateLayoutStrategy.py
│  ├─ TemplateTypographyRules.py
│  ├─ TemplateVisualRules.py
│  ├─ TemplatePaginationRules.py
│  ├─ TemplateVariantRules.py
│  ├─ TemplateConstraintValidator.py
│  ├─ TemplateVersionManager.py
│  └─ TemplatePreviewBuilder.py
│
├─ documents/
│  ├─ DocumentBuilder.py
│  ├─ DocumentSchema.py
│  ├─ PageLayoutEngine.py
│  ├─ PaginationEngine.py
│  ├─ MasterPageManager.py
│  ├─ TypographyLayoutEngine.py
│  ├─ VisualPlacementEngine.py
│  ├─ TableBuilder.py
│  ├─ ChartBuilder.py
│  ├─ DocumentRules.py
│  ├─ DocumentVariantGenerator.py
│  └─ DocumentExportCoordinator.py
│
├─ renderers/
│  ├─ PDFRenderer.py
│  ├─ DOCXRenderer.py
│  ├─ GoogleDocsRenderer.py
│  ├─ GoogleSheetsRenderer.py
│  ├─ GoogleSlidesRenderer.py
│  ├─ HTMLRenderer.py
│  ├─ MarkdownRenderer.py
│  └─ CanvaRenderer.py
│
├─ marks/
│  ├─ LogoComposer.py
│  ├─ LogoVariantGenerator.py
│  ├─ LogoLayoutEngine.py
│  ├─ LogoRules.py
│  ├─ TypographicLogoGenerator.py
│  ├─ GlyphExtractor.py
│  ├─ FontToPathConverter.py
│  ├─ KerningGeometryEngine.py
│  ├─ IconGenerator.py
│  ├─ IconSetBuilder.py
│  ├─ IconConstraints.py
│  ├─ GeometryValidator.py
│  ├─ VisualComplexityScorer.py
│  ├─ MarkSimilarityDetector.py
│  ├─ MarkCatalog.py
│  ├─ MarkVersioning.py
│  └─ MarkMetadata.py
│
├─ svg/
│  ├─ SVGDrawer.py
│  ├─ SVGPathBuilder.py
│  ├─ SVGTransformEngine.py
│  ├─ SVGBooleanOps.py
│  ├─ SVGTextParser.py
│  ├─ SVGGeometryUtils.py
│  ├─ SVGOptimizer.py
│  └─ SVGExporter.py
│
├─ image/
│  ├─ AssetOptimizer.py
│  ├─ ImageAssetLibrary.py
│  ├─ ImageBrowser.py
│  ├─ ImageCollections.py
│  ├─ ImageFieldRenderer.py
│  ├─ ImageRenderer.py
│  └─ ImageTemplate.py
│
│
├─ visual/
│  ├─ VisualBrand.py
│  ├─ VisualCheck.py
│  ├─ VisualManager.py
│  └─ VisualValueAlignment.py
│
├─ integrations/
│  ├─ CanvaIntegration.py
│  ├─ GoogleIntegration.py
│  ├─ IntegrationAdapter.py
│  ├─ WebhookReceiver.py
│  ├─ WebScraper.py
│  └─ YouTubeAPI.py
│
├─ observability/
│  ├─ AIQualityMonitor.py
│  ├─ CostMonitor.py
│  ├─ MetricsCollector.py
│  └─ TraceLogger.py
│
├─ orchestration/
│  ├─ AnalyticsHandler.py
│  ├─ BatchProcessor.py
│  ├─ CacheHandler.py
│  ├─ FeatureFlags.py
│  ├─ GlobalRules.py
│  ├─ JobSimulator.py
│  ├─ MigrationHandler.py
│  ├─ Notification.py
│  ├─ Orchestrator.py
│  ├─ PerformanceOptimizer.py
│  ├─ PolicyEngine.py
│  ├─ QueueHandler.py
│  ├─ RateLimiter.py
│  ├─ RequestHandler.py
│  ├─ Scheduler.py
│  ├─ StatusHandler.py
│  └─ TestHandler.py
│
├─ publishing/
│  ├─ DistributionManager.py
│  └─ PublishController.py
│
├─ resilience/
│  ├─ FailureClassifier.py
│  ├─ RetryPolicyEngine.py
│  ├─ FallbackStrategyResolver.py
│  ├─ SalvageCoordinator.py
│  ├─ RecoveryStateBuilder.py
│  ├─ ResumePointManager.py
│  ├─ FailureNarrator.py
│  ├─ EscalationRouter.py
│  ├─ HumanHandoffManager.py
│  └─ FailureAuditRecorder.py
│
├─ review/
│  ├─ ApprovalManager.py
│  ├─ ManualOverride.py
│  └─ ReviewQueue.py
│
├─ services/
│  ├─ APIKeyManager.py
│  ├─ AuthHandler.py
│  ├─ ContentMetadataManager.py
│  ├─ DatabaseHandler.py
│  ├─ DataSanitizer.py
│  ├─ GeneratedContentCatalog.py
│  ├─ IngestionEngine.py
│  ├─ Librarian.py
│  ├─ PromptBuilder.py
│  ├─ PromptCondenser.py
│  ├─ PythonRunner.py
│  ├─ ResearchTools.py
│  ├─ VectorStore.py
│  └─ FileManager.py
│
├─ state/
│  ├─ ContextAssembler.py
│  ├─ StateManager.py
│  └─ StateTransitionValidator.py
│
├─ template/
│  ├─ OutlineGenerator.py
│  ├─ TemplateEngine.py
│  ├─ TemplateFactory.py
│  ├─ TemplateLibraryManager.py
│  └─ ThemeGenerator.py
│
├─ typography/
│  ├─ FontManager.py
│  ├─ Typography.py
│  └─ TypographyManager.py
│
├─ verification/
│  ├─ AccessibilityValidator.py
│  ├─ ContentIntegrityValidator.py
│  ├─ SemanticCoherenceValidator.py
│  ├─ SignpostingValidator.py
│  └─ ScopeValidator.py
│
├─ versioning/
│  ├─ ChangeLog.py
│  └─ VersionManager.py
│
└─ system/
   ├─ FailureHandler.py   # mechanical only
   ├─ Logger.py
   ├─ Writer.py
   ├─ Parsers.py
   ├─ Archivist.py
   ├─ Builders.py
   └─ GarbageCollector.py

   