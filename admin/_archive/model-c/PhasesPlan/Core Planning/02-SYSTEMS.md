# Systems Index

> All systems defined by function. Each system contains: Manager → Workers → Functions

---

## Systems Overview (26 Total)

| # | System | Function |
|---|--------|----------|
| 1 | [Orchestration](#1-orchestration-system) | Job coordination, queues, scheduling, status |
| 2 | [AI Provider](#2-ai-provider-system) | Calls to AI APIs (OpenAI, Claude, Gemini, Groq, etc.) |
| 3 | [Prompt](#3-prompt-system) | Prompt building, templates, variables |
| 4 | [Brand](#4-brand-system) | Brand identity, tracking, audiences |
| 5 | [Colour](#5-colour-system) | Palette generation, mixing, tokens, accessibility |
| 6 | [Typography](#6-typography-system) | Font selection, pairing, scales |
| 7 | [Theme](#7-theme-system) | Design tokens, visual systems |
| 8 | [Image Creation](#8-image-creation-system) | AI image gen, logos, SVG |
| 9 | [Image Storage](#9-image-storage-system) | Collections, browse, curate |
| 10 | [Text Generation](#10-text-generation-system) | Content writing, copy |
| 11 | [Language Check](#11-language-check-system) | Grammar, spelling, quality |
| 12 | [Document](#12-document-system) | PDF, legal docs |
| 13 | [Output](#13-output-system) | Exports, formatting, delivery |
| 14 | [Email](#14-email-system) | Compose, deliver |
| 15 | [Calendar](#15-calendar-system) | Scheduling, content calendars, events |
| 16 | [Publishing](#16-publishing-system) | Post to platforms (social, CMS) |
| 17 | [Metrics](#17-metrics-system) | Analytics across all sources |
| 18 | [Research](#18-research-system) | Web search, scraping, synthesis |
| 19 | [Database](#19-database-system) | SQLite, queries, migrations |
| 20 | [File](#20-file-system) | Read, write, storage |
| 21 | [Cache](#21-cache-system) | Caching, temp data |
| 22 | [Logger](#22-logger-system) | Logging, log queries |
| 23 | [API](#23-api-system) | Keys, rates, requests |
| 24 | [Auth](#24-auth-system) | Authentication, permissions |
| 25 | [Cost](#25-cost-system) | Budgets, tracking |
| 26 | [Policy](#26-policy-system) | Rules, gates, validation, errors |

---

## System Dependencies

How systems communicate with each other. Systems never call each other directly - they go through cross-cutting services or shared data in SQLite.

| System | Depends On | For |
|--------|-----------|-----|
| Text Generation | AI Provider | Making AI calls for content |
| Text Generation | Brand | Getting brand voice and tone |
| Text Generation | Prompt | Building prompts from templates |
| Colour | AI Provider | Palette generation via AI |
| Colour | Brand | Brand values for colour selection |
| Typography | AI Provider | Font pairing suggestions |
| Typography | Brand | Brand personality for font matching |
| Theme | Colour | Colour tokens |
| Theme | Typography | Font tokens |
| Image Creation | AI Provider | DALL-E, Midjourney, Stable Diffusion |
| Image Creation | Brand | Style and visual guidelines |
| Publishing | Calendar | Scheduling posts |
| Publishing | Image Storage | Getting images to post |
| Email | Text Generation | Email body content |
| Document | Brand | Branding for PDFs |
| Research | AI Provider | Synthesis via AI |
| Language Check | AI Provider | Grammar checking (optional) |

**Note:** All systems use Database, Cache, Log, File managers implicitly via Cross-Cutting Services.

---

## Task Routing

Task Manager routes tasks to systems based on task type.

| Task Type | System | Action |
|-----------|--------|--------|
| `fetch-brand` | Brand | fetch-values |
| `generate-palette` | Colour | generate-palette |
| `check-accessibility` | Colour | check-accessibility |
| `generate-tokens` | Theme | generate-tokens |
| `select-fonts` | Typography | select-fonts |
| `generate-tagline` | Text Generation | generate-tagline |
| `generate-blog-post` | Text Generation | generate-content |
| `generate-social-post` | Text Generation | generate-content |
| `check-grammar` | Language Check | check |
| `generate-image` | Image Creation | generate |
| `create-logo` | Image Creation | create-logo |
| `fetch-stock-image` | Image Storage | fetch |
| `create-pdf` | Document | generate-pdf |
| `send-email` | Email | send |
| `post-instagram` | Publishing | post-instagram |
| `post-twitter` | Publishing | post-twitter |
| `schedule-post` | Calendar | schedule |
| `web-search` | Research | search |
| `ai-completion` | AI Provider | completion |
| `ai-image` | AI Provider | image-generation |

---

## System Details

### 1. Orchestration System
**Function:** Central coordination of jobs, task queues, and scheduling

**Clarification:** This system IS the implementation of Task Manager, Batch Manager, and Worker Manager described in [01-ARCHITECTURE.md](01-ARCHITECTURE.md). The "cross-cutting managers" are conceptual roles; this system provides the actual workers that perform those roles.

| Component | Type | Responsibility |
|-----------|------|----------------|
| OrchestrationManager | Manager | Routes jobs, coordinates all orchestration workers |
| JobWorker | Worker | Creates, updates, completes jobs (Job Manager role) |
| TaskWorker | Worker | Breaks jobs into tasks, manages dependencies (Task Manager role) |
| BatchWorker | Worker | Groups tasks by type, manages batch execution (Batch Manager role) |
| QueueWorker | Worker | Manages task queue, ordering, priorities |
| ScheduleWorker | Worker | Handles timed/recurring tasks |
| StatusWorker | Worker | Tracks progress, timeouts, checkpoints |
| RecoveryWorker | Worker | Handles system restart, resumes interrupted work |

**Config:** `orchestration.json` - queue limits, priorities, timeouts, concurrency settings

---

### 2. AI Provider System
**Function:** All interactions with AI APIs

| Component | Type | Responsibility |
|-----------|------|----------------|
| AIManager | Manager | Selects provider, handles failures, fallbacks |
| OpenAIWorker | Worker | Calls OpenAI API |
| ClaudeWorker | Worker | Calls Anthropic API |
| GeminiWorker | Worker | Calls Google Gemini API |
| GroqWorker | Worker | Calls Groq API |
| OpenRouterWorker | Worker | Calls OpenRouter API |
| ResponseParser | Function | Parses AI responses |
| TokenCounter | Function | Counts tokens for requests |

**Config:** `ai-provider.json` - models, endpoints, keys, fallback order

---

### 3. Prompt System
**Function:** Building and managing prompts

| Component | Type | Responsibility |
|-----------|------|----------------|
| PromptManager | Manager | Routes prompt requests |
| TemplateWorker | Worker | Loads prompt templates |
| BuilderWorker | Worker | Compiles prompts with variables |
| VariableResolver | Function | Resolves template variables |
| ConstraintApplier | Function | Applies constraints to prompts |

**Config:** `prompt.json` - template paths, default variables

---

### 4. Brand System
**Function:** Brand identity building and tracking

| Component | Type | Responsibility |
|-----------|------|----------------|
| BrandManager | Manager | Orchestrates brand building phases |
| BrandWorker | Worker | Fetches/saves brand data |
| AudienceWorker | Worker | Manages audience segments |
| TrackerWorker | Worker | Tracks build progress |
| BrandValidator | Function | Validates brand data |

**Config:** `brand.json` - phases, required fields, validation rules

---

### 5. Colour System
**Function:** Colour palette generation and management

| Component | Type | Responsibility |
|-----------|------|----------------|
| ColourManager | Manager | Routes colour requests |
| PaletteWorker | Worker | Generates palettes via AI |
| TokenWorker | Worker | Creates colour tokens |
| ColourMixer | Function | Blends colours |
| HarmonyGenerator | Function | Creates colour harmonies |
| AccessibilityChecker | Function | WCAG contrast checking |

**Config:** `colour.json` - default count, accessibility level, mood mappings

---

### 6. Typography System
**Function:** Font selection and typographic systems

| Component | Type | Responsibility |
|-----------|------|----------------|
| TypographyManager | Manager | Routes typography requests |
| FontWorker | Worker | Fetches font data, licensing |
| PairingWorker | Worker | Suggests font pairings |
| ScaleGenerator | Function | Creates type scales |
| FontMatcher | Function | Matches fonts to brand values |

**Config:** `typography.json` - font sources, scale ratios, defaults

---

### 7. Theme System
**Function:** Design tokens and visual systems

| Component | Type | Responsibility |
|-----------|------|----------------|
| ThemeManager | Manager | Coordinates theme generation |
| TokenWorker | Worker | Creates design tokens |
| ThemeWorker | Worker | Builds complete themes |
| TokenFormatter | Function | Formats tokens for platforms |
| ValueAligner | Function | Aligns visuals to brand values |

**Config:** `theme.json` - token structure, platforms, defaults

---

### 8. Image Creation System
**Function:** AI image generation, logos, SVG

| Component | Type | Responsibility |
|-----------|------|----------------|
| ImageCreationManager | Manager | Routes image creation requests |
| AIImageWorker | Worker | Calls DALL-E, Midjourney, Stable Diffusion |
| LogoWorker | Worker | Generates logo concepts |
| SVGWorker | Worker | Creates/manipulates SVG |
| ImageOptimizer | Function | Compresses, resizes |
| StyleApplier | Function | Applies brand style to images |

**Config:** `image-creation.json` - providers, sizes, styles, defaults

---

### 9. Image Storage System
**Function:** Image collections, browsing, curation

| Component | Type | Responsibility |
|-----------|------|----------------|
| ImageStorageManager | Manager | Routes storage requests |
| UnsplashWorker | Worker | Fetches from Unsplash API |
| PexelsWorker | Worker | Fetches from Pexels API |
| LocalWorker | Worker | Manages local image storage |
| CollectionWorker | Worker | Curates collections |
| ImageTagger | Function | Tags images for search |

**Config:** `image-storage.json` - API keys, storage paths, collection rules

---

### 10. Text Generation System
**Function:** Content writing and copy generation

| Component | Type | Responsibility |
|-----------|------|----------------|
| TextManager | Manager | Routes text generation requests |
| ContentWorker | Worker | Generates general content |
| WebsiteWorker | Worker | Creates website copy |
| TaglineWorker | Worker | Creates taglines, headlines |
| TextFormatter | Function | Formats output text |
| ToneAdjuster | Function | Adjusts tone to brand voice |

**Config:** `text-generation.json` - content types, lengths, tones

---

### 11. Language Check System
**Function:** Grammar, spelling, quality checking

| Component | Type | Responsibility |
|-----------|------|----------------|
| LanguageManager | Manager | Routes language checks |
| GrammarWorker | Worker | Runs grammar checks (Python) |
| SpellWorker | Worker | Runs spell checks |
| QualityWorker | Worker | Assesses text quality |
| SuggestionFormatter | Function | Formats correction suggestions |

**Config:** `language-check.json` - dictionaries, rules, severity levels

---

### 12. Document System
**Function:** PDF and legal document generation

| Component | Type | Responsibility |
|-----------|------|----------------|
| DocumentManager | Manager | Routes document requests |
| PDFWorker | Worker | Generates PDFs |
| LegalWorker | Worker | Creates legal documents |
| TemplateLoader | Function | Loads document templates |
| DataMerger | Function | Merges data into templates |

**Config:** `document.json` - templates, fonts, watermarks

---

### 13. Output System
**Function:** Exports and delivery

| Component | Type | Responsibility |
|-----------|------|----------------|
| OutputManager | Manager | Routes output requests |
| ExportWorker | Worker | Creates export packages |
| FormatWorker | Worker | Converts between formats |
| DeliveryWorker | Worker | Delivers to destinations |
| Packager | Function | Bundles outputs |

**Config:** `output.json` - export formats, delivery methods

---

### 14. Email System
**Function:** Email composition and delivery

| Component | Type | Responsibility |
|-----------|------|----------------|
| EmailManager | Manager | Routes email requests |
| ComposeWorker | Worker | Composes emails from templates |
| SendGridWorker | Worker | Sends via SendGrid |
| SMTPWorker | Worker | Sends via SMTP |
| TemplateRenderer | Function | Renders email templates |
| PersonalizationResolver | Function | Resolves personalization tokens |

**Config:** `email.json` - providers, templates, from addresses

---

### 15. Calendar System
**Function:** Scheduling and content calendars

| Component | Type | Responsibility |
|-----------|------|----------------|
| CalendarManager | Manager | Routes calendar requests |
| PlannerWorker | Worker | Creates content calendars |
| GoogleCalendarWorker | Worker | Syncs with Google Calendar |
| ScheduleWorker | Worker | Manages scheduled items |
| DateCalculator | Function | Calculates dates, recurrence |
| ConflictChecker | Function | Checks for conflicts |

**Config:** `calendar.json` - timezones, defaults, sync settings

---

### 16. Publishing System
**Function:** Post to social platforms and CMS

| Component | Type | Responsibility |
|-----------|------|----------------|
| PublishingManager | Manager | Routes publish requests |
| InstagramWorker | Worker | Posts to Instagram |
| FacebookWorker | Worker | Posts to Facebook |
| LinkedInWorker | Worker | Posts to LinkedIn |
| TwitterWorker | Worker | Posts to Twitter/X |
| TikTokWorker | Worker | Posts to TikTok |
| PinterestWorker | Worker | Posts to Pinterest |
| PostFormatter | Function | Formats posts per platform |
| HashtagGenerator | Function | Generates relevant hashtags |

**Config:** `publishing.json` - platform credentials, post limits, character counts

---

### 17. Metrics System
**Function:** Analytics and reporting

| Component | Type | Responsibility |
|-----------|------|----------------|
| MetricsManager | Manager | Routes metrics requests |
| SocialMetricsWorker | Worker | Collects social analytics |
| SystemMetricsWorker | Worker | Collects system usage |
| ReportWorker | Worker | Generates reports |
| Aggregator | Function | Aggregates metric data |
| TrendAnalyzer | Function | Analyzes trends |

**Config:** `metrics.json` - collection intervals, retention, thresholds

---

### 18. Research System
**Function:** Web search, scraping, synthesis

| Component | Type | Responsibility |
|-----------|------|----------------|
| ResearchManager | Manager | Routes research requests |
| WebSearchWorker | Worker | Searches via APIs |
| ScraperWorker | Worker | Scrapes web content |
| SynthesisWorker | Worker | Synthesizes findings via AI |
| ContentExtractor | Function | Extracts relevant content |
| SourceValidator | Function | Validates sources |

**Config:** `research.json` - search APIs, scrape limits, domains

---

### 19. Database System
**Function:** SQLite operations and queries

| Component | Type | Responsibility |
|-----------|------|----------------|
| DatabaseManager | Manager | Routes database requests |
| QueryWorker | Worker | Executes queries |
| MigrationWorker | Worker | Runs schema migrations |
| ConnectionWorker | Worker | Manages connections |
| QueryBuilder | Function | Builds SQL queries |
| ResultMapper | Function | Maps results to objects |

**Config:** `database.json` - paths, pool size, timeout

---

### 20. File System
**Function:** File read/write operations

| Component | Type | Responsibility |
|-----------|------|----------------|
| FileManager | Manager | Routes file requests |
| ReadWorker | Worker | Reads files |
| WriteWorker | Worker | Writes files |
| PathWorker | Worker | Manages paths, directories |
| PathBuilder | Function | Builds file paths |
| Validator | Function | Validates file data |

**Config:** `file.json` - base paths, allowed extensions

---

### 21. Cache System
**Function:** Caching and temporary data

| Component | Type | Responsibility |
|-----------|------|----------------|
| CacheManager | Manager | Routes cache requests |
| MemoryWorker | Worker | In-memory cache operations |
| DiskWorker | Worker | Disk cache operations |
| TTLManager | Function | Manages time-to-live |
| KeyGenerator | Function | Generates cache keys |

**Config:** `cache.json` - TTLs, size limits, tiers

---

### 22. Logger System
**Function:** Logging and log queries

| Component | Type | Responsibility |
|-----------|------|----------------|
| LoggerManager | Manager | Routes log requests |
| WriteWorker | Worker | Writes logs to storage |
| QueryWorker | Worker | Queries historical logs |
| Aggregator | Function | Aggregates log entries |
| Formatter | Function | Formats log output |

**Config:** `logger.json` - levels, retention, output paths

---

### 23. API System
**Function:** API key and request management

**Clarification:** This system provides the workers that implement API functionality. The cross-cutting **API Manager** is conceptual - this system's workers are the actual implementation:
- API Manager (cross-cutting) = KeyWorker + config loading
- Actual HTTP calls = RequestWorker
- Rate enforcement = RateLimitWorker (works with Provider Rate Limiter validator)

| Component | Type | Responsibility |
|-----------|------|----------------|
| APIManager | Manager | Routes API requests, coordinates workers |
| KeyWorker | Worker | Loads, validates, rotates API keys |
| RequestWorker | Worker | Makes actual HTTP requests |
| RateLimitWorker | Worker | Enforces rate limits per provider |
| RetryHandler | Function | Handles retry logic with backoff |
| ResponseNormalizer | Function | Normalizes API responses to common format |

**Config:** `api.json` - keys, rate limits, timeouts, retry policies

---

### 24. Auth System
**Function:** Authentication and permissions

| Component | Type | Responsibility |
|-----------|------|----------------|
| AuthManager | Manager | Routes auth requests |
| TokenWorker | Worker | Manages auth tokens |
| OAuthWorker | Worker | Handles OAuth flows |
| PermissionChecker | Function | Checks permissions |
| TokenValidator | Function | Validates tokens |

**Config:** `auth.json` - providers, token expiry, permissions

---

### 25. Cost System
**Function:** Budget management and cost tracking

| Component | Type | Responsibility |
|-----------|------|----------------|
| CostManager | Manager | Routes cost requests |
| TrackerWorker | Worker | Records costs |
| BudgetWorker | Worker | Manages budgets |
| AlertWorker | Worker | Sends cost alerts |
| Calculator | Function | Calculates costs |
| BurnRateAnalyzer | Function | Analyzes spend rate |

**Config:** `cost.json` - budgets, alert thresholds, rates

---

### 26. Policy System
**Function:** Rules, validation, and error handling

| Component | Type | Responsibility |
|-----------|------|----------------|
| PolicyManager | Manager | Routes policy checks |
| GateWorker | Worker | Checks gates before execution |
| ValidationWorker | Worker | Validates data |
| ErrorWorker | Worker | Handles errors, recovery |
| RuleEvaluator | Function | Evaluates policy rules |
| EscalationRouter | Function | Routes failures to handlers |

**Config:** `policy.json` - rules, gates, escalation levels

---

## Cross-Cutting Services

These are **not systems** - they are shared service managers and validators that all systems use to access external resources.

### Service Managers

| Manager | Responsibility | Systems Use For |
|---------|----------------|-----------------|
| **API Manager** | API keys, endpoints, auth config | Getting API configuration |
| **Database Manager** | SQLite queries, connections | All data persistence |
| **Cache Manager** | Memory/disk caching, TTL | Performance optimization |
| **Log Manager** | Logging, queries, retention | All logging output |
| **File Manager** | Filesystem operations | Reading/writing files |
| **Config Manager** | Env vars, secrets, flags | Accessing configuration |
| **Event Manager** | Pub/sub, notifications | System event coordination |

### Validators

Validators track resource usage in memory and persist to SQLite. Managers query them before making decisions.

| Validator | Tracks | Provides To Managers |
|-----------|--------|---------------------|
| **API Rate Validator** | Requests per minute per provider | Which providers have capacity |
| **Token Validator** | Token usage per model | Which models have tokens available |
| **Budget Validator** | Spend per day/week/month | How much budget remains |
| **Provider Rate Limiter** | Rate limits across ALL systems per provider | Global provider capacity |

**Note:** API Rate Validator and Provider Rate Limiter may seem similar:
- **API Rate Validator** = Per-system tracking (e.g., "AI System used 30 of its 60 RPM")
- **Provider Rate Limiter** = Cross-system tracking (e.g., "OpenAI total: 55/60 RPM across all systems")

### Job Estimator

Not a validator, but a core service that estimates job token/cost before execution.

| Component | Responsibility |
|-----------|----------------|
| **Job Estimator** | Pre-flight token/cost estimation for jobs |

See [01-ARCHITECTURE.md](01-ARCHITECTURE.md) for detailed cross-cutting service documentation.

---

## Integration Workers

External integrations (Canva, Google, YouTube) are **workers within relevant systems**, not separate systems:

| Integration | System | Worker |
|-------------|--------|--------|
| Canva | Image Creation | CanvaWorker |
| Canva | Output | CanvaExportWorker |
| Google Calendar | Calendar | GoogleCalendarWorker |
| Google Drive | File | GoogleDriveWorker |
| Google Docs | Document | GoogleDocsWorker |
| YouTube | Publishing | YouTubeWorker |
| YouTube | Metrics | YouTubeMetricsWorker |

---

## Summary

- **26 Systems** organized by function
- Each system has: **System Orchestrator → Managers → Workers → Functions**
- **7 Cross-cutting Service Managers** (API, DB, Cache, Log, File, Config, Event)
- **4 Validators** (API Rate, Token, Budget, Provider Rate Limiter)
- **1 Job Estimator** for pre-flight cost/token estimation
- External integrations are **workers** within functional systems
- All config in JSON, all logic in TypeScript, all state in SQLite

### Key Clarifications

| Overlap | Resolution |
|---------|------------|
| Orchestration System vs Task/Batch/Worker Managers | Orchestration System IS the implementation - workers perform those roles |
| API System vs API Manager (cross-cutting) | API System workers implement API Manager functionality |
| Database/Cache/Logger/File Systems vs cross-cutting managers | Systems provide workers; cross-cutting managers are the public interface |

---

## Next Documents

- [01-ARCHITECTURE.md](01-ARCHITECTURE.md) - Core architecture
- [03-STORAGE.md](03-STORAGE.md) - Database schema and config structure
- [04-FILE-STRUCTURE.md](04-FILE-STRUCTURE.md) - Project folder layout
- [05-API.md](05-API.md) - Interface layer (CLI, API, Svelte webapp)
- [06-BUILD-PLAN.md](06-MIGRATION-PLAN.md) - What to build, existing projects as reference
