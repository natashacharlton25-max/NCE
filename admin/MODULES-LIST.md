# NCE Modules List

> Consolidated list of all modules, services, handlers, and apps.
> **Status:** Final architecture - ready for build planning

---

## System Modules (Core Infrastructure)

| Module | Purpose | Notes |
|--------|---------|-------|
| **Writer** | Writes system data to disk | Only module that writes data (via Archivist) |
| **Logger** | Central logging (temp + SQLite) | Writes own logs, all services log through it |
| **FailSys** | System-wide error patterns, circuit breakers, halt-on-critical | Uses FailureHandler rules |
| **Archivist** | Write path: validate → catalog → store | Handles semantic versioning |
| **Parsers** | All parsing + export: parseJSON(), parseCSS(), parseAIResponse(), toMarkdown(), toPDF() | Single module, multiple functions |
| **Fetchers** | Get external data (HTTP/API) | Generic fetch operations |
| **Builders** | File paths registry, create folders (mkdir) | Path creation + folder scaffolding only |

**Write/Read Separation:**

| Module | Writes | Reads | What |
|--------|--------|-------|------|
| **Writer** | ✓ | - | System data (via Archivist) |
| **Logger** | ✓ | ✓ | Own logs only (temp + SQLite) |
| **Builders** | ✓ | - | Folders only (mkdir) |
| **FileManager** | - | ✓ | All file reads |
| **Librarian** | - | ✓ | Library reads (with cache/match) |

**Write path:**
```
Service → Archivist (validate) → Builders (path) → Writer (bytes to disk)
```

**Read path:**
```
FileManager.read() → fs (direct)
Librarian.search() → fs (with caching/matching)
```

---

## Logger (Detailed)

Two-stage retention with auto-promotion based on error rate:

```
All services → Logger.log()
                  │
                  ▼
            ┌─────────────┐
            │  TEMP FILES │  ← Fast writes, 24h retention
            └──────┬──────┘
                   │
         Error rate < 5%? ──YES──▶ Auto-promote to SQLite
                   │
                  NO
                   │
                   ▼
         Hold for human review
                   │
                   ▼
            ┌─────────────┐
            │   SQLITE    │  ← Permanent, queryable
            └─────────────┘
```

**Logger bricks:**

| Brick | Job |
|-------|-----|
| `log()` | Write to temp, update counts |
| `queryTemp()` | Search raw recent logs (debugging) |
| `query()` | Search SQLite history (patterns) |
| `promote()` | Move temp → SQLite |
| `calculateRate()` | Error rate from rolling 24h window |
| `clean()` | Delete temp > 24h |
| `getStats()` | Current counts, rates, health |

**Retention rules (logger.rules.json):**
```json
{
  "tempRetention": "24h",
  "autoPromote": {
    "enabled": true,
    "conditions": { "errorRateBelow": 5 }
  },
  "alwaysPromote": ["error", "critical", "jobFailed"],
  "neverPromote": ["debug", "verbose"],
  "humanReview": {
    "trigger": "errorRateAbove",
    "threshold": 5
  }
}
```

**Use cases:**

| Need | Source | Why |
|------|--------|-----|
| "What broke right now?" | Temp | Raw data, full context |
| "Is this a pattern?" | SQLite | Query history |
| "Error rate trend over 30 days" | SQLite | Aggregated stats |
| "What was the exact API response?" | Temp | Verbose raw data |

---

## Service Modules (Central Gatekeepers)

| Module | Purpose | Notes |
|--------|---------|-------|
| **Librarian** | Read path: search → match → recommend → return | All library access (see dedicated section) |
| **PromptBuilder** | Assemble prompts from templates | Variable resolution, constraints, tone |
| **AICaller** | All AI API calls | Cost control, safety, observability |
| **FileManager** | File reads only | Public read API (does NOT write) |
| **PhotoManager** | Image-specific (resize, compress, metadata) | Uses FileManager internally |
| **AudioManager** | Audio-specific (TTS, format conversion, voice profiles) | Uses FileManager internally |
| **APIKeyManager** | Credential store for external services | Machine credentials |
| **AuthHandler** | Google OAuth login, session management | Google authenticates, allowed-email list authorizes |
| **IntegrationAdapter** | Base pattern all integrations extend | auth → fetch → normalise → validate |

---

## Orchestration (Flat Structure)

| Handler | Purpose | Notes |
|---------|---------|-------|
| **Orchestrator** | Coordinates pipelines, job queue, state machine | First-class, non-optional |
| **RequestHandler** | Routes requests through pipelines | Entry point (API/UI/Webhook) |
| **FailureHandler** | Pipeline-level retry logic | Used by FailSys |
| **SuccessHandler** | Completion, approvals, human review gates | |
| **StatusHandler** | Real-time progress for UI | Emits structured events |
| **QueueHandler** | Priority, throttling, concurrency | |
| **CacheHandler** | Speed/caching | |
| **NotificationHandler** | Alerts to humans (email/slack/webhooks) | |
| **VersionHandler** | Track store changes, rollback | Semantic versioning |
| **ScheduleHandler** | Recurring/delayed jobs | Cron-style scheduling |
| **AnalyticsHandler** | Insights from logs, cost reporting | |
| **MigrationHandler** | Schema updates, data migrations, GDPR retention/deletion | |
| **TestHandler** | Mock data, integration tests | |
| **PolicyEngine** | Pre-flight checks: permissions, cost limits, safety gates | Runs before Orchestrator |
| **UsageManager** | Rate limiting, cost tracking, batch/queue for API calls | See dedicated section below |
| **ModelManager** | Model selection, fallbacks, model metadata/notes | See dedicated section below |
| **TokenManager** | Token budgets, pricing, usage dashboards, alerts | See dedicated section below |
| **FeatureFlags** | Enable/disable features per brand/environment | |

---

## UsageManager (Dedicated System)

Handles runtime API usage enforcement:

| Function | Purpose |
|----------|---------|
| **RateLimiter** | Throttle requests per minute/hour/day by API, brand, or pipeline |
| **BatchQueue** | Group small requests together, optimise API efficiency |
| **QuotaEnforcer** | Hard limits per brand/period, block when exceeded |

**Why it's separate:**
- Needs to intercept *all* external API calls (not just AI)
- Must persist state across requests (running totals)
- Needs to make batching decisions (hold request, wait for more, send together)

**Flow:**
```
AICaller.call(prompt)
    ↓
ModelManager.select() → Which model for this task?
    ↓
UsageManager.check() → Within rate limits?
    ↓
TokenManager.estimate() → Token count, cost estimate
    ↓
UsageManager.queue() → Batch with other requests? Send now?
    ↓
Actual API call
    ↓
TokenManager.record() → Actual tokens, actual cost
    ↓
TokenManager.alert() → Budget threshold hit?
    ↓
On model failure: ModelManager.fallback() → Try next model
```

---

## ModelManager (Dedicated System)

Handles AI model selection, configuration, and fallbacks:

| Function | Purpose |
|----------|---------|
| **ModelRegistry** | Index of all available models with metadata |
| **DefaultSelector** | Which model to use for each task type (text, image, TTS, embedding) |
| **FallbackChain** | Ordered list of alternatives when primary fails |
| **LimitFallback** | Switch to next model when current hits rate/token limit |
| **ModelCards** | JSON data per model: pricing, limits, capabilities, personal notes |

**Model Card structure:**
```json
{
  "id": "gpt-4-turbo",
  "provider": "openai",
  "type": "text",
  "pricing": {
    "input": 0.01,
    "output": 0.03,
    "unit": "1K tokens"
  },
  "limits": {
    "contextWindow": 128000,
    "maxOutput": 4096,
    "requestsPerMinute": 500
  },
  "defaults": {
    "temperature": 0.7,
    "maxTokens": 2048
  },
  "fallbackTo": "gpt-4o-mini",
  "notes": {
    "bestFor": ["complex reasoning", "long context", "code generation"],
    "avoidFor": ["simple tasks (expensive)", "high volume"],
    "quirks": "Sometimes verbose, tends to over-explain"
  },
  "status": "active"
}
```

**Task type defaults:**
| Task Type | Default Model | Fallback Chain |
|-----------|---------------|----------------|
| text-generation | gpt-4-turbo | gpt-4o-mini → ollama:ncempire |
| text-fast | groq:llama | gpt-4o-mini → ollama:ncempire |
| text-cheap | ollama:ncempire | groq:llama → gpt-4o-mini |
| image-generation | dall-e-3 | leonardo → stable-diffusion |
| tts | elevenlabs | openai-tts → google-tts |
| embedding | text-embedding-3-small | ollama:nomic-embed |

**Fallback triggers:**
- API error (500, timeout, rate limit)
- Model-specific error (content policy, context too long)
- Budget/quota exceeded for that model
- Model marked as "degraded" or "offline"

---

## TokenManager (Dedicated System)

Handles token tracking, budgets, pricing, and reporting:

| Function | Purpose |
|----------|---------|
| **TokenEstimator** | Estimate tokens before sending (tiktoken for OpenAI, approximations for others) |
| **TokenRecorder** | Log actual tokens used per request |
| **PricingEngine** | Per-model rate cards, calculate costs |
| **BudgetTracker** | Token/cost budgets per brand, pipeline, or period |
| **UsageDashboard** | Aggregated views of token usage |
| **AlertManager** | Notifications when approaching/exceeding limits |

**What gets tracked per AI call:**
```json
{
  "requestId": "uuid",
  "timestamp": "2026-01-17T10:30:00Z",
  "model": "gpt-4-turbo",
  "provider": "openai",
  "taskType": "text-generation",
  "brand": "brand-name",
  "pipeline": "brand-kit",
  "tokens": {
    "estimated": { "input": 1500, "output": 500 },
    "actual": { "input": 1523, "output": 487 }
  },
  "cost": {
    "estimated": 0.0297,
    "actual": 0.0298
  },
  "success": true,
  "latencyMs": 2340
}
```

**Budget structure:**
```json
{
  "global": {
    "monthly": { "tokens": 1000000, "cost": 50.00 },
    "daily": { "tokens": 50000, "cost": 5.00 }
  },
  "perBrand": {
    "default": { "monthly": { "tokens": 100000, "cost": 10.00 } }
  },
  "perModel": {
    "gpt-4-turbo": { "daily": { "cost": 10.00 } }
  }
}
```

**Alerts:**
| Threshold | Action |
|-----------|--------|
| 50% of budget | Log warning |
| 75% of budget | NotificationHandler → email/slack |
| 90% of budget | Dashboard warning, slow down non-critical |
| 100% of budget | Block new requests, use fallback models |

---

## PromptBuilder (Dedicated System)

Compiler that takes refs, fetches data, outputs assembled prompt JSON.

```
Input: job context with refs
         ↓
Fetch from repos:
  FileManager.get('prompts', refs.prompt)      → template
  FileManager.get('tones', refs.tone)          → tone rules
  FileManager.get('brand-profiles', brandId)   → brand context
         ↓
Compile: merge template + tone + brand
         ↓
Output: ready-to-send prompt JSON
```

**PromptBuilder bricks:**

| Brick | Job |
|-------|-----|
| `loadTemplate(promptId)` | Fetch prompt template from repos |
| `loadContext(refs, brandId)` | Fetch tone, brand, values from refs |
| `resolveVariables(template, context)` | Replace `{{brand.name}}`, `{{tone.style}}` |
| `applyConstraints(prompt, limits)` | Token limits, safety rules |
| `compile(refs, brandId)` | Full flow: load → resolve → constrain → output |

**Template example:**
```json
{
  "id": "brand-intro-v2",
  "system": "You are a brand strategist. Write in {{tone.style}} voice.",
  "user": "Create an introduction for {{brand.name}}, a {{brand.industry}} company.",
  "constraints": {
    "maxTokens": 500,
    "temperature": 0.7
  }
}
```

**Compiled output:**
```json
{
  "model": "gpt-4-turbo",
  "messages": [
    { "role": "system", "content": "You are a brand strategist. Write in formal, professional voice." },
    { "role": "user", "content": "Create an introduction for Acme Corp, a technology company." }
  ],
  "max_tokens": 500,
  "temperature": 0.7
}
```

**On failure → FailureHandler → Librarian:**

```
PromptBuilder.compile() fails
  "Tone 'formal-corporate' not found"
         ↓
Escalate to FailureHandler
         ↓
FailureHandler asks Librarian:
  Librarian.recommend('tones', {
    similar_to: 'formal-corporate',
    fallback: 'professional'
  })
         ↓
Librarian returns: { id: 'business-formal', score: 0.9 }
         ↓
FailureHandler → Orchestrator: retry with refs.tone = 'business-formal'
```

Same pattern for any missing ref (prompt, model, brand config).

---

## Librarian (Dedicated System)

Discovery and recommendations for repo data. FileManager handles direct lookups.

| Function | Purpose |
|----------|---------|
| **search()** | Find entries by query, filters, fuzzy matching |
| **match()** | Score entries against criteria, ranked results |
| **findSimilar()** | Given an entry, find related entries (same category, tags, capabilities) |
| **recommend()** | Given constraints, suggest best options (used by FailureHandler) |

**Librarian vs FileManager (same repos, different jobs):**

| Need | Tool | Example |
|------|------|---------|
| Know exact ID | FileManager.get() | `FileManager.get('model-cards', 'gpt-4-turbo')` |
| Need to discover | Librarian.search() | `Librarian.search('model-cards', { type: 'text' })` |
| Need alternative | Librarian.recommend() | `Librarian.recommend('model-cards', { exclude: [...] })` |

**Collaboration pattern:**

```
┌─────────────────┐                    ┌─────────────────┐
│   FILEMANAGER   │◄──────────────────►│   LIBRARIAN     │
│                 │                    │                 │
│ "I need a model │                    │ "Found match,   │
│  but no ID"     │───── asks ────────►│  need full      │
│                 │                    │  data for it"   │
│                 │◄─── returns ID ────│                 │
│                 │                    │                 │
│ Gets full data  │                    │ Asks FileManager│
│ by ID           │◄─── asks ──────────│ for full entry  │
│                 │                    │                 │
│ Returns entry   │───── returns ─────►│ Returns to      │
│                 │                    │ caller          │
└─────────────────┘                    └─────────────────┘
```

**Example flows:**

```javascript
// Flow 1: Caller needs discovery → Librarian finds → FileManager fetches
const matches = await Librarian.search('model-cards', { type: 'text' });
const fullData = await FileManager.get('model-cards', matches[0].id);

// Flow 2: Librarian internally uses FileManager for full entry
const recommended = await Librarian.recommend('model-cards', { ... });
// Librarian already called FileManager.get() to enrich the result
```

**Librarian bricks:**

| Brick | Job |
|-------|-----|
| `search(repo, query)` | Search repo, return ranked matches |
| `match(repo, criteria, options)` | Score entries against criteria |
| `findSimilar(repo, entryId)` | Find entries similar to given one |
| `recommend(repo, constraints)` | Suggest best options for given constraints |
| `cache.get()` | Check cache before disk read |
| `cache.set()` | Update cache with results |
| `cache.invalidate()` | Clear cache on repo update |

**Recommendation flow (used by FailureHandler):**

```
FailureHandler: "Model gpt-4-turbo failed, fallback gpt-4o-mini also failed"
         ↓
Librarian.recommend('model-cards', {
  type: 'text',
  exclude: ['gpt-4-turbo', 'gpt-4o-mini'],
  preferLowerCost: true,
  status: 'active'
})
         ↓
Returns: [
  { id: 'groq:llama', score: 0.85, reason: 'Fast, free tier' },
  { id: 'ollama:ncempire', score: 0.72, reason: 'Local, no API limits' }
]
         ↓
FailureHandler picks top result → sends to Orchestrator as solution
```

---

## Repos (Data Storage)

All data lives in repos (JSON files). Two ways to access the same data:

```
┌─────────────────────────────────────────────────────────┐
│                     REPOS                               │
│              (JSON data files)                          │
│                                                         │
│  model-cards/  tones/  values/  colours/  prompts/     │
│  brand-profiles/  budgets/  token-usage/  config/      │
└─────────────────────────────────────────────────────────┘
         │                              │
         ▼                              ▼
┌─────────────────┐            ┌─────────────────┐
│   LIBRARIAN     │            │  FILEMANAGER    │
│                 │            │                 │
│ • search()      │            │ • get(id)       │
│ • match()       │            │                 │
│ • recommend()   │            │ Direct lookup   │
│ • findSimilar() │            │ when you know   │
│                 │            │ exact ID        │
│ Discovery &     │            │                 │
│ recommendations │            │                 │
└─────────────────┘            └─────────────────┘
```

**Same data, two access patterns:**

| Need | Tool | Example |
|------|------|---------|
| "Find me a fast text model" | Librarian.search() | Don't know which one yet |
| "Get gpt-4-turbo details" | FileManager.get() | Know exact ID |
| "Model failed, find alternative" | Librarian.recommend() | Need scoring/exclusions |
| "Load brand acme's config" | FileManager.get() | Know exact key |
| "Find tones matching 'professional'" | Librarian.match() | Fuzzy search |
| "Get tone entry 'formal-corporate'" | FileManager.get() | Know exact ID |

**Repos list:**

| Repo | Purpose |
|------|---------|
| **model-cards** | AI model metadata, pricing, limits, capabilities |
| **tones** | Voice/tone entries |
| **values** | Brand values entries |
| **colours** | Colour palette definitions |
| **prompts** | Prompt templates |
| **brand-profiles** | Brand configuration data |
| **config** | System configuration |
| **run-logs** | Structured execution logs + traces |
| **token-usage** | Token/cost tracking per request |
| **budgets** | Token/cost budgets per brand/model/period |
| **temp-overrides** | Circuit breaker overrides (auto-expires) |
| **failure-dumps** | Raw failure data for debugging |

**Repo structure (one file per entry):**

```
/repos/model-cards/
  gpt-4-turbo.json          ← Individual entry
  gpt-4o-mini.json
  groq-llama.json
  ollama-ncempire.json
  _index.json               ← Librarian's cached index (auto-generated)

/repos/tones/
  formal-corporate.json
  friendly-casual.json
  technical-precise.json
  _index.json

/repos/brand-profiles/
  acme/
    brand.json              ← Brand config
    assets/                 ← Brand-specific files
  widgets-inc/
    brand.json
    assets/
```

**Why one file per entry:**
- Fast individual lookups (no parsing large files)
- Git-friendly (changes isolated to single files)
- Easy to add/remove entries
- Librarian builds `_index.json` for fast search

**Data flow: pass references, not data**

Modules pass paths/IDs in job context. Each module fetches only what it needs.

```
BAD: Pass entire repo data through chain
┌────────────┐    { models: [100 items], tones: [50 items], ... }
│ Orchestrator│──────────────────────────────────────────────────►
└────────────┘    Bloated payload, slow, memory waste

GOOD: Pass references, fetch on demand
┌────────────┐    { modelId: 'gpt-4-turbo', toneId: 'formal-corporate' }
│ Orchestrator│──────────────────────────────────────────────────►
└────────────┘    Lean payload
                        │
                        ▼
                  PromptBuilder needs tone?
                  → FileManager.get('tones', 'formal-corporate')
                  → Gets only that one file
```

**Job context example:**
```json
{
  "jobId": "job_20260117_143052",
  "brandId": "acme",
  "refs": {
    "model": "gpt-4-turbo",
    "tone": "formal-corporate",
    "prompt": "brand-intro-v2"
  }
}
```

Each module fetches its own needs:
- PromptBuilder: `FileManager.get('prompts', refs.prompt)`
- AICaller: `FileManager.get('model-cards', refs.model)`
- No module receives data it doesn't need

**Access examples:**
```javascript
// Discovery - don't know exact ID
const models = await Librarian.search('model-cards', { type: 'text', provider: 'openai' });
const similar = await Librarian.findSimilar('model-cards', 'gpt-4-turbo');

// Direct lookup - know exact ID
const model = await FileManager.get('model-cards', 'gpt-4-turbo');
const brand = await FileManager.get('brand-profiles', 'acme');
```

---

## Integration Modules (External APIs)

| Module | Purpose | Status |
|--------|---------|--------|
| **IntegrationAdapter** (base) | Standard lifecycle: auth → fetch → normalise → validate | Pattern |

### AI Text Providers (via AICaller)

| Integration | Purpose | Status |
|-------------|---------|--------|
| **GoogleAI** | Gemini models | Has key |
| **Groq** | Fast inference, free tier | Has key |
| **OpenAI** | GPT models (text) | Has key |
| **OpenRouter** | Multi-model access | Has key |
| **Ollama** | Local AI (ncempire:latest) | Configured |

### AI Image Generation (via PhotoManager)

| Integration | Purpose | Status |
|-------------|---------|--------|
| **LeonardoAI** | Image generation, fine-tuned models | Placeholder |
| **DALL-E** | OpenAI image generation | Via OpenAI key |
| **StableDiffusion** | Via API providers (Stability AI, Replicate) | Needs key |
| **Midjourney** | Via Discord or API proxy | Needs setup |
| **Ideogram** | Text-in-image generation | Needs key |
| **Flux** | Via Replicate/fal.ai | Needs key |
| **GoogleImagen** | Via Vertex AI | Needs setup |

**Note:** AI image calls go through AICaller → then PhotoManager for:
- Image-specific post-processing (resize, format conversion)
- Storage handling (save to brand assets)
- Metadata tagging (prompt used, model, settings)

### AI Text-to-Speech (via AudioManager)

| Integration | Purpose | Status |
|-------------|---------|--------|
| **ElevenLabs** | High-quality voice cloning, natural speech | Needs key |
| **OpenAI TTS** | GPT-4 voices (alloy, echo, fable, onyx, nova, shimmer) | Via OpenAI key |
| **GoogleTTS** | Cloud Text-to-Speech, WaveNet voices | Via Google API |
| **Azure TTS** | Microsoft neural voices | Needs key |
| **PlayHT** | Ultra-realistic voices | Needs key |
| **Murf** | Studio-quality voiceovers | Needs key |

**Note:** TTS calls go through AICaller → then AudioManager for:
- Audio format conversion (mp3, wav, ogg)
- Voice profile management (save preferred voices per brand)
- Caching (same text + voice = same audio)
- Storage handling (save to brand assets)

### Stock Photos

| Integration | Purpose | Status |
|-------------|---------|--------|
| **Unsplash** | Stock photos | Has key |
| **Pexels** | Stock photos | Has key |
| **Pixabay** | Stock photos | Has key |

### Image Processing

| Integration | Purpose | Status |
|-------------|---------|--------|
| **RemoveBG** | Background removal | Has key |

### Email

| Integration | Purpose | Status |
|-------------|---------|--------|
| **Gmail** | SMTP send + IMAP read | Has key |
| **Emailit** | Alternative SMTP | Has key |

### Design Tools

| Integration | Purpose | Status |
|-------------|---------|--------|
| **CanvaConnect** | Sync assets, OAuth flow | Has key |
| **CanvaSDK** | External app integration | Configured |

### Data & Visualization

| Integration | Purpose | Status |
|-------------|---------|--------|
| **Flourish** | Data visualization | Has key |
| **GoogleSheets** | Service account access | Placeholder |

### Publishing / Docs

| Integration | Purpose | Status |
|-------------|---------|--------|
| **GitHub** | Push MD files to website repo, version control | Needs token |
| **Astro** | Static site generator with content collections | Target framework |
| **CloudflarePages** | Static hosting, auto-deploy on push | Has account |

**Publishing flow (Astro content collections):**
```
JSON data (from stores/pipelines)
    ↓
Generate assets (PDFs, images)
    ↓
CloudflareR2.upload() → Returns public URLs
    ↓
Parsers.toMarkdown() → Generate MD with URLs in frontmatter
    ↓
GitHub.push() → Push to /src/content/ in Astro repo
    ↓
Cloudflare Pages detects push → triggers Astro build
    ↓
Website updates with new content + R2 asset links
```

**Astro content collection format:**
```markdown
---
title: "Blog Post Title"
date: 2025-01-17
author: "Brand Name"
tags: ["tag1", "tag2"]
pdfUrl: "https://r2.yourdomain.com/exports/brand-kit-2025-01-17.pdf"
coverImage: "https://r2.yourdomain.com/images/cover-abc123.jpg"
---

Content body here...

[Download PDF](https://r2.yourdomain.com/exports/brand-kit-2025-01-17.pdf)
```

### Cloud Storage

| Integration | Purpose | Status |
|-------------|---------|--------|
| **CloudflareR2** | Object storage for finished PDFs, large assets | Has account |
| **GoogleDrive** | Backup/sync, client sharing | Via OAuth |

**R2 usage:**
- Store finished PDFs (export outputs)
- Large assets (videos, high-res images)
- Cheaper than S3 for egress

**NCE stores direct R2 URLs for admin access.**

Public download auth (magic links, email verification) is handled by the website system, not NCE.

### Business / Legal

| Integration | Purpose | Status |
|-------------|---------|--------|
| **CompaniesHouse** | UK company lookup | Has key |

### Google Workspace

| Integration | Purpose | Status |
|-------------|---------|--------|
| **GoogleOAuth** | SSO login | Has key |
| **GoogleDrive** | File storage, sync, sharing | Needs scope |
| **GoogleDocs** | Document creation, templates, export | Needs scope |
| **GoogleSheets** | Spreadsheet read/write, data sync | Placeholder key |
| **GoogleSlides** | Presentation generation | Needs scope |
| **GoogleCalendar** | Scheduling, reminders | Needs scope |
| **GoogleContacts** | Contact management, CRM sync | Needs scope |
| **Gmail** | Send/read via API (not SMTP) | Needs scope |
| **GoogleFonts** | Font previews/downloads | Via Google API |
| **GoogleTasks** | Task management | Needs scope |

**Note:** Most Google APIs use OAuth scopes. Add scopes to your Google OAuth config as needed:
```
GOOGLE_SCOPES=openid,email,profile,https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/documents,...
```

---

## Pipelines (Workflows)

| Pipeline | Purpose |
|----------|---------|
| **brand-kit** | Brand profile generation |
| **content** | Content generation workflow |
| **canva** | Canva automation workflow |
| **typography** | Font pairing/selection |
| **colour-token** | Colour palette management |
| **folder-creator** | Project folder setup |
| **logo-maker** | Logo generation/management |
| **visual-checker** | Brand visual alignment |

---

## Chain Execution Patterns

Orchestrator supports both sequential and parallel step execution.

### Sequential (Default)

Steps run one after another:

```json
{
  "name": "simple-chain",
  "steps": [
    { "module": "AICaller", "step": "callWithRetry" },
    { "module": "Parser", "step": "convertToMarkdown" },
    { "module": "Exporter", "step": "saveToDisk" }
  ]
}
```

### Parallel (Fan-out/Fan-in)

Steps that don't depend on each other run simultaneously:

```json
{
  "name": "content-with-images",
  "steps": [
    { "module": "AICaller", "step": "callWithRetry" },
    {
      "parallel": [
        { "module": "Parser", "step": "convertToMarkdown" },
        { "module": "ImageFetcher", "step": "fetchFromUnsplash" }
      ],
      "wait": "all"
    },
    { "module": "Assembler", "step": "combineContent" }
  ]
}
```

**Execution flow:**
```
Step 1: AICaller.callWithRetry
         │
         ▼ success
         │
    ┌────┴────┐  (parallel - both start simultaneously)
    ▼         ▼
 Parser    ImageFetcher
    │         │
    ▼         ▼
 markdown   images
    │         │
    └────┬────┘  (wait for ALL to complete)
         │
         ▼
Step 3: Assembler.combineContent
        (has both markdown AND images in context)
```

### Wait Options

| Option | Meaning |
|--------|---------|
| `"all"` | Wait for ALL parallel steps to complete (default) |
| `"any"` | Continue when FIRST one completes |
| `"allSettled"` | Wait for all, even if some fail |

### Context Merging

After parallel steps complete, their outputs merge into context:

```javascript
// Before parallel
context.refs = {
  aiResponse: "job_xxx/ai-response.json"
}

// After parallel (both outputs added)
context.refs = {
  aiResponse: "job_xxx/ai-response.json",
  markdown: "job_xxx/content.md",        // from Parser
  images: "job_xxx/images.json"          // from ImageFetcher
}
```

### Error Handling in Parallel

| wait | On failure |
|------|------------|
| `"all"` | Whole parallel group fails → FailureHandler |
| `"any"` | First success continues, others cancelled |
| `"allSettled"` | Continues with partial results, marks failed step |

FailureHandler can suggest:
- Retry failed step only
- Skip failed step, use fallback data
- Fail entire chain

---

## Failure Handling Levels

Three levels of failure handling, from local to system-wide:

### Level 1: Module (Expected Failures)

Modules handle common, expected failures internally using their own rules:

```
AICaller: Model A fails
    ↓
Check module.config.json rules
    ↓
Has fallback? → Try Model B
Rate limited? → Wait 60s, retry
Token limit? → Queue to batch
    ↓
Log failure (always)
    ↓
Return success (if handled) or escalate
```

**Module-level rules (in module.config.json):**
```json
{
  "fallbacks": {
    "gpt-4-turbo": ["gpt-4o-mini", "groq:llama", "ollama:ncempire"]
  },
  "onRateLimit": { "action": "waitAndRetry", "delay": 60000, "maxRetries": 3 },
  "onTokenLimit": { "action": "queueToBatch" },
  "escalateAfter": { "failures": 3, "window": "10m" }
}
```

### Level 2: Circuit Breaker (Pattern Detection)

When module-level failures exceed threshold, escalate to prevent wasted attempts:

```
Job 1: Model A fails → try Model B (module handles)
Job 2: Model A fails → try Model B (module handles)
Job 3: Model A fails → try Model B (module handles)
Job 4: Model A fails → ESCALATE (3+ failures in 10min)
         ↓
FailureHandler sets TEMP OVERRIDE (6 hours):
"Skip Model A, use Model B directly"
         ↓
Jobs 5-100: Use Model B directly (no wasted attempts)
         ↓
After 6 hours: Reset, try Model A again
```

**Temp overrides (auto-managed):**
```json
{
  "tempOverrides": {
    "AICaller.defaultModel": {
      "value": "gpt-4o-mini",
      "reason": "gpt-4-turbo failing",
      "expires": "2026-01-17T20:00:00Z",
      "setBy": "failureHandler"
    }
  }
}
```

### Level 3: System (Complex Failures)

FailureHandler analyzes via Logger for complex diagnosis:

```
Orchestrator → FailureHandler
                    ↓
         Query Logger for job history
                    ↓
         Analyze:
         • First failure for this ID?
         • Same ID failed before? Where?
         • Same ERROR across DIFFERENT IDs?
         • What's the actual failure reason?
                    ↓
         Return solution chain to Orchestrator
                    ↓
         Orchestrator executes solution
```

**FailureHandler returns solution chains (defined in system.config.json):**
```json
{
  "action": "executeChain",
  "chain": "failureProcess346",
  "then": "retryOriginal"
}
```

### Dynamic Fallbacks (Librarian as Advisor)

When hardcoded fallbacks are exhausted, FailureHandler asks Librarian for alternatives:

```
Model A fails → Module tries fallback (Model B)
                    ↓
Model B also fails → Escalate to FailureHandler
                    ↓
FailureHandler: "Hardcoded fallbacks exhausted"
                    ↓
Librarian.recommend('model-cards', {
  type: 'text',
  exclude: ['model-a', 'model-b'],
  status: 'active'
})
                    ↓
Librarian returns ranked alternatives with reasons
                    ↓
FailureHandler picks top result → solution to Orchestrator
                    ↓
Orchestrator executes with dynamic alternative
```

**Why Librarian for recommendations:**
- Model-cards is a Library (searchable metadata)
- Librarian knows model capabilities, status, costs
- Can exclude known-broken models dynamically
- Returns scored, ranked alternatives with reasoning
- Same pattern works for any library (tones, values, colours)

### Escalation Levels

Full escalation chain when failures cascade:

```
Level 1: Module handles internally
         ↓ fails
Level 2: Librarian recommends alternative
         ↓ fails
Level 3: Create new entry, wait, retry
         ↓ fails
Level 4: Human intervention
```

**Level 3 detail (system creates its own solution):**

The system uses its own AI/content generation to create missing entries. Self-healing.

**Philosophy: Use the best tool for the job.**

The system has global creation rules with your values and processes built in. For content creation tasks (tones, prompts, palettes), the system IS the best tool - it does what you would do, but faster and more consistently. Human intervention isn't a "last resort" - it's for a different category of work: novel strategic decisions the system has no precedent for.

| Task Type | Best Tool | Why |
|-----------|-----------|-----|
| Missing tone entry | System | Content creation is what it does |
| Missing prompt template | System | Has your patterns built in |
| Missing colour palette | System | Follows your brand rules |
| New brand strategy decision | Human | Novel, no precedent |
| Should this brand even exist? | Human | Strategic judgment |
| Edge case the system can't categorise | Human | Needs new rules |

```
All alternatives exhausted
         ↓
FailureHandler triggers CREATE chain:
  Orchestrator.run('create-tone-entry', {
    similar_to: 'formal-corporate',
    domain: 'business',
    type: 'professional'
  })
         ↓
CREATE chain runs (uses system's own capabilities):
  1. PromptBuilder.compile({ prompt: 'generate-tone-entry' })
  2. AICaller.call() → AI generates new tone JSON
  3. Parsers.validate() → Check schema
  4. Writer.save() → Save to /repos/tones/
         ↓
New entry created: 'business-professional-v1'
         ↓
Librarian.cache.invalidate('tones')  ← Update index
         ↓
Retry original with refs.tone = 'business-professional-v1'
```

**System generates its own solutions:**
- Missing tone? → AI generates tone entry (has your voice patterns)
- Missing prompt? → AI generates prompt template (follows your structure)
- Missing colour palette? → AI generates based on brand (uses your rules)
- Human for: novel decisions, new categories, strategic judgment

### Summary

| Level | Handled By | Triggers | Action |
|-------|------------|----------|--------|
| 1. Module | module.config.json | Expected errors | Fallback, retry, queue |
| 2. Recommend | FailureHandler + Librarian | No fallback works | Find similar, exclude failed |
| 3. Create | FailureHandler + Orchestrator | No alternatives exist | System creates (best tool for content) |
| 4. Human | NotificationHandler | Novel decision, new category | Strategic judgment (different job, not fallback) |
| Circuit Breaker | FailureHandler | 3+ failures in window | Temp system override (6h) |

**All failures logged** (even if handled internally) for pattern detection and analytics.

---

## Config Overrides

Modules receive config overrides in their instruction from Orchestrator:

**Normal call (uses module defaults):**
```json
{
  "module": "AICaller",
  "step": "callWithRetry",
  "refs": { "prompt": "job_xxx/prompt.json" }
}
```

**Override call (from FailureHandler solution):**
```json
{
  "module": "AICaller",
  "step": "callWithRetry",
  "refs": { "prompt": "job_xxx/prompt.json" },
  "config": {
    "model": "groq:llama",
    "maxRetries": 1
  }
}
```

**Module pattern:**
```javascript
async function callWithRetry(instruction) {
  const config = {
    ...defaultConfig,           // from module.config.json
    ...instruction.config       // override (if any)
  };
  // Use merged config
}
```

**Override precedence:**
```
instruction.config  >  module.config.json  >  system defaults
     (runtime)            (module)              (hardcoded)
```

---

## UI (User Interfaces)

| Module | Purpose | Features |
|--------|---------|----------|
| **Dashboard** | Monitoring and control panel | System health, job status, analytics |
| **ApprovalInbox** | Human review queue for flagged content | Review, approve, reject, edit before release |
| **AuditViewer** | History of all actions | What ran, when, results, who approved |
| **ImageBrowser** | Browse/manage images | |
| **LibrarianApp** | Browse/edit library entries | |

---

## Request Flow

```
User action (UI or API)
    ↓
AuthHandler.verify() → Valid session?
    ↓
RequestHandler → Creates Job ID, loads pipeline
    ↓
PolicyEngine → Can this run? (permissions, cost limits, safety)
    ↓
Orchestrator → Runs pipeline tasks in sequence
    ↓
Each task that calls external API:
    ↓
    UsageManager.check() → Within quota?
    UsageManager.estimate() → Token count, cost
    UsageManager.queue() → Batch or send now?
    Actual API call
    UsageManager.record() → Log actual usage
    ↓
On failure: FailureHandler → Retry/recover/fail
    ↓
On success: SuccessHandler → Approve/release (→ ApprovalInbox if flagged)
    ↓
StatusHandler → Updates UI in real-time
    ↓
Logger → Records full trace
    ↓
Response returned to user
```

---

## Final Hierarchy

```
/modules/
  /system/
    /writer/
    /logger/
    /failsys/
    /archivist/
    /parsers/
    /fetchers/
    /builders/

  /services/
    /librarian/
    /prompt-builder/
    /ai-caller/
    /file-manager/
    /photo-manager/
    /audio-manager/
    /api-key-manager/
    /auth-handler/
    /integration-adapter/

  /orchestration/
    orchestrator.js
    request-handler.js
    failure-handler.js
    success-handler.js
    status-handler.js
    queue-handler.js
    cache-handler.js
    notification-handler.js
    version-handler.js
    schedule-handler.js
    analytics-handler.js
    migration-handler.js
    test-handler.js
    policy-engine.js
    feature-flags.js

  /usage-manager/
    rate-limiter.js
    batch-queue.js
    quota-enforcer.js

  /model-manager/
    model-registry.js
    default-selector.js
    fallback-chain.js
    limit-fallback.js
    model-cards.js

  /token-manager/
    token-estimator.js
    token-recorder.js
    pricing-engine.js
    budget-tracker.js
    usage-dashboard.js
    alert-manager.js

  /integrations/
    /base/
      integration-adapter.js
    /canva/
    /stock-photo/
    /google-fonts/
    /companies-house/

/repos/                     # JSON data (Librarian searches, FileManager gets)
  /model-cards/
  /tones/
  /values/
  /colours/
  /prompts/
  /brand-profiles/
  /config/
  /run-logs/
  /token-usage/
  /budgets/
  /temp-overrides/
  /failure-dumps/

/pipelines/
  brand-kit.pipeline.json
  content.pipeline.json
  canva.pipeline.json
  typography.pipeline.json
  colour-token.pipeline.json
  folder-creator.pipeline.json
  logo-maker.pipeline.json
  visual-checker.pipeline.json

/ui/
  /dashboard/
  /image-browser/
  /librarian-app/
```

---

## Build Priority

| Priority | Module | Reason |
|----------|--------|--------|
| 1 | Writer | Foundation - data persistence |
| 2 | Logger | System-wide - all services log through it |
| 3 | FailSys | System-wide - uses Logger for pattern detection |
| 4 | Parsers | Processing - data transformation |
| 5 | Builders | Processing - path/folder creation |
| 6 | Archivist | Processing - validates before write |
| 7 | FileManager | Façade - public API for file ops |
| 8 | Fetchers | Processing - external data |
| 9 | Librarian | High-level - library access |
| 10 | Orchestrator | Coordination - everything depends on it |

---

## Skipped (Overkill for Single-User)

| Suggested | What It Does | Why Skip |
|-----------|--------------|----------|
| **API Gateway** | Separate service for SSL, validation, auth, routing, load balancing | Single user, no public API. RequestHandler routes, AuthHandler authenticates. No load balancing needed. |
| **RBAC / Permissions** | Role-based access control (admin, editor, viewer roles) | Single user. AuthHandler + Google OAuth + allowed-email list is enough. |
| **Secret Manager (Vault)** | Enterprise credential management (HashiCorp Vault, AWS Secrets Manager) | APIKeyManager + .env files is sufficient. No compliance requirements for external audit. |

---

*Last updated: 2026-01-17*
