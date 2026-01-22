# Systems Index

> **Purpose:** Index of all system categories and modules
> **Last Updated:** 2026-01-21

---

## 1. Category Folders

| Folder | Description |
|--------|-------------|
| **access/** | Permissions & capabilities - roles, capabilities, brand permission resolution |
| **ai/** | Text AI services - API calls, model management, token tracking, cost management |
| **ai-image/** | AI image generation - prompting, calling, managing visual AI workflows |
| **audit/** | Audit trails - decision timelines, override ledgers, AI usage attribution |
| **brand/** | Brand management - brand profiles, audience management, brand tracking |
| **checks/** | Quality checks - grammar, spelling, language, layout, values validation |
| **colours/** | Colour systems - colour creation, mixing, token management |
| **content/** | Content creation - content management, legal docs, exports |
| **content-pipeline/** | Publishing workflows - multi-format orchestration, output formatting, distribution |
| **document-templates/** | Document template system - schemas, layout rules, typography rules, versioning |
| **documents/** | Document building - layout engines, pagination, tables, charts, export |
| **email/** | Email content - composition, sequences, deliverability, analytics, providers |
| **explainability/** | AI transparency - decision explanations, prompt diffs, model choice rationale |
| **feedback/** | Feedback loops - collection, analysis, prompt improvement application |
| **governance/** | Rules & compliance - policy registry, rules engine, compliance checking |
| **image/** | Image handling - browsing, collections, rendering, templates (non-AI) |
| **insights/** | Strategic intelligence - pattern detection, recommendations, strategy signals |
| **integrations/** | External APIs - Canva, Google, YouTube, web scraping, webhooks |
| **marks/** | Logos & icons - composition, variants, typography, geometry, cataloging |
| **memory/** | Long-term knowledge - knowledge curation, confidence scoring, expiry management |
| **observability/** | System monitoring - metrics, tracing, AI quality, cost monitoring |
| **orchestration/** | System orchestration - handlers, queues, scheduling, policies, analytics |
| **publishing/** | Release & distribution - publish control, platform distribution |
| **recovery/** | Workflow recovery - state building, resume points, failure narration |
| **renderers/** | Output renderers - PDF, DOCX, HTML, Markdown, Google Docs/Sheets/Slides, Canva |
| **review/** | Human oversight - review queues, approvals, manual overrides |
| **services/** | Core services - file management, auth, database, prompts, libraries |
| **social/** | Social media - planning, management, collation, analytics |
| **state/** | State management - context assembly, state transitions, validation |
| **svg/** | SVG operations - paths, transforms, boolean ops, optimization, export |
| **system/** | Core infrastructure - writing, logging, parsing, archiving, building |
| **template/** | Template system - template engine, factory, outline and theme generation |
| **typography/** | Typography - font management, typography rules and management |
| **verification/** | Post-creation verification and validation |
| **versioning/** | Version control - change logs, version management, rollback |
| **visual/** | Visual brand - brand visuals, visual checks, visual management |
| **website/** | Website content - pages, sections, SEO, metadata, sitemaps, analytics |

---

## 2. All System Modules

### access/
- _BrandPermissionResolver — Resolves brand-specific permission overrides. Supports multi-brand permission isolation.
- _CapabilityManager — Defines what users/brands/roles can do. Manages capability matrices and action gating.
- _RoleManager — Manages user roles and hierarchies. Defines role inheritance and default capabilities.

### ai/
- *AICaller — Makes API calls to text AI providers. Handles retries, rate limits, and response parsing.
- *AISystemManager — Coordinates AI text services. Orchestrates model selection, token estimation, and cost checking.
- _AIWebSearch — Performs web searches via AI-powered search APIs. Returns structured search results.
- _CostBudgetManager — Manages AI spending budgets and enforces limits. Blocks requests exceeding caps.
- _CostTracker — Records actual AI usage costs per request. Tracks spend by brand, job, and model.
- _EmbeddingService — Generates vector embeddings from text. Used for semantic search and validation.
- _ModelManager — Manages AI model selection and fallback chains. Stores model cards with pricing and capabilities.
- _OptimizationLoop — Analyzes feedback patterns to identify underperforming prompts. Suggests improvements.
- _TokenManager — Estimates tokens before calls and records actual usage. Manages token budgets.

### ai-image/
- *AIImageCreation — Generates images using AI providers. Handles provider routing and style application.
- _AIVizCaller — Makes API calls to AI image generation providers. Handles provider-specific formats.
- _AIVizManager — Coordinates AI image generation workflows. Manages provider selection and fallbacks.
- _AIVizPrompter — Builds prompts for AI image generation. Translates brand style into effective prompts.
- _AIVizTemplate — Manages reusable templates for AI image prompts. Stores style presets.

### audit/
- _AIUsageAttribution — Tracks AI model usage per content piece. Supports AI disclosure requirements.
- _DecisionTimeline — Reconstructs who did what, why, and with what AI. Complete audit trail for compliance.
- _HumanOverrideLedger — Records all human overrides of AI/rules. Captures rationale for audit.

### brand/
- _AudienceManager — Manages target audience profiles and segments. Stores demographics and preferences.
- _BrandCheck — Validates content against brand guidelines. Ensures tone and voice align with brand.
- _BrandManager — Manages brand profiles and configurations. Central source for brand voice and values.
- _BrandTracker — Tracks brand usage and consistency metrics. Monitors guideline application.

### checks/
- _GrammarCheck — Checks content for grammar errors. Returns issues with suggestions.
- _LanguageCheck — Validates language usage, style, and readability. Ensures audience-appropriate level.
- _LayoutCheck — Validates content layout and structure. Ensures sections and formatting are correct.
- _PostCreationCheckManager — Orchestrates all post-creation quality checks. Runs all validators.
- _PostCreationQualityDecider — Decides what to do with check results. Determines pass, retry, or review.
- _SpellGrammarCheck — Combined spelling and grammar checking. Single pass for both error types.
- _SpellingCheck — Checks content for spelling errors. Supports custom dictionaries.
- _TransformationCheck — Validates content transformations. Ensures adapted content maintains integrity.
- _ValuesCheck — Validates content against brand values. Ensures messaging aligns with principles.

### colours/
- _ColourMaker — Creates colour palettes from brand inputs. Generates harmonious colour schemes.
- _ColourMix — Mixes and blends colours for harmonious palettes. Handles colour theory and accessibility.
- _ColourToken — Manages colour tokens for design systems. Exports colours in various formats.

### content/
- _Content — Core content generation and management. Central module for content operations.
- _ContentAdapterEngine — Repurposes content across formats. Converts workbooks to social posts, etc.
- _ContentAlignment — Aligns content with brand strategy. Ensures content serves strategic objectives.
- _ContentAlignmentCheck — Validates content-brand alignment. Scores how well content matches brand.
- _ContentLifecycleManager — Tracks content through entire lifecycle. Manages draft to archived states.
- _ContentManager — Manages content storage and retrieval. Handles content versioning and metadata.
- _ContentPersonalizer — Adapts content for specific audiences. Adjusts tone and vocabulary per segment.
- _ContentSalvageModule — Recovers partial content when generation fails. Enables graceful degradation.
- _ContentTextAssessor — Assesses text quality and readability. Scores clarity and effectiveness.
- _ContentValidator — Validates content against rules. Enforces content policies and constraints.
- _Email — Generates email content. Creates email sequences and campaigns.
- _EmailLit — Email generation via Emailit service. Alternative email sending provider.
- _ExportManager — Manages content export workflows. Handles multi-format export and packaging.
- _LegalDocCreator — Creates legal documents from templates. Generates terms and policies.
- _PDFMaker — Creates PDF documents. Handles PDF generation with proper formatting.
- _VariantGenerator — Generates multiple content variants for A/B testing. Creates variations.
- _WebsiteContent — Generates website content. Creates pages, posts, and web copy.

### content-pipeline/
- _MultiFormatOrchestrator — Coordinates same content across multiple output formats.
- _OutputFormatterOrchestrator — Applies format-specific optimizations. Adds tracking and metadata.
- _PublishingOrchestrator — Master orchestrator for publishing workflow. Coordinates distribution.

### explainability/
- _DecisionExplainer — Surfaces why AI made specific decisions. Provides audit-ready explanations.
- _ModelChoiceRationale — Explains why specific AI models were selected. Surfaces cost/capability tradeoffs.
- _PromptDiffViewer — Shows prompt changes and condensation. Enables debugging of prompt transformations.

### feedback/
- _FeedbackAnalyzer — Converts feedback into actionable insights. Identifies recurring issues.
- _FeedbackCollector — Captures user edits, rejections, and approvals. Normalizes feedback signals.
- _PromptFeedbackApplier — Closes the learning loop. Suggests prompt changes based on feedback.

### governance/
- _ComplianceChecker — Enforces legal, ethical, and safety constraints. Blocks unsafe outputs.
- _PolicyRegistry — Stores and versions active policies. Resolves applicable policies per context.
- _RulesEngine — Central rule evaluation engine. Single source of truth for "is this allowed?"

### image/
- _AssetOptimizer — Converts uploaded assets to optimized formats. Handles WebP and aspect ratios.
- _ImageAssetLibrary — Catalogs AI-generated images for discovery. Tracks usage and reuse.
- _ImageBrowser — Browses and searches image libraries. Finds stock photos and brand assets.
- _ImageCollections — Manages image collections and organization. Groups and tags images.
- _ImageFieldRenderer — Renders image fields in templates. Handles placement and sizing.
- _ImageRenderer — Renders and processes images. Applies effects, overlays, and compositing.
- _ImageTemplate — Applies visual templates to images. Adds brand overlays and styling.

### insights/
- _PatternDetector — Detects patterns in system analytics. Identifies performance correlations and anomalies.
- _RecommendationEmitter — Generates actionable recommendations. Prioritizes by impact potential.
- _StrategySignalGenerator — Produces strategic intelligence signals. Aggregates cross-brand insights.

### integrations/
- _CanvaIntegration — Integrates with Canva API. Syncs assets and manages designs.
- _GoogleIntegration — Integrates with Google services. Handles Drive, Docs, Sheets, Slides.
- _IntegrationAdapter — Base adapter pattern for external APIs. Abstracts auth and retries.
- _WebhookReceiver — Handles inbound webhook events. Validates and routes callbacks.
- _WebScraper — Scrapes web content for research. Extracts text and data from URLs.
- _YouTubeAPI — Integrates with YouTube API. Manages video metadata and channel data.

### logo/
- _LogoMaker — Creates and manages logo assets. Generates logo variations and formats.

### memory/
- _KnowledgeCurator — Curates and validates system knowledge. Manages long-term memory of learned patterns.
- _KnowledgeExpiryManager — Manages knowledge entry expiration. Ensures system doesn't rely on stale knowledge.
- _MemoryConfidenceScorer — Scores confidence in knowledge entries. Determines reliance level based on evidence.

### observability/
- _AIQualityMonitor — Evaluates AI output quality over time. Tracks hallucinations and drift.
- _CostMonitor — Provides financial observability. Aggregates costs and detects abnormal spend.
- _MetricsCollector — Collects system metrics. Tracks timings, counts, and throughput.
- _TraceLogger — Provides end-to-end execution tracing. Correlates logs with trace IDs.

### orchestration/
- _AnalyticsHandler — Collects and processes system analytics. Generates usage reports.
- _BatchProcessor — Processes batched operations. Handles bulk jobs efficiently.
- _CacheHandler — Manages system caching. Controls TTL, invalidation, and cache tiers.
- _FeatureFlags — Controls feature availability. Enables/disables features per brand or environment.
- _GlobalRules — Enforces system-wide rules. Applies universal constraints.
- _JobSimulator — Estimates cost and time before execution. Runs dry runs for validation.
- _MigrationHandler — Handles data migrations. Manages schema updates and transforms.
- _Notification — Sends system notifications. Delivers alerts via email, Slack, or webhooks.
- _Orchestrator — Coordinates pipeline execution. Manages job flow and dependencies.
- _PerformanceOptimizer — Monitors and optimizes system performance. Identifies bottlenecks.
- _PolicyEngine — Enforces policies and permissions. Runs pre-flight checks.
- _QueueHandler — Manages job queues. Handles priority, throttling, and concurrency.
- _RateLimiter — Enforces API rate limits. Prevents exceeding provider quotas.
- _RequestHandler — Routes incoming requests. Entry point for API, UI, and webhooks.
- _Scheduler — Schedules recurring and delayed jobs. Provides cron-style scheduling.
- _StatusHandler — Tracks and reports job status. Emits real-time progress for UI.
- _TestHandler — Manages test execution. Provides mock data and integration testing.

### publishing/
- _DistributionManager — Pushes content to platforms. Tracks delivery and handles retries.
- _PublishController — Final release gate for content. Decides when content is publishable.

### recovery/
- _FailureNarrator — Provides human-readable failure explanations. Presents recovery options clearly.
- _RecoveryStateBuilder — Builds recovery state from failed workflows. Enables graceful continuation.
- _ResumePointManager — Manages workflow resume points. Allows continuation from specific checkpoints.

### renderers/
- _CanvaRenderer — Renders content to Canva designs. Creates slides and visual assets.
- _DOCXRenderer — Renders content to DOCX format. Creates editable Word documents.
- _GoogleDocsRenderer — Renders content to Google Docs. Creates cloud-native documents.
- _GoogleSheetsRenderer — Renders content to Google Sheets. Creates spreadsheets.
- _GoogleSlidesRenderer — Renders content to Google Slides. Creates presentations.
- _HTMLRenderer — Renders content to HTML. Creates responsive web content.
- _MarkdownRenderer — Renders content to Markdown. Creates documentation and blog posts.
- _PDFRenderer — Renders content to PDF. Creates print-ready documents.

### review/
- _ApprovalManager — Handles final authority decisions. Accepts, rejects, or requests changes.
- _ManualOverride — Allows humans to override AI/rules. Captures rationale for audit trail.
- _ReviewQueue — Queues items needing human approval. Prioritizes by severity and risk.

### services/
- _APIKeyManager — Manages API keys and credentials. Secure storage for authentication.
- _AuthHandler — Handles authentication and sessions. Manages Google OAuth and access.
- _ContentMetadataManager — Tracks content provenance and metadata. Links to generation context.
- _DatabaseHandler — Manages database operations. Central interface for SQLite queries.
- _DataSanitizer — Scrubs PII from prompts before API calls. Ensures data protection compliance.
- _GeneratedContentCatalog — Indexes generated content for discovery. Enables search and reuse.
- _IngestionEngine — Ingests external data sources. Imports and normalizes external content.
- _Librarian — Searches and recommends library entries. Provides discovery for templates and tones.
- _PromptBuilder — Assembles prompts from templates. Resolves variables and applies constraints.
- _PromptCondenser — Condenses prompts to fit token limits. Summarizes while preserving meaning.
- _PythonRunner — Executes Python scripts. Enables custom Python-based operations.
- _ResearchTools — Provides research and lookup tools. Supports content research workflows.
- _VectorStore — Stores and retrieves vector embeddings. Enables semantic search and matching.
- FileManager — Reads files from the filesystem. Public API for all file read operations.

### social/
- _SocialMediaAnalytics — Analyzes social media performance. Tracks engagement and reach.
- _SocialMediaCollator — Collates social media content. Gathers posts for scheduling.
- _SocialMediaManager — Manages social media publishing. Handles posting to platforms.
- _SocialMediaPlanner — Plans social media campaigns. Creates content calendars.

### state/
- _ContextAssembler — Assembles execution context. Merges brand, audience, theme, and job context.
- _StateManager — Canonical source of truth for state. Tracks workflow and enables recovery.
- _StateTransitionValidator — Prevents illegal state transitions. Validates workflow moves.

### svg/
- _SVGDrawer — Creates and manipulates SVG graphics. Generates scalable vector assets.

### system/
- _Archivist — Archives and versions stored data. Manages data retention and recovery.
- _Builders — Creates file paths and folders. Handles path construction and scaffolding.
- _FailureHandler — Handles errors and recovery. Manages retries, fallbacks, and escalation.
- _GarbageCollector — Cleans up temp files and stale exports. Prevents disk bloat.
- _Logger — Logs system events and errors. Central logging with temp and SQLite storage.
- _Parsers — Parses and transforms data formats. Handles JSON, CSV, Markdown, and AI responses.
- Writer — Writes data to the filesystem. Only module that writes system data.

### template/
- _TemplateFactory — Creates template instances. Builds templates from definitions.
- _TemplateLibraryManager — Manages template versions and evolution. Tracks changes and rollback.
- OutlineGenerator — Generates content outlines from themes. Creates section structures.
- TemplateEngine — Executes templates to produce content. Renders sections with AI generation.
- ThemeGenerator — Generates themes from brand and audience. Creates strategic messaging frameworks.

### typography/
- _FontManager — Manages font assets and pairings. Handles font loading and recommendations.
- _Typography — Defines typography rules. Sets font sizes, line heights, and spacing.
- _TypographyManager — Manages typography configurations. Applies typography across content.

### verification/
- _AccessibilityValidator — Validates WCAG compliance, readability, and alt text. Ensures accessibility standards.
- _ContentIntegrityValidator — Checks narrative coherence and contradictions. Ensures consistency across sections.
- _SemanticCoherenceValidator — Checks logical flow between sections. Ensures content reads as coherent whole.
- _SignpostingValidator — Verifies crisis signposting is included. Directs to professional support where appropriate.
- _ScopeValidator — Ensures content stays educational/peer support scope. Flags clinical advice language.

### versioning/
- _ChangeLog — Records why changes happened. Provides human-readable history with attribution.
- _VersionManager — Manages versions of templates, prompts, and rules. Enables rollback.

### visual/
- _VisualBrand — Manages visual brand identity. Stores logos, colours, and visual guidelines.
- _VisualCheck — Validates visual brand compliance. Ensures visuals match guidelines.
- _VisualManager — Manages visual assets. Coordinates visual resources across systems.
- _VisualValueAlignment — Aligns visuals with brand values. Ensures imagery supports principles.

---

## Status Key

| Prefix | Status |
|--------|--------|
| *(none)* | Approved spec |
| `*` | Pre-spec notes added |
| `_` | Basic notes only |

---

## Totals

- **Categories:** 33
- **Systems:** 166
- **Approved:** 6
- **Pre-spec Notes:** 3
- **Placeholder:** 157
