# System Field Mapping

## Approved Specs

## FileManager
- Fields: repo, entryId, path, encoding, raw, bypassSizeLimit, stats (size, mtime, created)

## OutlineGenerator
- Fields: id, name, themeId, templateId, brandId, audienceId, contentType, templateSnapshot, contentTypeSnapshot, themeSnapshot, cognitiveTypeId, planDepth, truthStrategy (sequenceType, selectedTruths, primaryTruth, secondaryTruths, localTruths), sections (SectionOutline[]), globalImageStyle, globalImageMood, createdAt, updatedAt, createdBy, usageCount, lastUsedAt, status, quality, rating

## TemplateEngine
- Fields: id, name, description, contentType, sections[], createdAt, updatedAt, createdBy, status, version, runId, renderer, outputPath, outputId, previewUrl, format, totalTokens, totalCost, renderTime

## ThemeGenerator
- Fields: id, name, topic, scope (brand, audience, contentType, platform), brandId, audienceId, contentTypeId, platformId, concept, message, angle, angleMetadata, targetOutcomes, transformationFramework (primaryOutcome, secondaryOutcomes, beliefShifts, natashaPerspective, audienceTransformation, contentIntent), keywords, tone, values, truths[] (index, statement, application, dependsOn, status, performance), temporal (type, validFrom, validUntil, seasonTags), usageCount, lastUsedAt, updatedAt, derivedFrom, status, quality, rating

## Writer
- Fields: path, data, content, encoding, indent, bypassSizeLimit, reason, dryRun, createIfMissing, newline, from, to, bytes, atomic

---

## Pending Specs

## AICaller
- Fields: provider, model, prompt, parameters, response, tokenCounts (input, output), errorCode, callId, status

## AIImageCreation
- Fields: prompt, provider, model, size, quality, style, brandId, negativePrompt, seed, n, generatedImage (url, binary), styledImage, upscaledImage, variations[]

## AISystemManager
- Fields: AIRequest (prompt, brandId, jobId, requirements, fallbackStrategy, timeout), AIResponse (success, content, raw, model, provider, tokens, cost, latency, fallbackUsed), SystemStatus, ProviderStatus

## AIWebSearch
- Fields:

## AnalyticsHandler
- Fields:

## APIKeyManager
- Fields:

## Archivist
- Fields:

## AudienceManager
- Fields:

## AuthHandler
- Fields:

## BatchProcessor
- Fields:

## BrandCheck
- Fields:

## BrandManager
- Fields:

## BrandTracker
- Fields:

## Builders
- Fields:

## CacheHandler
- Fields:

## CanvaIntegration
- Fields:

## CanvaRenderer
- Fields:

## ColourMaker
- Fields:

## ColourMix
- Fields:

## ColourToken
- Fields:

## Content
- Fields:

## ContentAlignment
- Fields:

## ContentAlignmentCheck
- Fields:

## ContentManager
- Fields:

## ContentTextAssessor
- Fields:

## ContentValidator
- Fields:

## CostBudgetManager
- Fields:

## CostTracker
- Fields:

## DatabaseHandler
- Fields:

## DOCXRenderer
- Fields:

## Email
- Fields:

## EmailLit
- Fields:

## ExportManager
- Fields:

## FailureHandler
- Fields:

## FeatureFlags
- Fields:

## FontManager
- Fields:

## GlobalRules
- Fields:

## GoogleDocsRenderer
- Fields:

## GoogleIntegration
- Fields:

## GoogleSheetsRenderer
- Fields:

## GoogleSlidesRenderer
- Fields:

## GrammarCheck
- Fields:

## HTMLRenderer
- Fields:

## ImageBrowser
- Fields:

## ImageCollections
- Fields:

## ImageFieldRenderer
- Fields:

## ImageRenderer
- Fields:

## ImageTemplate
- Fields:

## IngestionEngine
- Fields:

## LanguageCheck
- Fields:

## LayoutCheck
- Fields:

## LegalDocCreator
- Fields:

## Librarian
- Fields:

## Logger
- Fields:

## LogoMaker
- Fields:

## MarkdownRenderer
- Fields:

## MigrationHandler
- Fields:

## ModelManager
- Fields:

## Notification
- Fields:

## Orchestrator
- Fields:

## Parsers
- Fields:

## PDFMaker
- Fields:

## PDFRenderer
- Fields:

## PolicyEngine
- Fields:

## PostCreationCheckManager
- Fields:

## PromptBuilder
- Fields:

## PromptCondenser
- Fields:

## PythonRunner
- Fields:

## QueueHandler
- Fields:

## RateLimiter
- Fields:

## RequestHandler
- Fields:

## ResearchTools
- Fields:

## Scheduler
- Fields:

## SocialMediaAnalytics
- Fields:

## SocialMediaCollator
- Fields:

## SocialMediaManager
- Fields:

## SocialMediaPlanner
- Fields:

## SpellGrammarCheck
- Fields:

## SpellingCheck
- Fields:

## StatusHandler
- Fields:

## SVGDrawer
- Fields:

## TemplateFactory
- Fields:

## TestHandler
- Fields:

## TokenManager
- Fields:

## TransformationCheck
- Fields:

## Typography
- Fields:

## TypographyManager
- Fields:

## ValuesCheck
- Fields:

## VisualBrand
- Fields:

## VisualCheck
- Fields:

## VisualManager
- Fields:

## VisualValueAlignment
- Fields:

## WebScraper
- Fields:

## WebsiteContent
- Fields:

## YouTubeAPI
- Fields:
