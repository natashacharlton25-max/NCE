# Module Index - Categorized

> **Total Modules:** 73 | **Last Updated:** 2026-01-18

---

## Quick Navigation

| Category | Count | Description |
|----------|-------|-------------|
| [Core Architecture](#core-architecture) | 2 | Central coordination & rules |
| [AI Services](#ai-services) | 5 | AI API management & prompts |
| [Brand Management](#brand-management) | 4 | Brand building & tracking |
| [Visual Design](#visual-design) | 12 | Colors, logos, typography, images |
| [Content Creation](#content-creation) | 8 | Text, documents, emails |
| [Content Quality](#content-quality) | 4 | Validation & assessment |
| [Social Media](#social-media) | 4 | Planning, posting, analytics |
| [Data Management](#data-management) | 6 | Storage, files, templates |
| [Cost & Performance](#cost--performance) | 3 | Budgets, tokens, tracking |
| [System Services](#system-services) | 17 | Infrastructure & utilities |
| [Integrations](#integrations) | 8 | External APIs & services |

---

## Core Architecture

| Module | File | Role |
|--------|------|------|
| **Orchestrator** | [ORCHESTRATOR.md](ORCHESTRATOR.md) | Central coordinator managing job lifecycle, chain execution, priority queues, and timeout handling. The brain of the system. |
| **Global Rules** | [GLOBAL-RULES.md](GLOBAL-RULES.md) | System-wide conventions, naming standards, and architectural principles that all modules must follow. |

---

## AI Services

| Module | File | Role |
|--------|------|------|
| **AICaller** | [AICALLER.md](AICALLER.md) | Ultra-simple gateway for all AI API calls. Delegates token tracking, cost tracking, and parsing to specialized modules. |
| **AISystemManager** | [AISYSTEMMANAGER.md](AISYSTEMMANAGER.md) | Coordinator for all AI operations, managing relationships between AICaller, ModelManager, TokenManager, and CostBudgetManager. |
| **ModelManager** | [MODELMANAGER.md](MODELMANAGER.md) | Selects optimal AI model based on task requirements. Provides model configs and fallback options when primary models fail. |
| **TokenManager** | [TOKENMANAGER.md](TOKENMANAGER.md) | Estimates tokens before API calls and records actual usage after. Reads from AICaller's raw responses. |
| **PromptBuilder** | [PROMPTBUILDER.md](PROMPTBUILDER.md) | Compiles prompts from templates with variable resolution, conditionals, partials, and constraint application. |

---

## Brand Management

| Module | File | Role |
|--------|------|------|
| **BrandManager** | [BRANDMANAGER.md](BRANDMANAGER.md) | Orchestrates brand building with phase dependencies (core → identity → visuals → complete). Coordinates multiple brand modules. |
| **BrandTracker** | [BRANDTRACKER.md](BRANDTRACKER.md) | Monitors brand build progress, section completion, and validation status across 6 sections and 4 phases. |
| **AudienceManager** | [AUDIENCEMANAGER.md](AUDIENCEMANAGER.md) | Defines and segments target audiences. Creates personas and suggests content strategies per segment. |
| **Managers Overview** | [MANAGERS-OVERVIEW.md](MANAGERS-OVERVIEW.md) | Documentation of all manager-level coordinators and their responsibilities. |

---

## Visual Design

| Module | File | Role |
|--------|------|------|
| **VisualBrand** | [VISUALBRAND.md](VISUALBRAND.md) | Complete visual brand system generation combining colors, fonts, imagery, and design tokens. |
| **VisualValueAlignment** | [VISUALVALUEALIGNMENT.md](VISUALVALUEALIGNMENT.md) | Validates that visual elements align with brand values and identity standards. |
| **ColourMaker** | [COLOURMAKER.md](COLOURMAKER.md) | Creates color palettes from moods, values, images, or descriptions with mood-color mapping. |
| **ColourMix** | [COLOURMIX.md](COLOURMIX.md) | Blends colors and creates harmonies (complementary, analogous, triadic, split-complementary). |
| **ColourToken** | [COLOURTOKEN.md](COLOURTOKEN.md) | Generates color palettes with WCAG accessibility checking, design tokens, and variations. |
| **LogoMaker** | [LOGOMAKER.md](LOGOMAKER.md) | Logo generation with concepts, monograms, wordmarks, variations, and multi-format exports. |
| **Typography** | [TYPOGRAPHY.md](TYPOGRAPHY.md) | Font selection, pairing suggestions, type scale generation, and typographic system creation. |
| **FontManager** | [FONTMANAGER.md](FONTMANAGER.md) | Manages font assets, licensing, and availability across web, print, and app platforms. |
| **SVGDrawer** | [SVGDRAWER.md](SVGDRAWER.md) | Generates and manipulates SVG graphics including logos, icons, patterns, and color application. |
| **AIImageCreation** | [AIIMAGECREATION.md](AIIMAGECREATION.md) | AI image generation via DALL-E 3, Midjourney, Stable Diffusion with brand style application. |
| **ImageTemplate** | [IMAGETEMPLATE.md](IMAGETEMPLATE.md) | Reusable branded image templates with dynamic placeholders for social media and marketing. |
| **ImageBrowser** | [IMAGEBROWSER.md](IMAGEBROWSER.md) | Unified interface for browsing images from all sources with search, filtering, and collections. |

---

## Content Creation

| Module | File | Role |
|--------|------|------|
| **Content** | [CONTENT.md](CONTENT.md) | Text content generation pipeline for intros, taglines, descriptions, and general copy. |
| **WebsiteContent** | [WEBSITECONTENT.md](WEBSITECONTENT.md) | Website page content generation including hero sections, about pages, and service descriptions. |
| **Email** | [EMAIL.md](EMAIL.md) | Email composition from templates with brand styling, personalization, and formatting. |
| **EmailLit** | [EMAILLIT.md](EMAILLIT.md) | Email delivery infrastructure via SendGrid, SMTP, or Mailgun with tracking. |
| **PDFMaker** | [PDFMAKER.md](PDFMAKER.md) | PDF document generation from templates with watermarking, compression, and brand styling. |
| **LegalDocCreator** | [LEGALDOCCREATOR.md](LEGALDOCCREATOR.md) | Legal document generation (contracts, terms, NDAs, policies) from templates with customization. |
| **Writer** | [WRITER.md](WRITER.md) | Low-level file write operations with safety validation before persisting to repos. |
| **Builders** | [BUILDERS.md](BUILDERS.md) | Creates output paths, manages scaffolding, generates IDs, and validates path structures. |

---

## Content Quality

| Module | File | Role |
|--------|------|------|
| **ContentAlignment** | [CONTENTALIGNMENT.md](CONTENTALIGNMENT.md) | Validates content alignment with brand guidelines, audience expectations, and strategic goals. |
| **ContentTextAssessor** | [CONTENTTEXTASSESSOR.md](CONTENTTEXTASSESSOR.md) | Assesses text quality, clarity, persuasiveness, SEO optimization, and CTA effectiveness. |
| **LanguageCheck** | [LANGUAGECHECK.md](LANGUAGECHECK.md) | Python-based grammar, spelling, and language quality checking with suggestions. |
| **Parsers** | [PARSERS.md](PARSERS.md) | Parses and validates data formats (JSON, AI responses, schemas) with helpful error messages. |

---

## Social Media

| Module | File | Role |
|--------|------|------|
| **SocialMedia Planner** | [SOCIALMEDIA-PLANNER.md](SOCIALMEDIA-PLANNER.md) | Plans content calendars, optimizes posting times, manages campaigns, and repurposes content. |
| **SocialMedia Manager** | [SOCIALMEDIA-MANAGER.md](SOCIALMEDIA-MANAGER.md) | Account management, posting, scheduling, and engagement across Instagram, Facebook, LinkedIn, Twitter, TikTok, Pinterest. |
| **SocialMedia Collator** | [SOCIALMEDIA-COLLATOR.md](SOCIALMEDIA-COLLATOR.md) | Collects and analyzes social data including mentions, hashtags, sentiment, and trends. |
| **ImageCollections** | [IMAGECOLLECTIONS.md](IMAGECOLLECTIONS.md) | Multi-source image management from Unsplash, Pexels, and local storage with curation tools. |

---

## Data Management

| Module | File | Role |
|--------|------|------|
| **Librarian** | [LIBRARIAN.md](LIBRARIAN.md) | Search and discovery layer with cached indexes for fast lookups, recommendations, and alternatives. |
| **FileManager** | [FILEMANAGER.md](FILEMANAGER.md) | Direct file operations by ID with schema validation. Supports read/write to known data structures. |
| **Templates** | [TEMPLATES.md](TEMPLATES.md) | Central template manager for loading, validation, inheritance, and caching of all template types. |
| **Archivist** | [ARCHIVIST.md](ARCHIVIST.md) | Validates and saves data to repos with integrity checking before permanent storage. |
| **CacheHandler** | [CACHEHANDLER.md](CACHEHANDLER.md) | System-wide intelligent caching with hot/warm/cold/session/persistent tiers and TTL management. |
| **Logger** | [LOGGER.md](LOGGER.md) | System-wide logging with job log consolidation, reading from outputPaths after each step. |

---

## Cost & Performance

| Module | File | Role |
|--------|------|------|
| **CostBudgetManager** | [COSTBUDGETMANAGER.md](COSTBUDGETMANAGER.md) | Tracks costs, manages budgets, provides spending analytics with pre-approval checks. |
| **CostTracker** | [COSTTRACKER.md](COSTTRACKER.md) | Real-time cost monitoring with burn rate estimation and configurable alert thresholds. |
| **AnalyticsHandler** | [ANALYTICSHANDLER.md](ANALYTICSHANDLER.md) | Usage analytics tracking, metric aggregation, pattern analysis, and insight generation. |

---

## System Services

| Module | File | Role |
|--------|------|------|
| **PolicyEngine** | [POLICYENGINE.md](POLICYENGINE.md) | Gatekeeper checking permissions, costs, data availability, safety, and system readiness before chain execution. |
| **FailureHandler** | [FAILUREHANDLER.md](FAILUREHANDLER.md) | 4-level escalation (Retry → Alternative → Self-heal → Human) with circuit breaker pattern. |
| **SuccessHandler** | [SUCCESSHANDLER.md](SUCCESSHANDLER.md) | Handles job completion with archiving, cleanup, post-success actions, and notifications. |
| **StatusHandler** | [STATUSHANDLER.md](STATUSHANDLER.md) | Process status tracking, timeout management, pause state handling, and dashboard data. |
| **RequestHandler** | [REQUESTHANDLER.md](REQUESTHANDLER.md) | Standardized HTTP request management with retries, timeouts, and error normalization. |
| **QueueHandler** | [QUEUEHANDLER.md](QUEUEHANDLER.md) | Job queue management with priority ordering, load balancing, and restart persistence. |
| **RateLimiter** | [RATELIMITER.md](RATELIMITER.md) | Centralized rate limiting for API calls with exponential backoff and multi-provider coordination. |
| **BatchProcessor** | [BATCHPROCESSOR.md](BATCHPROCESSOR.md) | Large batch processing with rate limiting, progress tracking, and pause/resume capability. |
| **Scheduler** | [SCHEDULER.md](SCHEDULER.md) | Manages timed jobs, cron-style recurring tasks, and delayed execution with timezone support. |
| **Notification** | [NOTIFICATION.md](NOTIFICATION.md) | Single point for all human notifications (email, Slack, SMS, dashboard) with priority routing. |
| **APIKeyManager** | [APIKEYMANAGER.md](APIKEYMANAGER.md) | Secure API key access via environment variables with credential rotation support. |
| **AuthHandler** | [AUTHHANDLER.md](AUTHHANDLER.md) | Authentication management for future multi-user system. Currently single-user passthrough. |
| **FeatureFlags** | [FEATUREFLAGS.md](FEATUREFLAGS.md) | Feature toggle management with percentage rollouts, allowlists, and A/B testing support. |
| **TestHandler** | [TESTHANDLER.md](TESTHANDLER.md) | Automated testing framework for unit, integration, e2e, and regression tests with mocking. |
| **MigrationHandler** | [MIGRATIONHANDLER.md](MIGRATIONHANDLER.md) | Data and schema migrations with version tracking, transformation pipelines, and rollback. |
| **PythonRunner** | [PYTHONRUNNER.md](PYTHONRUNNER.md) | Sandboxed Python script execution for data processing, ML tasks, and complex computations. |
| **ResearchTools** | [RESEARCHTOOLS.md](RESEARCHTOOLS.md) | Multi-source research coordination combining web search, scraping, and AI synthesis. |

---

## Integrations

| Module | File | Role |
|--------|------|------|
| **Canva Integration** | [CANVA-INTEGRATION.md](CANVA-INTEGRATION.md) | Canva API operations for folder management, design creation, template usage, and exports. |
| **Google Integration** | [GOOGLE-INTEGRATION.md](GOOGLE-INTEGRATION.md) | Google APIs (Calendar, Drive, Docs) with OAuth token management and sync. |
| **YouTubeAPI** | [YOUTUBEAPI.md](YOUTUBEAPI.md) | YouTube video uploads, channel management, analytics, and playlist operations. |
| **WebScraper** | [WEBSCRAPER.md](WEBSCRAPER.md) | Web content extraction with configurable selectors, pagination, and rate limiting. |
| **AIWebSearch** | [AIWEBSEARCH.md](AIWEBSEARCH.md) | AI-powered web search combining search APIs with AI synthesis for research and information gathering. |

---

## Architecture Summary

```
                    ┌─────────────────┐
                    │   ORCHESTRATOR  │  ← Central Coordinator
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         ▼                   ▼                   ▼
   ┌───────────┐      ┌───────────┐      ┌───────────┐
   │ MANAGERS  │      │ SERVICES  │      │  SYSTEM   │
   │ (Domain)  │      │ (Business)│      │ (Infra)   │
   └─────┬─────┘      └─────┬─────┘      └─────┬─────┘
         │                  │                  │
         ▼                  ▼                  ▼
   ┌───────────────────────────────────────────────┐
   │              MODULE LAYER                      │
   │  (Single responsibility, pass references)     │
   └───────────────────────────────────────────────┘
         │                  │                  │
         ▼                  ▼                  ▼
   ┌───────────────────────────────────────────────┐
   │           DATA LAYER (Repos)                  │
   │  Librarian ↔ FileManager ↔ Archivist/Writer  │
   └───────────────────────────────────────────────┘
```

### Key Patterns
- **3-Tier Architecture:** Orchestrator → Managers → Modules
- **4-Level Failure Escalation:** Retry → Alternative → Self-heal → Human
- **Pass References:** Modules pass IDs/paths, not raw data
- **Circuit Breaker:** Prevents cascade failures in external services

---

*Generated from 73 module documentation files*
