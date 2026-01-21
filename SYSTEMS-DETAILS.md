# Systems Details

> Overview and Notes for all system modules
> Last Updated: 2026-01-21

---

## ai/

**Text AI services - API calls, model management, token tracking, cost management**

### *AICaller

**Overview:** AICaller makes API calls to AI providers. 

**Notes:**
### Provider Support
| Provider | Status | Use Case | Config |
|----------|--------|----------|--------|
| Anthropic (Claude) | Primary | Complex reasoning, long context | @anthropic-ai/sdk |
| OpenAI (GPT) | Primary | Speed, cost optimization | openai SDK |
| Google AI | Supported | Alternative fast model | REST API |
| Groq | Supported | Ultra-fast inference | REST API |
| OpenRouter | Supported | Multi-model routing | REST API |
| Ollama | Future | Local/GPU models | HTTP |
### Key Responsibilities
- **Make API calls**: Execute requests to AI providers
- **Provider routing**: Route calls based on config, load, fallback strategy
- **Rate limiting**: Respect per-provider limits, queue if needed
- **Retry logic**: Exponential backoff, distinguish transient vs permanent failures
- **Return raw response**: Pass full provider response to caller
- **Error classification**: Transient (retry) vs permanent (send to FailureHandler)
### Data Flow
- **Input**: Receives prompt from PromptBuilder
- **API Keys**: Gets credentials from APIKeyManager
- **Output**: Returns raw provider response
- **Token extraction**: Done by Parser or AISystemManager → TokenManager
- **Cost tracking**: Done by CostTracker using parsed token data
### Depends On
- APIKeyManager - provider credentials
- RateLimiter - enforce rate limits
- FailureHandler - error recovery
- Parser - token extraction
- TokenManager - token tracking
### Feeds Into (Called By)
- AISystemManager - orchestrates AI operations
- ThemeGenerator - theme generation
- OutlineGenerator - outline generation
- All downstream content generation modules
### Architecture Considerations
- **Worker pools per provider**: Separate workers/threads per model to prevent slow calls blocking fast ones
- **Queue management**: Per-provider queues for better load balancing
- **Concurrency limits**: Respect provider-specific concurrent request limits
### Pre-Build Concerns (from MODULE-SPEC-CHECKLIST.md)
**1. Dependencies to Document**
- **Depends on** (modules AICaller calls): APIKeyManager, RateLimiter, FailureHandler
- **Feeds into** (modules that call AICaller): PromptBuilder → AICaller → Parser → TokenManager
- **Downstream consumers**: AISystemManager, ThemeGenerator, OutlineGenerator, and all content generation modules
- This should be explicit in the spec
**2. Error Codes**
- AICaller needs to emit error codes (add to ERROR-CODES.md)
- Examples: `API_RATE_LIMIT_EXCEEDED`, `API_PROVIDER_TIMEOUT`, `API_INVALID_CREDENTIALS`, `API_QUOTA_EXCEEDED`, `API_MALFORMED_RESPONSE`
- These should be added to ERROR-CODES.md *before* FailureHandler can be written
**3. Schemas & Validation**
- AICaller will need Zod schemas for:
  - Provider configuration (API keys, models, rate limits)
  - Request payloads (prompt, model, parameters per provider)
  - Response validation (detect malformed responses, token counts)
  - Error responses (distinguish transient vs permanent)
- These go in `/src/schemas/AICaller.ts` *after* spec approval
**4. Prompt Ownership**
- AICaller doesn't *create* prompts—it *calls* them (PromptBuilder → AICaller)
- This boundary should be clear in the spec
**5. Concern Ownership Cross-Check**
- **Token extraction**: Parser owns this (AICaller returns raw response + raw token counts → Parser extracts structured data → TokenManager tracks)
- **Logging format**: Logger owns (AICaller references Logger spec, not duplicates)
- **Retry logic**: FailureHandler owns (AICaller emits error codes, FailureHandler decides recovery)
- **Rate limiting**: RateLimiter owns (AICaller respects it, doesn't implement it)
**6. What NOT to Include**
- Retry backoff timing (belongs in FailureHandler)
- Logging statements (belongs in Logger spec)
- Validation implementation details (belongs in Validator)
- Cost tracking formulas (belongs in CostTracker)
- Specific provider API keys/secrets (belongs in config files referenced by APIKeyManager)

---

### *AISystemManager

**Overview:** AISystemManager coordinates AI-related modules and manages overall AI system configuration.

**Notes:**
### Role
AISystemManager is a **Manager** (domain coordinator), not a Module. It coordinates AI services but doesn't make API calls itself.
### Coordinates (from ROLE-MATRIX)
- AICaller - makes API calls
- ModelManager - model selection and fallbacks
- TokenManager - token estimation and tracking
- CostBudgetManager - budget checking and cost tracking
### Key Responsibilities
- **Orchestrate AI requests**: Route requests through the right sequence of modules
- **Model selection**: Use ModelManager to pick optimal model for request
- **Pre-flight checks**: Validate budget, estimate tokens before calling
- **Post-call processing**: Extract tokens, track costs, log usage
- **Fallback coordination**: If primary model fails, coordinate fallback sequence
- **System health**: Monitor AI system status, provider availability
### Does NOT Do
- Make API calls directly (AICaller does that)
- Select models (ModelManager does that)
- Track tokens (TokenManager does that)
- Manage budgets (CostBudgetManager does that)
- Build prompts (PromptBuilder does that)
- Parse responses (Parsers does that)
### Data Flow
```
Caller (ThemeGenerator, etc.)
     ↓
AISystemManager.call(request)
     ↓
1. TokenManager.estimateTokens(prompt)
2. CostBudgetManager.checkBudget(brandId, estimate)
3. ModelManager.selectModel(requirements)
     ↓
4. AICaller.call(prompt, model)
     ↓
5. Parsers.parseAIResponse(response)
6. TokenManager.recordUsage(response)
7. CostBudgetManager.recordCost(jobId, cost)
     ↓
Return parsed response to caller
```
### Depends On
- AICaller - execute API calls
- ModelManager - model selection
- TokenManager - token estimation/tracking
- CostBudgetManager - budget management
- Parsers - response parsing
- FailureHandler - error recovery coordination
### Feeds Into (Called By)
- ThemeGenerator - theme generation requests
- OutlineGenerator - outline generation requests
- TemplateEngine - section content generation
- Content - various content generation
- Any module needing AI calls
### Functions (Draft)
| Function | Input | Output |
|----------|-------|--------|
| `call(request)` | AIRequest | AIResponse |
| `callWithFallback(request)` | AIRequest | AIResponse |
| `getSystemStatus()` | - | SystemStatus |
| `getProviderStatus(provider)` | provider | ProviderStatus |
| `estimateCost(request)` | AIRequest | CostEstimate |
### AIRequest Type (Draft)
```javascript
{
  prompt: string,              // compiled prompt from PromptBuilder
  brandId: string,             // for budget tracking
  jobId?: string,              // for cost attribution
  requirements?: {
    maxTokens?: number,        // output limit
    temperature?: number,      // creativity
    speed?: "fast" | "normal", // model selection hint
    quality?: "high" | "standard",
    capabilities?: string[]    // e.g., ["vision", "long-context"]
  },
  fallbackStrategy?: "auto" | "none" | string[],  // model fallback list
  timeout?: number             // ms
}
```
### AIResponse Type (Draft)
```javascript
{
  success: boolean,
  content: any,                // parsed response content
  raw: object,                 // raw provider response
  model: string,               // model actually used
  provider: string,            // provider used
  tokens: {
    input: number,
    output: number,
    total: number
  },
  cost: number,                // calculated cost
  latency: number,             // ms
  fallbackUsed?: boolean       // true if primary failed
}
```
### Error Codes (to add to ERROR-CODES.md)
- `AI_BUDGET_EXCEEDED` - request would exceed brand budget
- `AI_ALL_PROVIDERS_FAILED` - all providers/fallbacks failed
- `AI_NO_SUITABLE_MODEL` - no model meets requirements
- `AI_SYSTEM_UNAVAILABLE` - AI system not initialized
### Architecture Considerations
- **Single entry point**: All AI requests go through AISystemManager for consistent tracking
- **Request queuing**: May need queue for high-volume periods
- **Circuit breaker**: Track provider health, skip unhealthy providers
- **Cost alerts**: Notify when approaching budget limits
- **Audit trail**: Log all AI requests for debugging/billing
### Pre-Build Concerns
1. **Boundary clarity**: Manager coordinates, doesn't implement
2. **Sync vs async**: Some callers need sync, others async
3. **Streaming**: Support streaming responses for long generation
4. **Cancellation**: Allow cancelling in-progress requests

---

### _AIWebSearch

**Overview:** AIWebSearch provides AI-enhanced web search capabilities for research and content gathering.

**Notes:**
-

---

### _CostBudgetManager

**Overview:** CostBudgetManager sets and enforces spending limits for AI calls and external services.

**Notes:**
-

---

### _CostTracker

**Overview:** CostTracker monitors and reports costs from AI API calls and external service usage.

**Notes:**
-

---

### _ModelManager

**Overview:** ModelManager handles AI model selection per task type. Per TECH-STACK.md: Haiku for simple tasks, Sonnet for complex tasks.

**Notes:**
- Model selection per-task (TECH-STACK.md)
- Theme generation: claude-3-sonnet (needs nuance)
- Outline planning: claude-3-sonnet (needs coherence)
- Section content: claude-3-haiku (faster, cheaper)
- Validation/parsing: claude-3-haiku (simple extraction)
-

---

### _TokenManager

**Overview:** TokenManager tracks token usage and enforces limits for AI API calls.

**Notes:**
- Max AI response: Model-dependent token limits (TECH-STACK.md)
-

---

## ai-image/

**AI image generation - prompting, calling, managing visual AI workflows**

### *AIImageCreation

**Overview:** AIImageCreation generates custom images using AI image generation models based on prompts and style guidance.

**Notes:**
### Provider Support
| Provider | Status | Use Case | Config |
|----------|--------|----------|--------|
| DALL-E 3 (OpenAI) | Primary | High-quality generation | openai SDK |
| Midjourney | Future | Artistic styles | API (when available) |
| Stable Diffusion | Supported | Local/self-hosted, variations | REST API |
| Leonardo.ai | Supported | Fast generation, fine-tuned models | REST API |
| Ideogram | Future | Text-in-image | REST API |
### Key Responsibilities
- **Generate images**: Execute image generation from text prompts
- **Apply brand styling**: Ensure generated images match brand visual identity
- **Upscale images**: Increase resolution for print/high-res needs
- **Create variations**: Generate alternative versions of existing images
- **Provider routing**: Route calls based on style requirements, cost, quality needs
### Does NOT Do
- Search stock images (ImageBrowser does that)
- Store/organize images (ImageCollections does that)
- Build image prompts (PromptBuilder does that)
- Composite/layer images (ImageRenderer does that)
- Create templates (ImageTemplate does that)
- Blur/redact sensitive content (separate module)
- Verify brand guidelines compliance (VisualCheck does that)
### Data Flow
```
PromptBuilder → image prompt
     ↓
AIImageCreation.generate(prompt, options)
     ↓
Provider API (DALL-E, etc.)
     ↓
Generated image (URL or binary)
     ↓
ImageCollections (optional: store to brand library)
```
### Depends On
- APIKeyManager - provider credentials (OpenAI, Leonardo, etc.)
- RateLimiter - enforce rate limits per provider
- FailureHandler - error recovery (generation failures, content policy)
- PromptBuilder - builds image prompts from context
- BrandManager - brand style data for applyBrandStyle()
- Logger - expensive/risky operations need logging
### Feeds Into (Called By)
- TemplateEngine - AI-generated image fields in sections
- ImageTemplate - dynamic image generation for templates
- LogoMaker - AI-assisted logo generation
- SocialMediaManager - on-demand social images
### Functions (from ROLE-MATRIX)
| Function | Input | Output |
|----------|-------|--------|
| `generate(prompt, options)` | prompt, options | generatedImage |
| `applyBrandStyle(image, brandId)` | image, brandId | styledImage |
| `upscale(image, scale)` | image, scale | upscaledImage |
| `variations(image, count)` | image, count | imageVariations |
### Options Type (Draft)
```javascript
{
  provider?: string,           // "dalle3" | "leonardo" | "stable-diffusion"
  model?: string,              // provider-specific model ID
  size?: string,               // "1024x1024" | "1792x1024" | "1024x1792"
  quality?: string,            // "standard" | "hd"
  style?: string,              // "vivid" | "natural" (DALL-E specific)
  brandId?: string,            // for brand style application
  negativePrompt?: string,     // what to avoid (Stable Diffusion)
  seed?: number,               // reproducibility
  n?: number                   // number of images (default: 1)
}
```
### Error Codes (to add to ERROR-CODES.md)
- `IMAGE_GENERATION_FAILED` - provider returned error
- `IMAGE_CONTENT_POLICY` - prompt violated content policy
- `IMAGE_RATE_LIMIT` - provider rate limit exceeded
- `IMAGE_QUOTA_EXCEEDED` - account quota exceeded
- `IMAGE_INVALID_SIZE` - requested size not supported
- `IMAGE_PROVIDER_TIMEOUT` - generation timed out
- `IMAGE_PARTIAL_FAILURE` - some images in batch failed
### Architecture Considerations
- **Async generation**: Some providers take 10-60 seconds; need polling/webhooks
- **Cost tracking**: Image generation is expensive; track per-brand costs
- **Content moderation**: Pre-filter prompts to avoid policy violations
- **Caching**: Cache identical prompt results to reduce costs
- **Brand consistency**: Store brand style presets for consistent generation
### Pre-Build Concerns
#### 1. Prompt Building - Interface Design
- Does AIImageCreation receive a full natural-language prompt from PromptBuilder?
- Or does it receive structured data (brand style + visual keywords + mood)?
- **Decision needed**: This affects the interface contract
#### 2. Brand Style Application - `applyBrandStyle()` Clarity
- Does it modify the prompt BEFORE sending to provider?
- Or does it post-process the generated image?
- Does it leverage ColourMix, FontManager, VisualBrand modules?
- Or is it self-contained?
- Where does brand style data come from—BrandManager directly, or through VisualBrand?
#### 3. Image Storage & Metadata Ownership
- Who owns the decision to store generated images? (ImageCollections? Writer?)
- What metadata gets persisted?
  - Generation params, timestamp, prompt, provider, cost
- Does AIImageCreation write to a database (image_generation.db)?
- Or pass raw image + metadata to ImageCollections?
#### 4. Cost Tracking
- Image generation costs are significant and variable by provider/size/quality
- CostTracker should own the formula
- AIImageCreation needs to emit structured cost data:
  - provider, model, tokens/credits consumed
- **Decision**: Separate cost event or bundled with response?
#### 5. Error Handling Nuance
- `IMAGE_CONTENT_POLICY`: Filter prompts before sending, or let provider reject and catch?
- Provider-side timeout vs client-side timeout—how to distinguish?
- Partial failures (1 of 5 images succeeded)—how does that flow to FailureHandler?
#### 6. Async Considerations
- If generation takes 10-60s, is this a synchronous blocking call?
- Or should it return a job ID + polling?
- How does this integrate with Orchestrator job queue/task management?
#### 7. Schema Validation
- Input validation schema for `generate()` options (Zod)
- Validate before sending to expensive API calls

---

### _AIVizCaller

**Overview:** AIVizCaller makes API calls to AI image generation providers (DALL-E, Leonardo, Stable Diffusion, etc.), handling provider-specific configurations, authentication, and response parsing.

**Notes:**
- Executes calls to image generation providers
- Handles provider-specific API formats
- Manages authentication via APIKeyManager
- Respects rate limits via RateLimiter
- Returns generated images (URL or binary) to AIVizManager
- Parallel to AICaller but for image generation

---

### _AIVizManager

**Overview:** AIVizManager coordinates the AI image generation workflow, orchestrating AIVizPrompter, AIVizCaller, and AIVizTemplate. Handles provider selection, cost tracking, and fallback strategies for image generation.

**Notes:**
- Coordinates: AIVizPrompter, AIVizCaller, AIVizTemplate
- Provider selection based on requirements (quality, speed, cost)
- Cost tracking for image generation (separate from text AI costs)
- Fallback handling when provider fails
- Parallel to AISystemManager but for image generation
- May share CostTracker, TokenManager infrastructure

---

### _AIVizPrompter

**Overview:** AIVizPrompter assembles and optimizes prompts for AI image generation, translating brand context, style requirements, and content needs into effective image prompts.

**Notes:**
- Builds image prompts from brand style + visual keywords + mood
- Translates structured data into natural language prompts
- Handles negative prompts for providers that support them
- May leverage brand data from BrandManager, VisualBrand
- Outputs to AIVizCaller

---

### _AIVizTemplate

**Overview:** AIVizTemplate manages reusable templates for AI image generation, storing prompt patterns, style presets, and brand-specific image configurations.

**Notes:**
- Stores reusable prompt templates for common image types
- Brand-specific style presets (colors, mood, composition)
- Template variables for dynamic content
- Different from ImageTemplate (which applies visual overlays to existing images)
- Feeds into AIVizPrompter for prompt assembly

---

## brand/

**Brand management - brand profiles, audience management, brand tracking**

### _AudienceManager

**Overview:** AudienceManager handles audience definitions, segments, and targeting criteria stored in /repos/audiences/.

**Notes:**
- Audience data in /repos/audiences/
-

---

### _BrandCheck

**Overview:** BrandCheck validates that generated content aligns with brand voice, uses correct terminology, and maintains brand identity. Focuses specifically on brand-level alignment.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### What BrandCheck Validates
| Check | Description |
|-------|-------------|
| Voice core present | Core voice elements appear in language |
| Terminology correct | Uses brand-specific terms correctly |
| Avoid list honored | Doesn't use prohibited terms/approaches |
| Signature phrases | Brand phrases used naturally |
| Philosophy consistent | Content doesn't contradict brand philosophy |
### Distinct From Other Checkers
- **ValuesCheck**: Brand values alignment (separate)
- **ContentAlignmentCheck**: Audience, theme, structure (separate)
- **TransformationCheck**: Belief journey, promises (separate)
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100
  checks: {
    voiceCore: { passed, evidence },
    terminology: { passed, issues },
    avoidList: { passed, violations },
    signaturePhrases: { passed, found, missing },
    philosophy: { passed, conflicts }
  }
}
```
### Dependencies
| Depends On | For |
|------------|-----|
| Brand data | Voice, terminology, avoid list |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Brand alignment scoring |

---

### _BrandManager

**Overview:** BrandManager handles brand configuration including voice, values, and governance settings stored in /repos/brands/.

**Notes:**
- Needs truthApprovalMode (strict/guided/trusted) for truth governance
- Brand data in /repos/brands/
- Truth approval workflow: strict (all require human approval), guided (auto-approve but flag for review), trusted (auto-approve for whitelisted brands)
-

---

### _BrandTracker

**Overview:** BrandTracker monitors brand consistency across generated content and tracks brand-related metrics.

**Notes:**
-

---

## checks/

**Quality checks - grammar, spelling, language, layout, values validation**

### _GrammarCheck

**Overview:** GrammarCheck detects grammar issues in generated content. Uses Python-based grammar checking (LanguageTool, textblob, or similar). Some issues can be auto-fixed.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### Capabilities
| Capability | Auto-Fix | Notes |
|------------|----------|-------|
| Subject-verb agreement | Some | Rule-based |
| Tense consistency | No | Flag only |
| Punctuation | Yes | Common patterns |
| Run-on sentences | No | Flag only |
| Fragment sentences | No | Flag only |
| Word repetition | No | Flag only |
| Passive voice | No | Flag only (style choice) |
### Tech Stack
- Python runtime required
- LanguageTool (comprehensive) or textblob (lighter)
- May need Java for LanguageTool
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100 (100 = no issues)
  issueCount: number,
  issues: [
    {
      type: string,           // "agreement", "tense", "punctuation", etc.
      text: string,
      suggestion: string,
      position: { start, end },
      autoFixable: boolean,
      severity: "error" | "warning" | "style"
    }
  ],
  autoFixed?: {
    content: string,
    fixCount: number
  }
}
```
### Style vs Error
| Type | Action |
|------|--------|
| Error | Must fix |
| Warning | Should fix |
| Style | Optional (passive voice, etc.) |
### Dependencies
| Depends On | For |
|------------|-----|
| Python runtime | Grammar checking |
| LanguageTool/textblob | Analysis |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Grammar validation |

---

### _LanguageCheck

**Overview:** ~~LanguageCheck validates language quality including grammar, spelling, and readability.~~

---

### _LayoutCheck

**Overview:** LayoutCheck validates the structural layout of rendered content. Catches issues like orphaned headers, page break problems, whitespace issues, and formatting inconsistencies.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### What LayoutCheck Validates
| Check | Auto-Fix | Notes |
|-------|----------|-------|
| Orphan/widow lines | Some | Page break adjustment |
| Header hierarchy | No | H1 → H2 → H3 order |
| Section spacing | Yes | Normalize gaps |
| List formatting | Yes | Consistent bullets/numbers |
| Paragraph length | No | Flag if too long/short |
| Whitespace | Yes | Remove excess |
### Distinct From VisualCheck
- **LayoutCheck**: Structure, formatting, text flow
- **VisualCheck**: Images, colors, visual elements
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100
  issues: [
    {
      type: string,           // "orphan", "whitespace", "hierarchy"
      severity: "critical" | "major" | "minor",
      location: string,
      description: string,
      autoFixable: boolean
    }
  ],
  autoFixed?: {
    changeCount: number,
    changes: string[]
  }
}
```
### Content Type Rules
| Content Type | Extra Checks |
|--------------|--------------|
| Workbook | Exercise boxes, reflection space |
| PDF | Page margins, headers/footers |
| Email | Line length, mobile width |
### Dependencies
| Depends On | For |
|------------|-----|
| Rendered output | Layout analysis |
| Content type config | Type-specific rules |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Layout validation |

---

### _PostCreationCheckManager

**Overview:** PostCreationCheckManager coordinates multiple quality checkers after content generation. It runs checks in parallel, aggregates scores, and decides whether to accept, flag, auto-fix, or regenerate content.
```
┌─────────────────────────────────────────────────────────────────┐
│                    POST-CREATION CHECK FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  TemplateEngine.renderSection()                                 │
│           │                                                      │
│           ↓                                                      │
│  PostCreationCheckManager.check(content, context, scope)        │
│           │                                                      │
│           ├──→ BrandCheck            ──→ brand score            │
│           ├──→ ValuesCheck           ──→ values score           │
│           ├──→ ContentAlignmentCheck ──→ content score          │
│           ├──→ TransformationCheck   ──→ transformation score   │
│           ├──→ SpellingCheck         ──→ spelling score         │
│           ├──→ GrammarCheck          ──→ grammar score          │
│           ├──→ LayoutCheck           ──→ layout score           │
│           └──→ VisualCheck           ──→ visual score           │
│           │        (all run in parallel)                        │
│           ↓                                                      │
│  Aggregate scores + decide action                               │
│           │                                                      │
│           ├─── All pass ────────→ Accept                        │
│           ├─── Minor issues ────→ Accept + warnings             │
│           ├─── Fixable issues ──→ Auto-fix + recheck            │
│           └─── Major issues ────→ Regenerate or flag            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Notes:**
### Checker Registry
| Checker | Category | Auto-Fix | Notes |
|---------|----------|----------|-------|
| BrandCheck | Brand | No | Voice, terminology, identity |
| ValuesCheck | Brand | No | Values, principles, ethics |
| ContentAlignmentCheck | Content | No | Audience, theme, tone, structure |
| TransformationCheck | Content | No | Belief journey, promises |
| SpellingCheck | Language | Yes | Python-based spelling |
| GrammarCheck | Language | Some | Python-based grammar |
| LayoutCheck | Visual | Some | Structure, formatting, spacing |
| VisualCheck | Visual | Some | Images, colors, graphics |
### Validation Scopes
| Scope | Checkers Run | Use Case |
|-------|--------------|----------|
| `quick` | SpellingCheck, GrammarCheck | Drafts, rapid iteration |
| `language` | SpellingCheck, GrammarCheck | Language review only |
| `brand` | BrandCheck, ValuesCheck | Brand compliance only |
| `content` | ContentAlignmentCheck, TransformationCheck | Content quality only |
| `visual` | LayoutCheck, VisualCheck | Visual review only |
| `standard` | All except Visual | Normal generation |
| `full` | All checkers | Final output, premium tier |
| `custom` | Specified list | Specific needs |
### Category Weights
```javascript
const categoryWeights = {
  brand: 0.20,        // BrandCheck + ValuesCheck
  content: 0.35,      // ContentAlignmentCheck + TransformationCheck
  language: 0.25,     // SpellingCheck + GrammarCheck
  visual: 0.20        // LayoutCheck + VisualCheck
};
```
### Decision Matrix
| Scenario | Action |
|----------|--------|
| All scores ≥ 80 | Accept |
| Any score 60-79, none < 60 | Accept + warnings |
| Language < 80, auto-fixable | Auto-fix + recheck |
| Layout minor issues | Auto-fix + recheck |
| Brand/Content < 60 | Regenerate with feedback |
| Multiple scores < 60 | Flag for human review |
| Any critical failure | Block + flag |
> **See Also:** Detailed thresholds, weights, and critical failure rules in [_constants.md](../../reference/_constants.md#postcreationcheckmanager-thresholds)
### Functions (Planned)
| Function | Input | Output |
|----------|-------|--------|
| `check(content, context, scope)` | content, context, scope | CheckResult |
| `runCheckers(checkers, content, context)` | checkers[], content, context | CheckerResults[] |
| `aggregate(results, strategy)` | results[], strategy | AggregatedScore |
| `decide(aggregated, thresholds)` | score, thresholds | Decision |
| `autoFix(content, fixableIssues)` | content, issues | FixedContent |
| `recheck(content, failedCheckers)` | content, checkers | RecheckResult |
### Output Type
```javascript
{
  passed: boolean,
  overallScore: number,           // 0-100 weighted aggregate
  categoryScores: {
    brand: number,
    content: number,
    language: number,
    visual: number
  },
  checkerResults: {
    brandCheck: CheckerResult,
    valuesCheck: CheckerResult,
    contentAlignmentCheck: CheckerResult,
    transformationCheck: CheckerResult,
    spellingCheck: CheckerResult,
    grammarCheck: CheckerResult,
    layoutCheck: CheckerResult,
    visualCheck: CheckerResult
  },
  decision: {
    action: "accept" | "accept_with_warnings" | "auto_fix" | "regenerate" | "flag",
    reasons: string[],
    autoFixApplied?: boolean,
    fixedContent?: string
  },
  issues: Issue[],                // Aggregated from all checkers
  warnings: string[]
}
```
### Dependencies
| Depends On | For |
|------------|-----|
| BrandCheck | Brand voice validation |
| ValuesCheck | Values alignment |
| ContentAlignmentCheck | Content alignment scoring |
| TransformationCheck | Transformation validation |
| SpellingCheck | Spelling validation |
| GrammarCheck | Grammar validation |
| LayoutCheck | Layout validation |
| VisualCheck | Visual validation |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| TemplateEngine | Post-render validation |

---

### _SpellGrammarCheck

**Overview:** SpellGrammarCheck validates spelling and grammar in generated content. Uses Python-based tools (LanguageTool, pyspellchecker, or similar) for detection and can auto-fix common issues.
~~Runs as part of PostCreationCheckManager (in parallel with other checkers).~~

**Notes:**
### Capabilities
| Capability | Auto-Fix | Notes |
|------------|----------|-------|
| Spelling errors | Yes | Dictionary-based correction |
| Grammar issues | Some | Rule-based fixes only |
| Punctuation | Yes | Common patterns |
| Capitalization | Yes | Sentence starts, proper nouns |
| Word repetition | No | Flag only |
| Passive voice | No | Flag only (style choice) |
### Tech Stack Options
| Tool | Pros | Cons |
|------|------|------|
| LanguageTool | Comprehensive, multi-language | Java dependency |
| pyspellchecker | Pure Python, fast | Spelling only |
| textblob | Easy API | Limited grammar |
| Grammarly API | Best quality | Paid, external |
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100 (100 = no issues)
  issueCount: number,
  issues: [
    {
      type: "spelling" | "grammar" | "punctuation",
      text: string,           // The problematic text
      suggestion: string,     // Suggested fix
      position: { start, end },
      autoFixable: boolean
    }
  ],
  autoFixed?: {
    content: string,          // Content with auto-fixes applied
    fixCount: number
  }
}
```
### Thresholds
| Metric | Threshold | Notes |
|--------|-----------|-------|
| Score to pass | ≥85 | Allows minor issues |
| Auto-fix trigger | Score < 85 | Apply fixes if below |
| Recheck after fix | Required | Verify fixes worked |
### Brand Dictionary
Support brand-specific terms that shouldn't be flagged:
- Brand name variations
- Product names
- Industry jargon
- Proprietary terms
### Dependencies
| Depends On | For |
|------------|-----|
| Python runtime | Spell/grammar tools |
| Brand dictionary | Custom terms |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Language quality |

---

### _SpellingCheck

**Overview:** SpellingCheck detects spelling errors in generated content. Uses Python-based spell checking (pyspellchecker or similar). Can auto-fix common misspellings.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### Capabilities
| Capability | Auto-Fix | Notes |
|------------|----------|-------|
| Misspellings | Yes | Dictionary-based |
| Typos | Yes | Common patterns |
| Capitalization | Yes | Proper nouns, sentence starts |
| Brand terms | No | Custom dictionary, flag only |
### Tech Stack
- Python runtime required
- pyspellchecker or hunspell
- Brand-specific dictionary support
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100 (100 = no errors)
  errorCount: number,
  errors: [
    {
      word: string,
      suggestions: string[],
      position: { start, end },
      autoFixable: boolean
    }
  ],
  autoFixed?: {
    content: string,
    fixCount: number
  }
}
```
### Brand Dictionary
Custom terms that shouldn't be flagged:
- Brand name variations
- Product names
- Industry jargon
### Dependencies
| Depends On | For |
|------------|-----|
| Python runtime | Spell checking |
| Brand dictionary | Custom terms |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Spelling validation |

---

### _TransformationCheck

**Overview:** TransformationCheck validates that generated content properly facilitates the belief transformation journey defined in the theme. It checks that belief shifts are supported, promised outcomes are delivered, and brand perspective is enforced.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### What TransformationCheck Validates
| Check | Description |
|-------|-------------|
| Belief shifts facilitated | Content moves reader from old → new belief |
| Signature phrases present | Brand's signature phrases appear naturally |
| Rejected language absent | Brand's "whatImRejecting" language avoided |
| Promised outcomes addressed | Section.promisesOutcome[] delivered |
| Emotional journey followed | Content matches theme.emotionalJourney |
### Distinct from ContentAlignment
**ContentAlignment** (separate checker):
- General alignment: brand voice, audience language, theme truths, tone, structure
- Scores: 0-100 per category
**TransformationCheck** (this module):
- Transformation-specific: belief shifts, promises, brand perspective enforcement
- Binary + score: { passed: boolean, score: number, issues: string[] }
Both run in parallel via PostCreationCheckManager.
### Inputs
```javascript
{
  content: string,           // Generated content
  theme: Theme,              // For transformationFramework, beliefShifts
  outline: Outline,          // For section.promisesOutcome
  brand: Brand               // For signature phrases, rejected language
}
```
### Output
```javascript
{
  passed: boolean,
  score: number,             // 0-100
  checks: {
    beliefShifts: { passed, issues },
    signaturePhrases: { passed, found, missing },
    rejectedLanguage: { passed, violations },
    promisedOutcomes: { passed, delivered, missing },
    emotionalJourney: { passed, issues }
  },
  issues: string[],
  suggestions: string[]
}
```
### Thresholds
| Check | Pass Threshold | Notes |
|-------|----------------|-------|
| Belief shifts | ≥1 shift supported | Must facilitate at least one |
| Signature phrases | ≥50% present | At least half of signature phrases |
| Rejected language | 0 violations | Zero tolerance |
| Promised outcomes | 100% delivered | All promises must be kept |
### Dependencies
| Depends On | For |
|------------|-----|
| (none) | Stateless checker |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Transformation validation |

---

### _ValuesCheck

**Overview:** ValuesCheck validates that generated content embodies and reflects the brand's core values and principles. Separate from brand voice/terminology (BrandCheck) - focuses on deeper value alignment.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### What ValuesCheck Validates
| Check | Description |
|-------|-------------|
| Values reflected | Brand values implicit in content |
| Principles upheld | Core principles not contradicted |
| Ethical alignment | Content meets ethical standards |
| Mission support | Content advances brand mission |
### Distinct From BrandCheck
- **BrandCheck**: Voice, terminology, identity (surface)
- **ValuesCheck**: Values, principles, ethics (deeper)
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100
  checks: {
    valuesReflected: { passed, valuesFound, valuesMissing },
    principlesUpheld: { passed, conflicts },
    ethicalAlignment: { passed, concerns },
    missionSupport: { passed, evidence }
  }
}
```
### Dependencies
| Depends On | For |
|------------|-----|
| Brand data | Values, principles, mission |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Values alignment scoring |

---

## colours/

**Colour systems - colour creation, mixing, token management**

### _ColourMaker

**Overview:** ColourMaker generates colour palettes and schemes for brand consistency and visual design.

**Notes:**
-

---

### _ColourMix

**Overview:** ColourMix provides pure functions for colour manipulation including mixing, blending, and colour math.

**Notes:**
- Pure functions (no side effects)
-

---

### _ColourToken

**Overview:** ColourToken manages named colour tokens for design system consistency across outputs.

**Notes:**
-

---

## content/

**Content creation - content management, email, PDF, legal docs, exports**

### _Content

**Overview:** Content handles core content generation logic and content lifecycle management.

**Notes:**
-

---

### _ContentAlignment

**Overview:** ContentAlignment validates that generated content aligns with all original input fields: brand voice, tone, audience psychographics, framework elements, theme truths, and content structure requirements. It provides grading scores and identifies specific misalignments.
```
┌─────────────────────────────────────────────────────────────────┐
│                    CONTENT ALIGNMENT PIPELINE                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  INPUTS                          CONTENT                         │
│  ├─ Brand (voice, values)        Generated section/paragraph     │
│  ├─ Audience (psychographics)              │                     │
│  ├─ Theme (truths, beliefShifts)           │                     │
│  ├─ Framework (stages, prompts)            ↓                     │
│  ├─ Tone (merged tone notes)     ┌─────────────────┐            │
│  └─ Structure (section spec)     │ ContentAlignment │            │
│                                  └────────┬────────┘            │
│                                           │                      │
│                                           ↓                      │
│                             ┌─────────────────────────┐         │
│                             │   AlignmentReport       │         │
│                             │   ├─ overallScore       │         │
│                             │   ├─ categoryScores     │         │
│                             │   ├─ misalignments[]    │         │
│                             │   └─ suggestions[]      │         │
│                             └─────────────────────────┘         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Check implementations**: Need NLP/embedding-based checks for semantic alignment
- **Baseline calibration**: Establish baseline scores from successful content
- **Learning**: Track which misalignments correlate with poor content performance
- **Custom checks**: Allow brands to add custom alignment checks
- **Batch validation**: Support validating entire outlines efficiently
### Alignment Check Approaches
1. **Keyword matching**: Check for presence of required terms
2. **Semantic similarity**: Embedding-based comparison to reference content
3. **Pattern matching**: Regex for structure verification
4. **LLM-based**: Use AI to evaluate subjective alignment (tone, voice)
### Performance Considerations
- Cache brand/audience embeddings for similarity checks
- Run checks in parallel where independent
- Skip expensive checks for draft content (full checks on final only)

---

### _ContentAlignmentCheck

**Overview:** ContentAlignmentCheck validates that generated content aligns with audience expectations, theme truths, tone requirements, and structural specifications. Focuses on content-level alignment (not brand-level).
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### What ContentAlignmentCheck Validates
| Check | Description |
|-------|-------------|
| Audience language | Language matches audience cognitive level |
| Audience pain points | References audience challenges |
| Theme truths present | Primary/secondary truths appear |
| Theme concept coherent | Stays on-topic with theme |
| Tone consistent | Matches merged tone guidance |
| Structure followed | Section type requirements met |
| Framework elements | Required elements present |
### Distinct From Other Checkers
- **BrandCheck**: Brand voice, terminology (separate)
- **ValuesCheck**: Brand values, principles (separate)
- **TransformationCheck**: Belief journey, promises (separate)
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100
  categoryScores: {
    audience: { score, checks },
    theme: { score, checks },
    tone: { score, checks },
    structure: { score, checks },
    framework: { score, checks }
  }
}
```
### Dependencies
| Depends On | For |
|------------|-----|
| Audience data | Language level, pain points |
| Theme data | Truths, concept, tone |
| Section spec | Structure requirements |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Content alignment scoring |

---

### _ContentManager

**Overview:** ContentManager coordinates content creation across themes, outlines, templates, and content modules.

**Notes:**
- Show "Verified" badge for themes where humanReviewed: true
-

---

### _ContentTextAssessor

**Overview:** ContentTextAssessor evaluates content quality metrics including readability scores, complexity, and engagement potential.

**Notes:**
-

---

### _ContentValidator

**Overview:** ContentValidator checks that AI-generated content properly reflects the transformation framework, brand perspective, and promised outcomes. Runs post-generation to ensure quality and alignment.

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Transformation framework validation**:
  - Check beliefShifts are facilitated in content
  - Verify primaryOutcome is addressed
  - Ensure audienceTransformation promises are delivered
- **Brand perspective enforcement**:
  - Check signature phrases appear (from natashaPerspective.signaturePhrases)
  - Verify "whatImRejecting" language is avoided
  - Confirm brand voice alignment
- **Promised outcomes validation**:
  - Verify section.promisesOutcome[] are delivered
  - Check theyGain/theyExperience/theyWalkAwayWith alignment
- **Quality scoring**:
  - Coherence score (0-1)
  - Brand alignment score (0-1)
  - Audience fit score (0-1)
  - Template compliance (pass/fail)
### Validation Workflow
1. Receive: filled content + theme + outline
2. Extract: transformation framework from theme
3. Check: signature phrases present
4. Check: rejected language absent
5. Check: belief shifts facilitated
6. Score: overall quality
7. Return: { valid: boolean, score: number, issues: string[] }
### Integration Points
- Called by: TemplateEngine (post-fill validation)
- Reads: Theme.transformationFramework
- Reads: Outline.sections[].promisesOutcome
- Reports to: QualityTracker (if exists)
### Relationship to ContentAlignment
**ContentAlignment** (separate module) handles general alignment grading:
- Brand voice/values alignment
- Audience language/level match
- Theme truth presence
- Tone consistency
- Framework element coverage
- Structure compliance
**ContentValidator** handles transformation-specific validation:
- Belief shifts facilitated
- Brand perspective enforcement (signature phrases, rejected language)
- Promised outcomes delivered
- Transformation promises kept
**Decision needed:** Merge into one module or keep separate with ContentValidator calling ContentAlignment for general scoring.
### Thresholds (from _constants.md)
| Metric | Threshold | Action if Below |
|--------|-----------|-----------------|
| Coherence | 0.7 | Flag for review |
| Brand alignment | 0.8 | Regenerate with more context |
| Audience fit | 0.75 | Adjust language patterns |

---

### _Email

**Overview:** Email generates email content using templates from /repos/email-templates/.

**Notes:**
- Uses templates from /repos/email-templates/
-

---

### _EmailLit

**Overview:** EmailLit generates more literary, narrative-style email content for storytelling and engagement.

**Notes:**
-

---

### _ExportManager

**Overview:** ExportManager coordinates export operations across renderers and output formats.

**Notes:**
-

---

### _LegalDocCreator

**Overview:** LegalDocCreator generates legal documents (terms, policies, agreements) using templates from /repos/templates/.

**Notes:**
- Uses templates from /repos/templates/
-

---

### _PDFMaker

**Overview:** PDFMaker creates PDFs with complex layouts, graphics, and formatting beyond basic PDFRenderer capabilities.

**Notes:**
-

---

### _WebsiteContent

**Overview:** WebsiteContent generates content optimized for websites including landing pages, about pages, and web copy.

**Notes:**
-

---

## image/

**Image handling - browsing, collections, rendering, templates (non-AI)**

### _ImageBrowser

**Overview:** ImageBrowser searches and retrieves stock images from Unsplash using visual keywords from themes/outlines.

**Notes:**
- Uses unsplash-js library (TECH-STACK.md)
- Receives Visual Nouns and Action Verbs from ThemeGenerator keywords
- imageKeywords from OutlineGenerator sections guide searches
-

---

### _ImageCollections

**Overview:** ImageCollections manages brand-specific image libraries for consistent visual identity.

**Notes:**
- TemplateEngine uses for image fields
-

---

### _ImageFieldRenderer

**Overview:** ImageFieldRenderer handles image field processing within templates. Resolves image sources, generates alt text, handles responsive images, and provides placeholder fallbacks.

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Image source resolution**: Resolve image URLs/paths from various sources
- **Alt text generation**: Generate accessible alt text from context
- **Responsive image handling**: Support srcset and sizes for responsive images
- **Placeholder fallbacks**: Provide fallback images when source unavailable
- **Format conversion**: Handle different image formats as needed
- **Integration with ImageBrowser**: Use theme.keywords for image selection
### Used By
- TemplateEngine: For rendering image fields in templates
- Social templates: Platform-specific image requirements

---

### _ImageRenderer

**Overview:** ImageRenderer outputs PNG/JPG images using Sharp/Canvas libraries. Receives filled sections from TemplateEngine and produces image files.

**Notes:**
- Uses Sharp/Canvas for image generation
- For social media graphics, infographics, etc.
-

---

### _ImageTemplate

**Overview:** ImageTemplate applies visual templates to images for consistent branding and styling.

**Notes:**
-

---

## integrations/

**External APIs - Canva, Google, YouTube, web scraping**

### _CanvaIntegration

**Overview:** CanvaIntegration wraps the Canva Connect API for design creation and management via @canva/design-api.

**Notes:**
- Library: @canva/design-api (TECH-STACK.md)
- Used by CanvaRenderer
-

---

### _GoogleIntegration

**Overview:** GoogleIntegration wraps Google Workspace APIs (Docs, Sheets, Slides) via googleapis library.

**Notes:**
- Library: googleapis (TECH-STACK.md)
- Used by GoogleDocsRenderer, GoogleSheetsRenderer, GoogleSlidesRenderer
-

---

### _WebScraper

**Overview:** WebScraper extracts content from websites for research and data gathering purposes.

**Notes:**
-

---

### _YouTubeAPI

**Overview:** YouTubeAPI provides YouTube video integration for embedding and video content management via googleapis.

**Notes:**
- Library: googleapis (TECH-STACK.md)
-

---

## logo/

**Logo generation and management**

### _LogoMaker

**Overview:** LogoMaker creates and manages logo assets and variations.

**Notes:**
-

---

## orchestration/

**System orchestration - handlers, queues, scheduling, policies, analytics**

### _AnalyticsHandler

**Overview:** AnalyticsHandler collects metrics on system usage, content generation patterns, and performance.

**Notes:**
-

---

### _BatchProcessor

**Overview:** BatchProcessor handles batch operations for bulk content generation, updates, and processing.

**Notes:**
-

---

### _CacheHandler

**Overview:** CacheHandler stores and retrieves cached data to reduce AI calls and improve performance. Manages cache.db with configurable TTLs per data type.

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Cache key generation**: Consistent keys from input params (brandId + themeId + templateId + hash)
- **TTL management**: Per-data-type TTLs (themes: 48h, outlines: 24h, AI responses: 1h)
- **Invalidation strategies**:
  - Time-based expiry
  - Event-based (library update → invalidate related cache)
  - Manual flush
- **Memory vs disk**: Hot data in memory, cold data in cache.db
- **Section-level caching**: Cache individual sections for partial regeneration
- **Cache warming**: Pre-populate common queries on startup

---

### _FeatureFlags

**Overview:** FeatureFlags enables/disables features at runtime without code changes. Supports gradual rollouts and A/B testing.

**Notes:**
-

---

### _GlobalRules

**Overview:** GlobalRules defines and enforces system-wide rules, constraints, and configuration.

**Notes:**
-

---

### _MigrationHandler

**Overview:** MigrationHandler manages forward-only database migrations. Scripts stored in /migrations/ directory.

**Notes:**
- Forward-only migrations (no downgrades)
- Migration scripts in /migrations/
- Per TECH-STACK.md: Custom scripts approach
-

---

### _Notification

**Overview:** Notification sends alerts about job completions, errors, and system events via configured channels.

**Notes:**
-

---

### _Orchestrator

**Overview:** Orchestrator coordinates content generation jobs, manages job queue (jobs.db), and orchestrates module execution.

**Notes:**
- Owns jobs.db (TECH-STACK.md)
- Use activeOn filter in ThemeGenerator.list() to hide expired trending themes from UI
-

---

### _PolicyEngine

**Overview:** PolicyEngine enforces business rules, content policies, and governance constraints across the system.

**Notes:**
-

---

### _QueueHandler

**Overview:** QueueHandler manages job queues for asynchronous processing. Works with jobs.db for persistence.

**Notes:**
- Works with jobs.db (per TECH-STACK.md)
-

---

### _RateLimiter

**Overview:** RateLimiter throttles outbound API calls to stay within provider limits. Default: 3 concurrent AI calls (per TECH-STACK.md).

**Notes:**
- Concurrent AI calls: 3 default (TECH-STACK.md constraint)
### Feeds Into
- AICaller - enforces rate limits on AI API calls

---

### _RequestHandler

**Overview:** RequestHandler receives and routes incoming requests to the appropriate processing modules.

**Notes:**
-

---

### _Scheduler

**Overview:** Scheduler manages timing and execution of automated jobs. Multiple auto-generate jobs are queued via Scheduler.

**Notes:**
- Manual import has priority; auto-generate goes to scheduler queue
- Multiple auto-generate jobs are queued
- Template updates wait until generation completes
-

---

### _StatusHandler

**Overview:** StatusHandler provides system health checks and job status reporting.

**Notes:**
-

---

### _TestHandler

**Overview:** TestHandler runs tests and validates system behavior. Integrates with the testing framework (likely Vitest per TECH-STACK.md).

**Notes:**
- Testing framework: TBD (Vitest likely)
-

---

## renderers/

**Output renderers - PDF, DOCX, HTML, Markdown, Google Docs/Sheets/Slides, Canva**

### _CanvaRenderer

**Overview:** CanvaRenderer maps section types to Canva page layouts and outputs designs via CanvaIntegration. Receives filled sections from TemplateEngine and produces Canva documents.

**Notes:**
- Outputs via CanvaIntegration API wrapper
- Maps section types to Canva page layouts
-

---

### _DOCXRenderer

**Overview:** DOCXRenderer outputs Microsoft Word documents using docx library. Receives filled sections from TemplateEngine and produces .docx files.

**Notes:**
- Uses docx library for Word document generation
-

---

### _GoogleDocsRenderer

**Overview:** GoogleDocsRenderer maps section types to Google Docs layouts and outputs documents via GoogleIntegration. Receives filled sections from TemplateEngine and produces Google Docs.

**Notes:**
- Outputs via GoogleIntegration API wrapper
- Maps section types to Google Docs formatting
-

---

### _GoogleSheetsRenderer

**Overview:** GoogleSheetsRenderer maps section types to Google Sheets layouts and outputs spreadsheets via GoogleIntegration. Receives filled sections from TemplateEngine and produces Google Sheets.

**Notes:**
- Outputs via GoogleIntegration API wrapper
- Maps section types to spreadsheet cells/formatting
-

---

### _GoogleSlidesRenderer

**Overview:** GoogleSlidesRenderer maps section types to Google Slides layouts and outputs presentations via GoogleIntegration. Receives filled sections from TemplateEngine and produces Google Slides.

**Notes:**
- Outputs via GoogleIntegration API wrapper
- Maps section types to slide layouts
-

---

### _HTMLRenderer

**Overview:** HTMLRenderer outputs HTML files. Receives filled sections from TemplateEngine and produces HTML documents for web use.

**Notes:**
- For web content, email HTML, landing pages
-

---

### _MarkdownRenderer

**Overview:** MarkdownRenderer outputs Markdown (.md) files. Receives filled sections from TemplateEngine and produces formatted Markdown documents.

**Notes:**
- Simple text-based output
- Useful for documentation, static sites, GitHub content
-

---

### _PDFRenderer

**Overview:** PDFRenderer outputs PDF documents using PDFKit library. Receives filled sections from TemplateEngine and produces PDF files.

**Notes:**
- Uses PDFKit for PDF generation
- Default renderer in TemplateEngine config
-

---

## services/

**Core services - file management, auth, database, prompts, libraries**

### _APIKeyManager

**Overview:** APIKeyManager securely stores and retrieves API keys for external integrations (Anthropic, OpenAI, Google, Canva, Unsplash, etc.).

**Notes:**
### Feeds Into
- AICaller - provides API credentials for AI providers

---

### _AuthHandler

**Overview:** AuthHandler manages user authentication and authorization. Currently single-user system; multi-user layer to be added if needed.

**Notes:**
- Currently single-user system
- Multi-user concurrency layer to be added if needed (per TECH-STACK.md)
-

---

### _DatabaseHandler

**Overview:** DatabaseHandler provides SQLite operations for all library databases. Handles connection pooling, query building, version management, and full-text search helpers.

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Connection pooling**: Manage SQLite connections efficiently
- **Query builders**: Common patterns for CRUD, list, search
- **Version management**: Read latest vs specific version
- **FTS query helpers**: Unified interface for full-text search
- **Transaction handling**: Atomic operations for multi-step updates
- **Index optimization**: Ensure indexes exist for common queries
- **Backup/restore**: Support database backup before migrations
### Data Access Pattern
- Each module accesses its OWN database directly via DatabaseHandler
- External modules access other repos via FileManager (abstracted)

---

### _IngestionEngine

**Overview:** IngestionEngine manages the promotion of drafts from staging areas to active library entries. Validates references, handles version increments, and maintains audit trails for all promotions.

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Staging validation**: Validate draft entries before promotion
  - Schema validation against library specs
  - Reference integrity (all referenced IDs exist)
  - Required fields present
- **Promotion workflow**:
  1. Validate draft in staging area
  2. Check reference integrity via Librarian
  3. Assign version number (increment or initial)
  4. Insert into library database
  5. Update `_index` table
  6. Log promotion in audit trail
  7. Remove from staging area
- **Version increment logic**:
  - New entry: version = 1
  - Update: version = latest + 1
- **Rollback support**: Ability to revert promotion if issues discovered
- **Batch promotion**: Promote multiple related drafts atomically
- **Audit trail**: Log who promoted what and when
### Integration Points
- Reads from: staging area (`/repos/drafts/`)
- Writes to: library databases via DatabaseHandler
- Validates via: Librarian (reference integrity)
- Notifies: CacheHandler (invalidate related cache)

---

### _Librarian

**Overview:** Librarian provides the discovery layer across repos and databases. While FileManager handles direct fetches (know the ID), Librarian handles discovery (search, list, recommend, suggest).

**Notes:**
- FileManager handles direct fetch operations
- Librarian handles discovery operations
### Dev Team Gap Analysis (2026-01-20)
- **Multi-library search**: Aggregate results across brands, audiences, themes, templates
- **Recommendation engine**: Suggest themes/templates based on usage patterns
- **Reference integrity checking**: Validate cross-library references exist
- **Cascade notifications**: When entity archived, notify dependents
  - Archive cascade: Mark `ref_status='stale'` on soft dependents
  - Block archive if active hard dependents exist
- **FTS query helpers**: Unified search interface across all FTS-enabled libraries
- **Cache invalidation signals**: Notify CacheHandler when library data changes
### Reference Integrity Workflow
1. **On Archive Request**: Check dependents via `ref_status` queries
2. **Hard Dependency Found**: Block archive, return dependent IDs
3. **Soft Dependency Found**: Archive proceeds, mark dependents `ref_status='stale'`
4. **No Dependents**: Archive proceeds normally
5. **Notify**: CacheHandler invalidates affected entries
### Cascade Rules by Library
| Library | On Archive | Dependents Affected |
|---------|------------|---------------------|
| brands | warn | themes, audiences, frameworks (proprietary) |
| audiences | warn | themes |
| themes | block if active outlines | outlines |
| templates | block if active outlines | outlines |
| outcomes | mark themes stale | themes |
| cognitive-types | warn | templates, outlines |
| frameworks | warn | content-type-frameworks, themes, outlines |
| content-type-frameworks | warn | templates, outlines |
| paragraph-structures | warn | outlines |
| section-specs | block if active mappings | content-type-frameworks, paragraph-structures, templates, outlines |
| content-types | warn | templates |
| voices | warn | brands |
| tones | warn | brands |

---

### _PromptBuilder

**Overview:** PromptBuilder assembles prompts from templates and context data, validates them against model token limits, and condenses when necessary. It ensures prompts are valid and optimized before sending to AICaller.
```
┌─────────────────────────────────────────────────────────────────┐
│                    PROMPT BUILDING PIPELINE                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. LOAD TEMPLATE                                               │
│     └─ Load prompt template from /repos/prompts/                │
│                          ↓                                       │
│  2. INJECT CONTEXT                                              │
│     ├─ Brand context (voice, values)                            │
│     ├─ Audience context (psychographics, language)              │
│     ├─ Theme context (concept, truths, beliefShifts)            │
│     ├─ Framework context (stages, prompts)                      │
│     ├─ Section context (plan, keyPoints)                        │
│     └─ Paragraph context (structure, components)                │
│                          ↓                                       │
│  3. MERGE TONES                                                 │
│     └─ theme.tone + section.toneNotes → merged tone             │
│                          ↓                                       │
│  4. VALIDATE & CONDENSE (via PromptCondenser)                   │
│     ├─ Count tokens                                             │
│     ├─ If over limit: PromptCondenser condenses by priority     │
│     └─ Verify prompt fits model context                         │
│                          ↓                                       │
│  5. RETURN VALIDATED PROMPT                                     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Template parser**: Choose templating engine (Handlebars, Mustache, custom)
- **Variable resolution**: Implement path resolution for nested context (`{{brand.voice.core}}`)
- **Caching**: Consider caching assembled prompts for repeated context combinations
- **Metrics**: Track template usage, context sizes, assembly performance
### PromptCondenser Integration
PromptBuilder delegates all validation and condensing to PromptCondenser:
```javascript
// PromptBuilder.build() final step:
const structured = structurePrompt(assembled, priorities);
const result = PromptCondenser.condense(structured, model);
return result.success ? result : throw new Error(result.error);
```
See [PromptCondenser](../_PromptCondenser/PromptCondenser.md) for token counting, priority system, and condensing strategies.
### Integration with Generation Stack
```javascript
// ThemeGenerator calls PromptBuilder:
const prompt = await PromptBuilder.buildThemePrompt({
  templateId: "theme/generate",
  model: "claude-3-haiku",
  brand: brand,
  audience: audience,
  targetOutcomes: outcomes
});
// AICaller receives validated prompt:
const response = await AICaller.generate(prompt.prompt, model);
```

---

### _PromptCondenser

**Overview:** PromptCondenser validates prompts against model token limits and applies intelligent condensing strategies when prompts exceed available context. It preserves essential content while removing or summarizing lower-priority content.
```
┌─────────────────────────────────────────────────────────────────┐
│                    CONDENSING PIPELINE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. MEASURE                                                     │
│     └─ Count tokens by section and priority                     │
│                          ↓                                       │
│  2. EVALUATE                                                    │
│     ├─ Compare total vs available context                       │
│     └─ If under limit → return unchanged                        │
│                          ↓                                       │
│  3. PLAN CONDENSING                                             │
│     ├─ Calculate tokens to remove                               │
│     ├─ Build removal plan by priority                           │
│     └─ Never touch Essential (priority 1)                       │
│                          ↓                                       │
│  4. EXECUTE                                                     │
│     ├─ Remove Optional content (priority 5)                     │
│     ├─ Remove Supporting content (priority 4)                   │
│     ├─ Truncate Important content (priority 3)                  │
│     └─ Summarize Critical content (priority 2)                  │
│                          ↓                                       │
│  5. VERIFY                                                      │
│     ├─ Recount tokens                                           │
│     ├─ Confirm under limit                                      │
│     └─ Return condensed prompt + report                         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Notes:**
### Dev Team Gap Analysis (2026-01-20)
- **Tokenizer implementation**: Need to implement/integrate tokenizers for each model family
- **Caching token counts**: Consider caching counts for repeated content (brand context reuse)
- **Quality metrics**: Track condensing impact on output quality
- **Adaptive budgets**: Learn optimal budgets from successful generations
### Token Counting Approaches
1. **Exact (preferred)**: Use model-specific tokenizer (tiktoken, claude tokenizer)
2. **Approximate**: Use general tokenizer with adjustment factor per model
3. **Estimate**: Word count × 1.3 (least accurate, fastest fallback)
### Performance Considerations
- Token counting is O(n) on content length
- Cache token counts for repeated sections (brand, framework)
- Pre-count during prompt assembly to avoid double-counting

---

### _PythonRunner

**Overview:** PythonRunner executes Python scripts when specialized Python libraries are needed for processing tasks.

**Notes:**
-

---

### _ResearchTools

**Overview:** ResearchTools provides utilities for researching topics, gathering data, and collecting external information.

**Notes:**
-

---

### FileManager

**Overview:** FileManager is a low-level utility module for file system read operations. It is the read counterpart to Writer - intentionally "dumb" in that it fetches what you ask for without making decisions about what you should fetch. Intelligence lives in modules like Librarian (discovery) and Archivist (validation workflows).
```
Known ID → FileManager.get() → File content
```
**Kitchen Analogy:** FileManager is the pantry - you ask for a specific ingredient by name, it hands it to you. It doesn't suggest alternatives, doesn't check if the recipe needs it, doesn't know what you're cooking. It just retrieves.
**Two access patterns:**
- **FileManager**: Know exact ID → use this
- **Librarian**: Need to search/discover → use Librarian instead

---

## social/

**Social media - planning, management, collation, analytics**

### _SocialMediaAnalytics

**Overview:** SocialMediaAnalytics tracks and reports on social media content performance and engagement.

**Notes:**
-

---

### _SocialMediaCollator

**Overview:** SocialMediaCollator aggregates and organizes social media content for analysis and planning.

**Notes:**
-

---

### _SocialMediaManager

**Overview:** SocialMediaManager handles social media content lifecycle using templates from /repos/social-templates/.

**Notes:**
- Uses templates from /repos/social-templates/
-

---

### _SocialMediaPlanner

**Overview:** SocialMediaPlanner creates and manages social media content calendars and scheduling.

**Notes:**
-

---

## svg/

**SVG drawing and manipulation**

### _SVGDrawer

**Overview:** SVGDrawer creates and manipulates SVG graphics for icons, illustrations, and vector elements.

**Notes:**
-

---

## system/

**Core infrastructure - writing, logging, parsing, archiving, building**

### _Archivist

**Overview:** Archivist manages archival storage, versioning, and retrieval of historical content and data.

**Notes:**
-

---

### _Builders

**Overview:** Builders provides high-level orchestration for creating complete documents, emails, and social posts. Coordinates TemplateEngine, renderers, and other content modules.

**Notes:**
- Document/Email/Social builders
- Calls TemplateEngine.run()
-

---

### _FailureHandler

**Overview:** FailureHandler receives errors and decides recovery strategies. Uses ERROR-CODES.md to determine appropriate recovery actions. Owns retry/backoff logic for entire system.

**Notes:**
- Owns retry/backoff logic (per Spec Writing Guidelines)
- Uses ERROR-CODES.md to decide recovery strategies
- Handles OUTLINE_TEMPLATE_DRIFT_DETECTED: decides use_archived_template / migrate_outline / generate_fresh based on severity
### Dev Team Gap Analysis (2026-01-20)
- **Failure classification**: Distinguish transient (retry) vs permanent (abort) errors
- **Retry policies**: Exponential backoff with jitter, max attempts per error type
- **Fallback chains**: Define fallback sequences (e.g., Anthropic → OpenAI → cached)
- **Graceful degradation**: Return partial results when full generation fails
- **User notification**: When to surface errors vs handle silently
- **Logging/alerting**: Log failures for debugging, alert on critical patterns

---

### _Logger

**Overview:** Logger provides structured JSON logging with debug, info, warn, error levels. Outputs to file and console based on environment configuration.

**Notes:**
- Owns logging patterns for entire system (per Spec Writing Guidelines)
- Format: Structured JSON
- Levels: debug, info, warn, error
-

---

### _Parsers

**Overview:** Parsers extracts structured fields from AI responses. Called by TemplateEngine after AICaller returns content.

**Notes:**
- Called by TemplateEngine.fillSections()
### Dev Team Gap Analysis (2026-01-20)
- **Schema validation**: Use Zod schemas to validate AI output structure
- **Field extraction**: Extract title, body, cta from AI responses
- **Normalization**: Trim whitespace, fix common formatting issues
- **Error recovery**: Handle partial/malformed responses gracefully
- **Partial parse handling**: Return what parsed + flag incomplete sections
- **Type safety**: Output typed objects matching library schemas

---

### Writer

**Overview:** Writer is a low-level utility module for file system write operations. It is intentionally "dumb" - it writes what it's given without making decisions about content validity. Intelligence lives upstream in modules like Archivist (validation) and PolicyEngine (permissions).
```
Caller (Archivist, Builders, Logger) → Writer → File System
```
**Kitchen Analogy:** Writer is the oven - you put food in, it cooks. It doesn't decide WHAT to cook, doesn't check if the recipe is right, doesn't clean up if things burn. It just applies heat to whatever you give it.

---

## template/

**Template system - template engine, factory, outline and theme generation**

### _TemplateFactory

**Overview:** TemplateFactory handles all template write operations: create, update, delete, list, and search. Must archive previous version when template structure changes (for OutlineGenerator drift handling).

**Notes:**
- Version archiving required for OutlineGenerator template drift handling
- TemplateEngine reads templates; TemplateFactory writes templates
- Must archive previous version when sections added/removed/reordered
-

---

### OutlineGenerator

**Overview:** OutlineGenerator creates structured outlines that bridge themes and content generation. It takes a theme (the WHAT) and a template (the STRUCTURE) and produces section-by-section plans (the HOW) that guide TemplateEngine's content generation.
```
Theme        = WHAT to say (truths, concept, message, keywords)
     ↓
Outline      = HOW to structure it (plan per section, which truths where)
     ↓
TemplateEngine = FULL content (complete paragraphs, rendered output)
```
### Why Outlines Matter
Without outlines, each section is generated independently - leading to:
- Repetition across sections
- Inconsistent truth distribution
- No narrative arc
- Disjointed documents
With outlines:
- Truths distributed intentionally across sections
- Coherent narrative flow
- Each section has clear purpose
- Pre-generation during quiet times saves cost

---

### TemplateEngine

**Overview:** TemplateEngine is the orchestration layer for content generation. It reads a template (an ordered list of section types), coordinates with AI services to fill each section, and hands the assembled content to a Renderer for final output.
```
Template → TemplateEngine → Filled Sections → Renderer → Final Output
```
**Kitchen Analogy:** TemplateEngine is the head chef coordinating a meal. The recipe (template) says what courses to prepare, the chef directs each station (AI, Image systems) to prepare their part, then hands everything to plating (Renderer) for final presentation.

---

### ThemeGenerator

**Overview:** ThemeGenerator creates and manages themes - the developed, brand-aligned versions of raw topics. A theme provides the concept, message, keywords, and angle that drive content generation and image search.
```
Outcomes Library (perspectives, belief shifts)
         ↓
Topic → ThemeGenerator → Theme (with truths, angles) → OutlineGenerator → TemplateEngine → Content
```
**What is a Theme?**
| Thing | What It Is | Example |
|-------|------------|---------|
| **Topic** | Raw subject | "healthy conversations" |
| **Theme** | Developed angle with keywords, message, concept | Full theme object with brand perspective |
Topic is input. Theme is the developed, brand-aligned version.

---

## typography/

**Typography - font management, typography rules and management**

### _FontManager

**Overview:** FontManager handles font loading, font fallbacks, and font availability for rendering.

**Notes:**
-

---

### _Typography

**Overview:** Typography manages typography rules, text hierarchy, and styling for consistent text presentation.

**Notes:**
-

---

### _TypographyManager

**Overview:** TypographyManager coordinates typography-related functionality across FontManager, Typography, and related modules.

**Notes:**
-

---

## verification/

**Post-creation verification and validation**

## visual/

**Visual brand - brand visuals, visual checks, visual management**

### _VisualBrand

**Overview:** VisualBrand manages the visual aspects of brand identity including colours, fonts, and design elements.

**Notes:**
-

---

### _VisualCheck

**Overview:** VisualCheck validates visual elements in rendered content - images, graphics, color usage, and visual consistency. Catches missing images, incorrect sizing, and visual mismatches.
Runs as part of PostCreationCheckManager (in parallel with other checkers).

**Notes:**
### What VisualCheck Validates
| Check | Auto-Fix | Notes |
|-------|----------|-------|
| Images present | No | Flag missing |
| Image sizing | Some | Resize to fit |
| Image quality | No | Flag low resolution |
| Alt text present | No | Accessibility |
| Color consistency | No | Brand colors used |
| Visual hierarchy | No | Flag cluttered areas |
| Image relevance | No | Match to keywords |
### Distinct From LayoutCheck
- **LayoutCheck**: Structure, formatting, text flow
- **VisualCheck**: Images, colors, visual elements
### Output
```javascript
{
  passed: boolean,
  score: number,              // 0-100
  issues: [
    {
      type: string,           // "missing_image", "sizing", "quality"
      severity: "critical" | "major" | "minor",
      element: string,        // Image reference
      description: string,
      autoFixable: boolean
    }
  ],
  imageReport: {
    total: number,
    present: number,
    missing: number,
    qualityIssues: number
  }
}
```
### Image Validation
| Check | Threshold |
|-------|-----------|
| Minimum resolution | 72 DPI (web), 300 DPI (print) |
| Maximum file size | 5MB |
| Supported formats | PNG, JPG, SVG, WebP |
| Alt text required | Yes (accessibility) |
### Dependencies
| Depends On | For |
|------------|-----|
| Rendered output | Visual analysis |
| Image assets | Validation |
| Brand colors | Color consistency |
**Depended On By:**
| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Visual validation |

---

### _VisualManager

**Overview:** VisualManager coordinates visual design functionality across colour, image, and visual brand modules.

**Notes:**
-

---

### _VisualValueAlignment

**Overview:** VisualValueAlignment verifies that visual choices align with brand values and messaging.

**Notes:**
-

---

