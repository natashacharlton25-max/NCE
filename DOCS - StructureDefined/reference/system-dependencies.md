# System Dependencies

## Approved Specs

## FileManager
- Systems: None (foundational)
- Feeds: repos (all)

## OutlineGenerator
- Systems: DatabaseHandler, ThemeGenerator, FileManager, PromptBuilder, AICaller
- Feeds: outlines, themes, templates, section-specs, content-types, cognitive-types, frameworks, paragraph-structures

## TemplateEngine
- Systems: DatabaseHandler, FileManager, ThemeGenerator, OutlineGenerator, PromptBuilder, AICaller, Parsers, CacheHandler, AIImageCreation, ImageBrowser, ImageCollections, Renderers (PDF, DOCX, Markdown, HTML, Canva, GoogleDocs, GoogleSheets, GoogleSlides, Image)
- Feeds: templates, outlines, section-specs, brands, audiences, paragraph-structures

## ThemeGenerator
- Systems: DatabaseHandler, BrandManager, AudienceManager, FileManager, PromptBuilder, AICaller
- Feeds: themes, outcomes, brands, audiences, frameworks

## Writer
- Systems: None (foundational)
- Feeds: repos (all), jobs, exports, logs

---

## Pending Specs

## AICaller
- Systems: APIKeyManager, RateLimiter, FailureHandler, Parsers, TokenManager
- Feeds: model-cards

## AIImageCreation
- Systems: APIKeyManager, RateLimiter, FailureHandler, PromptBuilder, BrandManager
- Feeds: brands (style data)

## AISystemManager
- Systems: AICaller, ModelManager, TokenManager, CostBudgetManager, Parsers, FailureHandler
- Feeds: model-cards

## AIWebSearch
- Systems:
- Feeds:

## AnalyticsHandler
- Systems:
- Feeds:

## APIKeyManager
- Systems:
- Feeds:

## Archivist
- Systems:
- Feeds:

## AudienceManager
- Systems:
- Feeds:

## AuthHandler
- Systems:
- Feeds:

## BatchProcessor
- Systems:
- Feeds:

## BrandCheck
- Systems:
- Feeds:

## BrandManager
- Systems:
- Feeds:

## BrandTracker
- Systems:
- Feeds:

## Builders
- Systems:
- Feeds:

## CacheHandler
- Systems:
- Feeds:

## CanvaIntegration
- Systems:
- Feeds:

## CanvaRenderer
- Systems:
- Feeds:

## ColourMaker
- Systems:
- Feeds:

## ColourMix
- Systems:
- Feeds:

## ColourToken
- Systems:
- Feeds:

## Content
- Systems:
- Feeds:

## ContentAlignment
- Systems:
- Feeds:

## ContentAlignmentCheck
- Systems:
- Feeds:

## ContentManager
- Systems:
- Feeds:

## ContentTextAssessor
- Systems:
- Feeds:

## ContentValidator
- Systems:
- Feeds:

## CostBudgetManager
- Systems:
- Feeds:

## CostTracker
- Systems:
- Feeds:

## DatabaseHandler
- Systems:
- Feeds:

## DOCXRenderer
- Systems:
- Feeds:

## Email
- Systems:
- Feeds:

## EmailLit
- Systems:
- Feeds:

## ExportManager
- Systems:
- Feeds:

## FailureHandler
- Systems:
- Feeds:

## FeatureFlags
- Systems:
- Feeds:

## FontManager
- Systems:
- Feeds:

## GlobalRules
- Systems:
- Feeds:

## GoogleDocsRenderer
- Systems:
- Feeds:

## GoogleIntegration
- Systems:
- Feeds:

## GoogleSheetsRenderer
- Systems:
- Feeds:

## GoogleSlidesRenderer
- Systems:
- Feeds:

## GrammarCheck
- Systems:
- Feeds:

## HTMLRenderer
- Systems:
- Feeds:

## ImageBrowser
- Systems:
- Feeds:

## ImageCollections
- Systems:
- Feeds:

## ImageFieldRenderer
- Systems:
- Feeds:

## ImageRenderer
- Systems:
- Feeds:

## ImageTemplate
- Systems:
- Feeds:

## IngestionEngine
- Systems:
- Feeds:

## LanguageCheck
- Systems:
- Feeds:

## LayoutCheck
- Systems:
- Feeds:

## LegalDocCreator
- Systems:
- Feeds:

## Librarian
- Systems:
- Feeds:

## Logger
- Systems:
- Feeds:

## LogoMaker
- Systems:
- Feeds:

## MarkdownRenderer
- Systems:
- Feeds:

## MigrationHandler
- Systems:
- Feeds:

## ModelManager
- Systems:
- Feeds:

## Notification
- Systems:
- Feeds:

## Orchestrator
- Systems:
- Feeds:

## Parsers
- Systems:
- Feeds:

## PDFMaker
- Systems:
- Feeds:

## PDFRenderer
- Systems:
- Feeds:

## PolicyEngine
- Systems:
- Feeds:

## PostCreationCheckManager
- Systems:
- Feeds:

## PromptBuilder
- Systems:
- Feeds:

## PromptCondenser
- Systems:
- Feeds:

## PythonRunner
- Systems:
- Feeds:

## QueueHandler
- Systems:
- Feeds:

## RateLimiter
- Systems:
- Feeds:

## RequestHandler
- Systems:
- Feeds:

## ResearchTools
- Systems:
- Feeds:

## Scheduler
- Systems:
- Feeds:

## SocialMediaAnalytics
- Systems:
- Feeds:

## SocialMediaCollator
- Systems:
- Feeds:

## SocialMediaManager
- Systems:
- Feeds:

## SocialMediaPlanner
- Systems:
- Feeds:

## SpellGrammarCheck
- Systems:
- Feeds:

## SpellingCheck
- Systems:
- Feeds:

## StatusHandler
- Systems:
- Feeds:

## SVGDrawer
- Systems:
- Feeds:

## TemplateFactory
- Systems:
- Feeds:

## TestHandler
- Systems:
- Feeds:

## TokenManager
- Systems:
- Feeds:

## TransformationCheck
- Systems:
- Feeds:

## Typography
- Systems:
- Feeds:

## TypographyManager
- Systems:
- Feeds:

## ValuesCheck
- Systems:
- Feeds:

## VisualBrand
- Systems:
- Feeds:

## VisualCheck
- Systems:
- Feeds:

## VisualManager
- Systems:
- Feeds:

## VisualValueAlignment
- Systems:
- Feeds:

## WebScraper
- Systems:
- Feeds:

## WebsiteContent
- Systems:
- Feeds:

## YouTubeAPI
- Systems:
- Feeds:
