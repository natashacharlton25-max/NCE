# Service Architecture

> Central services that handle all core operations. Apps and pipelines request from services - they never access resources directly.
> **Principle:** Each service does ONE thing. Pipelines orchestrate services. Apps just request what they need.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              APPS & PIPELINES                                    │
│                         (Request what they need)                                │
│                                                                                 │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│   │ BrandKit    │  │ Content     │  │ Canva       │  │ Future      │          │
│   │ Workflow    │  │ Pipeline    │  │ Pipeline    │  │ Apps        │          │
│   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘          │
│          │                │                │                │                  │
└──────────┼────────────────┼────────────────┼────────────────┼──────────────────┘
           │                │                │                │
           ▼                ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                              CENTRAL SERVICES                                    │
│              (Gatekeepers - handle all operations, enforce rules)               │
│                                                                                 │
│   ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐       │
│   │ LIBRARIAN │ │  PROMPT   │ │    AI     │ │   DATA    │ │   FILE    │       │
│   │           │ │  BUILDER  │ │  CALLER   │ │  PARSER   │ │  MANAGER  │       │
│   │All library│ │All prompt │ │ All AI    │ │All format │ │ All file  │       │
│   │  access   │ │ assembly  │ │  calls    │ │conversion │ │operations │       │
│   └───────────┘ └───────────┘ └───────────┘ └───────────┘ └───────────┘       │
│                                                                                 │
│   ┌───────────┐                                                                │
│   │  PHOTO    │   + APIKeyManager (credential store for all services)          │
│   │  MANAGER  │                                                                │
│   │All image  │                                                                │
│   │operations │                                                                │
│   └───────────┘                                                                │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
           │                │                │                │
           ▼                ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           INTEGRATION MODULES                                    │
│                    (Connect to external APIs)                                   │
│                                                                                 │
│   ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐         │
│   │    Canva     │ │  Stock Photo │ │ Google Fonts │ │  Companies   │         │
│   │ Integration  │ │ Integration  │ │ Integration  │ │    House     │         │
│   └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘         │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
           │                │                │                │
           ▼                ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────────┐
│                            EXTERNAL APIS                                         │
│                                                                                 │
│     Canva API    Unsplash/Pexels    Google Fonts    Companies House            │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

---

## Core Principles

### 1. Apps Never Access Resources Directly

```
WRONG:                              RIGHT:
──────                              ──────

App → Library File                  App → Librarian → Library File
App → AI API                        App → AICaller → AI API
App → File System                   App → FileManager → File System
```

### 2. Services Are Gatekeepers

Each service:
- **Checks permissions** - Can this app do this operation?
- **Validates requests** - Is this a valid query/data?
- **Routes to resources** - Where does this data live?
- **Logs operations** - Track all access for analytics
- **Returns clean data** - App receives exactly what it needs

### 3. Contracts Define Access

```json
{
  "contracts": {
    "brandkit-workflow": {
      "librarian": { "brand/*": "read", "prompts/*": "read" },
      "file-manager": { "brands/*": "read-write" },
      "ai-caller": { "models/*": "call" }
    },
    "content-pipeline": {
      "librarian": { "content/*": "read-write" },
      "prompts/*": "read"
    }
  }
}
```

### 4. Services Can Call Services

```
App: "Generate blog post with images and save as PDF"
         │
         ▼
    Librarian → Get content templates
         │
         ▼
    PromptBuilder → Assemble prompt
         │
         ▼
    AICaller → Generate content
         │
         ▼
    PhotoManager → Get stock images
         │       └─→ AICaller → Suggest search terms
         │
         ▼
    DataParser → Convert to PDF
         │
         ▼
    FileManager → Save to brand folder
```

---

## The Six Central Services

### 1. LIBRARIAN

> **Purpose:** Central access point for ALL library data across all systems.

**What it controls:**
- Library registry (where all libraries live)
- Access contracts (who can read/write what)
- Entry fetching and searching
- Entry creation and editing
- Usage analytics
- Stock level monitoring

**Apps never know:**
- Where libraries are stored
- Library file formats
- Access rules
- How to search/match

**Request flow:**
```
App: "Get 3 therapeutic voice entries"
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                      LIBRARIAN                           │
│                                                         │
│  1. Check registry     → voice library at C:\BrandLib   │
│  2. Check contract     → App can read voice? ✓          │
│  3. Load library       → 45 entries found               │
│  4. Filter by domain   → 12 therapeutic entries         │
│  5. Select entries     → Return top 3 by weight         │
│  6. Log usage          → Track which entries returned   │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
Returns: [entry1, entry2, entry3]
```

**Contains:**

```
Librarian/
├── _config/
│   ├── registry.json           # All library locations
│   ├── contracts.json          # Access permissions per app
│   └── stock-thresholds.json   # When to build more entries
│
├── scripts/
│   ├── fetch-entries.ts        # Get entries by criteria
│   ├── search-library.ts       # Search across libraries
│   ├── match-entries.ts        # Match field values to entries
│   ├── create-entry.ts         # Add new entry
│   ├── edit-entry.ts           # Modify entry
│   ├── delete-entry.ts         # Remove entry
│   ├── validate-entry.ts       # Check against schema
│   ├── track-usage.ts          # Log entry access
│   ├── check-stock.ts          # Monitor stock levels
│   └── build-stock.ts          # Trigger AI generation
│
├── analytics/
│   ├── usage-by-library.json   # Usage stats per library
│   ├── usage-by-entry.json     # Usage stats per entry
│   └── usage-by-app.json       # Usage stats per app
│
└── index.ts                    # Public API
```

**Registry structure:**
```json
{
  "libraries": {
    "brand": {
      "path": "C:\\Users\\Business\\BrandLib",
      "categories": ["voice", "visual", "content"],
      "status": "active"
    },
    "content": {
      "path": "C:\\Users\\Business\\ContentLib",
      "categories": ["templates", "formats"],
      "status": "planned"
    },
    "prompts": {
      "path": "C:\\Users\\Business\\PromptLib",
      "categories": ["system", "templates", "chains"],
      "status": "planned"
    }
  }
}
```

**Extract from current build:**

| Current Location | Function |
|------------------|----------|
| `lib/server/library-matcher.ts` | → `scripts/match-entries.ts` |
| `lib/server/library-browser.ts` | → `scripts/search-library.ts` |
| `lib/server/library-linker.ts` | → `scripts/fetch-entries.ts` |
| `lib/server/library-creator.ts` | → `scripts/create-entry.ts` |
| `lib/server/library-editor.ts` | → `scripts/edit-entry.ts` |
| `lib/server/domain-detector.ts` | → `scripts/detect-domain.ts` |

---

### 2. PROMPT BUILDER

> **Purpose:** Central service for ALL prompt construction.

**What it controls:**
- Prompt template library
- Variable injection
- Template chaining
- Token estimation
- Prompt validation

**Apps never know:**
- Where templates live
- How to assemble prompts
- Variable requirements
- Token limits

**Request flow:**
```
App: "Build voice generation prompt for therapeutic brand"
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                    PROMPT BUILDER                        │
│                                                         │
│  1. Load template      → prompts/brand/voice-gen.md     │
│  2. Get required vars  → brand_name, domain, examples   │
│  3. Validate vars      → All provided? ✓               │
│  4. Inject variables   → Fill placeholders              │
│  5. Estimate tokens    → ~1,200 tokens                  │
│  6. Return prompt      → Ready for AI                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
Returns: { prompt, token_estimate, template_used }
```

**Contains:**

```
PromptBuilder/
├── _config/
│   ├── variables.json          # Required vars per template
│   ├── chains.json             # Multi-step sequences
│   └── limits.json             # Token limits per type
│
├── templates/
│   ├── brand/
│   │   ├── voice-generation.md
│   │   ├── identity-extraction.md
│   │   └── domain-detection.md
│   ├── content/
│   │   ├── blog-post.md
│   │   └── social-caption.md
│   └── system/
│       ├── json-output.md
│       └── extraction.md
│
├── scripts/
│   ├── load-template.ts        # Get template from library
│   ├── inject-variables.ts     # Fill in context
│   ├── chain-prompts.ts        # Multi-step assembly
│   ├── validate-prompt.ts      # Check all vars filled
│   ├── estimate-tokens.ts      # Size check
│   └── optimize-prompt.ts      # Trim if too long
│
└── index.ts                    # Public API
```

**Extract from current build:**

| Current Location | Function |
|------------------|----------|
| `lib/server/prompt-builder.ts` | → All prompt functions |
| `lib/server/template-generator.ts` | → `scripts/generate-template.ts` |
| `BrandKit AIGen/sections/*.json` | → `templates/` |

---

### 3. AI CALLER

> **Purpose:** Central service for ALL AI API calls.

**What it controls:**
- Model library (available models)
- API routing (which provider)
- Rate limiting
- Token tracking
- Cost calculation
- Retry logic

**Apps never know:**
- API keys
- Which model to use
- Rate limits
- Token costs

**Request flow:**
```
App: "Call AI with this prompt"
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                      AI CALLER                           │
│                                                         │
│  1. Check rate limit   → Under limit? ✓ (or queue)     │
│  2. Select model       → groq/llama-3.3-70b            │
│  3. Get API key        → From APIKeyManager            │
│  4. Make API call      → Send to provider              │
│  5. Track tokens       → 1,200 in, 450 out             │
│  6. Calculate cost     → $0.0012                       │
│  7. Log usage          → Record for analytics          │
│  8. Return response    → Clean response to app         │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
Returns: { response, tokens_used, model, cost }
```

**Contains:**

```
AICaller/
├── _config/
│   ├── models.json             # Available models + capabilities
│   ├── providers.json          # Provider endpoints + auth
│   ├── rate-limits.json        # Limits per provider
│   ├── costs.json              # Cost per token
│   └── section-models.json     # Model mapping per section type
│
├── scripts/
│   ├── call-model.ts           # Make API request
│   ├── select-model.ts         # Pick best model for task
│   ├── check-rate-limit.ts     # Queue if at limit
│   ├── count-tokens.ts         # Estimate before calling
│   ├── handle-stream.ts        # Streaming responses
│   ├── retry-failed.ts         # Retry logic
│   ├── track-usage.ts          # Log usage/cost
│   └── test-connection.ts      # Verify API connectivity
│
├── analytics/
│   ├── usage-by-app.json       # Usage per app
│   ├── usage-by-model.json     # Usage per model
│   ├── costs-daily.json        # Daily cost tracking
│   └── errors.json             # Error rates
│
└── index.ts                    # Public API
```

**Extract from current build:**

| Current Location | Function |
|------------------|----------|
| `lib/server/ai-client.ts` | → `scripts/call-model.ts` |
| `_config/section-models.json` | → `_config/section-models.json` |

**New functionality to add:**
- Rate limiting with queue
- Token tracking per app
- Cost calculation
- Usage analytics

**Token Counting for Prompt Context Limits (Future Enhancement):**

Token counting utility needed for AI prompt context limits. Should be a shared utility in AICaller:

| Feature | Script | Description |
|---------|--------|-------------|
| Count tokens | `count-tokens.ts` | Use tiktoken or provider APIs to count tokens |
| Truncate context | - | Intelligently truncate if exceeding model limits |
| Report usage | `track-usage.ts` | Include token counts in responses |

**Use Cases:**
- **Prompt Pack export**: Count tokens in generated system prompts to ensure they fit in AI context windows
- **Section generation**: Estimate tokens before API call to select appropriate context
- **Cost estimation**: Calculate expected cost before generating content

**Integration with Phase 8 Export:**
```typescript
// In prompt-pack-exporter.ts, could add:
const systemPrompt = generateSystemPrompt(sectionData);
const tokenCount = await countTokens(systemPrompt, 'claude-3');
if (tokenCount > 8000) {
  // Warn or truncate for smaller context models
}
```

---

### 4. DATA PARSER

> **Purpose:** Central service for ALL format conversion and parsing.

**What it controls:**
- Input format detection
- Format conversion (JSON, MD, PDF, HTML, CSV)
- AI response parsing
- Schema validation
- Placeholder replacement

**Apps never know:**
- How to parse formats
- Conversion logic
- Schema locations
- Validation rules

**Request flow:**
```
App: "Convert this JSON to Markdown"
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                     DATA PARSER                          │
│                                                         │
│  1. Detect input       → JSON object                    │
│  2. Select converter   → json-to-md.ts                  │
│  3. Load template      → markdown/brand-doc.md          │
│  4. Convert data       → Apply template                 │
│  5. Validate output    → Valid markdown? ✓             │
│  6. Return result      → Markdown string                │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
Returns: { data, format, template_used }
```

**Contains:**

```
DataParser/
├── _config/
│   ├── schemas/                # Validation schemas
│   ├── format-rules.json       # Conversion rules
│   └── templates.json          # Template registry
│
├── templates/
│   ├── markdown/
│   │   ├── brand-doc.md
│   │   ├── brand-guide.md          # Full brand guide template
│   │   └── prompt-pack.md
│   ├── pdf/
│   │   ├── brand-guidelines.html
│   │   ├── modern.html             # Modern PDF template
│   │   ├── classic.html            # Classic PDF template
│   │   └── minimal.html            # Minimal PDF template
│   ├── html/
│   │   └── export.html
│   └── prompts/
│       ├── system-prompt.md        # AI system prompt template
│       ├── voice-guide.md          # Voice guide template
│       ├── quick-reference.md      # Quick reference template
│       └── writing-checklist.md    # Writing checklist template
│
├── scripts/
│   ├── detect-format.ts        # What format is this?
│   ├── json-to-md.ts           # JSON → Markdown
│   ├── json-to-pdf.ts          # JSON → PDF (via HTML + puppeteer)
│   ├── json-to-html.ts         # JSON → HTML
│   ├── json-to-csv.ts          # JSON → CSV
│   ├── md-to-html.ts           # Markdown → HTML
│   ├── css-to-colours.ts       # CSS → colour objects
│   ├── scss-to-colours.ts      # SCSS → colour objects
│   ├── design-tokens-to-colours.ts # Style Dictionary → colour objects
│   ├── ai-to-json.ts           # AI response → structured
│   ├── validate-schema.ts      # Validate against schema
│   ├── replace-placeholders.ts # {{var}} replacement
│   ├── parse-file.ts           # Parse uploaded file
│   ├── export-router.ts        # Route export requests (Phase 8)
│   ├── export-manager.ts       # Manage export history/cleanup (Phase 8)
│   ├── json-export.ts          # Full JSON brand export (Phase 8)
│   └── prompt-pack.ts          # AI prompt pack generation (Phase 8)
│
└── index.ts                    # Public API
```

**Extract from current build:**

| Current Location | Function |
|------------------|----------|
| `lib/server/response-processor.ts` | → `scripts/ai-to-json.ts` |
| `lib/parsers/*.ts` | → `scripts/parse-file.ts` |
| `lib/server/css-token-parser.ts` | → `scripts/css-to-colours.ts` |
| `lib/server/legal-templates.ts` | → `scripts/replace-placeholders.ts` |
| `lib/server/website-fetcher.ts` | → `scripts/fetch-website.ts` |
| `lib/server/exporters/json-exporter.ts` | → `scripts/json-export.ts` |
| `lib/server/exporters/markdown-exporter.ts` | → `scripts/json-to-md.ts` |
| `lib/server/exporters/pdf-exporter.ts` | → `scripts/json-to-pdf.ts` |
| `lib/server/exporters/prompt-pack-exporter.ts` | → `scripts/prompt-pack.ts` |
| `lib/server/export-router.ts` | → `scripts/export-router.ts` |
| `lib/server/export-manager.ts` | → `scripts/export-manager.ts` |

**Phase 8 Export System (Complete):**
- JSON export with metadata and library refs
- Markdown export with section templates and TOC
- PDF export via HTML with brand styling (puppeteer optional)
- Prompt Pack export for AI assistants (system prompt, voice guide, checklist)
- Export Manager for orchestration, history, cleanup
- Puppeteer availability check (`isPuppeteerAvailable()`) - PDF only shown if installed
- Test script: `scripts/test-export.ts` (58 tests, 100% pass)

---

### 5. FILE MANAGER

> **Purpose:** Central service for ALL file system operations.

**What it controls:**
- File read/write
- Folder creation
- Brand folder structure
- File validation
- Operation logging

**Apps never know:**
- File paths
- Folder structures
- What exists/doesn't exist
- How to create folders

**Request flow:**
```
App: "Save this MD to test-brand's blog folder"
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                     FILE MANAGER                         │
│                                                         │
│  1. Check contract     → App can write to brands? ✓    │
│  2. Resolve path       → C:\BrandData\test-brand\...   │
│  3. Check exists       → Brand folder exists? ✓        │
│  4. Create subfolder   → blog/ doesn't exist, create   │
│  5. Write file         → Save content                   │
│  6. Log operation      → Record in operations log       │
│  7. Return result      → { success, path, created }    │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
Returns: { success, path, created_folders, logged }
```

**Contains:**

```
FileManager/
├── _config/
│   ├── locations.json          # Base paths for each type
│   ├── structures.json         # Folder templates
│   ├── permissions.json        # What apps can write where
│   └── naming.json             # File naming conventions
│
├── scripts/
│   ├── check-exists.ts         # Does file/folder exist?
│   ├── create-folder.ts        # Create with structure
│   ├── create-brand.ts         # Full brand folder setup
│   ├── write-file.ts           # Write with validation
│   ├── read-file.ts            # Read with error handling
│   ├── move-file.ts            # Relocate files
│   ├── delete-file.ts          # Safe delete
│   ├── copy-file.ts            # Copy with options
│   ├── get-path.ts             # Resolve correct location
│   ├── log-operation.ts        # Track all file ops
│   └── validate-file.ts        # Check file type/size
│
├── logs/
│   ├── operations.log          # All file operations
│   ├── errors.log              # Failed operations
│   └── created.log             # New files/folders
│
└── index.ts                    # Public API
```

**Extract from current build:**

| Current Location | Function |
|------------------|----------|
| `FolderCreator/` | → Merge into FileManager |
| `lib/server/upload-handler.ts` | → `scripts/write-file.ts` |
| `lib/server/section-utils.ts` | → `scripts/read-file.ts`, `write-file.ts` |
| `lib/server/brand-status.ts` | → `scripts/read-file.ts` |

---

### 6. PHOTO MANAGER

> **Purpose:** Central service for ALL image operations.

**What it controls:**
- Stock photo search
- Image selection
- Resizing/formatting
- License tracking
- Attribution management

**Apps never know:**
- Which stock API to use
- Search term optimization
- License requirements
- Image processing

**Request flow:**
```
App: "Get 3 spa photos, 1200x630"
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                     PHOTO MANAGER                        │
│                                                         │
│  1. Optimize search    → AICaller: suggest terms        │
│  2. Search APIs        → Unsplash + Pexels              │
│  3. Filter results     → 20 candidates                  │
│  4. Score relevance    → Rank by match                  │
│  5. Select best        → Top 3 images                   │
│  6. Resize images      → 1200x630                       │
│  7. Track license      → Record attribution             │
│  8. Return images      → Ready for use                  │
│                                                         │
└─────────────────────────────────────────────────────────┘
         │
         ▼
Returns: [{ image, attribution, license }]
```

**Contains:**

```
PhotoManager/
├── _config/
│   ├── apis.json               # Stock photo APIs
│   ├── sizes.json              # Standard sizes
│   └── attribution.json        # License requirements
│
├── scripts/
│   ├── search-stock.ts         # Query stock APIs
│   ├── optimize-search.ts      # AI-enhanced search terms
│   ├── select-best.ts          # Choose from results
│   ├── resize-image.ts         # Resize to specs
│   ├── download-image.ts       # Fetch from API
│   ├── check-license.ts        # Verify usage rights
│   ├── track-attribution.ts    # Record credits
│   └── get-sizes.ts            # Standard size presets
│
├── cache/
│   └── search-results/         # Cached search results
│
└── index.ts                    # Public API
```

**Extract from current build:**

| Current Location | Function |
|------------------|----------|
| `lib/server/imagebrowser-integration.ts` | → Core functions |

**New functionality to add:**
- Stock API integration (Unsplash, Pexels)
- AI-enhanced search terms
- Image resizing
- License tracking

---

## Integration Modules

Integration modules connect services to external APIs. Services call integration modules - never external APIs directly.

### Module List

| Module | External API | Used By |
|--------|--------------|---------|
| CanvaIntegration | Canva API | FileManager |
| StockPhotoIntegration | Unsplash, Pexels | PhotoManager |
| GoogleFontsIntegration | Google Fonts API | DataParser |
| CompaniesHouseIntegration | Companies House API | Librarian |
| ABNLookupIntegration | ABN Lookup API | Librarian |

### Module Structure

```
IntegrationModules/
├── canva/
│   ├── auth.ts                 # OAuth flow
│   ├── brand-kit.ts            # Create/update brand kit
│   ├── assets.ts               # Upload assets
│   └── index.ts
│
├── stock-photos/
│   ├── unsplash.ts             # Unsplash API
│   ├── pexels.ts               # Pexels API
│   ├── search.ts               # Multi-provider search
│   └── index.ts
│
├── google-fonts/
│   ├── search.ts               # Font search
│   ├── metadata.ts             # Font metadata
│   └── index.ts
│
├── companies-house/
│   ├── search.ts               # Company search
│   ├── details.ts              # Company details
│   └── index.ts
│
├── abn-lookup/
│   ├── search.ts               # ABN search
│   └── index.ts
│
└── manager/
    ├── health-check.ts         # Check all integrations
    ├── status.ts               # Connection status
    └── index.ts
```

**Extract from current build:**

| Current Location | Integration Module |
|------------------|-------------------|
| `lib/server/companies-house.ts` | → `companies-house/` |

---

## Contracts System

### Contract Structure

```json
{
  "app": "brandkit-workflow",
  "version": "1.0",
  "contracts": {
    "librarian": {
      "brand/voice": "read",
      "brand/visual": "read",
      "brand/content": "read",
      "prompts/*": "read"
    },
    "prompt-builder": {
      "brand/*": "build",
      "content/*": "build"
    },
    "ai-caller": {
      "models/*": "call"
    },
    "data-parser": {
      "convert/*": "use",
      "parse/*": "use"
    },
    "file-manager": {
      "brands/*": "read-write",
      "exports/*": "write"
    },
    "photo-manager": {
      "search": "use",
      "download": "use"
    }
  }
}
```

### Permission Levels

| Permission | Meaning |
|------------|---------|
| `read` | Can fetch/query data |
| `write` | Can create new data |
| `read-write` | Can fetch and create |
| `delete` | Can remove data |
| `call` | Can invoke (for AI) |
| `use` | Can use service |
| `build` | Can construct (for prompts) |
| `*` | Wildcard (all in category) |

### Contract Enforcement

```typescript
// In Librarian service
async function getEntries(app: string, library: string, query: Query) {
  // 1. Load app contract
  const contract = await loadContract(app);

  // 2. Check permission
  if (!hasPermission(contract, 'librarian', library, 'read')) {
    throw new AccessDeniedError(`${app} cannot read ${library}`);
  }

  // 3. Proceed with operation
  return await fetchEntries(library, query);
}
```

---

## Analytics & Logging

### Every Service Tracks

| Metric | Purpose |
|--------|---------|
| Request count | Usage volume |
| Request source | Which app |
| Operation type | Read/write/etc |
| Duration | Performance |
| Errors | Failure rate |
| Data size | Volume |

### Central Analytics

```
Analytics/
├── librarian/
│   ├── usage-by-library.json
│   ├── usage-by-entry.json
│   └── usage-by-app.json
│
├── ai-caller/
│   ├── tokens-by-app.json
│   ├── tokens-by-model.json
│   ├── costs-daily.json
│   └── errors.json
│
├── file-manager/
│   ├── operations.json
│   └── storage-usage.json
│
└── summary/
    ├── daily-summary.json
    └── weekly-summary.json
```

---

## Example: Full Pipeline Flow

**Request:** "Generate brand voice for therapeutic spa and save to brand folder"

```
BrandKit Workflow App
         │
         │ 1. "Get therapeutic voice examples"
         ▼
    ┌─────────────┐
    │  LIBRARIAN  │ → Check contract ✓
    │             │ → Find voice library
    │             │ → Return 5 entries
    └─────────────┘
         │
         │ 2. "Build voice generation prompt"
         ▼
    ┌─────────────┐
    │   PROMPT    │ → Load template
    │   BUILDER   │ → Inject brand + examples
    │             │ → Estimate 1,200 tokens
    └─────────────┘
         │
         │ 3. "Call AI with this prompt"
         ▼
    ┌─────────────┐
    │  AI CALLER  │ → Check rate limit ✓
    │             │ → Select model (groq)
    │             │ → Make API call
    │             │ → Track tokens/cost
    └─────────────┘
         │
         │ 4. "Parse AI response to JSON"
         ▼
    ┌─────────────┐
    │    DATA     │ → Detect JSON in response
    │   PARSER    │ → Parse and validate
    │             │ → Return structured data
    └─────────────┘
         │
         │ 5. "Convert to Markdown"
         ▼
    ┌─────────────┐
    │    DATA     │ → Load MD template
    │   PARSER    │ → Convert JSON → MD
    │             │ → Return markdown
    └─────────────┘
         │
         │ 6. "Save to brand folder and log"
         ▼
    ┌─────────────┐
    │    FILE     │ → Check contract ✓
    │   MANAGER   │ → Resolve path
    │             │ → Create folders if needed
    │             │ → Write file
    │             │ → Log operation
    └─────────────┘
         │
         ▼
    DONE ✓
```

---

## Extraction Priority

Order for extracting current code into services:

| Priority | Service | Complexity | Current Code Ready |
|----------|---------|------------|-------------------|
| 1 | **Librarian** | HIGH | 6 modules ready |
| 2 | **FileManager** | MEDIUM | 4 modules + FolderCreator |
| 3 | **DataParser** | MEDIUM | 5 modules ready |
| 4 | **PromptBuilder** | LOW | 2 modules ready |
| 5 | **AICaller** | MEDIUM | 1 module + new code |
| 6 | **PhotoManager** | MEDIUM | 1 module + new code |

---

## Migration Path

### Phase 1: Create Service Shells
- Create folder structure for each service
- Set up contracts system
- Create service interfaces

### Phase 2: Extract Librarian
- Move library modules to Librarian service
- Add registry and contracts
- Update BrandKit Workflow to call Librarian

### Phase 3: Extract FileManager
- Merge FolderCreator into FileManager
- Move file operation modules
- Update apps to use FileManager

### Phase 4: Extract DataParser
- Move parser modules
- Add format conversion
- Create unified interface

### Phase 5: Extract PromptBuilder
- Move prompt modules
- Consolidate templates
- Add template registry

### Phase 6: Build AICaller
- Extract ai-client
- Add rate limiting
- Add token tracking
- Add cost calculation

### Phase 7: Build PhotoManager
- Extract image integration
- Add stock API connectors
- Add image processing

---

## Related Documentation

| Document | Purpose |
|----------|---------|
| [BUILD-CHECKLIST.md](../Build%20Lists%20Detailed/BUILD-CHECKLIST.md) | Current build status |
| [LIBRARIAN-APP.md](../Future%20Builds%20Plans/LIBRARIAN-APP.md) | Librarian details (to create) |
| [FOLDER-CREATOR-APP.md](../Future%20Builds%20Plans/FOLDER-CREATOR-APP.md) | FileManager enhancements |
| [PLAN-OVERVIEW.md](../Future%20Builds%20Plans/PLAN-OVERVIEW.md) | System overview |

---

*Last updated: 2026-01-16*
