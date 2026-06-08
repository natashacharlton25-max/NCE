# Automation Pipeline Specification

> **Purpose:** Map out the daily content generation automation and all services required
> **Created:** 2026-01-16
> **Status:** Architecture Planning
> **Related Docs:** [LIBRARIAN-ARCHITECTURE.md](./LIBRARIAN-ARCHITECTURE.md), [LIBRARIAN-APP.md](./LIBRARIAN-APP.md), [LIBRARIAN-SERVICE.md](./LIBRARIAN-SERVICE.md)

---

## Pipeline Overview: Daily Content Generation

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                           SCHEDULER (10pm Daily Trigger)                             │
│                                                                                      │
│  For each brand in active_brands:                                                   │
│    → Run content generation pipeline                                                │
│    → Log results                                                                    │
│    → Handle failures                                                                │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                              PIPELINE STEPS                                          │
│                                                                                      │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐          │
│  │ 1.Theme │───▶│ 2.Prompt│───▶│ 3.AI    │───▶│ 4.Parse │───▶│ 5.Media │          │
│  │ Select  │    │ Build   │    │ Generate│    │ & Check │    │ Fetch   │          │
│  └─────────┘    └─────────┘    └─────────┘    └─────────┘    └─────────┘          │
│       │              │              │              │              │                 │
│       ▼              ▼              ▼              ▼              ▼                 │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐          │
│  │ 6.PDF   │───▶│ 7.Store │───▶│ 8.Log   │───▶│ 9.Notify│───▶│ 10.Done │          │
│  │ Create  │    │ Upload  │    │ Results │    │ (opt)   │    │         │          │
│  └─────────┘    └─────────┘    └─────────┘    └─────────┘    └─────────┘          │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Service Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                              SERVICES LAYER                                          │
│                                                                                      │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐        │
│  │   LIBRARIAN   │  │ PROMPT MASTER │  │  AI ROUTER    │  │  DATA PARSER  │        │
│  │               │  │               │  │               │  │               │        │
│  │ • Registry    │  │ • Templates   │  │ • Model pick  │  │ • Validation  │        │
│  │ • Contracts   │  │ • Assembly    │  │ • API keys    │  │ • Cleaning    │        │
│  │ • load/save   │  │ • Variables   │  │ • Retry       │  │ • Transform   │        │
│  │ • search      │  │ • Tokenize    │  │ • Fallback    │  │ • Schema      │        │
│  └───────┬───────┘  └───────┬───────┘  └───────┬───────┘  └───────┬───────┘        │
│          │                  │                  │                  │                 │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐        │
│  │ IMAGE MANAGER │  │  PDF MAKER    │  │ STORAGE SYNC  │  │  SCHEDULER    │        │
│  │               │  │               │  │               │  │               │        │
│  │ • Unsplash    │  │ • Templates   │  │ • Google Drive│  │ • Cron jobs   │        │
│  │ • Pexels      │  │ • Puppeteer   │  │ • Local FS    │  │ • Queue       │        │
│  │ • Local cache │  │ • Layouts     │  │ • S3 (opt)    │  │ • Retry       │        │
│  │ • Resize      │  │ • Branding    │  │ • Versioning  │  │ • History     │        │
│  └───────────────┘  └───────────────┘  └───────────────┘  └───────────────┘        │
│                                                                                      │
│  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐                           │
│  │ TEXT CHECKER  │  │  SVG MAKER    │  │  LOGGER       │                           │
│  │               │  │               │  │               │                           │
│  │ • Spell check │  │ • Logo gen    │  │ • Structured  │                           │
│  │ • Grammar     │  │ • Icons       │  │ • Per-brand   │                           │
│  │ • Python lib  │  │ • Graphics    │  │ • Searchable  │                           │
│  └───────────────┘  └───────────────┘  └───────────────┘                           │
└─────────────────────────────────────────────────────────────────────────────────────┘
                                        │
                                        ▼
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                              LIBRARIES LAYER                                         │
│                                                                                      │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│  │ Brands  │ │  Tone   │ │ Colours │ │  Fonts  │ │  Legal  │ │ Themes  │          │
│  │ Library │ │ Library │ │ Library │ │ Library │ │ Library │ │ Library │          │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘          │
│                                                                                      │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│  │ Prompts │ │ Frames  │ │ Images  │ │ PDFTempl│ │ Vocab   │ │ (new)   │          │
│  │ Library │ │ Library │ │ Library │ │ Library │ │ Library │ │ ...     │          │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘          │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

---

## Detailed Pipeline Steps

### Step 1: Theme Selection

```
┌─────────────────────────────────────────────────────────────────┐
│ THEME SELECTOR                                                   │
│                                                                  │
│ Input:  brand_id                                                │
│ Output: theme object with keywords, mood, content_type          │
│                                                                  │
│ Process:                                                        │
│ 1. Librarian.load('themes', brand_id)                          │
│ 2. Filter themes not used recently (check history)             │
│ 3. Pick one (random or scheduled rotation)                     │
│ 4. Return theme data                                           │
│                                                                  │
│ Theme structure:                                                │
│ {                                                               │
│   id: "monday-motivation",                                      │
│   keywords: ["inspiration", "goals", "success"],               │
│   mood: "uplifting",                                           │
│   content_type: "long_form_article",                           │
│   image_style: "bright, minimalist",                           │
│   platforms: ["blog", "linkedin", "newsletter"]                │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 2: Prompt Building

```
┌─────────────────────────────────────────────────────────────────┐
│ PROMPT MASTER                                                    │
│                                                                  │
│ Input:  brand_id, theme, content_type                           │
│ Output: assembled prompt ready for AI                           │
│                                                                  │
│ Process:                                                        │
│ 1. Load brand data via Librarian:                              │
│    - brand identity, voice, values                             │
│    - vocabulary (use/avoid words)                              │
│    - tone settings                                             │
│                                                                  │
│ 2. Load prompt template:                                        │
│    - base template for content_type                            │
│    - section templates if multi-part                           │
│                                                                  │
│ 3. Assemble prompt:                                             │
│    - Inject brand variables                                    │
│    - Add theme keywords                                        │
│    - Include constraints (word count, format)                  │
│                                                                  │
│ 4. Count tokens:                                                │
│    - Check against model limit                                 │
│    - Truncate context if needed                                │
│    - Select model based on size                                │
│                                                                  │
│ Output:                                                         │
│ {                                                               │
│   prompt: "...",                                                │
│   token_count: 2450,                                           │
│   recommended_model: "claude-3-sonnet",                        │
│   context_used: { brand: true, theme: true, examples: 2 }      │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 3: AI Generation

```
┌─────────────────────────────────────────────────────────────────┐
│ AI ROUTER                                                        │
│                                                                  │
│ Input:  assembled prompt, model recommendation                  │
│ Output: raw AI response                                         │
│                                                                  │
│ Process:                                                        │
│ 1. Select API key from pool:                                   │
│    - Check rate limits                                         │
│    - Rotate keys if needed                                     │
│                                                                  │
│ 2. Choose model:                                                │
│    - Use recommendation or override                            │
│    - Consider cost vs quality setting                          │
│                                                                  │
│ 3. Make API call:                                               │
│    - Send prompt                                               │
│    - Handle streaming if supported                             │
│                                                                  │
│ 4. Retry logic:                                                 │
│    - On failure, wait and retry (3 attempts)                   │
│    - On rate limit, switch key or model                        │
│    - On timeout, fallback to smaller model                     │
│                                                                  │
│ 5. Multi-part generation (if needed):                          │
│    - First call: generate outline                              │
│    - Second call: expand each section                          │
│    - Combine results                                           │
│                                                                  │
│ Output:                                                         │
│ {                                                               │
│   content: "...",                                               │
│   model_used: "claude-3-sonnet",                               │
│   tokens_in: 2450,                                             │
│   tokens_out: 1890,                                            │
│   api_key_used: "key_3",                                       │
│   attempts: 1                                                  │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 4: Parse & Check

```
┌─────────────────────────────────────────────────────────────────┐
│ DATA PARSER + TEXT CHECKER                                       │
│                                                                  │
│ Input:  raw AI response                                         │
│ Output: validated, cleaned content                              │
│                                                                  │
│ Process:                                                        │
│ 1. Parse response:                                              │
│    - Extract structured sections                               │
│    - Identify title, body, meta                                │
│    - Handle markdown/formatting                                │
│                                                                  │
│ 2. Validate structure:                                          │
│    - Check required fields present                             │
│    - Verify word counts in range                               │
│    - Ensure format matches template                            │
│                                                                  │
│ 3. Text checking (Python service):                             │
│    - Spell check                                               │
│    - Grammar check                                             │
│    - Brand vocabulary check (avoid words)                      │
│    - Tone consistency check                                    │
│                                                                  │
│ 4. Log any issues:                                              │
│    - Corrections made                                          │
│    - Warnings (optional review)                                │
│    - Errors (needs human review)                               │
│                                                                  │
│ Output:                                                         │
│ {                                                               │
│   content: { title, sections, meta },                          │
│   checks: { spelling: "pass", grammar: "pass", tone: "pass" }, │
│   corrections: [ { original, fixed, reason } ],                │
│   status: "approved" | "needs_review"                          │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 5: Media Fetch

```
┌─────────────────────────────────────────────────────────────────┐
│ IMAGE MANAGER                                                    │
│                                                                  │
│ Input:  theme, brand colours, content keywords                  │
│ Output: images ready for use                                    │
│                                                                  │
│ Process:                                                        │
│ 1. Build search query:                                          │
│    - Theme keywords                                            │
│    - Image style from theme                                    │
│    - Exclude previously used                                   │
│                                                                  │
│ 2. Search sources:                                              │
│    - Unsplash API (primary)                                    │
│    - Pexels API (fallback)                                     │
│    - Local cache (if available)                                │
│                                                                  │
│ 3. Select and download:                                         │
│    - Pick best match                                           │
│    - Download high-res                                         │
│    - Cache locally                                             │
│                                                                  │
│ 4. Process:                                                     │
│    - Resize for target platforms                               │
│    - Apply brand overlay (optional)                            │
│    - Generate thumbnails                                       │
│                                                                  │
│ Output:                                                         │
│ {                                                               │
│   hero: { path, url, attribution },                            │
│   thumbnails: [ { platform, path } ],                          │
│   source: "unsplash",                                          │
│   photographer: "John Doe"                                     │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 6: PDF Creation

```
┌─────────────────────────────────────────────────────────────────┐
│ PDF MAKER                                                        │
│                                                                  │
│ Input:  content, images, brand config                           │
│ Output: branded PDF document                                    │
│                                                                  │
│ Process:                                                        │
│ 1. Load PDF template:                                           │
│    - Select based on content_type                              │
│    - Load brand-specific overrides                             │
│                                                                  │
│ 2. Apply branding:                                              │
│    - Brand colours                                             │
│    - Brand fonts                                               │
│    - Logo placement                                            │
│    - Footer/header                                             │
│                                                                  │
│ 3. Render content:                                              │
│    - Place title, sections                                     │
│    - Insert images                                             │
│    - Format markdown                                           │
│    - Page breaks                                               │
│                                                                  │
│ 4. Generate PDF:                                                │
│    - Puppeteer render                                          │
│    - Optimize file size                                        │
│    - Add metadata                                              │
│                                                                  │
│ Output:                                                         │
│ {                                                               │
│   path: "/exports/brand/2026-01-16-theme.pdf",                 │
│   pages: 4,                                                    │
│   size_kb: 1240                                                │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 7: Storage Upload

```
┌─────────────────────────────────────────────────────────────────┐
│ STORAGE SYNC                                                     │
│                                                                  │
│ Input:  files to upload, brand config                           │
│ Output: cloud URLs                                              │
│                                                                  │
│ Process:                                                        │
│ 1. Determine destinations:                                      │
│    - Check brand storage config                                │
│    - Google Drive folder structure                             │
│                                                                  │
│ 2. Upload files:                                                │
│    - PDF to Drive                                              │
│    - Images to Drive                                           │
│    - Raw content to Drive                                      │
│                                                                  │
│ 3. Organize:                                                    │
│    - Create dated folders                                      │
│    - Update index files                                        │
│    - Set sharing permissions                                   │
│                                                                  │
│ 4. Return URLs:                                                 │
│    - Shareable links                                           │
│    - Direct download links                                     │
│                                                                  │
│ Output:                                                         │
│ {                                                               │
│   pdf_url: "https://drive.google.com/...",                     │
│   folder_url: "https://drive.google.com/...",                  │
│   local_backup: "/exports/brand/..."                           │
│ }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Step 8: Logging

```
┌─────────────────────────────────────────────────────────────────┐
│ LOGGER                                                           │
│                                                                  │
│ Input:  all step results                                        │
│ Output: structured log entry                                    │
│                                                                  │
│ Log entry structure:                                            │
│ {                                                               │
│   run_id: "uuid",                                               │
│   brand_id: "test-brand",                                       │
│   timestamp: "2026-01-16T22:00:00Z",                           │
│   theme_used: "monday-motivation",                              │
│   content_type: "long_form_article",                           │
│   steps: [                                                      │
│     { step: "theme", status: "success", ms: 45 },              │
│     { step: "prompt", status: "success", ms: 120 },            │
│     { step: "generate", status: "success", ms: 8500 },         │
│     ...                                                        │
│   ],                                                            │
│   tokens: { in: 2450, out: 1890 },                             │
│   model: "claude-3-sonnet",                                    │
│   outputs: {                                                    │
│     pdf: "/path/to/pdf",                                       │
│     drive_url: "https://..."                                   │
│   },                                                            │
│   status: "success" | "partial" | "failed"                     │
│ }                                                               │
│                                                                  │
│ Stored at:                                                      │
│ - /BrandData/{brand}/_logs/pipeline/{date}.json                │
│ - /System/_logs/daily/{date}.json (aggregate)                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Service Dependencies

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                          SERVICE DEPENDENCY GRAPH                                    │
│                                                                                      │
│                              ┌─────────────┐                                        │
│                              │  SCHEDULER  │                                        │
│                              └──────┬──────┘                                        │
│                                     │                                               │
│                                     ▼                                               │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              LIBRARIAN                                       │   │
│  │                     (Central data access layer)                              │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│        │              │              │              │              │                │
│        ▼              ▼              ▼              ▼              ▼                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │ PROMPT   │  │ AI       │  │ DATA     │  │ IMAGE    │  │ PDF      │            │
│  │ MASTER   │  │ ROUTER   │  │ PARSER   │  │ MANAGER  │  │ MAKER    │            │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────┘            │
│        │              │              │              │              │                │
│        └──────────────┴──────────────┴──────────────┴──────────────┘                │
│                                     │                                               │
│                                     ▼                                               │
│                            ┌─────────────┐                                         │
│                            │   LOGGER    │                                         │
│                            └──────┬──────┘                                         │
│                                   │                                                 │
│                                   ▼                                                 │
│                          ┌─────────────────┐                                       │
│                          │  STORAGE SYNC   │                                       │
│                          └─────────────────┘                                       │
└─────────────────────────────────────────────────────────────────────────────────────┘

Key insight: LIBRARIAN is the hub - every service goes through it for data access
```

---

## What Needs Building (Priority Order)

| Priority | Service | Why First | Depends On |
|----------|---------|-----------|------------|
| **1** | Librarian | Everything needs data access | File Manager (exists) |
| **2** | Prompt Master | Core of content generation | Librarian |
| **3** | AI Router | Wraps existing AI Caller | Librarian, AI Caller (exists) |
| **4** | Data Parser | Validates AI output | None |
| **5** | Scheduler | Runs automations | All above |
| **6** | Text Checker | Quality assurance | Python environment |
| **7** | Image Manager | Stock photo fetching | Librarian |
| **8** | PDF Maker | Document generation | Puppeteer (exists) |
| **9** | Storage Sync | Cloud uploads | Google Drive API |
| **10** | Logger | Already partially exists | None |
| **11** | SVG Maker | Nice-to-have | None |

---

## Librarian Service Detail

Since everything depends on Librarian, here's its spec:

```typescript
// Core Librarian interface
interface Librarian {
  // Registry management
  registerLibrary(name: string, config: LibraryConfig): void;
  getRegistry(): Record<string, LibraryConfig>;

  // Data access (the main methods)
  load(library: string, id: string): Promise<any>;
  save(library: string, id: string, data: any): Promise<void>;
  list(library: string, filter?: Filter): Promise<string[]>;
  search(library: string, query: string): Promise<SearchResult[]>;

  // Contract enforcement
  checkPermission(service: string, library: string, action: string): boolean;
  getContract(service: string): ServiceContract;

  // Utilities
  exists(library: string, id: string): Promise<boolean>;
  getMetadata(library: string, id: string): Promise<Metadata>;
}

// Library configuration
interface LibraryConfig {
  basePath: string;           // Where data lives
  schema?: JSONSchema;        // Optional validation
  indexFields?: string[];     // Fields to index for search
  cacheable?: boolean;        // Cache in memory
  permissions: {
    read: string[];           // Services that can read
    write: string[];          // Services that can write
  };
}

// Service contract (what a service can access)
interface ServiceContract {
  name: string;
  canRead: string[];          // Library names
  canWrite: string[];         // Library names
  methods: string[];          // Allowed methods
}
```

**Example usage in pipeline:**

```typescript
// Theme selector
const themes = await librarian.load('themes', brandId);
const theme = selectUnusedTheme(themes, history);

// Prompt builder
const brand = await librarian.load('brands', brandId);
const tone = await librarian.load('tone', brand.toneId);
const template = await librarian.load('prompts', contentType);

// After generation
await librarian.save('content', `${brandId}/${date}`, generatedContent);
```

---

## Next Steps

1. **Build Librarian service** - The foundation everything needs
2. **Define library schemas** - What data looks like in each library
3. **Create Prompt Master** - Handles prompt assembly
4. **Wrap AI Caller** - Add model selection, key rotation, retry
5. **Build Scheduler** - Simple cron-based job runner
6. **Add remaining services** - One at a time, test as we go

---

*Ready to start building the Librarian service?*
