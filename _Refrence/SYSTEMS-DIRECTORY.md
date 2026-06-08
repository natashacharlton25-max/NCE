# Systems Directory

> Complete listing of all system categories and their modules with intent/role descriptions
> Last Updated: 2026-01-21

---

## access/
**Intent:** Control permissions, roles, and capabilities across brands and users.

- **BrandPermissionResolver** — Handles brand-specific permission overrides. Allows different brands to have different capability configurations.
- **CapabilityManager** — Defines what a user, brand, or role can do. Manages the capability matrix that controls actions across the system.
- **RoleManager** — Manages user roles and role hierarchies. Defines what roles exist, their inheritance, and default capabilities.

---

## ai/
**Intent:** Handle all text-based AI operations including API calls, model management, token tracking, and cost control.

- **AICaller** — Makes API calls to text AI providers (OpenAI, Anthropic, Groq, etc.). Handles retries, rate limits, and response parsing.
- **AISystemManager** — Coordinates AI text services. Orchestrates model selection, token estimation, and cost checking before calls.
- **AIWebSearch** — Performs web searches via AI-powered search APIs. Returns structured search results for content research.
- **CostBudgetManager** — Manages AI spending budgets and enforces limits. Blocks requests that would exceed daily/monthly caps.
- **CostTracker** — Records actual AI usage costs per request. Tracks spend by brand, job, and model.
- **EmbeddingService** — Generates vector embeddings from text. Cheaper than LLM calls, used for semantic search and validation.
- **ModelManager** — Manages AI model selection and fallback chains. Stores model cards with pricing, limits, and capabilities.
- **OptimizationLoop** — Analyzes feedback patterns to identify underperforming prompts. Suggests improvements for PromptBuilder.
- **TokenManager** — Estimates tokens before calls and records actual usage. Manages token budgets and alerts.

---

## ai-image/
**Intent:** Handle all AI image generation operations separate from text AI. Manage visual AI prompting, calling, and workflows.

- **AIImageCreation** — Generates images using AI providers (DALL-E, Leonardo, Stable Diffusion). Handles provider routing and style application.
- **AIVizCaller** — Makes API calls to AI image generation providers. Handles provider-specific formats and authentication.
- **AIVizManager** — Coordinates AI image generation workflows. Manages provider selection, cost tracking, and fallbacks.
- **AIVizPrompter** — Builds prompts for AI image generation. Translates brand style and visual requirements into effective prompts.
- **AIVizTemplate** — Manages reusable templates for AI image prompts. Stores style presets and brand-specific configurations.

---

## audit/
**Intent:** Provide narrative audit trails for compliance and trust.

- **AIUsageAttribution** — Tracks which AI models were used for which content and decisions. Provides transparency for compliance and disclosure requirements.
- **DecisionTimeline** — Reconstructs who did what, why, and with what AI involvement. Provides a complete audit trail for compliance and review.
- **HumanOverrideLedger** — Maintains a permanent record of all human overrides of AI decisions or system rules. Critical for compliance.

---

## brand/
**Intent:** Manage brand identity, profiles, audience segments, and brand consistency tracking.

- **AudienceManager** — Manages target audience profiles and segments. Stores demographics, psychographics, and communication preferences.
- **BrandCheck** — Validates content against brand guidelines. Ensures tone, voice, and messaging align with brand identity.
- **BrandManager** — Manages brand profiles and configurations. Central source for brand voice, values, and visual identity.
- **BrandTracker** — Tracks brand usage and consistency metrics. Monitors how brand guidelines are applied across content.

---

## checks/
**Intent:** Validate content quality across grammar, spelling, language, layout, and brand values.

- **GrammarCheck** — Checks content for grammar errors. Returns issues with suggestions for fixes.
- **LanguageCheck** — Validates language usage, style, and readability. Ensures content matches target audience level.
- **LayoutCheck** — Validates content layout and structure. Ensures sections, headings, and formatting are correct.
- **PostCreationCheckManager** — Orchestrates all post-creation quality checks. Runs grammar, spelling, values, and layout checks.
- **PostCreationQualityDecider** — Decides what to do with check results. Determines pass, retry, salvage, or manual review.
- **SpellGrammarCheck** — Combined spelling and grammar checking. Single pass for both error types.
- **SpellingCheck** — Checks content for spelling errors. Supports custom dictionaries and brand terminology.
- **TransformationCheck** — Validates content transformations. Ensures adapted content maintains core message integrity.
- **ValuesCheck** — Validates content against brand values. Ensures messaging aligns with brand principles.

---

## colours/
**Intent:** Handle colour palette creation, mixing, and design token management.

- **ColourMaker** — Creates colour palettes from brand inputs. Generates harmonious colour schemes based on brand identity.
- **ColourMix** — Mixes and blends colours for harmonious palettes. Handles colour theory calculations and accessibility.
- **ColourToken** — Manages colour tokens for design systems. Exports colours in CSS variables, JSON, and other formats.

---

## content/
**Intent:** Handle content creation, management, adaptation, and lifecycle from generation through archival.

- **Content** — Core content generation and management. Central module for content operations.
- **ContentAdapterEngine** — Repurposes content across formats. Converts workbooks to social posts, emails to blogs, etc.
- **ContentAlignment** — Aligns content with brand strategy. Ensures content serves strategic objectives.
- **ContentAlignmentCheck** — Validates content-brand alignment. Scores how well content matches brand positioning.
- **ContentLifecycleManager** — Tracks content through entire lifecycle. Manages draft → QA → published → archived states.
- **ContentManager** — Manages content storage and retrieval. Handles content versioning and metadata.
- **ContentPersonalizer** — Adapts content for specific audiences. Adjusts tone, vocabulary, and examples per segment.
- **ContentSalvageModule** — Recovers partial content when generation fails. Enables graceful degradation and retry.
- **ContentTextAssessor** — Assesses text quality and readability. Scores content on clarity, engagement, and effectiveness.
- **ContentValidator** — Validates content against rules. Enforces content policies and constraints.
- **Email** — Generates email content. Creates email sequences and campaigns.
- **EmailLit** — Email generation via Emailit service. Alternative email sending provider.
- **ExportManager** — Manages content export workflows. Handles multi-format export and packaging.
- **LegalDocCreator** — Creates legal documents from templates. Generates terms, policies, and agreements.
- **PDFMaker** — Creates PDF documents. Handles PDF generation with proper formatting.
- **VariantGenerator** — Generates multiple content variants for A/B testing. Creates tone, length, and approach variations.
- **WebsiteContent** — Generates website content. Creates pages, posts, and web copy.

---

## content-pipeline/
**Intent:** Orchestrate content publishing workflows from generation through multi-platform distribution.

- **MultiFormatOrchestrator** — Coordinates same content across multiple output formats. Manages PDF, DOCX, HTML, Canva rendering.
- **OutputFormatterOrchestrator** — Applies format-specific optimizations. Adds tracking pixels, metadata, and platform requirements.
- **PublishingOrchestrator** — Master orchestrator for publishing workflow. Coordinates rendering, uploads, and distribution.

---

## explainability/
**Intent:** Make AI decisions transparent and auditable.

- **DecisionExplainer** — Surfaces why AI chose a model, rejected content, or altered output. Provides human-readable explanations for trust and audits.
- **ModelChoiceRationale** — Explains why the system selected a specific AI model for a task. Surfaces cost, capability, and availability factors.
- **PromptDiffViewer** — Shows what changed in prompts — before/after condensation, variable substitution, and optimization.

---

## feedback/
**Intent:** Collect, analyze, and apply user feedback to improve content generation quality.

- **FeedbackAnalyzer** — Converts feedback into actionable insights. Identifies recurring issues and pattern weaknesses.
- **FeedbackCollector** — Captures user edits, rejections, and approvals. Normalizes feedback signals for analysis.
- **PromptFeedbackApplier** — Closes the learning loop. Suggests prompt and rule changes based on feedback patterns.

---

## governance/
**Intent:** Enforce rules, policies, and compliance across all content and AI operations.

- **ComplianceChecker** — Enforces legal, ethical, and safety constraints. Blocks unsafe or disallowed outputs.
- **PolicyRegistry** — Stores and versions active policies. Resolves applicable policies per context.
- **RulesEngine** — Central rule evaluation engine. Single source of truth for "is this allowed?" decisions.

---

## image/
**Intent:** Handle non-AI image operations including browsing, collections, rendering, and optimization.

- **AssetOptimizer** — Converts uploaded assets to optimized formats. Handles WebP conversion and aspect ratio adjustment.
- **ImageAssetLibrary** — Catalogs AI-generated images for discovery. Tracks usage and identifies reuse opportunities.
- **ImageBrowser** — Browses and searches image libraries. Finds stock photos and brand assets.
- **ImageCollections** — Manages image collections and organization. Groups and tags images for easy retrieval.
- **ImageFieldRenderer** — Renders image fields in templates. Handles image placement and sizing.
- **ImageRenderer** — Renders and processes images. Applies effects, overlays, and compositing.
- **ImageTemplate** — Applies visual templates to images. Adds brand overlays and consistent styling.

---

## insights/
**Intent:** Convert metrics into strategic recommendations.

- **PatternDetector** — Analyzes system metrics and feedback to identify recurring patterns. Converts raw data into actionable pattern insights.
- **RecommendationEmitter** — Converts detected patterns into actionable recommendations. Suggests specific improvements for templates, prompts, and workflows.
- **StrategySignalGenerator** — Produces high-level strategic signals for decision makers. Aggregates insights into board-level intelligence.

---

## integrations/
**Intent:** Connect to external platforms and APIs with standardized adapters and webhook handling.

- **CanvaIntegration** — Integrates with Canva API. Syncs assets and manages Canva designs.
- **GoogleIntegration** — Integrates with Google services. Handles Drive, Docs, Sheets, and Slides.
- **IntegrationAdapter** — Base adapter pattern for external APIs. Abstracts auth, retries, and rate limits.
- **WebhookReceiver** — Handles inbound webhook events. Validates and routes platform callbacks.
- **WebScraper** — Scrapes web content for research. Extracts text and data from URLs.
- **YouTubeAPI** — Integrates with YouTube API. Manages video metadata and channel data.

---

## logo/
**Intent:** Handle logo generation and management as a specialized visual asset type.

- **LogoMaker** — Creates and manages logo assets. Generates logo variations and formats.

---

## memory/
**Intent:** Store validated, long-term system knowledge.

- **KnowledgeCurator** — Persists validated knowledge, not just raw embeddings. Manages the system's long-term memory of learned patterns and validated truths.
- **KnowledgeExpiryManager** — Handles expiration and archival of knowledge entries. Ensures the system doesn't rely on stale or outdated knowledge.
- **MemoryConfidenceScorer** — Evaluates the confidence level of knowledge entries. Determines how much the system should rely on stored knowledge.

---

## observability/
**Intent:** Monitor system health, AI quality, costs, and provide end-to-end tracing.

- **AIQualityMonitor** — Evaluates AI output quality over time. Tracks hallucinations, drift, and failure patterns.
- **CostMonitor** — Provides financial observability. Aggregates costs and detects abnormal spend.
- **MetricsCollector** — Collects system metrics. Tracks timings, counts, and throughput.
- **TraceLogger** — Provides end-to-end execution tracing. Correlates logs across systems with trace IDs.

---

## orchestration/
**Intent:** Coordinate system operations including job queues, scheduling, caching, and policy enforcement.

- **AnalyticsHandler** — Collects and processes system analytics. Generates usage reports and insights.
- **BatchProcessor** — Processes batched operations. Handles bulk jobs efficiently.
- **CacheHandler** — Manages system caching. Controls TTL, invalidation, and cache tiers.
- **FeatureFlags** — Controls feature availability. Enables/disables features per brand or environment.
- **GlobalRules** — Enforces system-wide rules. Applies universal constraints across all operations.
- **JobSimulator** — Estimates cost and time before execution. Runs dry runs for batch validation.
- **MigrationHandler** — Handles data migrations. Manages schema updates and data transforms.
- **Notification** — Sends system notifications. Delivers alerts via email, Slack, or webhooks.
- **Orchestrator** — Coordinates pipeline execution. Manages job flow and task dependencies.
- **PerformanceOptimizer** — Monitors and optimizes system performance. Identifies bottlenecks and recommends parallelization.
- **PolicyEngine** — Enforces policies and permissions. Runs pre-flight checks before operations.
- **QueueHandler** — Manages job queues. Handles priority, throttling, and concurrency.
- **RateLimiter** — Enforces API rate limits. Prevents exceeding provider quotas.
- **RequestHandler** — Routes incoming requests. Entry point for API, UI, and webhook requests.
- **Scheduler** — Schedules recurring and delayed jobs. Provides cron-style scheduling.
- **StatusHandler** — Tracks and reports job status. Emits real-time progress for UI.
- **TestHandler** — Manages test execution. Provides mock data and integration testing.

---

## publishing/
**Intent:** Control the final release gate and manage content distribution to platforms.

- **DistributionManager** — Pushes content to platforms. Tracks delivery success and handles retries.
- **PublishController** — Final release gate for content. Decides when content is publishable.

---

## recovery/
**Intent:** Enable graceful recovery from failures with user control.

- **FailureNarrator** — Translates technical failures into human-readable explanations. Presents users with clear context about what went wrong and recovery options.
- **RecoveryStateBuilder** — Constructs recovery state from failed or interrupted workflows. Enables graceful continuation from the last successful step.
- **ResumePointManager** — Manages resume points in workflows. Allows users to continue from specific checkpoints rather than restarting entirely.

---

## renderers/
**Intent:** Convert content to specific output formats for different platforms and use cases.

- **CanvaRenderer** — Renders content to Canva designs. Creates slides and visual assets.
- **DOCXRenderer** — Renders content to DOCX format. Creates editable Word documents.
- **GoogleDocsRenderer** — Renders content to Google Docs. Creates cloud-native documents.
- **GoogleSheetsRenderer** — Renders content to Google Sheets. Creates spreadsheets and data tables.
- **GoogleSlidesRenderer** — Renders content to Google Slides. Creates presentations.
- **HTMLRenderer** — Renders content to HTML. Creates responsive web content.
- **MarkdownRenderer** — Renders content to Markdown. Creates documentation and blog posts.
- **PDFRenderer** — Renders content to PDF. Creates print-ready documents.

---

## review/
**Intent:** Enable human oversight with review queues, approvals, and manual override capabilities.

- **ApprovalManager** — Handles final authority decisions. Accepts, rejects, or requests changes.
- **ManualOverride** — Allows humans to override AI/rules. Captures rationale for audit trail.
- **ReviewQueue** — Queues items needing human approval. Prioritizes by severity and risk.

---

## services/
**Intent:** Provide core shared services for file management, authentication, database, and data operations.

- **APIKeyManager** — Manages API keys and credentials. Secure storage for external service authentication.
- **AuthHandler** — Handles authentication and sessions. Manages Google OAuth and user access.
- **ContentMetadataManager** — Tracks content provenance and metadata. Links content to its generation context.
- **DatabaseHandler** — Manages database operations. Central interface for SQLite queries.
- **DataSanitizer** — Scrubs PII from prompts before API calls. Ensures compliance with data protection.
- **GeneratedContentCatalog** — Indexes generated content for discovery. Enables search and reuse of past content.
- **IngestionEngine** — Ingests external data sources. Imports and normalizes external content.
- **Librarian** — Searches and recommends library entries. Provides discovery for templates, tones, and models.
- **PromptBuilder** — Assembles prompts from templates. Resolves variables and applies constraints.
- **PromptCondenser** — Condenses prompts to fit token limits. Summarizes context while preserving meaning.
- **PythonRunner** — Executes Python scripts. Enables custom Python-based operations.
- **ResearchTools** — Provides research and lookup tools. Supports content research workflows.
- **VectorStore** — Stores and retrieves vector embeddings. Enables semantic search and similarity matching.
- **FileManager** — Reads files from the filesystem. Public API for all file read operations.

---

## social/
**Intent:** Plan, create, publish, and analyze social media content across platforms.

- **SocialMediaAnalytics** — Analyzes social media performance. Tracks engagement and reach metrics.
- **SocialMediaCollator** — Collates social media content. Gathers posts for scheduling and review.
- **SocialMediaManager** — Manages social media publishing. Handles posting to platforms.
- **SocialMediaPlanner** — Plans social media campaigns. Creates content calendars and strategies.

---

## state/
**Intent:** Manage system state, execution context, and workflow transitions.

- **ContextAssembler** — Assembles execution context. Merges brand, audience, theme, and job context.
- **StateManager** — Canonical source of truth for state. Tracks workflow and enables recovery/rollback.
- **StateTransitionValidator** — Prevents illegal state transitions. Validates workflow moves.

---

## svg/
**Intent:** Create and manipulate SVG graphics for scalable visual assets.

- **SVGDrawer** — Creates and manipulates SVG graphics. Generates scalable vector assets.

---

## system/
**Intent:** Provide core infrastructure for writing, logging, parsing, archiving, and file system operations.

- **Archivist** — Archives and versions stored data. Manages data retention and recovery.
- **Builders** — Creates file paths and folders. Handles path construction and directory scaffolding.
- **FailureHandler** — Handles errors and recovery. Manages retries, fallbacks, and escalation.
- **GarbageCollector** — Cleans up temp files and stale exports. Prevents disk bloat.
- **Logger** — Logs system events and errors. Central logging with temp and SQLite storage.
- **Parsers** — Parses and transforms data formats. Handles JSON, CSV, Markdown, and AI responses.
- **Writer** — Writes data to the filesystem. Only module that writes system data.

---

## template/
**Intent:** Manage content templates, outlines, and theme generation for structured content creation.

- **OutlineGenerator** — Generates content outlines from themes. Creates section structures with truth strategies.
- **TemplateEngine** — Executes templates to produce content. Renders sections with AI generation.
- **TemplateFactory** — Creates template instances. Builds templates from definitions.
- **TemplateLibraryManager** — Manages template versions and evolution. Tracks changes and enables rollback.
- **ThemeGenerator** — Generates themes from brand and audience. Creates strategic messaging frameworks.

---

## typography/
**Intent:** Manage fonts, typography rules, and text styling for brand consistency.

- **FontManager** — Manages font assets and pairings. Handles font loading and recommendations.
- **Typography** — Defines typography rules. Sets font sizes, line heights, and spacing.
- **TypographyManager** — Manages typography configurations. Applies typography across content.

---

## verification/
**Intent:** Post-creation verification and validation of content quality and compliance.

- **AccessibilityValidator** — Validates WCAG compliance, readability scores, and alt text. Ensures content meets accessibility standards.
- **ContentIntegrityValidator** — Checks for narrative coherence and contradictions. Ensures content doesn't contradict itself across sections.
- **SemanticCoherenceValidator** — Checks logical flow between sections. Ensures content makes sense as a complete piece.
- **SignpostingValidator** — Verifies appropriate crisis signposting is included. Ensures content directs to professional support where appropriate.
- **ScopeValidator** — Ensures content stays within educational/peer support scope. Flags content that might read as clinical advice.

---

## versioning/
**Intent:** Track changes, manage versions, and maintain audit trails for templates, prompts, and rules.

- **ChangeLog** — Records why changes happened. Provides human-readable history with attribution.
- **VersionManager** — Manages versions of templates, prompts, and rules. Enables rollback.

---

## visual/
**Intent:** Manage visual brand identity, visual checks, and visual asset coordination.

- **VisualBrand** — Manages visual brand identity. Stores logos, colours, and visual guidelines.
- **VisualCheck** — Validates visual brand compliance. Ensures visuals match brand guidelines.
- **VisualManager** — Manages visual assets. Coordinates visual resources across systems.
- **VisualValueAlignment** — Aligns visuals with brand values. Ensures imagery supports brand principles.

---

## Summary

| Category | Systems | Intent |
|----------|---------|--------|
| access | 3 | Permissions & capabilities |
| ai | 9 | Text AI operations |
| ai-image | 5 | AI image generation |
| audit | 3 | Audit trails |
| brand | 4 | Brand management |
| checks | 9 | Quality validation |
| colours | 3 | Colour management |
| content | 17 | Content creation |
| content-pipeline | 3 | Publishing workflows |
| explainability | 3 | AI transparency |
| feedback | 3 | Feedback loops |
| governance | 3 | Rules & compliance |
| image | 7 | Image handling |
| insights | 3 | Strategic intelligence |
| integrations | 6 | External APIs |
| logo | 1 | Logo management |
| memory | 3 | Long-term knowledge |
| observability | 4 | System monitoring |
| orchestration | 17 | System coordination |
| publishing | 2 | Release & distribution |
| recovery | 3 | Workflow recovery |
| renderers | 8 | Output formats |
| review | 3 | Human oversight |
| services | 14 | Core services |
| social | 4 | Social media |
| state | 3 | State management |
| svg | 1 | SVG graphics |
| system | 7 | Core infrastructure |
| template | 5 | Template system |
| typography | 3 | Typography |
| verification | 5 | Post-creation checks |
| versioning | 2 | Version control |
| visual | 4 | Visual brand |
| **TOTAL** | **166** | |
