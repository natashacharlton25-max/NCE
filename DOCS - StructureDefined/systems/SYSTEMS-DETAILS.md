# Systems Details

> One-line description of each system's job
> Last Updated: 2026-01-21

---

## ai/
*Text AI services - API calls, model management, token tracking, cost management*

| System | Job |
|--------|-----|
| *AICaller | Makes API calls to text AI providers (OpenAI, Anthropic, etc.) |
| *AISystemManager | Coordinates AI text services and orchestrates AI workflows |
| _AIWebSearch | Performs web searches via AI-powered search APIs |
| _CostBudgetManager | Manages AI spending budgets and limits |
| _CostTracker | Tracks and records AI usage costs |
| _ModelManager | Manages AI model selection, fallbacks, and configurations |
| _TokenManager | Tracks token usage and estimates for AI calls |

---

## ai-image/
*AI image generation - prompting, calling, managing visual AI workflows*

| System | Job |
|--------|-----|
| *AIImageCreation | Generates images using AI providers (DALL-E, Leonardo, etc.) |
| _AIVizCaller | Makes API calls to AI image generation providers |
| _AIVizManager | Coordinates AI image generation workflows |
| _AIVizPrompter | Builds prompts for AI image generation |
| _AIVizTemplate | Manages reusable templates for AI image prompts |

---

## brand/
*Brand management - brand profiles, audience management, brand tracking*

| System | Job |
|--------|-----|
| _AudienceManager | Manages target audience profiles and segments |
| _BrandCheck | Validates content against brand guidelines |
| _BrandManager | Manages brand profiles and configurations |
| _BrandTracker | Tracks brand usage and consistency metrics |

---

## checks/
*Quality checks - grammar, spelling, language, layout, values validation*

| System | Job |
|--------|-----|
| _GrammarCheck | Checks content for grammar errors |
| _LanguageCheck | Validates language usage and style |
| _LayoutCheck | Validates content layout and structure |
| _PostCreationCheckManager | Orchestrates post-creation quality checks |
| _SpellGrammarCheck | Combined spelling and grammar checking |
| _SpellingCheck | Checks content for spelling errors |
| _TransformationCheck | Validates content transformations |
| _ValuesCheck | Validates content against brand values |

---

## colours/
*Colour systems - colour creation, mixing, token management*

| System | Job |
|--------|-----|
| _ColourMaker | Creates colour palettes from brand inputs |
| _ColourMix | Mixes and blends colours for harmonious palettes |
| _ColourToken | Manages colour tokens for design systems |

---

## content/
*Content creation - content management, email, PDF, legal docs, exports*

| System | Job |
|--------|-----|
| _Content | Core content generation and management |
| _ContentAlignment | Aligns content with brand strategy |
| _ContentAlignmentCheck | Validates content-brand alignment |
| _ContentManager | Manages content lifecycle and storage |
| _ContentTextAssessor | Assesses text quality and readability |
| _ContentValidator | Validates content against rules |
| _Email | Generates email content |
| _EmailLit | Email generation via Emailit service |
| _ExportManager | Manages content export workflows |
| _LegalDocCreator | Creates legal documents from templates |
| _PDFMaker | Creates PDF documents |
| _WebsiteContent | Generates website content |

---

## image/
*Image handling - browsing, collections, rendering, templates (non-AI)*

| System | Job |
|--------|-----|
| _ImageBrowser | Browses and searches image libraries |
| _ImageCollections | Manages image collections and organization |
| _ImageFieldRenderer | Renders image fields in templates |
| _ImageRenderer | Renders and processes images |
| _ImageTemplate | Applies visual templates to images |

---

## integrations/
*External APIs - Canva, Google, YouTube, web scraping*

| System | Job |
|--------|-----|
| _CanvaIntegration | Integrates with Canva API |
| _GoogleIntegration | Integrates with Google services |
| _WebScraper | Scrapes web content |
| _YouTubeAPI | Integrates with YouTube API |

---

## logo/
*Logo generation and management*

| System | Job |
|--------|-----|
| _LogoMaker | Creates and manages logo assets |

---

## orchestration/
*System orchestration - handlers, queues, scheduling, policies, analytics*

| System | Job |
|--------|-----|
| _AnalyticsHandler | Collects and processes system analytics |
| _BatchProcessor | Processes batched operations |
| _CacheHandler | Manages system caching |
| _FeatureFlags | Controls feature availability |
| _GlobalRules | Enforces system-wide rules |
| _MigrationHandler | Handles data migrations |
| _Notification | Sends system notifications |
| _Orchestrator | Coordinates pipeline execution |
| _PolicyEngine | Enforces policies and permissions |
| _QueueHandler | Manages job queues |
| _RateLimiter | Enforces API rate limits |
| _RequestHandler | Routes incoming requests |
| _Scheduler | Schedules recurring jobs |
| _StatusHandler | Tracks and reports job status |
| _TestHandler | Manages test execution |

---

## renderers/
*Output renderers - PDF, DOCX, HTML, Markdown, Google Docs/Sheets/Slides, Canva*

| System | Job |
|--------|-----|
| _CanvaRenderer | Renders content to Canva |
| _DOCXRenderer | Renders content to DOCX format |
| _GoogleDocsRenderer | Renders content to Google Docs |
| _GoogleSheetsRenderer | Renders content to Google Sheets |
| _GoogleSlidesRenderer | Renders content to Google Slides |
| _HTMLRenderer | Renders content to HTML |
| _MarkdownRenderer | Renders content to Markdown |
| _PDFRenderer | Renders content to PDF |

---

## services/
*Core services - file management, auth, database, prompts, libraries*

| System | Job |
|--------|-----|
| _APIKeyManager | Manages API keys and credentials |
| _AuthHandler | Handles authentication and sessions |
| _DatabaseHandler | Manages database operations |
| _IngestionEngine | Ingests external data sources |
| _Librarian | Searches and recommends library entries |
| _PromptBuilder | Assembles prompts from templates |
| _PromptCondenser | Condenses prompts to fit token limits |
| _PythonRunner | Executes Python scripts |
| _ResearchTools | Provides research and lookup tools |
| FileManager | Reads files from the filesystem |

---

## social/
*Social media - planning, management, collation, analytics*

| System | Job |
|--------|-----|
| _SocialMediaAnalytics | Analyzes social media performance |
| _SocialMediaCollator | Collates social media content |
| _SocialMediaManager | Manages social media publishing |
| _SocialMediaPlanner | Plans social media campaigns |

---

## svg/
*SVG drawing and manipulation*

| System | Job |
|--------|-----|
| _SVGDrawer | Creates and manipulates SVG graphics |

---

## system/
*Core infrastructure - writing, logging, parsing, archiving, building*

| System | Job |
|--------|-----|
| _Archivist | Archives and versions stored data |
| _Builders | Creates file paths and folders |
| _FailureHandler | Handles errors and recovery |
| _Logger | Logs system events and errors |
| _Parsers | Parses and transforms data formats |
| Writer | Writes data to the filesystem |

---

## template/
*Template system - template engine, factory, outline and theme generation*

| System | Job |
|--------|-----|
| _TemplateFactory | Creates template instances |
| OutlineGenerator | Generates content outlines from themes |
| TemplateEngine | Executes templates to produce content |
| ThemeGenerator | Generates themes from brand and audience |

---

## typography/
*Typography - font management, typography rules and management*

| System | Job |
|--------|-----|
| _FontManager | Manages font assets and pairings |
| _Typography | Defines typography rules |
| _TypographyManager | Manages typography configurations |

---

## verification/
*Post-creation verification and validation*

*(No systems yet)*

---

## visual/
*Visual brand - brand visuals, visual checks, visual management*

| System | Job |
|--------|-----|
| _VisualBrand | Manages visual brand identity |
| _VisualCheck | Validates visual brand compliance |
| _VisualManager | Manages visual assets |
| _VisualValueAlignment | Aligns visuals with brand values |
