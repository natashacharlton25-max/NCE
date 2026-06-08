# Module Index

> **Complete catalog of NCE system modules**
> **Last updated:** 2026-01-17

---

## Module Categories

| Category | Count | Description |
|----------|-------|-------------|
| Core | 1 | Central orchestration |
| Managers | 6 | Domain-scoped coordinators |
| Trackers | 1 | Status tracking modules |
| Service | 10 | Business logic services |
| System Service | 26 | Infrastructure and utilities |
| Pipeline | 15 | Content generation pipelines |
| Integration | 12 | External API integrations |

**Total: 71 modules**

---

## Core Module

| Module | Purpose | Type |
|--------|---------|------|
| [Orchestrator](ORCHESTRATOR.md) | Chain coordination, job management | Core |

---

## Managers

> **Managers are domain-scoped coordinators** - they sit between Orchestrator and Modules, handling cross-module coordination within their domain. See [MANAGERS-OVERVIEW.md](MANAGERS-OVERVIEW.md) for architecture.

| Manager | Domain | Modules Coordinated | File |
|---------|--------|---------------------|------|
| [BrandManager](BRANDMANAGER.md) | Brand building/editing | Brand, AICaller, Writer, Parsers, BrandTracker | Documented |
| TypographyManager | Font selection/management | Typography, FontManager | Planned |
| VisualManager | Colors, images, visual assets | ColourMaker, ColourMix, ImageCollections | Planned |
| ContentManager | Content generation | Content, AICaller, LanguageCheck, Parsers | Planned |
| ExportManager | Output/export operations | PDFMaker, Export modules | Planned |
| SocialManager | Social media operations | SocialMedia-Planner, SocialMedia-Manager | Planned |

**Key Rule:** Managers don't call other managers directly - they signal Orchestrator for cross-domain work.

---

## Trackers

> **Trackers monitor status and completion** - they provide status checks for PolicyEngine gates.

| Tracker | Purpose | File |
|---------|---------|------|
| [BrandTracker](BRANDTRACKER.md) | Track brand build progress, section completion, validation status | Documented |

---

## Service Modules

| Module | Purpose | File |
|--------|---------|------|
| [PromptBuilder](PROMPTBUILDER.md) | Compile prompts from templates + refs | Service |
| [AICaller](AICALLER.md) | Single gateway for all AI API calls | Service |
| [Librarian](LIBRARIAN.md) | Search, discover, recommend from repos | Service |
| [FileManager](FILEMANAGER.md) | Direct file reads by ID | Service |
| [TokenManager](TOKENMANAGER.md) | Token estimation, budgets, usage | Service |
| [ModelManager](MODELMANAGER.md) | Model selection and fallbacks | Service |
| [Templates](TEMPLATES.md) | Central template management | Service |
| [AudienceManager](AUDIENCEMANAGER.md) | Target audience definition/segmentation | Service |
| [ContentAlignment](CONTENTALIGNMENT.md) | Ensure content aligns with brand/audience | Service |
| [FontManager](FONTMANAGER.md) | Font asset management and licensing | Service |

---

## System Service Modules

| Module | Purpose | File |
|--------|---------|------|
| [PolicyEngine](POLICYENGINE.md) | "Can this run?" gate - permissions, costs, safety | System Service |
| [AISystemManager](AISYSTEMMANAGER.md) | Coordinate AI-related modules | System Service |
| [FailureHandler](FAILUREHANDLER.md) | 4-level escalation, self-healing | System Service |
| [SuccessHandler](SUCCESSHANDLER.md) | Job completion, cleanup, post-actions | System Service |
| [Archivist](ARCHIVIST.md) | Validate and save to repos | System Service |
| [Writer](WRITER.md) | Low-level file writes | System Service |
| [Logger](LOGGER.md) | System-wide logging | System Service |
| [APIKeyManager](APIKEYMANAGER.md) | Secure API key access | System Service |
| [Parsers](PARSERS.md) | Parse/validate data formats | System Service |
| [Builders](BUILDERS.md) | Scaffold structures from templates | System Service |
| [Notification](NOTIFICATION.md) | All human notifications | System Service |
| [Scheduler](SCHEDULER.md) | Timed jobs, recurring tasks | System Service |
| [BatchProcessor](BATCHPROCESSOR.md) | Large batch processing with rate limiting | System Service |
| [CostBudgetManager](COSTBUDGETMANAGER.md) | Track costs, manage budgets | System Service |
| [CostTracker](COSTTRACKER.md) | Real-time cost tracking and monitoring | System Service |
| [RateLimiter](RATELIMITER.md) | Centralized rate limiting for API calls | System Service |
| [StatusHandler](STATUSHANDLER.md) | Process status and timeout tracking | System Service |
| [QueueHandler](QUEUEHANDLER.md) | Job queue management and priority | System Service |
| [CacheHandler](CACHEHANDLER.md) | System-wide caching | System Service |
| [RequestHandler](REQUESTHANDLER.md) | Standardized HTTP request management | System Service |
| [AuthHandler](AUTHHANDLER.md) | Authentication management (future) | System Service |
| [AnalyticsHandler](ANALYTICSHANDLER.md) | Usage analytics and patterns | System Service |
| [MigrationHandler](MIGRATIONHANDLER.md) | Data/schema migrations | System Service |
| [TestHandler](TESTHANDLER.md) | Automated testing | System Service |
| [FeatureFlags](FEATUREFLAGS.md) | Feature toggle management | System Service |
| [LanguageCheck](LANGUAGECHECK.md) | Grammar and spelling (Python) | System Service |

---

## Pipeline Modules

| Module | Purpose | File |
|--------|---------|------|
| [Content](CONTENT.md) | Text content generation | Pipeline |
| [WebsiteContent](WEBSITECONTENT.md) | Website page content | Pipeline |
| [Email](EMAIL.md) | Compose emails from templates | Pipeline |
| [PDFMaker](PDFMAKER.md) | PDF document generation | Pipeline |
| [SVGDrawer](SVGDRAWER.md) | SVG graphics generation | Pipeline |
| [AIImageCreation](AIIMAGECREATION.md) | AI image generation | Pipeline |
| [SocialMediaPlanner](SOCIALMEDIA-PLANNER.md) | Content calendar planning | Pipeline |
| [ColourToken](COLOURTOKEN.md) | Color palettes and design tokens | Pipeline |
| [ColourMix](COLOURMIX.md) | Color blending and harmonies | Pipeline |
| [ColourMaker](COLOURMAKER.md) | Create colors from moods/values | Pipeline |
| [LogoMaker](LOGOMAKER.md) | Logo generation and variations | Pipeline |
| [Typography](TYPOGRAPHY.md) | Font selection and type systems | Pipeline |
| [VisualBrand](VISUALBRAND.md) | Complete visual brand systems | Pipeline |
| [ImageTemplate](IMAGETEMPLATE.md) | Branded image templates | Pipeline |
| [LegalDocCreator](LEGALDOCCREATOR.md) | Legal document generation | Pipeline |

---

## Integration Modules

| Module | Purpose | File |
|--------|---------|------|
| [CanvaIntegration](CANVA-INTEGRATION.md) | Canva API operations | Integration |
| [GoogleIntegration](GOOGLE-INTEGRATION.md) | Google Calendar/Drive | Integration |
| [Emaillit](EMAILLIT.md) | Email delivery (API/SMTP) | Integration |
| [SocialMediaManager](SOCIALMEDIA-MANAGER.md) | Platform posting/engagement | Integration |
| [SocialMediaCollator](SOCIALMEDIA-COLLATOR.md) | Social data collection/analysis | Integration |
| [ImageCollections](IMAGECOLLECTIONS.md) | Multi-source image management | Integration |
| [ImageBrowser](IMAGEBROWSER.md) | Unified image browsing | Integration |
| [YouTubeAPI](YOUTUBEAPI.md) | YouTube video/channel management | Integration |
| [WebScraper](WEBSCRAPER.md) | Web content extraction | Integration |
| [AIWebSearch](AIWEBSEARCH.md) | AI-powered web search | Integration |
| [PythonRunner](PYTHONRUNNER.md) | Python script execution | Integration |
| [ResearchTools](RESEARCHTOOLS.md) | Multi-source research utilities | Integration |

---

## Additional Modules

| Module | Purpose | File |
|--------|---------|------|
| [VisualValueAlignment](VISUALVALUEALIGNMENT.md) | Visual-value alignment checking | Service |
| [ContentTextAssessor](CONTENTTEXTASSESSOR.md) | Text quality assessment | Service |

---

## Module Relationships

### Three-Tier Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  ORCHESTRATOR (cross-domain)                                    │
│  • Assigns work to correct manager                              │
│  • Cross-domain sequencing                                      │
│  • Long-running job tracking (days/weeks)                       │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  MANAGERS (domain-scoped coordinators)                          │
│  • BrandManager, TypographyManager, VisualManager, etc.         │
│  • Each owns configs for ONE domain                             │
│  • Cannot call other managers directly → signal Orchestrator    │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  MODULES (single responsibility)                                │
│  • Own bricks only, no coordination                             │
│  • Just do their job when called                                │
└─────────────────────────────────────────────────────────────────┘
```

### Core Dependencies

```
Orchestrator
    ├── Managers (domain work)
    │   ├── BrandManager → Brand, AICaller, Writer, Parsers, BrandTracker
    │   ├── TypographyManager → Typography, FontManager
    │   └── VisualManager → ColourMaker, ColourMix, ImageCollections
    ├── FailureHandler (on error)
    └── SuccessHandler (on complete)
```

### Data Access

```
Repos (Data Storage)
    ├── Librarian (search/discover)
    └── FileManager (direct lookup)
            └── Writer (low-level writes)
                    └── Archivist (validated saves)
```

### Brand Build Chain

```
Orchestrator: "Build Acme Brand"
    │
    ├── BrandManager (Phase 1-2: Core, Identity)
    │   ├── Brand module
    │   ├── AICaller
    │   ├── Writer
    │   └── BrandTracker
    │
    ├── TypographyManager (Phase 3: Fonts)
    │   ├── Typography
    │   └── FontManager
    │
    ├── VisualManager (Phase 3: Colors)
    │   ├── ColourMaker → ColourMix → ColourToken
    │   └── ImageCollections
    │
    └── BrandManager (Phase 4: Complete)
        └── BrandTracker marks complete
```

### Content Generation Chain

```
Content → Email/WebsiteContent → PDFMaker
    │
    ├── Typography → FontManager
    ├── LogoMaker → SVGDrawer
    └── VisualBrand → ImageTemplate
```

### Social Media Chain

```
SocialMediaPlanner → SocialMediaManager → SocialMediaCollator
                            │
                            └── ImageTemplate, Content
```

---

## Quick Reference

### By Function

| Need | Use Module |
|------|------------|
| Generate text content | Content |
| Build brand identity | BrandManager (coordinates Brand, AICaller, etc.) |
| Make color palette | ColourMaker → ColourToken |
| Design logo | LogoMaker |
| Create PDF | PDFMaker |
| Send email | Email → Emaillit |
| Schedule posts | SocialMediaPlanner |
| Post to social | SocialMediaManager |
| Track mentions | SocialMediaCollator |
| Generate images | AIImageCreation |
| Legal documents | LegalDocCreator |
| Check grammar | LanguageCheck |

### By Integration

| Platform | Module |
|----------|--------|
| Canva | CanvaIntegration |
| Google (Calendar/Drive) | GoogleIntegration |
| Email (SMTP/API) | Emaillit |
| Instagram/Twitter/LinkedIn | SocialMediaManager |
| YouTube | YouTubeAPI |
| Unsplash/Pexels | ImageCollections |
| Web Research | AIWebSearch, WebScraper, ResearchTools |

---

## System Rules

All modules follow the standards defined in [GLOBAL-RULES.md](GLOBAL-RULES.md):

- **Pass references, not data** - Lean payloads
- **4-level failure escalation** - Retry → Alternative → Self-heal → Human
- **Kitchen analogy** - Modules are independent stations
- **Functions = Bricks** - Atomic operations
- **Steps = Sequences** - Within one module only
- **Chains** - Cross-module orchestration (system.config)

---

*Last updated: 2026-01-17*
