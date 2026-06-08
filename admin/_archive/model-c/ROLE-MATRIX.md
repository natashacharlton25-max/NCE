
# System Role Matrix

> Complete inventory of all modules, managers, and functions across the NCE system.
> **Total Modules:** 83 | **Last Updated:** 2026-01-19

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│  ORCHESTRATOR (cross-domain coordination)                       │
│  • Assigns work to correct manager                              │
│  • Cross-domain sequencing                                      │
│  • Long-running job tracking                                    │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  MANAGERS (domain-scoped coordinators)                          │
│  • Each manager owns configs for ONE domain                     │
│  • Coordinates modules needed for those configs                 │
│  • Cannot call other managers directly → signal Orchestrator    │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  MODULES (single responsibility)                                │
│  • Own functions (bricks) only                                  │
│  • No coordination                                              │
│  • Just do their job when called                                │
└─────────────────────────────────────────────────────────────────┘
```

---

## Managers (Domain Coordinators)

| Manager | Domain | Modules Coordinated |
|---------|--------|---------------------|
| **BrandManager** | Brand building/editing | Brand, AICaller, Writer, Parsers, BrandTracker |
| **TypographyManager** | Font selection/management | Typography, FontManager |
| **VisualManager** | Colors, images, visual assets | ColourMaker, ColourMix, ImageCollections |
| **ContentManager** | Content generation | Content, AICaller, LanguageCheck, Parsers |
| **ExportManager** | Output/export operations | PDFMaker, Export modules |
| **SocialManager** | Social media operations | SocialMedia-Planner, SocialMedia-Manager |

**Key Rule:** Managers don't call other managers directly - they signal Orchestrator for cross-domain work.

---

## Trackers

| Tracker | Purpose |
|---------|---------|
| **BrandTracker** | Track brand build progress, section completion, validation status across 6 sections and 4 phases |

---

## Core Architecture

### Orchestrator

| Function | Input | Output |
|----------|-------|--------|
| `createJob(chainName, params)` | chainName, params | jobContext |
| `loadChain(chainName)` | chainName | chainDefinition |
| `executeStep(step, context)` | step, context | updatedContext |
| `runChain(chainName, params)` | chainName, params | result |
| `handleStepResult(result, context)` | result, context | updatedContext |
| `checkCheckpoint(step)` | step | boolean |
| `saveCheckpoint(context)` | context | checkpointId |
| `resumeFromCheckpoint(checkpointId)` | checkpointId | context |
| `queueJob(chainName, params, priority)` | chainName, params, priority | queuePosition |
| `getNextJob()` | - | jobContext |
| `calculatePriority(params)` | params | priorityScore |

**Steps:**
- `runJob`: createJob → loadChain → runChain
- `resumeJob`: resumeFromCheckpoint → runChain
- `processQueue`: getNextJob → runJob (loop)

---

## AI Services

### AICaller

| Function | Input | Output |
|----------|-------|--------|
| `call(request)` | request | callResult |
| `getStatus(callId)` | callId | status |

### AISystemManager

Coordinates: AICaller, ModelManager, TokenManager, CostBudgetManager

### ModelManager

| Function | Input | Output |
|----------|-------|--------|
| `selectModel(requirements)` | requirements | selectedModel |
| `getFallbacks(model)` | model | fallbackList |
| `getModelConfig(modelId)` | modelId | modelConfig |

### TokenManager

| Function | Input | Output |
|----------|-------|--------|
| `estimateTokens(content, model)` | content, model | tokenEstimate |
| `estimateCost(tokens, model)` | tokens, model | costEstimate |
| `checkBudget(brandId, estimatedCost)` | brandId, estimatedCost | budgetStatus |
| `recordUsage(responsePath, brandId, jobId)` | responsePath, brandId, jobId | usageRecord |
| `getBudget(brandId)` | brandId | budgetInfo |
| `getUsage(brandId, period)` | brandId, period | usageStats |
| `setBudget(brandId, limits)` | brandId, limits | updated |

**Steps:**
- `validateAndTrack`: estimateTokens → estimateCost → checkBudget
- `recordFromResponse`: recordUsage

### PromptBuilder

| Function | Input | Output |
|----------|-------|--------|
| `loadTemplate(promptId)` | promptId | template |
| `loadContext(refs, brandId)` | refs, brandId | context |
| `loadPartial(partialId)` | partialId | partial |
| `resolveVariables(template, context)` | template, context | resolvedTemplate |
| `resolveConditionals(template, context)` | template, context | processedTemplate |
| `resolvePartials(template)` | template | expandedTemplate |
| `applyConstraints(prompt, limits)` | prompt, limits | constrainedPrompt |
| `validateTokens(prompt, modelLimits)` | prompt, modelLimits | validation |
| `addExamples(prompt, examples)` | prompt, examples | promptWithExamples |
| `compile(refs, brandId)` | refs, brandId | compiledPrompt |
| `getCached(key)` | key | cached |
| `setCache(key, value, ttl)` | key, value, ttl | success |

**Steps:**
- `compile`: loadTemplate → loadContext → resolvePartials → resolveConditionals → resolveVariables → addExamples → applyConstraints → validateTokens
- `compileWithFallback`: compile (with Librarian fallback)
- `compileWithCache`: getCached → compile → setCache

---

## Brand Management

### BrandManager

| Function | Input | Output |
|----------|-------|--------|
| `startBuild(brandId, input)` | brandId, input | buildSession |
| `updateSection(brandId, section, data)` | brandId, section, data | updateResult |
| `validateSection(brandId, section)` | brandId, section | validationResult |
| `getStatus(brandId)` | brandId | statusReport |
| `requestCrossDomain(brandId, domain)` | brandId, domain | request |
| `receiveResult(brandId, domain, result)` | brandId, domain, result | received |
| `completeBuild(brandId)` | brandId | completion |

**Steps:**
- `initBrand`: validateInput → createFolder → saveInitial → initTracker
- `buildCore`: collectInput → validateData → saveCore → updateTracker
- `generateMission`: buildPrompt → callAI → parseResponse → saveDraft
- `requestVisuals`: packageContext → signalOrchestrator
- `completeBuild`: finalValidation → markComplete → signalDone

### BrandTracker

| Function | Input | Output |
|----------|-------|--------|
| `initTracker(brandId)` | brandId | trackerStatus |
| `updateSection(brandId, section, status)` | brandId, section, status | updated |
| `getProgress(brandId)` | brandId | progressReport |
| `canProceed(brandId, requirements)` | brandId, requirements | canProceed |
| `markComplete(brandId)` | brandId | completed |

### AudienceManager

| Function | Input | Output |
|----------|-------|--------|
| `defineAudience(brandId, segments)` | brandId, segments | audienceProfile |
| `getPersonas(brandId)` | brandId | personas |
| `suggestStrategy(brandId, segment)` | brandId, segment | contentStrategy |

---

## Visual Design

### ColourMaker

| Function | Input | Output |
|----------|-------|--------|
| `fromMood(moods)` | moods | colors |
| `fromValues(values)` | values | colors |
| `fromImage(image)` | image | colors |
| `fromDescription(description)` | description | colors |
| `fromIndustry(industry)` | industry | colors |
| `fromName(colorName)` | colorName | color |
| `random(constraints)` | constraints | color |

### ColourMix

| Function | Input | Output |
|----------|-------|--------|
| `blend(colors)` | colors | blendedColor |
| `complementary(color)` | color | complementaryColor |
| `analogous(color)` | color | analogousColors |
| `triadic(color)` | color | triadicColors |
| `splitComplementary(color)` | color | splitColors |

### ColourToken

| Function | Input | Output |
|----------|-------|--------|
| `generatePalette(brief)` | brief | palette |
| `createTokens(palette)` | palette | tokens |
| `checkAccessibility(colors)` | colors | accessibilityReport |
| `generateVariations(baseColor, type)` | baseColor, type | variations |
| `harmonize(colors)` | colors | harmonizedPalette |
| `convertFormats(color, formats)` | color, formats | converted |
| `extractFromImage(image)` | image | extractedPalette |

**Steps:**
- `createBrandPalette`: generatePalette → checkAccessibility → createTokens
- `systemFromPrimary`: generateVariations → harmonize → createTokens

### Typography

| Function | Input | Output |
|----------|-------|--------|
| `selectFont(brief)` | brief | fontRecommendations |
| `pairFonts(primaryFont)` | primaryFont | pairingSuggestions |
| `generateScale(baseSize, ratio)` | baseSize, ratio | typeScale |
| `createSystem(config)` | config | typographySystem |

### FontManager

| Function | Input | Output |
|----------|-------|--------|
| `getFont(fontId)` | fontId | fontData |
| `checkLicense(fontId, usage)` | fontId, usage | licenseStatus |
| `getFontFiles(fontId, formats)` | fontId, formats | fontFiles |

### LogoMaker

| Function | Input | Output |
|----------|-------|--------|
| `generateConcepts(brief)` | brief | logoConcepts |
| `createMonogram(letters, style)` | letters, style | monogram |
| `createWordmark(text, style)` | text, style | wordmark |
| `generateVariations(logo)` | logo | variations |
| `exportFormats(logo, formats)` | logo, formats | exportedFiles |

### SVGDrawer

| Function | Input | Output |
|----------|-------|--------|
| `createShape(type, params)` | type, params | svgElement |
| `combinePaths(paths)` | paths | combinedSvg |
| `applyColors(svg, palette)` | svg, palette | coloredSvg |
| `optimize(svg)` | svg | optimizedSvg |
| `export(svg, format)` | svg, format | exportedFile |

### AIImageCreation

| Function | Input | Output |
|----------|-------|--------|
| `generate(prompt, options)` | prompt, options | generatedImage |
| `applyBrandStyle(image, brandId)` | image, brandId | styledImage |
| `upscale(image, scale)` | image, scale | upscaledImage |
| `variations(image, count)` | image, count | imageVariations |

### ImageTemplate

| Function | Input | Output |
|----------|-------|--------|
| `createTemplate(spec)` | spec | template |
| `applyData(template, data)` | template, data | renderedImage |
| `generateForPlatform(template, platform)` | template, platform | platformImage |

### ImageBrowser

| Function | Input | Output |
|----------|-------|--------|
| `search(query, sources)` | query, sources | searchResults |
| `filter(images, criteria)` | images, criteria | filteredImages |
| `createCollection(name, images)` | name, images | collection |

### VisualBrand

| Function | Input | Output |
|----------|-------|--------|
| `createSystem(brandId)` | brandId | visualSystem |
| `generateStyleGuide(brandId)` | brandId | styleGuide |
| `validateAssets(brandId)` | brandId | validationReport |

### VisualValueAlignment

| Function | Input | Output |
|----------|-------|--------|
| `checkAlignment(visuals, values)` | visuals, values | alignmentScore |
| `suggestImprovements(visuals, values)` | visuals, values | suggestions |

---

## Content Creation

### Content

| Function | Input | Output |
|----------|-------|--------|
| `prepareIntro(context)` | context | preparedPrompt |
| `prepareTagline(context)` | context | preparedPrompt |
| `prepareDescription(context, type)` | context, type | preparedPrompt |
| `validateContent(content, type)` | content, type | validationResult |
| `refineContent(content, feedback)` | content, feedback | preparedPrompt |

**Steps:**
- `generateIntro`: prepareIntro → validateContent
- `generateTagline`: prepareTagline → validateContent
- `generateDescription`: prepareDescription → validateContent
- `refine`: refineContent → validateContent

### WebsiteContent

| Function | Input | Output |
|----------|-------|--------|
| `generateHero(brandId)` | brandId | heroContent |
| `generateAbout(brandId)` | brandId | aboutContent |
| `generateServices(brandId, services)` | brandId, services | servicesContent |
| `generateFooter(brandId)` | brandId | footerContent |

### Email

| Function | Input | Output |
|----------|-------|--------|
| `compose(template, data)` | template, data | composedEmail |
| `applyBranding(email, brandId)` | email, brandId | brandedEmail |
| `personalize(email, recipient)` | email, recipient | personalizedEmail |

### EmailLit

| Function | Input | Output |
|----------|-------|--------|
| `send(email, provider)` | email, provider | sendResult |
| `trackDelivery(emailId)` | emailId | deliveryStatus |
| `getAnalytics(campaignId)` | campaignId | analytics |

### PDFMaker

| Function | Input | Output |
|----------|-------|--------|
| `createFromTemplate(templateId, data)` | templateId, data | pdfDocument |
| `applyBranding(pdf, brandId)` | pdf, brandId | brandedPdf |
| `addWatermark(pdf, watermark)` | pdf, watermark | watermarkedPdf |
| `compress(pdf, quality)` | pdf, quality | compressedPdf |
| `merge(pdfs)` | pdfs | mergedPdf |

### LegalDocCreator

| Function | Input | Output |
|----------|-------|--------|
| `createContract(type, data)` | type, data | contract |
| `createTerms(brandId, options)` | brandId, options | termsDoc |
| `createNDA(parties, terms)` | parties, terms | ndaDoc |
| `createPolicy(type, brandId)` | type, brandId | policyDoc |

### Writer

| Function | Input | Output |
|----------|-------|--------|
| `write(path, data, options?)` | path, data, options | writeResult |
| `writeRaw(path, content, options?)` | path, content, options | writeResult |
| `append(path, content, options?)` | path, content, options | appendResult |
| `ensureDir(path)` | path | created |
| `move(from, to)` | from, to | moveResult |
| `copy(from, to)` | from, to | copyResult |
| `remove(path)` | path | removeResult |

**Internal:** `validatePath(path)` - security validation called by all write operations

### Builders

| Function | Input | Output |
|----------|-------|--------|
| `createOutputPath(template, vars)` | template, vars | path |
| `scaffoldBrand(brandId)` | brandId | folderStructure |
| `generateId(type)` | type | uniqueId |
| `validatePath(path)` | path | validation |
| `deletePath(path)` | path | deleted |

---

## Content Quality

### ContentAlignment

| Function | Input | Output |
|----------|-------|--------|
| `checkBrandAlignment(content, brandId)` | content, brandId | alignmentScore |
| `checkAudienceAlignment(content, audience)` | content, audience | alignmentScore |
| `getSuggestions(content, target)` | content, target | suggestions |

### ContentTextAssessor

| Function | Input | Output |
|----------|-------|--------|
| `assessQuality(text)` | text | qualityScore |
| `assessClarity(text)` | text | clarityScore |
| `assessPersuasiveness(text)` | text | persuasivenessScore |
| `assessSEO(text, keywords)` | text, keywords | seoScore |
| `assessCTA(text)` | text | ctaEffectiveness |

### LanguageCheck

| Function | Input | Output |
|----------|-------|--------|
| `checkGrammar(text)` | text | grammarIssues |
| `checkSpelling(text)` | text | spellingIssues |
| `checkStyle(text, style)` | text, style | styleIssues |
| `getSuggestions(text)` | text | suggestions |

### Parsers

| Function | Input | Output |
|----------|-------|--------|
| `parseJSON(input)` | input | parsedJSON |
| `parseAIResponse(response, format)` | response, format | parsedContent |
| `validateSchema(data, schema)` | data, schema | validation |
| `extractContent(response)` | response | extractedContent |

---

## Social Media

### SocialMediaPlanner

| Function | Input | Output |
|----------|-------|--------|
| `createCalendar(brandId, period)` | brandId, period | calendar |
| `suggestTimes(platform, audience)` | platform, audience | optimalTimes |
| `planCampaign(brief)` | brief | campaignPlan |
| `repurposeContent(content, platforms)` | content, platforms | repurposedPosts |

### SocialMediaManager

| Function | Input | Output |
|----------|-------|--------|
| `connect(platform, credentials)` | platform, credentials | connection |
| `post(platform, content)` | platform, content | postResult |
| `schedule(platform, content, time)` | platform, content, time | scheduled |
| `getEngagement(postId)` | postId | engagementData |

### SocialMediaCollator

| Function | Input | Output |
|----------|-------|--------|
| `collectMentions(brandId, period)` | brandId, period | mentions |
| `analyzeHashtags(hashtags)` | hashtags | hashtagAnalysis |
| `analyzeSentiment(posts)` | posts | sentimentAnalysis |
| `getTrends(industry)` | industry | trends |

### ImageCollections

| Function | Input | Output |
|----------|-------|--------|
| `searchUnsplash(query)` | query | images |
| `searchPexels(query)` | query | images |
| `curateCollection(name, images)` | name, images | collection |
| `tagImages(images)` | images | taggedImages |

---

## Data Management

### Librarian

| Function | Input | Output |
|----------|-------|--------|
| `search(repo, query)` | repo, query | matchingEntries |
| `recommend(repo, requirements)` | repo, requirements | recommendations |
| `getIndex(repo)` | repo | index |
| `rebuildIndex(repo)` | repo | newIndex |
| `scoreEntry(entry, criteria)` | entry, criteria | score |
| `findSimilar(repo, entryId)` | repo, entryId | similarEntries |
| `validateIndex(repo)` | repo | validationResult |

**Steps:**
- `searchWithFallback`: validateIndex → search (or rebuildIndex → search)
- `recommendAlternative`: search → scoreEntry → recommend

### FileManager

| Function | Input | Output |
|----------|-------|--------|
| `initialize()` | - | initResult |
| `get(repo, entryId, options?)` | repo, entryId, options | entryData |
| `exists(repo, entryId, options?)` | repo, entryId, options | existsResult |
| `list(repo)` | repo | entryIds[] |
| `listRepos()` | - | repos[] |
| `getRepoPath(repo)` | repo | path |
| `validate(data, schemaId)` | data, schemaId | validationResult |
| `getSchema(schemaId)` | schemaId | schema |

**Internal:** `validatePath(path)` - security validation with symlink resolution

### Archivist

| Function | Input | Output |
|----------|-------|--------|
| `save(repo, data)` | repo, data | saved |
| `validateAndSave(repo, data)` | repo, data | savedWithValidation |
| `archive(data, destination)` | data, destination | archived |

### CacheHandler

| Function | Input | Output |
|----------|-------|--------|
| `get(key)` | key | cachedValue |
| `set(key, value, tier)` | key, value, tier | success |
| `invalidate(key)` | key | invalidated |
| `getTier(key)` | key | tierInfo |

### Logger

| Function | Input | Output |
|----------|-------|--------|
| `log(level, message, context)` | level, message, context | logged |
| `addToJobLog(jobId, entry)` | jobId, entry | added |
| `getJobLog(jobId)` | jobId | jobLog |
| `query(filters)` | filters | logEntries |

---

## Template System

> See [TEMPLATE-ARCHITECTURE.md](TEMPLATE-ARCHITECTURE.md) for design rationale.
> Templates = section assembly instructions (data). Modules orchestrate and render.

### TemplateEngine

| Function | Input | Output |
|----------|-------|--------|
| `run(templateId, context)` | templateId, context | renderedOutput |
| `fillSections(sections, context)` | sections[], context | filledSections[] |
| `handToRenderer(renderer, data)` | renderer, data | outputPath |

### TemplateFactory

| Function | Input | Output |
|----------|-------|--------|
| `createFromOutput(aiOutput, name)` | aiOutput, name | template |
| `detectSections(output)` | output | sections[] |
| `save(template)` | template | templateId |

---

## Renderers

> Each renderer maps section types to page layouts for its output format.

### CanvaRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, externalTemplateId)` | sections[], externalTemplateId | designId |
| `export(designId, format)` | designId, format | filePath |

### GoogleDocsRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, externalTemplateId)` | sections[], externalTemplateId | docId |
| `export(docId, format)` | docId, format | filePath |

### GoogleSheetsRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, externalTemplateId)` | sections[], externalTemplateId | sheetId |
| `export(sheetId, format)` | sheetId, format | filePath |

### GoogleSlidesRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, externalTemplateId)` | sections[], externalTemplateId | presentationId |
| `export(presentationId, format)` | presentationId, format | filePath |

### ImageRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, imageTemplateId)` | sections[], imageTemplateId | imagePath |
| `composite(layers)` | layers[] | imagePath |

### PDFRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, renderTemplateId)` | sections[], renderTemplateId | pdfPath |

### DOCXRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, renderTemplateId)` | sections[], renderTemplateId | docxPath |

### MarkdownRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, renderTemplateId)` | sections[], renderTemplateId | mdPath |

### HTMLRenderer

| Function | Input | Output |
|----------|-------|--------|
| `render(sections, renderTemplateId)` | sections[], renderTemplateId | htmlPath |

---

## Cost & Performance

### CostBudgetManager

| Function | Input | Output |
|----------|-------|--------|
| `checkBudget(brandId, estimatedCost)` | brandId, estimatedCost | budgetStatus |
| `recordCost(jobId, cost)` | jobId, cost | recorded |
| `getBudgetStatus(brandId)` | brandId | budgetReport |
| `setLimits(brandId, limits)` | brandId, limits | updated |
| `getSpendingAnalytics(brandId, period)` | brandId, period | analytics |

### CostTracker

| Function | Input | Output |
|----------|-------|--------|
| `track(event)` | event | tracked |
| `getBurnRate(brandId, period)` | brandId, period | burnRate |
| `setAlertThreshold(brandId, threshold)` | brandId, threshold | set |
| `getAlerts(brandId)` | brandId | alerts |

### AnalyticsHandler

| Function | Input | Output |
|----------|-------|--------|
| `track(event)` | event | tracked |
| `aggregate(metrics, period)` | metrics, period | aggregated |
| `analyzePatterns(data)` | data | patterns |
| `generateInsights(brandId)` | brandId | insights |

---

## System Services

### PolicyEngine

| Function | Input | Output |
|----------|-------|--------|
| `canRun(request)` | request | decision |
| `checkPermissions(userId, resource)` | userId, resource | allowed |
| `checkCost(jobEstimate, brandId)` | jobEstimate, brandId | allowed |
| `checkData(requirements)` | requirements | available |
| `checkSafety(content)` | content | safe |
| `checkSystem()` | - | ready |
| `getPolicy(policyId)` | policyId | policy |
| `explainDenial(decision)` | decision | explanation |

### FailureHandler

| Function | Input | Output |
|----------|-------|--------|
| `handleFailure(error, context)` | error, context | solution |
| `classifyError(error)` | error | classification |
| `attemptRetry(error, context, options)` | error, context, options | retryResult |
| `findAlternative(error, context)` | error, context | alternative |
| `triggerSelfHealing(error, context)` | error, context | newEntry |
| `escalateToHuman(error, context)` | error, context | ticket |
| `logFailure(error, context, resolution)` | error, context, resolution | logged |
| `checkCircuitBreaker(moduleId)` | moduleId | status |

**Steps:**
- `handleWithEscalation`: classifyError → attemptRetry → findAlternative → triggerSelfHealing → escalateToHuman → logFailure
- `autoRecover`: classifyError → attemptRetry → findAlternative → triggerSelfHealing

### SuccessHandler

| Function | Input | Output |
|----------|-------|--------|
| `handleSuccess(jobContext)` | jobContext | successReport |
| `archiveJob(jobContext)` | jobContext | archivePath |
| `cleanupTemp(jobId)` | jobId | cleaned |
| `triggerPostActions(jobContext)` | jobContext | actionsTriggered |
| `notifyCompletion(jobContext)` | jobContext | notified |
| `logMetrics(jobContext)` | jobContext | logged |
| `scheduleFollowUp(jobContext, followUpConfig)` | jobContext, followUpConfig | scheduled |

**Steps:**
- `processSuccess`: handleSuccess → archiveJob → cleanupTemp → triggerPostActions
- `quickArchive`: archiveJob → cleanupTemp

### StatusHandler

| Function | Input | Output |
|----------|-------|--------|
| `setStatus(jobId, status, details)` | jobId, status, details | updated |
| `getStatus(jobId)` | jobId | status |
| `checkTimeout(jobId)` | jobId | timeoutStatus |
| `getDashboardData()` | - | dashboardData |

### RequestHandler

| Function | Input | Output |
|----------|-------|--------|
| `request(options)` | options | response |
| `retry(request, options)` | request, options | response |
| `normalizeError(error)` | error | normalizedError |

### QueueHandler

| Function | Input | Output |
|----------|-------|--------|
| `enqueue(job)` | job | queuePosition |
| `dequeue()` | - | nextJob |
| `prioritize(jobId, priority)` | jobId, priority | updated |
| `getQueue()` | - | queueState |
| `persist()` | - | persisted |
| `restore()` | - | restored |

### RateLimiter

| Function | Input | Output |
|----------|-------|--------|
| `checkLimit(provider)` | provider | canProceed |
| `recordRequest(provider)` | provider | recorded |
| `getStatus(provider)` | provider | limitStatus |
| `backoff(provider)` | provider | backoffTime |

### BatchProcessor

| Function | Input | Output |
|----------|-------|--------|
| `processBatch(items, handler)` | items, handler | batchResult |
| `pause()` | - | paused |
| `resume()` | - | resumed |
| `getProgress()` | - | progress |

### Scheduler

| Function | Input | Output |
|----------|-------|--------|
| `schedule(job, time)` | job, time | scheduledId |
| `scheduleCron(job, cronExpr)` | job, cronExpr | scheduledId |
| `cancel(scheduledId)` | scheduledId | cancelled |
| `getScheduled()` | - | scheduledJobs |

### Notification

| Function | Input | Output |
|----------|-------|--------|
| `send(notification)` | notification | sent |
| `sendEmail(to, subject, body)` | to, subject, body | sent |
| `sendSlack(channel, message)` | channel, message | sent |
| `sendDashboard(notification)` | notification | sent |

### APIKeyManager

| Function | Input | Output |
|----------|-------|--------|
| `getKey(keyRef)` | keyRef | apiKey |
| `validateKey(keyRef)` | keyRef | valid |
| `rotateKey(keyRef)` | keyRef | newKey |

### AuthHandler

| Function | Input | Output |
|----------|-------|--------|
| `authenticate(credentials)` | credentials | authResult |
| `authorize(userId, resource)` | userId, resource | authorized |
| `refreshToken(token)` | token | newToken |

### FeatureFlags

| Function | Input | Output |
|----------|-------|--------|
| `isEnabled(flag)` | flag | enabled |
| `getFlags()` | - | allFlags |
| `setFlag(flag, value)` | flag, value | updated |

### TestHandler

| Function | Input | Output |
|----------|-------|--------|
| `runTests(suite)` | suite | testResults |
| `mock(module, method)` | module, method | mockFn |
| `assertResult(actual, expected)` | actual, expected | assertion |

### MigrationHandler

| Function | Input | Output |
|----------|-------|--------|
| `migrate(source, target, transform)` | source, target, transform | migrationResult |
| `getVersion()` | - | version |
| `rollback(version)` | version | rolledBack |

### PythonRunner

| Function | Input | Output |
|----------|-------|--------|
| `execute(script, params)` | script, params | result |
| `validate(script)` | script | validation |

### ResearchTools

| Function | Input | Output |
|----------|-------|--------|
| `search(query, sources)` | query, sources | searchResults |
| `scrape(url, selectors)` | url, selectors | scrapedData |
| `synthesize(sources)` | sources | synthesis |

---

## Integrations

### CanvaIntegration (API Wrapper)

| Function | Input | Output |
|----------|-------|--------|
| `initialize()` | - | initResult |
| `getTemplate(templateId)` | templateId | templateData |
| `listTemplates(folderId?)` | folderId | templates[] |
| `createDesign(templateId, data)` | templateId, data | designId |
| `autofill(designId, data)` | designId, data | autofillResult |
| `exportDesign(designId, format)` | designId, format | exportedFile |
| `getBrandKit()` | - | brandKit |
| `uploadAsset(file, folderId)` | file, folderId | assetId |
| `createFolder(name, parentId?)` | name, parentId | folderId |

### GoogleIntegration (API Wrapper)

| Function | Input | Output |
|----------|-------|--------|
| `initialize()` | - | initResult |
| `getTemplate(templateId, type)` | templateId, type | templateData |
| `createFromTemplate(templateId, data)` | templateId, data | documentId |
| `exportDocument(docId, format)` | docId, format | exportedFile |
| `uploadToDrive(file, folderId)` | file, folderId | fileId |
| `createFolder(name, parentId?)` | name, parentId | folderId |
| `shareDocument(docId, permissions)` | docId, permissions | shareResult |
| `createCalendarEvent(event)` | event | createdEvent |
| `syncCalendar(calendarId)` | calendarId | synced |

### YouTubeAPI

| Function | Input | Output |
|----------|-------|--------|
| `uploadVideo(video, metadata)` | video, metadata | uploadResult |
| `getChannelStats(channelId)` | channelId | stats |
| `managePlaylists(channelId)` | channelId | playlists |

### WebScraper

| Function | Input | Output |
|----------|-------|--------|
| `scrape(url, config)` | url, config | scrapedContent |
| `paginate(url, config)` | url, config | allPages |
| `extractData(html, selectors)` | html, selectors | extractedData |

### AIWebSearch

| Function | Input | Output |
|----------|-------|--------|
| `search(query)` | query | searchResults |
| `synthesize(results)` | results | synthesis |
| `factCheck(claim)` | claim | factCheckResult |

---

## 4-Level Failure Escalation

| Level | Problem | Handler | Solution | Example |
|-------|---------|---------|----------|---------|
| 1 | Transient | Retry logic | Wait and retry | API timeout, rate limit |
| 2 | Missing data | Librarian | Find alternative | Tone not found |
| 3 | No alternative | Self-healing | Create new entry | Generate missing tone |
| 4 | Novel decision | Human | Queue for review | Budget exceeded, auth error |

---

## Summary

| Category | Count |
|----------|-------|
| Core Architecture | 2 |
| AI Services | 5 |
| Brand Management | 4 |
| Visual Design | 12 |
| Content Creation | 8 |
| Content Quality | 4 |
| Social Media | 4 |
| Data Management | 5 |
| Template System | 2 |
| Renderers | 9 |
| Cost & Performance | 3 |
| System Services | 17 |
| Integrations | 8 |
| **Total Modules** | **83** |

---

| Component Type | Count |
|----------------|-------|
| Managers (Domain Coordinators) | 6 |
| Trackers | 1 |
| Modules | 83 |
| Functions | 260+ |
| Steps (Sequences) | 30+ |

---

*Last updated: 2026-01-19*
*Source: DOCS - Preplanning\DOCS - SystemPlan\Modules*
