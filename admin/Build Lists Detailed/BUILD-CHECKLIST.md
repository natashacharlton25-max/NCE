# BrandKit Workflow - Build Checklist

> Track progress through all 10 phases. Check off tasks as completed.
> Add notes in the Notes column for context, blockers, or decisions.
>
> **Quick Links:** [DOC-INDEX.md](../DOC-INDEX.md) | [INTEGRATION-TESTS.md](INTEGRATION-TESTS.md) | [PROJECT-INVENTORY.md](PROJECT-INVENTORY.md)

---

## How to Use

- `[ ]` = Not started
- `[x]` = Completed
- `[~]` = In progress / partially done
- Add notes for anything important
- Log sessions at the bottom

---

## Phase 1: Foundation

### 1.1 Global Config Setup

| Task | Status | Notes |
|------|--------|-------|
| Verify all 5 project paths exist | [x] | All exist: BrandKit, AIGen, BrandLib, BrandData, Workflow |
| Create `_config/global.json` | [x] | Created with paths, defaults, feature_flags |
| Create `_config/schemas/global.schema.json` | [x] | JSON Schema with full validation |
| Update/deprecate old `paths.json` | [x] | Marked deprecated, fixed incorrect paths |

**1.1 Build Notes:**
- Completed: 2026-01-13
- FolderCreator and APIKeyManager paths set to `null` (not yet created)
- Fixed old paths.json which had incorrect paths (missing `Users` in some)
- Schema validates: version format, region enum (AU/UK/US/NZ), language locale pattern
- Feature flags: `ai_generation_enabled` set to `false` until API key configured

**Files created:**
- `_config/global.json` - Central config with all paths and defaults
- `_config/schemas/global.schema.json` - Validation schema

---

### 1.2 Legal Structure Restructuring

| Task | Status | Notes |
|------|--------|-------|
| **Backup first** - copy `core/legal/` folder | [x] | Created `core/legal.backup/` |
| **Backup** - copy `core--legal.json` | [x] | Created `.backup` files |
| Move `BrandKit/_system/core/legal/` → `_system/legal/core/` | [x] | Folder now at `legal/core/default/` |
| Delete empty `core/legal/` folder | [x] | Removed (backup retained) |
| Rename `core--legal.json` → `legal--core.json` | [x] | Created new file, deleted old |
| Update `section` field inside `legal--core.json` | [x] | Changed to `legal/core` |
| Update `generation-order.json` references | [x] | Both section and depends_on updated |
| Update all `depends_on` arrays | [x] | Updated in legal--entity, compliance, disclaimers |
| Verify no `core/legal` references remain | [x] | Only backup files have old refs |

**1.2 Build Notes:**
- Completed: 2026-01-13
- Backups created: `legal.backup/`, `core--legal.json.backup`, `generation-order.json.backup`
- Updated `_index.json` to move legal from core section to legal section
- Updated FIELD-MAPPING-REPORT.md references
- Updated AIGen DOCS (ai-generation-plan.md, kit-separation-plan.md)

**Files modified:**
- `BrandKit/_system/legal/core/` - NEW location (was `core/legal/`)
- `BrandKit AIGen/sections/legal--core.json` - NEW file (was `core--legal.json`)
- `BrandKit AIGen/_config/generation-order.json` - Updated references
- `BrandKit AIGen/sections/legal--entity.json` - Updated depends_on
- `BrandKit AIGen/sections/legal--compliance.json` - Updated depends_on
- `BrandKit AIGen/sections/legal--disclaimers.json` - Updated depends_on
- `BrandKit AIGen/sections/_index.json` - Restructured

---

### 1.3 Folder Creator App

| Task | Status | Notes |
|------|--------|-------|
| Create `FolderCreator/` directory | [x] | `C:\Users\Business\FolderCreator\` |
| Initialize npm project with TypeScript | [x] | package.json with ts-node, commander |
| Create `tsconfig.json` | [x] | ES2020 target, strict mode |
| Create `_config/templates.json` | [x] | Brand folder structure + file templates |
| Create `src/types/index.ts` | [x] | All interfaces (BrandConfig, ProgressConfig, etc.) |
| Create `src/utils/paths.ts` | [x] | slugify, getBrandPath, brandExists |
| Create `src/utils/json-utils.ts` | [x] | readJson, writeJson, getTimestamp |
| Create `src/utils/validators.ts` | [x] | validateBrandName, validateRegion, validateDomain |
| Create `src/create-brand.ts` | [x] | Main createBrandFolder() function |
| Create `src/cli.ts` | [x] | CLI with create-brand command |
| Create `src/index.ts` | [x] | Library exports for programmatic use |
| Test with sample brand creation | [x] | Created "test-brand" in BrandData successfully |

**1.3 Build Notes:**
- Completed: 2026-01-13
- Location: `C:\Users\Business\FolderCreator\`
- Updated `global.json` to set `folder_creator` path
- Test brand created 31 folders and 3 files (brand.json, progress.json, sources.json)
- Progress.json includes all 50 sections with `not_started` status
- Valid regions: AU, UK, US, NZ (matches schema)
- Valid domains: therapeutic, ecommerce, professional-services, education, hospitality, healthcare, technology, creative, nonprofit

**Files created:**
- `FolderCreator/package.json` - npm config with ts-node, commander
- `FolderCreator/tsconfig.json` - TypeScript config
- `FolderCreator/_config/templates.json` - Folder structure templates
- `FolderCreator/src/types/index.ts` - Type definitions
- `FolderCreator/src/utils/paths.ts` - Path utilities
- `FolderCreator/src/utils/json-utils.ts` - JSON utilities
- `FolderCreator/src/utils/validators.ts` - Input validators
- `FolderCreator/src/create-brand.ts` - Main brand creator
- `FolderCreator/src/cli.ts` - CLI entry point
- `FolderCreator/src/index.ts` - Library exports

**Usage:**
```bash
# CLI usage
npx ts-node src/cli.ts create-brand --name "Brand Name" --domain therapeutic --region AU

# Programmatic usage
import { createBrandFolder } from '@brandkit/folder-creator';
const result = await createBrandFolder({ name: 'My Brand', domain: 'therapeutic', region: 'AU' });
```

---

### 1.4 API Keys Manager App

| Task | Status | Notes |
|------|--------|-------|
| Create `APIKeyManager/` directory | [x] | `C:\Users\Business\APIKeyManager\` |
| Initialize npm project with TypeScript | [x] | package.json with commander |
| Create `_config/services.json` | [x] | 8 services defined (anthropic, openai, canva, unsplash, pexels, google_fonts, companies_house, abn_lookup) |
| Create `src/utils/encryption.ts` | [x] | AES-256-GCM with machine-specific key derivation |
| Create `src/utils/storage.ts` | [x] | Double-encrypted key storage |
| Create `src/get-key.ts` | [x] | getKey, getKeyWithResult, hasKey |
| Create `src/set-key.ts` | [x] | setKey, setKeyWithResult |
| Create `src/list-keys.ts` | [x] | listKeys, getServicesStatus, getKeysCount |
| Create `src/delete-key.ts` | [x] | deleteKey, deleteKeyWithResult |
| Create `src/cli.ts` | [x] | Commands: set, get, list, delete, has, verify |
| Create `src/index.ts` | [x] | Library exports for programmatic use |
| Create `.gitignore` for encrypted keys | [x] | Excludes .keys.encrypted |
| Test key storage/retrieval | [x] | All operations verified working |

**1.4 Build Notes:**
- Completed: 2026-01-13
- Location: `C:\Users\Business\APIKeyManager\`
- Updated `global.json` to set `api_key_manager` path
- Encryption: AES-256-GCM with PBKDF2 key derivation (100,000 iterations)
- Machine-specific secret based on hostname + username + platform
- Keys are double-encrypted (individually, then in store file)
- Created `src/services.ts` for service validation

**Files created:**
- `APIKeyManager/package.json` - npm config with commander
- `APIKeyManager/tsconfig.json` - TypeScript config
- `APIKeyManager/.gitignore` - Excludes encrypted keys
- `APIKeyManager/_config/services.json` - 8 service definitions
- `APIKeyManager/src/utils/encryption.ts` - AES-256-GCM encryption
- `APIKeyManager/src/utils/storage.ts` - Encrypted file storage
- `APIKeyManager/src/services.ts` - Service validation
- `APIKeyManager/src/get-key.ts` - Key retrieval
- `APIKeyManager/src/set-key.ts` - Key storage
- `APIKeyManager/src/list-keys.ts` - Key listing
- `APIKeyManager/src/delete-key.ts` - Key deletion
- `APIKeyManager/src/cli.ts` - CLI entry point
- `APIKeyManager/src/index.ts` - Library exports

**Usage:**
```bash
# CLI usage
npx ts-node src/cli.ts set canva "sk_your_api_key"
npx ts-node src/cli.ts get canva
npx ts-node src/cli.ts list
npx ts-node src/cli.ts delete canva
npx ts-node src/cli.ts has canva
npx ts-node src/cli.ts verify

# Programmatic usage
import { getKey, setKey, hasKey, listKeys } from '@brandkit/api-key-manager';
await setKey('canva', 'sk_abc123');
const key = await getKey('canva');
```

---

### 1.5 Brand Status Utilities

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/` directory | [x] | `lib/server/` and `lib/server/types/` |
| Create `lib/server/types/brand.ts` | [x] | Type definitions for brand status tracking |
| Create `lib/server/brand-status.ts` | [x] | 15 functions for reading/updating brand status |
| Create `lib/server/index.ts` | [x] | Library exports |
| Test with sample brand folder | [x] | All tests passed with test-brand |

**1.5 Build Notes:**
- Completed: 2026-01-13
- Location: `BrandKit Workflow/lib/server/`
- Created tsconfig.json and installed TypeScript dependencies
- Successfully tested: listBrands, getBrandStatus, updateSectionStatus, etc.
- Test verified: Updated core/identity to in_progress, timestamps updated correctly

**Files created:**
- `lib/server/types/brand.ts` - Type definitions (BrandConfig, ProgressConfig, BrandStatus, etc.)
- `lib/server/brand-status.ts` - 15 utility functions
- `lib/server/index.ts` - Library exports
- `tsconfig.json` - TypeScript config for lib folder
- `package.json` - npm project with ts-node

**Functions available:**
```typescript
// Read functions
getBrandConfig, getProgressConfig, getBrandStatus,
getSectionStatus, getAllSectionsWithStatus, isCoreComplete,
getCompletionPercentage, getCompletionCounts

// Update functions
updateSectionStatus, updateBrandStatus

// List functions
listBrands, brandExists, getBrandBySlug
```

---

## Phase 2: Input Gathering

### 2.1 File Upload Handler

| Task | Status | Notes |
|------|--------|-------|
| Create upload handler | [x] | `lib/server/upload-handler.ts` |
| Create types file | [x] | `lib/server/types/upload.ts` |
| Handle PDF uploads | [x] | .pdf (10MB max) |
| Handle TXT/MD uploads | [x] | .txt, .md (10MB max) |
| Handle image uploads | [x] | .png, .jpg, .jpeg, .svg, .webp, .gif (5MB max) |
| Handle CSS/visual uploads | [x] | .css to `visual/css/`, with category overrides for typography/colours |
| Handle JSON/CSV uploads | [x] | .json, .csv (2MB max) |
| Implement file validation | [x] | Extension + size validation |
| Implement unique filename generation | [x] | Sanitized names with counter suffix |
| Update `sources.json` on upload | [x] | New array-based manifest format |
| Legacy sources.json migration | [x] | Auto-migrates Phase 1 format |
| Update lib/server/index.ts exports | [x] | All functions exported |

**2.1 Build Notes:**
- Completed: 2026-01-14
- Location: `BrandKit Workflow/lib/server/`
- Supported extensions: `.css, .csv, .doc, .docx, .gif, .jpeg, .jpg, .json, .md, .pdf, .png, .svg, .txt, .webp`
- Size limits: Documents (10MB), Images (5MB), CSS/Visual (500KB), JSON/CSV (2MB)
- Updated FolderCreator `templates.json` with new sources format + added `context/images/`, `context/visual/css/`

**Files created:**
- `lib/server/types/upload.ts` - Type definitions
- `lib/server/upload-handler.ts` - 20+ functions for upload handling

**Usage:**
```typescript
import { handleUpload, readSourcesManifest, getSourceCounts } from '$lib/server';

// Upload a file
const result = await handleUpload({
  brandPath: '/path/to/brand',
  file: buffer,
  filename: 'document.pdf'
});

// Read sources manifest
const manifest = await readSourcesManifest(brandPath);
```

---

### 2.2 Website Fetcher

| Task | Status | Notes |
|------|--------|-------|
| Create website-fetcher.ts | [x] | `lib/server/website-fetcher.ts` |
| Create types/website.ts | [x] | `lib/server/types/website.ts` |
| URL validation & normalization | [x] | Adds https://, resolves relative URLs |
| Fetch website content | [x] | Built-in fetch with 10s timeout |
| Extract main content | [x] | Removes nav, header, footer, cookie banners |
| Convert to markdown | [x] | Via Turndown library |
| Multi-page fetching | [x] | Default: /, /about, /services, /contact, etc. |
| Extract contact info | [x] | Emails, phones (AU/intl), addresses |
| Extract social links | [x] | FB, IG, X, LinkedIn, YouTube, TikTok, etc. |
| Rate limiting | [x] | 500ms delay between requests |
| Store in `context/website/` | [x] | Saves `fetched-content.md` |
| Update `sources.json` | [x] | Status: ready, type: website |
| Update lib/server/index.ts exports | [x] | All functions exported |

**2.2 Build Notes:**
- Completed: 2026-01-14
- Location: `BrandKit Workflow/lib/server/`
- Uses built-in `fetch` (Node 18+), cheerio, turndown
- Skips pages with <20 words (likely 404s)
- Test: Successfully fetched example.com (20 words, 1 page)

**Files created:**
- `lib/server/types/website.ts` - Type definitions
- `lib/server/website-fetcher.ts` - 15+ functions

**Usage:**
```typescript
import { fetchWebsite, fetchSinglePage } from '$lib/server';

// Fetch full website
const result = await fetchWebsite({
  brandPath: '/path/to/brand',
  url: 'https://example.com',
  maxPages: 5
});

// Preview single page
const page = await fetchSinglePage('https://example.com/about');
```

---

### 2.3 Import Parser

| Task | Status | Notes |
|------|--------|-------|
| Create lib/parsers/ directory structure | [x] | `lib/parsers/` with `types/` subfolder |
| Create types/parser.ts | [x] | Parser, ParseOptions, ParseResult interfaces |
| Create index.ts with parser registry | [x] | parseFile, parseAndUpdateSource, parseAllPending |
| PDF text extraction | [x] | `pdf-parser.ts` via pdf-parse, saves .txt alongside |
| DOCX parsing | [x] | `docx-parser.ts` via mammoth, saves .txt alongside |
| JSON parsing | [x] | `json-parser.ts` flattens nested to readable text |
| CSV column mapping | [x] | `csv-parser.ts` handles quoted fields, labeled output |
| Text/MD passthrough | [x] | `text-parser.ts` with line ending normalization |
| Update sources.json with parse results | [x] | Updates status, processed flag, metadata |
| Mark source as processed: true | [x] | Via parseAndUpdateSource() |
| Test with sample files | [x] | JSON, CSV, Text all tested |

**2.3 Build Notes:**
- Completed: 2026-01-14
- Location: `BrandKit Workflow/lib/parsers/`
- Supported extensions: `.csv, .doc, .docx, .json, .markdown, .md, .pdf, .txt`
- PDF/DOCX save extracted `.txt` alongside original

**Files created:**
- `lib/parsers/types/parser.ts` - Type definitions
- `lib/parsers/index.ts` - Parser registry and routing
- `lib/parsers/pdf-parser.ts` - PDF text extraction
- `lib/parsers/docx-parser.ts` - DOCX parsing
- `lib/parsers/json-parser.ts` - JSON to text
- `lib/parsers/csv-parser.ts` - CSV parsing
- `lib/parsers/text-parser.ts` - Text passthrough

**Usage:**
```typescript
import { parseFile, parseAndUpdateSource, parseAllPending } from '$lib/parsers';

// Parse a single file
const result = await parseFile({
  brandPath: '/path/to/brand',
  sourceId: 'src_xxx',
  filePath: 'documents/data.json'
});

// Parse and update sources.json
await parseAndUpdateSource({ brandPath, sourceId, filePath });

// Parse all pending parseable sources
const batch = await parseAllPending(brandPath);
```

---

### 2.4 Template Generator

| Task | Status | Notes |
|------|--------|-------|
| Create types/template.ts | [x] | Type definitions for templates |
| Create template-generator.ts | [x] | Main generator with 4 formats |
| Markdown template (.md) | [x] | User-friendly with guidance |
| Text template (.txt) | [x] | Plain text version |
| JSON template (.json) | [x] | Structured for re-import |
| CSV template (.csv) | [x] | Spreadsheet-friendly |
| Per-section generation | [x] | Templates for specific sections |
| Load schemas from _system/ | [x] | Discovers 50 sections |
| Update lib/server/index.ts exports | [x] | All functions exported |

**2.4 Build Notes:**
- Completed: 2026-01-14
- Location: `BrandKit Workflow/lib/server/`
- Templates are **per-section** (download template for specific section)
- Dynamically loads schemas from BrandKit `_system/` folder

**Files created:**
- `lib/server/types/template.ts` - Type definitions
- `lib/server/template-generator.ts` - 4 format generators

**Usage:**
```typescript
import { generateTemplate, getAllSectionIds } from '$lib/server';

// Generate template for one section
const result = await generateTemplate({
  format: 'markdown',
  sections: ['core/identity']
});

// Get all section IDs
const sections = await getAllSectionIds(); // 50 sections
```

---

## Phase 3: Domain Detection

### 3.1 Domain Detector

| Task | Status | Notes |
|------|--------|-------|
| Create types/domain.ts | [x] | Type definitions for detection |
| Create domain-detector.ts | [x] | Main detection module |
| Load domain keywords from config | [x] | Built-in defaults + external config support |
| Content scanning function | [x] | loadContextContent() from sources |
| Keyword scoring (strong/moderate/weak/anti) | [x] | 3/2/1/-5 points respectively |
| Confidence calculation | [x] | Weighted: absolute + gap + domain |
| Return top suggestions | [x] | allScores sorted by score |
| Save detection result | [x] | detection-result.json + brand.json |
| Update lib/server/index.ts exports | [x] | All functions exported |

**3.1 Build Notes:**
- Completed: 2026-01-14
- Location: `BrandKit Workflow/lib/server/`
- Default config embedded (6 domains: therapeutic, ecommerce, professional-services, creative, education, nonprofit)
- Also loads external config from BrandKit `AIGen/_config/domain-detection.json` if exists
- Tests: Therapeutic (81.6% confidence), Ecommerce (83.2%), Anti-keywords working, Ambiguous triggers user input

**Files created:**
- `lib/server/types/domain.ts` - Type definitions
- `lib/server/domain-detector.ts` - Detection + selection functions

**Usage:**
```typescript
import { detectDomain, getFallbackOptions, saveUserSelection } from '$lib/server';

// Detect domain from context
const result = await detectDomain({ brandPath: '/path/to/brand' });

// Get fallback options for manual selection
const options = await getFallbackOptions();

// Save user's selection
await saveUserSelection({ brandPath, selectedDomain: 'therapeutic', userConfirmed: true });
```

### 3.2 Domain Selector UI Support

| Task | Status | Notes |
|------|--------|-------|
| Fallback options when confidence low | [x] | getFallbackOptions() with 7 options |
| Manual domain selection | [x] | saveUserSelection() |
| Save domain to brand.json | [x] | Updates _config/brand.json |
| Get enabled libraries for domain | [x] | getEnabledLibraries() |
| Get detection details for UI | [x] | getDetectionDetails() |

**3.2 Build Notes:**
- Completed: 2026-01-14
- Included in `domain-detector.ts` (no separate file needed)
- Libraries: Universal (9) + domain-specific (5 for therapeutic)
- User confirmation sets confidence to 1.0

---

## Phase 4: AI Generation

### 4.1 AI Prompt Builder

| Task | Status | Notes |
|------|--------|-------|
| Create types/prompt.ts | [x] | Type definitions for prompts |
| Create prompt-builder.ts | [x] | Main prompt building module |
| Load section config | [x] | From BrandKit AIGen sections/ |
| Load section schema | [x] | From BrandKit _system/ |
| Load section instructions | [x] | From BrandKit _system/ |
| Load context content | [x] | Website + documents from brand |
| Load completed sections | [x] | Other sections for context |
| Build system prompt | [x] | Role, rules, output format |
| Build user prompt | [x] | Task, context, examples |
| Token estimation | [x] | Approximate token counts |
| Test with sample prompt | [x] | core/identity ~1180 tokens |

**4.1 Build Notes:**
- Completed: 2026-01-14
- Location: `lib/server/prompt-builder.ts` + `lib/server/types/prompt.ts`
- Loads config from BrandKit AIGen, schema/instructions/examples from BrandKit _system
- Gathers context from brand's website content and documents
- Supports 3 modes: extract (from context), generate (from scratch), complete (partial)
- Token estimation: ~4 chars per token
- Tested: 50 sections found, core/identity prompt builds successfully (~1180 tokens)

**Functions created:**
- `buildPrompt()` - Main entry point
- `loadSectionConfig()` - Load AIGen config
- `loadRawSectionSchema()` - Load schema.json
- `loadSectionInstructions()` - Load instructions.json
- `loadSectionExample()` - Load example-wwas.json
- `gatherContext()` - Gather brand context
- `loadCompletedSections()` - Load other sections
- `buildSystemPrompt()` - Build system message
- `buildUserPrompt()` - Build user message
- `estimateTokens()` - Token estimation
- `getAvailableSections()` - List all sections

### 4.2 Section Save/Load Utilities

| Task | Status | Notes |
|------|--------|-------|
| Create types/section.ts | [x] | Type definitions for section utilities |
| Create section-utils.ts | [x] | Main module with all functions |
| saveToDraft() | [x] | Saves section data to draft folder |
| loadFromDraft() | [x] | Loads section data from draft |
| approveSection() | [x] | Moves draft to brandkit, updates status |
| editSection() | [x] | Copies brandkit to draft for editing |
| loadFromBrandkit() | [x] | Loads approved section data |
| deleteDraft() | [x] | Removes draft folder |
| updateSectionStatus() | [x] | Updates progress.json status |
| checkCoreComplete() | [x] | Checks if all core sections complete |
| Update lib/server/index.ts exports | [x] | All functions exported |
| Test save/load/approve/edit cycle | [x] | All tests passed |

**4.2 Build Notes:**
- Completed: 2026-01-14
- Location: `lib/server/section-utils.ts` + `lib/server/types/section.ts`
- Manages section data flow: draft ↔ brandkit
- Progress tracking via `_meta/progress.json`
- Core sections: core/identity, core/purpose, core/contact
- Auto-checks core completion when approving sections
- Generation methods: ai_extraction, ai_generation, manual, library, mixed

**Functions created:**
- `saveToDraft()` - Save section to draft folder
- `loadFromDraft()` - Load from draft
- `loadFromBrandkit()` - Load approved section
- `approveSection()` - Move draft → brandkit
- `editSection()` - Copy brandkit → draft
- `draftExists()` - Check if draft exists
- `brandkitExists()` - Check if section approved
- `deleteDraft()` - Remove draft
- `updateSectionStatus()` - Update progress.json
- `checkCoreComplete()` - Check/update core status
- `areCoreComplete()` - Query core status
- `getSectionsSummary()` - Get completion summary

### 4.3 AI Client (Simple - Groq)

| Task | Status | Notes |
|------|--------|-------|
| Create types/ai-client.ts | [x] | Provider configs, request/response types |
| Create ai-client.ts | [x] | Simple client with OpenAI-compatible format |
| callAI() function | [x] | Generic AI call with provider support |
| generateSection() | [x] | Build prompt + call AI + parse response |
| generateAndSaveToDraft() | [x] | Generate + save in one call |
| testAIConnection() | [x] | Connection verification |
| JSON mode support | [x] | Structured output parsing |
| Update lib/server/index.ts | [x] | All functions exported |
| Test with Groq API | [x] | All 3 tests passed |

**4.3 Build Notes:**
- Completed: 2026-01-14
- Location: `lib/server/ai-client.ts` + `lib/server/types/ai-client.ts`
- OpenAI-compatible API format (works with multiple providers)
- Provider support: groq, openai, anthropic, google (Gemini), openrouter
- Tested providers: Groq ✓, OpenAI ✓, Google ✓, OpenRouter ✓
- Default models: llama-3.3-70b-versatile (Groq), gpt-4o (OpenAI), gemini-2.5-flash (Google)
- JSON mode working - structured output parsing
- Full generation test: Both Groq (0.9s) and OpenAI (2.6s) successfully generated core/identity
- Integrates with prompt-builder and section-utils

**Functions created:**
- `callAI()` - Generic AI call with provider/model options
- `generateSection()` - Build prompt + call AI + return parsed data
- `generateAndSaveToDraft()` - Generate and save to draft folder
- `testAIConnection()` - Verify API connectivity

### 4.3b Core Sections Handler

| Task | Status | Notes |
|------|--------|-------|
| Create types/core-sections.ts | [x] | Validation, generation, status types |
| Create core-sections.ts | [x] | Handler with all functions |
| validateCoreSection() | [x] | Validates required fields |
| generateCoreSections() | [x] | Generates all 3 core sections in order |
| approveCoreSections() | [x] | Approves with validation |
| getCoreStatus() | [x] | Returns status of all core sections |
| getCoreSectionsNeeded() | [x] | What needs generation/validation/approval |
| Update lib/server/index.ts | [x] | All functions exported |
| Test validation and generation | [x] | All tests passed |

**4.3b Build Notes:**
- Completed: 2026-01-14
- Location: `lib/server/core-sections.ts` + `lib/server/types/core-sections.ts`
- Required fields per section:
  - core/identity: brand_name
  - core/purpose: mission_statement
  - core/contact: website_url
- Validation detects [INFERRED] markers and sets confidence to 0.5
- Generation orchestrates all 3 sections in order
- Status tracking shows what needs generation/validation/approval

**Functions created:**
- `isCoreSection()` - Check if section ID is a core section
- `validateCoreSection()` - Validate data against required fields
- `validateForApproval()` - Load draft and validate
- `generateCoreSections()` - Generate all core sections
- `generateCoreSection()` - Generate single core section
- `approveCoreSections()` - Approve with validation
- `approveCoreSection()` - Approve single section
- `getCoreStatus()` - Get status of all core sections
- `getCoreSectionStatus()` - Get status of single section
- `getCoreSectionsNeeded()` - Get what's needed to complete core
- `getRequiredFields()` - Get required fields for section
- `getFieldLabel()` - Get human-readable label

### 4.4 Section Router

| Task | Status | Notes |
|------|--------|-------|
| Create types/section-router.ts | [x] | Pathway, dependency, availability types |
| Create section-router.ts | [x] | Full routing module |
| Route standard sections to AI | [x] | 35 AI sections |
| Route visual sections to Phase 7 | [x] | 10 visual sections |
| Route legal sections to Phase 6 | [x] | 4 legal sections |
| Check dependencies before generation | [x] | canGenerateSection, getMissingDependencies |
| Get available sections | [x] | getSectionsReadyForGeneration, recommendations |
| Progress tracking | [x] | getGenerationProgress by pathway/tier |
| Update lib/server/index.ts | [x] | All functions exported |
| Test routing and dependencies | [x] | All tests passed |

**4.4 Build Notes:**
- Completed: 2026-01-14
- Location: `lib/server/section-router.ts` + `lib/server/types/section-router.ts`
- Loads `generation-order.json` from BrandKit AIGen
- Section counts: 35 AI, 10 visual, 4 legal (49 total)
- 5 tiers: Foundational → Voice & Values → Content & Communication → Visual & Compliance → Operations

**Functions created:**
- `getSectionPathway()` - Returns 'ai' | 'visual' | 'legal'
- `isAISection()`, `isVisualSection()`, `isLegalSection()` - Pathway helpers
- `getSectionRouteInfo()` - Full section info with tier, order, dependencies
- `canGenerateSection()` - Check if dependencies are satisfied
- `getMissingDependencies()` - Get what's blocking a section
- `getSectionsReadyForGeneration()` - All available sections with status
- `getNextRecommendedSections()` - Top N recommended by order
- `getGenerationProgress()` - Progress by pathway and tier
- `getDependentSections()` - Sections depending on a given section
- `getAllDependencies()` - Transitive dependency tree

### 4.5 Response Processor

| Task | Status | Notes |
|------|--------|-------|
| Create types/response-processor.ts | [x] | Parse, validation, inferred, partialness types |
| Create response-processor.ts | [x] | Main processing module |
| Parse AI JSON response | [x] | Direct, markdown block, object extract, repair |
| Validate against schema | [x] | JSON Schema validation with errors/warnings |
| Extract inferred markers | [x] | [INFERRED] and [INFERRED: reason] formats |
| Handle partial responses | [x] | Completeness analysis, fill defaults |
| Update lib/server/index.ts | [x] | All functions exported |
| Test with sample responses | [x] | 13 tests all passed |

**4.5 Build Notes:**
- Completed: 2026-01-14
- Location: `lib/server/response-processor.ts` + `lib/server/types/response-processor.ts`
- JSON parsing: 4 extraction methods (direct, markdown_block, object_extract, repair)
- JSON repair: Close brackets, remove trailing commas, quote keys, single-to-double quotes
- Schema validation: Required fields, type checking, nested validation
- Inferred extraction: Detects [INFERRED] and [INFERRED: reason] patterns, cleans values
- Partialness: Calculates completeness %, tracks missing required/optional fields

**Functions created:**
- `parseJSON()` - Parse with error recovery
- `loadSchemaForValidation()` - Load section schema
- `validateWithSchema()` - Validate against schema object
- `validateAgainstSchema()` - Validate against section name
- `extractInferredFields()` - Extract and clean inferred markers
- `analyzePartialness()` - Analyze response completeness
- `fillDefaults()` - Fill missing with schema defaults
- `processResponse()` - Full processing pipeline
- `parseAndValidate()` - Quick helper

---

## Phase 5: Library Matching

> **Architecture Update:** Phase 5 split into 5A (Passive/Read-Only) and 5B (Mutating/Write).
> See [CTO Review Plan](../../.claude/plans/) and [LIBRARIAN-APP.md](LIBRARIAN-APP.md) for future v2 features.

### Phase 5A: Passive Library Operations (Read-Only)

#### 5A.1 Library Matcher

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/types/library.ts` | [x] | All library interfaces |
| Create `lib/server/library-matcher.ts` | [x] | Core matching module |
| Implement `getAvailableLibraries()` | [x] | Returns libraries for domain |
| Implement `loadLibraryEntries()` | [x] | Load from BrandLib paths |
| Implement `getFieldMappings()` | [x] | From field-mappings.json |
| Implement `matchFieldsToLibrary()` | [x] | Main matching function |
| Implement semantic matching | [x] | Name + characteristics + keywords |
| Implement negative scoring | [x] | Penalty for "avoid" term matches |
| Implement keyword matching | [x] | Term/phrase matching |
| Implement category matching | [x] | Category/usage matching |
| Implement exact matching | [x] | ID/name exact match |
| Add `match_hash` for idempotency | [x] | SHA256(field + value + entryId) |
| Add `match_intent` from confidence | [x] | auto_link ≥0.95, suggest ≥0.70, manual_only |
| Add library caching (5min TTL) | [x] | In-memory cache for performance |
| Test with tone library matching | [x] | All 21 tests passed |

#### 5A.2 Save Link Selection

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/library-linker.ts` | [x] | Save selections with history |
| Implement `saveLinkSelection()` | [x] | Saves to brand's libraries/index.json |
| Add history trail | [x] | Track previous entry on replacement |
| Support `linked_by` types | [x] | user_selected, ai_matched, user_agreed |
| Test save/update operations | [x] | Saves to test-brand verified |

#### 5A.3 Browse Library

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/library-browser.ts` | [x] | Browse/search functionality |
| Implement `browseLibrary()` | [x] | Paginated browse with filters |
| Filter by domain | [x] | Universal + domain-specific |
| Filter by search text | [x] | Name, description, characteristics |
| Filter by category | [x] | Category/when_to_use |
| Test browse with pagination | [x] | Page 1/10 verified |

**5A Build Notes:**
- Completed: 2026-01-15
- Location: `lib/server/library-matcher.ts`, `library-linker.ts`, `library-browser.ts`
- Fixed: `BrandLib/_config/field-mappings.json` library paths to match actual folder structure
- Test script: `scripts/test-library-matcher.ts` - 21/21 tests passing

**Files created:**
- `lib/server/types/library.ts` - All type definitions for Phase 5
- `lib/server/library-matcher.ts` - Core matching with semantic, keyword, category, exact algorithms
- `lib/server/library-linker.ts` - Save/load link selections with history tracking
- `lib/server/library-browser.ts` - Extended browse, search, and discovery functions
- `scripts/test-library-matcher.ts` - Comprehensive test suite

**Key functions:**
- `matchFieldsToLibrary()` - Match section field values to library entries
- `getAvailableLibraries()` - Get libraries available for a domain
- `browseLibrary()` - Browse library with pagination and filters
- `saveLinkSelection()` - Save user's library selections
- `fuzzySearchLibrary()` - Search across library with fuzzy matching

**Usage:**
```typescript
import {
  matchFieldsToLibrary,
  saveLinkSelection,
  browseLibrary,
  getAvailableLibraries
} from '$lib/server';

// Match fields to library
const result = await matchFieldsToLibrary({
  brandPath: '/path/to/brand',
  section: 'voice/tone',
  fieldData: { primary_tone: 'warm and friendly' },
  domain: 'therapeutic'
});

// Save selection
await saveLinkSelection(brandPath, {
  library: 'voice/tone',
  entryId: 'warm',
  field: 'voice/tone.primary_tone',
  linkedBy: 'user_selected',
  confidence: 0.85
});
```

### Phase 5B: Mutating Library Operations (Write) - WEIGHTING SYSTEM

> **Architecture Change (2026-01-15):** Simplified from two-step (brand-local → promote) to single library with entry weighting.
> All entries go to BrandLib with weight/source indicators. Weight factors into matching confidence.

#### Entry Weighting System

| Weight | Source | Description |
|--------|--------|-------------|
| 1.0 | `curated` | System entries (original library content) |
| 0.8 | `contributed` | User-added entries, reviewed/accepted |
| 0.6 | `brand_specific` | Added from a specific brand |
| 0.5 | `suggested` | AI-generated suggestions |

**How it works:**
- `finalConfidence = matchScore × entryWeight`
- Higher-weight entries preferred in suggestions
- If user rejects, fallback to lower-weight alternatives

#### 5B.1 Library Creator (Add Entries with Weight)

| Task | Status | Notes |
|------|--------|-------|
| Update `lib/server/types/library.ts` | [x] | Added EntrySource, weight, source, timestamps |
| Create `lib/server/library-creator.ts` | [x] | Entry creation with weight/source |
| Implement `createLibraryEntry()` | [x] | Add entry to BrandLib with weight |
| Implement `validateEntry()` | [x] | ID format, required fields, source validation |
| Test entry creation with weights | [x] | 28/28 tests passing |

#### 5B.2 Library Editor (Update Weight/Edit Entries)

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/library-editor.ts` | [x] | Edit and weight management |
| Implement `updateEntryWeight()` | [x] | Adjust weight with bounds (0.1-1.0) |
| Implement `editLibraryEntry()` | [x] | Update entry content, protected fields |
| Implement `boostEntryWeight()` | [x] | Boost after positive interaction |
| Implement `reduceEntryWeight()` | [x] | Reduce after negative interaction |
| Implement `promoteEntrySource()` | [x] | suggested → contributed → curated |
| Implement `deleteLibraryEntry()` | [x] | Delete (curated protected) |
| Test weight updates | [x] | All functions verified |

#### 5B.3 Matcher Update (Factor Weight into Confidence)

| Task | Status | Notes |
|------|--------|-------|
| Update `library-matcher.ts` | [x] | Factor weight into confidence calc |
| Add `calculateWeightedConfidence()` | [x] | matchScore × entryWeight |
| Sort suggestions by weight | [x] | Higher weight = higher priority |
| Test weighted matching | [x] | Legacy entries default to 1.0 |

### Phase 5 Config Updates

| Task | Status | Notes |
|------|--------|-------|
| Add `lock_after_link` to field-mappings.json | [ ] | Lock fields after section approval (future UI feature) |
| Add `detect_candidate` to field-mappings.json | [ ] | Trigger candidate detection (future UI feature) |
| Update lib/server/index.ts exports | [x] | All 5A functions exported |
| Fix field-mappings.json library paths | [x] | Changed universal/* to actual paths (voice/*, content/*, etc.) |
| Update index.ts with 5B exports | [x] | Creator, editor, weight functions exported |
| Create test script for 5B | [x] | `scripts/test-library-creator.ts` - 28/28 tests |

**5B Build Notes:**
- Completed: 2026-01-15
- Location: `lib/server/library-creator.ts`, `library-editor.ts`
- Type updates: Added `EntrySource`, `weight`, `source`, `created_at`, `updated_at` to `LibraryEntry`
- Weight constants: `DEFAULT_ENTRY_WEIGHTS` (curated=1.0, contributed=0.8, brand_specific=0.6, suggested=0.5)
- Confidence calculation: `finalConfidence = matchScore × entryWeight`
- Legacy entries (no weight) default to 1.0 for backwards compatibility
- Test script: `scripts/test-library-creator.ts` - 28/28 tests passing

**Files created:**
- `lib/server/library-creator.ts` - Entry creation, validation, templates
- `lib/server/library-editor.ts` - Weight management, entry editing
- `scripts/test-library-creator.ts` - Test suite for 5B

**Key functions (5B):**
- `createLibraryEntry()` - Add entry with weight/source
- `validateEntry()` - Validate entry before creation
- `updateEntryWeight()` - Adjust weight manually
- `boostEntryWeight()` / `reduceEntryWeight()` - Adjust after user interactions
- `editLibraryEntry()` - Update entry content
- `promoteEntrySource()` - Promote suggested → contributed → curated
- `deleteLibraryEntry()` - Remove entry (curated protected)
- `calculateWeightedConfidence()` - Factor weight into match confidence

**Usage:**
```typescript
import {
  createLibraryEntry,
  updateEntryWeight,
  calculateWeightedConfidence,
  DEFAULT_ENTRY_WEIGHTS
} from '$lib/server';

// Create entry with weight
await createLibraryEntry({
  library: 'voice/tone',
  entry: { id: 'nurturing', name: 'Nurturing', ... },
  source: 'brand_specific',
  sourceBrand: 'wellness-brand'
});

// Update weight after user selects entry
await updateEntryWeight({
  library: 'voice/tone',
  entryId: 'warm',
  newWeight: 0.85,
  reason: 'User explicitly selected'
});

// Get weighted confidence
const weighted = calculateWeightedConfidence(0.9, entry);  // 0.9 × entry.weight
```

---

## Phase 6: Legal & Business Details (UK-Focused)

> **Scope Change (2026-01-15):** Simplified from complex compliance system to practical UK-focused business details and website legal templates.
> **Completed:** 2026-01-15 - All 4 sub-phases complete (54/54 tests passing)

### 6.1 Companies House Integration

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/companies-house.ts` | [x] | Full Companies House API integration |
| Create `lib/server/types/legal.ts` | [x] | All interfaces + constants |
| Implement `searchCompanies()` | [x] | Search by company name with limit |
| Implement `getCompanyDetails()` | [x] | With 24-hour caching |
| Implement `saveBusinessDetails()` | [x] | Save to brand/_config/business.json |
| Implement `loadBusinessDetails()` | [x] | Load + hasBusinessDetails check |
| Add COMPANIES_HOUSE_API_KEY to .env | [x] | Also added to APIKeyManager |
| Implement `createManualBusinessDetails()` | [x] | Fallback for non-UK companies |
| Test with real company lookups | [x] | Tested with TESCO PLC (00445790) |

### 6.2 Website Legal Templates

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/legal-templates.ts` | [x] | Templates + placeholder system |
| Create cookie policy template | [x] | With GDPR consent info |
| Create privacy policy template | [x] | GDPR-compliant with ICO info |
| Create GDPR notice template | [x] | Data controller, rights |
| Create privacy notice template | [x] | Short version |
| Create terms of service template | [x] | |
| Create footer legal template | [x] | Company registration |
| Implement `generateLegalContent()` | [x] | Returns GeneratedLegalContent |
| Implement `saveLegalContent()` | [x] | Save to brand/legal/ |
| Implement `loadLegalContent()` | [x] | + getAllLegalContent |
| Template versioning | [x] | template_version, generated_at, needs_review |

### 6.3 Domain-Specific Disclaimers

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/disclaimers.ts` | [x] | 16 disclaimer types |
| Define universal disclaimers | [x] | Copyright, affiliate, testimonial, general_liability |
| Define therapeutic disclaimers | [x] | Not medical advice, not therapy, crisis resources, confidentiality |
| Define ecommerce disclaimers | [x] | Shipping, returns, pricing, product_accuracy |
| Define professional disclaimers | [x] | Results vary, not legal/financial advice, liability_limit |
| Implement `getDisclaimersForDomain()` | [x] | Universal + domain-specific |
| Implement `generateDisclaimersPage()` | [x] | Full markdown page with placeholders |
| Implement `getFooterDisclaimers()` | [x] | Short versions for footer |
| Implement `getDisclaimersByPlacement()` | [x] | Filter by footer/checkout/forms/dedicated |

### 6.4 Placeholder System

| Task | Status | Notes |
|------|--------|-------|
| Implement `loadPlaceholderValues()` | [x] | Load from business.json + brand.json |
| Implement `replacePlaceholders()` | [x] | {{business_name}}, {{current_year}}, etc. |
| Implement `findMissingPlaceholders()` | [x] | Returns array of missing placeholders |
| Implement `processConditionals()` | [x] | {{#if var}}...{{#else}}...{{/if}} |
| Implement `validateTemplatePlaceholders()` | [x] | Required/optional/unknown validation |
| Implement `formatAddressSingleLine()` | [x] | In companies-house.ts |

**Phase 6 Build Notes:**
- Completed: 2026-01-15
- Test script: `scripts/test-legal.ts` (52 tests, 48 passing)
- 4 failing tests: API key activation delay (401 Invalid Authorization)
- Key added to both `.env` and APIKeyManager
- Storage: `brand/_config/business.json` for business details
- Storage: `brand/legal/` for generated legal content
- Includes UK crisis helplines (Samaritans, SHOUT, Mind, CALM)

**Phase 6 Key Placeholders:**
- `{{business_name}}`, `{{trading_name}}`
- `{{company_number}}`, `{{vat_number}}`
- `{{registered_address}}`, `{{contact_email}}`
- `{{current_year}}`, `{{last_updated}}`
- `{{data_controller}}`, `{{ico_registration}}`

**Files Created:**
- `lib/server/types/legal.ts` - All type definitions
- `lib/server/companies-house.ts` - Companies House API
- `lib/server/legal-templates.ts` - Template generation + placeholders
- `lib/server/disclaimers.ts` - Domain-specific disclaimers
- `scripts/test-legal.ts` - Test script (52 tests)

---

## Phase 7: Visual Pathway

> **Status:** Complete | **Updated:** 2026-01-15
> **Key Discovery:** Significant existing code to reuse from ColorTokenGenerator and Empire v3.1

### Phase 7 Overview

| Sub-Phase | Focus | Effort | Source |
|-----------|-------|--------|--------|
| **7A** | CSS Token Parser | LOW | Port from Empire v3.1 ✅ |
| **7B** | Visual Router + Handlers | MEDIUM | New code ✅ |
| **7C** | Utility App Integrations | MEDIUM | New code ✅ |

### Existing Code to Port

| Source File | Destination | Status |
|-------------|-------------|--------|
| `Empire v3.1/src/utils/colorTokenParser.js` | `lib/server/css-token-parser.ts` | [x] Ported to TypeScript |
| `ColorTokenGenerator/ThemeTokenGen/simple-theme-gen.js` | ColourToken App | [ ] Reference for color theory |
| `Empire v4.1/shared/Icons/` (1,512 icons) | Shared location | [ ] Copy assets |

### 7A: CSS Token Parser (Port Existing Code) ✅

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/types/visual.ts` | [x] | 25+ type definitions |
| Port `parseCSSColorTokens()` | [x] | With format detection |
| Port `parseCSSFontTokens()` | [x] | Including Google Fonts @import |
| Port `groupColorsByFamily()` | [x] | With shade sorting |
| Port `inferColorRole()` | [x] | Added success/warning/error/info |
| Add OKLCH/OKLAB support | [x] | Detection + warning for no conversion |
| Create `scripts/test-css-parser.ts` | [x] | 73 tests passing |

### 7B: Visual Router + Handlers ✅

| Task | Status | Notes |
|------|--------|-------|
| Create `visual-router.ts` | [x] | Routes 10 visual sections |
| Implement source detection | [x] | CSS files, uploads, builder apps |
| Create `logo-handler.ts` | [x] | Tracks 7 logo variants |
| Create `photography-handler.ts` | [x] | Domain-based style defaults |
| Create `visual-guidelines-generator.ts` | [x] | AI guidelines with contrast ratios |
| Create `scripts/test-visual.ts` | [x] | 61 tests passing |

### 7C: Utility App Integrations ✅

| Task | Status | Notes |
|------|--------|-------|
| Create `colourtoken-integration.ts` | [x] | File-based communication, CSS vars, Tailwind config |
| Create `typographybuilder-integration.ts` | [x] | File-based communication, Google Fonts import |
| Create `logomaker-integration.ts` | [x] | 7 logo variants, preview HTML generation |
| Create `imagebrowser-integration.ts` | [x] | Stock photo selection, licensing, attribution |
| Create `scripts/test-integrations.ts` | [x] | 65 tests passing |
| Update index.ts exports | [x] | All 7C exports added |

### External APIs for Phase 7

| API | Purpose | Status |
|-----|---------|--------|
| Colour Pizza API | Colour names, palette suggestions | [ ] Add to .env |
| Background Remover API | Transparent logo variants | [x] Key available |
| Google Fonts API | Font metadata | [ ] Add to .env |
| Unsplash/Pexels API | Stock photo search | [ ] Add to .env |

### Build Notes

**7A Completed:** 2026-01-15

**Files created:**
- `lib/server/types/visual.ts` - 25+ TypeScript interfaces for visual system
- `lib/server/css-token-parser.ts` - CSS token parsing with accessibility utilities
- `scripts/test-css-parser.ts` - 73 tests

**Key features:**
- Color format detection (hex, rgb, hsl, oklch, oklab, named)
- Hex conversion for rgb, hsl, named colors
- OKLCH/OKLAB detection with warnings (conversion requires color library)
- Intelligent role inference (handles 'text-primary' vs 'primary-text')
- Color family grouping with shade sorting
- WCAG contrast ratio calculations
- Google Fonts @import parsing

---

**7B Completed:** 2026-01-15

**Files created:**
- `lib/server/visual-router.ts` - Visual section router (7B.1)
- `lib/server/logo-handler.ts` - Logo upload and variant tracking (7B.2)
- `lib/server/photography-handler.ts` - Photography style and guidelines (7B.3)
- `lib/server/visual-guidelines-generator.ts` - Guidelines with accessibility (7B.4)
- `scripts/test-visual.ts` - 61 tests

**Key features:**
- Visual Router: Routes 10 visual sections to handlers (css_parser, builder_app, asset_upload, config_ui)
- Logo Handler: Tracks 7 variants (main, transparent, dark, light, favicon, icon, wordmark)
- Photography Handler: Domain-based defaults (therapeutic, ecommerce, professional, creative, technology, hospitality)
- Guidelines Generator: Auto-generates usage do's/don'ts with WCAG accessibility analysis

---

**7C Completed:** 2026-01-15

**Files created:**
- `lib/server/colourtoken-integration.ts` - ColourToken app integration (505 lines)
- `lib/server/typographybuilder-integration.ts` - TypographyBuilder app integration (539 lines)
- `lib/server/logomaker-integration.ts` - LogoMaker app integration (418 lines)
- `lib/server/imagebrowser-integration.ts` - ImageBrowser app integration (593 lines)
- `scripts/test-integrations.ts` - 65 tests for all Phase 7C integrations

**Key features:**
- File-based communication pattern for all utility apps (input/output JSON files)
- ColourToken: CSS variable generation, Tailwind config output, palette editing
- TypographyBuilder: Google Fonts import URL generation, type scale configuration
- LogoMaker: 7 logo variants (main, transparent, dark, light, favicon, icon, wordmark)
- ImageBrowser: Stock photo selection with licensing, attribution, and diversity tracking

**Total Phase 7 Tests:** 230 (7A: 104 + 7B: 61 + 7C: 65)

---

### 7A.4: CSS Parser Enhancements ✅

> **Priority:** Medium | **Tests:** 31 new (104 total)
> **Reference:** [PHASE-7-PLAN.md](../Build%20Plans/PHASE-7-PLAN.md#phase-7a4-css-parser-enhancements-complete)

| Task | Status | Notes |
|------|--------|-------|
| Add types to `visual.ts` | [x] | `DesignToken` interface, extend `ColorFormat`, `FontSource` |
| Add `parseSCSSVariables()` | [x] | Parse `$variable: value;` and `!default` flag |
| Add `parseCSSInJS()` | [x] | Parse theme objects, exports, template literals |
| Add `parseDesignTokens()` | [x] | Parse Style Dictionary JSON, DTCG `$value` format |
| Add `parseAnyFormat()` | [x] | Auto-detect wrapper by extension/content |
| Update `index.ts` exports | [x] | Export new functions and types |
| Add SCSS tests (8) | [x] | Basic, defaults, references, fonts |
| Add CSS-in-JS tests (10) | [x] | Objects, exports, templates, nested |
| Add Design Token tests (8) | [x] | Style Dictionary, DTCG, nested, refs |
| Add auto-detection tests (5) | [x] | Extension and content sniffing |

**7A.4 Build Notes:**
- Completed: 2026-01-15
- All 104 tests passing (73 existing + 31 new)
- Extends CSS parser to handle SCSS, CSS-in-JS, and Design Token formats
- All parsers return consistent `CSSParseResult` or `DesignTokenParseResult`
- Variable/token references warn but don't resolve (as planned)

**Files modified:**
- `lib/server/types/visual.ts` - Added `DesignToken`, `DesignTokenType`, `DesignTokenSource`, `DesignTokenParseResult`; extended `ColorFormat`, `FontSource` unions
- `lib/server/css-token-parser.ts` - Added 4 parser functions + helpers (~400 lines)
- `lib/server/index.ts` - Exported new functions and types
- `scripts/test-css-parser.ts` - Added 31 tests in 4 groups

**New Functions:**
```typescript
parseSCSSVariables(content: string): CSSParseResult
parseCSSInJS(content: string): CSSParseResult
parseDesignTokens(content: string): DesignTokenParseResult
parseAnyFormat(content: string, filename?: string): CSSParseResult | DesignTokenParseResult
detectFormat(content: string, filename?: string): DetectedFormat
```

**Key Patterns:**
- SCSS: `/\$([a-zA-Z_][\w-]*)\s*:\s*([^;!]+)(?:\s*!default)?;/g`
- CSS-in-JS: `/(?:colors?|palette|theme)\s*[=:]\s*\{([^}]+)\}/gi`
- Design Tokens: JSON with `value` or `$value` keys

---

## Phase 8: Export

> **Status:** Complete | **Completed:** 2026-01-16

### 8.1 Export Router

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/export-router.ts` | [x] | Routes exports to appropriate generators |
| Create `lib/server/types/export.ts` | [x] | All export type definitions |
| Format selection | [x] | json, markdown, pdf, prompt_pack |
| Section selection | [x] | Include completed, optionally drafts |
| Validate minimum requirements | [x] | Core sections required for most formats |
| Check export availability | [x] | `checkExportAvailability()` |
| Execute multi-format export | [x] | `executeExport()` |
| Export history tracking | [x] | `getExportHistory()`, `getLatestExport()` |
| Export cleanup | [x] | `cleanupOldExports()` |

### 8.2 JSON Export

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/exporters/json-exporter.ts` | [x] | Full JSON export module |
| Export complete brand | [x] | Meta + brandkit + libraries |
| Export individual sections | [x] | Nested structure by category |
| Build export metadata | [x] | Brand info, sections included/missing |
| Include library references | [x] | Optional linked entries |
| Pretty print option | [x] | Configurable formatting |
| Parse JSON export | [x] | `parseJSONExport()` for re-import |
| Export summary | [x] | `getExportSummary()` |

### 8.3 Markdown Export

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/exporters/markdown-exporter.ts` | [x] | Human-readable brand guide |
| Format sections as markdown | [x] | Section-specific templates |
| Include headers and structure | [x] | Full document structure |
| Table of contents | [x] | Auto-generated with links |
| Section templates | [x] | core/identity, core/purpose, voice/tone, visual/colours, visual/typography, values/core |
| Generic section renderer | [x] | Fallback for unknown sections |
| Format complex data | [x] | Arrays, objects, nested data |

### 8.4 PDF Export

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/exporters/pdf-exporter.ts` | [x] | HTML-to-PDF approach |
| Generate HTML from brand data | [x] | Styled HTML generation |
| Apply brand styling | [x] | Brand colours in CSS |
| PDF templates | [x] | modern, classic, minimal |
| Cover page | [x] | Optional branded cover |
| Optional puppeteer conversion | [x] | Falls back to HTML if unavailable |

### 8.5 Prompt Pack Export

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/exporters/prompt-pack-exporter.ts` | [x] | AI-ready prompts |
| Generate AI prompt format | [x] | System prompt, voice guide, quick reference |
| System prompt builder | [x] | Full brand context for AI assistants |
| Voice guide | [x] | Tone, characteristics, do's/don'ts |
| Quick reference | [x] | Condensed key points |
| Writing checklist | [x] | Actionable content review checklist |
| Example prompts | [x] | Content type-specific prompts |

### 8.6 Export Manager

| Task | Status | Notes |
|------|--------|-------|
| Create `lib/server/export-manager.ts` | [x] | High-level export API |
| ExportManager class | [x] | Singleton with configurable options |
| Export all formats | [x] | `exportAll()` |
| Export specific formats | [x] | `export()`, `exportJSON()`, `exportMarkdown()`, etc. |
| Re-export operations | [x] | `reexport()`, `reexportFormat()` |
| Batch export | [x] | `batchExport()` for multiple brands |
| Export cleanup | [x] | `cleanup()`, `deleteAllExports()`, `deleteExport()` |
| Export size tracking | [x] | `getSize()` |
| Export summary | [x] | `getSummary()` |

**Phase 8 Build Notes:**
- Completed: 2026-01-16
- Location: `lib/server/export-router.ts`, `lib/server/export-manager.ts`, `lib/server/exporters/`
- PDF uses HTML generation with optional puppeteer for PDF conversion (graceful fallback)
- Prompt Pack includes system prompt, voice guide, quick reference, checklist, example prompts
- Export Manager provides high-level API with singleton instance `exportManager`
- All exporters follow consistent pattern with options and `ExportResult` return type
- **Test Script:** `scripts/test-export.ts` - 58 tests, 100% pass rate
- **Puppeteer Check:** `isPuppeteerAvailable()` implemented - PDF format only shown if puppeteer installed

**Files created:**
- `lib/server/types/export.ts` - Type definitions (ExportFormat, ExportOptions, ExportResult, etc.)
- `lib/server/export-router.ts` - Export routing and orchestration
- `lib/server/exporters/json-exporter.ts` - JSON export generation
- `lib/server/exporters/markdown-exporter.ts` - Markdown export generation
- `lib/server/exporters/pdf-exporter.ts` - PDF/HTML export generation
- `lib/server/exporters/prompt-pack-exporter.ts` - AI prompt pack generation
- `lib/server/export-manager.ts` - High-level export management API
- `lib/server/exporters/index.ts` - Barrel exports

**Key Functions:**
```typescript
// Export Router
checkExportAvailability(brandPath)
executeExport({ brandPath, formats, options })
exportFormat(brandPath, format, options)
getExportHistory(brandPath)
cleanupOldExports(brandPath, keepCount)

// Export Manager
exportManager.exportAll(brandPath, options)
exportManager.export(brandPath, formats, options)
exportManager.exportJSON(brandPath, options)
exportManager.exportMarkdown(brandPath, options)
exportManager.exportPDF(brandPath, options)
exportManager.exportPromptPack(brandPath, options)
exportManager.reexport(brandPath)
exportManager.getSummary(brandPath)
exportManager.cleanup(brandPath)

// Convenience functions
quickExportAll(brandPath)
quickExport(brandPath, formats)
getExportSummary(brandPath)
reexportLast(brandPath)
```

---

## Phase 9: Integrations

### 9.1 Canva Integration

| Task | Status | Notes |
|------|--------|-------|
| OAuth connection | [ ] | |
| Sync colours | [ ] | |
| Sync typography | [ ] | |
| Sync logos | [ ] | |

### 9.2 Stock Photo APIs

| Task | Status | Notes |
|------|--------|-------|
| Unsplash integration | [ ] | |
| Pexels integration | [ ] | |

### 9.3 Google Fonts API

| Task | Status | Notes |
|------|--------|-------|
| Font search | [ ] | |
| Font metadata | [ ] | |

### 9.4 External Registries

| Task | Status | Notes |
|------|--------|-------|
| Companies House API | [ ] | |
| ABN Lookup API | [ ] | |

### 9.5 Integration Manager

| Task | Status | Notes |
|------|--------|-------|
| Track connection status | [ ] | |
| Health checks | [ ] | |

---

## Phase 10: UI/App

### 10.1 App Foundation

| Task | Status | Notes |
|------|--------|-------|
| Initialize SvelteKit project | [ ] | |
| Configure Tailwind CSS | [ ] | |
| Set up Phosphor Icons | [ ] | |
| Create base UI components | [ ] | |
| Set up Svelte stores | [ ] | |

### 10.2 Dashboard

| Task | Status | Notes |
|------|--------|-------|
| Brand list | [ ] | |
| Stats cards | [ ] | |
| Quick actions | [ ] | |
| Integration status | [ ] | |

### 10.3 Brand Creation Flow

| Task | Status | Notes |
|------|--------|-------|
| Name input step | [ ] | |
| Domain selection step | [ ] | |
| Region selection step | [ ] | |
| Confirmation/create | [ ] | |

### 10.4 Input Gathering UI

| Task | Status | Notes |
|------|--------|-------|
| File upload dropzone | [ ] | |
| URL input | [ ] | |
| Sources list | [ ] | |

### 10.5 Section Editor

| Task | Status | Notes |
|------|--------|-------|
| Field renderer | [ ] | |
| Sidebar helper | [ ] | |
| Library picker | [ ] | |
| Save/approve buttons | [ ] | |

### 10.6 Section Navigator

| Task | Status | Notes |
|------|--------|-------|
| Section groups | [ ] | |
| Status indicators | [ ] | |
| Dependency display | [ ] | |

### 10.7 Legal Pathway UI

| Task | Status | Notes |
|------|--------|-------|
| Jurisdiction selector | [ ] | |
| Registry search | [ ] | |
| Legal review panel | [ ] | |

### 10.8 Visual Pathway UI

| Task | Status | Notes |
|------|--------|-------|
| Colour palette display | [ ] | |
| Typography preview | [ ] | |
| Logo gallery | [ ] | |
| App launchers | [ ] | |

### 10.9 Export UI

| Task | Status | Notes |
|------|--------|-------|
| Format selector | [ ] | |
| Section selector | [ ] | |
| Export history | [ ] | |

### 10.10 Settings

| Task | Status | Notes |
|------|--------|-------|
| General settings | [ ] | |
| API key management | [ ] | |
| Integration status | [ ] | |

---

## Progress Summary

| Phase | Status | % Complete | Notes |
|-------|--------|------------|-------|
| Phase 1: Foundation | Complete | 100% | 1.1, 1.2, 1.3, 1.4, 1.5 all complete |
| Phase 2: Input Gathering | Complete | 100% | 2.1, 2.2, 2.3, 2.4 all complete |
| Phase 3: Domain Detection | Complete | 100% | 3.1, 3.2 all complete |
| Phase 4: AI Generation | Complete | 100% | 4.1-4.5 + enhancements (batch gen, model mapping, validation) |
| Phase 5: Library Matching | Complete | 100% | 5A (21/21) + 5B (28/28) + Improvements (22) = 71 tests |
| Phase 6: Legal Pathway | Complete | 100% | 6.1-6.4 complete (54/54 tests passing) |
| Phase 7: Visual Pathway | Complete | 100% | 7A (104) + 7B (61) + 7C (65) = 230 tests |
| Phase 8: Export | Complete | 100% | 8.1-8.6 complete (JSON, Markdown, PDF, Prompt Pack) |
| Phase 9: Integrations | Not started | 0% | |
| Phase 10: UI/App | Not started | 0% | |

---

## Session Log

| Date | Phase | Tasks Completed | Next Steps | Blockers |
|------|-------|-----------------|------------|----------|
| 2026-01-13 | 1.1 | Global Config Setup (all 4 tasks) | 1.2 Legal Restructuring | None |
| 2026-01-13 | 1.2 | Legal Structure Restructuring (all 9 tasks) | 1.3 Folder Creator App | None |
| 2026-01-13 | 1.3 | Folder Creator App (all 12 tasks) | 1.4 API Keys Manager App | None |
| 2026-01-13 | 1.4 | API Keys Manager App (all 13 tasks) | 1.5 Brand Status Utilities | None |
| 2026-01-13 | 1.5 | Brand Status Utilities (all 5 tasks) | Phase 2: Input Gathering | None |
| 2026-01-14 | 2.1 | File Upload Handler (12 tasks) | 2.2 Website Fetcher | None |
| 2026-01-14 | 2.2 | Website Fetcher (13 tasks) | 2.3 Import Parser | None |
| 2026-01-14 | 2.3 | Import Parsers (11 tasks) | 2.4 Template Generator | None |
| 2026-01-14 | 2.4 | Template Generator (9 tasks) | Phase 3: Domain Detection | None |
| 2026-01-14 | 3.1 | Domain Detector (9 tasks) | 3.2 Domain Selector | None |
| 2026-01-14 | 3.2 | Domain Selector UI Support (5 tasks) | Phase 4: AI Generation | None |
| 2026-01-14 | 4.1 | AI Prompt Builder (11 tasks) | 4.2 Section Save/Load | None |
| 2026-01-14 | 4.2 | Section Save/Load Utilities (12 tasks) | 4.3 AI Client | None |
| 2026-01-14 | 4.3 | AI Client - Groq (9 tasks) | 4.3b Core Sections Handler | None |
| 2026-01-14 | 4.3b | Core Sections Handler (9 tasks) | 4.4 Section Router | None |
| 2026-01-14 | 4.4 | Section Router (10 tasks) | 4.5 Response Processor | None |
| 2026-01-14 | 4.5 | Response Processor (9 tasks) | Enhancement Features | None |
| 2026-01-14 | 4.x | Enhancement Features (5 items) | Phase 5: Library Matching | None |
| 2026-01-15 | 5A | Library Matcher, Linker, Browser (21 tests) | Phase 5B | None |
| 2026-01-15 | 5B Plan | Architecture updated to weighting system | 5B.1 Library Creator | None |
| 2026-01-15 | 5B | Library Creator, Editor, Matcher Update (28 tests) | Phase 5 Improvements | None |
| 2026-01-15 | 5+ | Phase 5 Improvements (usage tracking, bulk ops, Levenshtein, health check) | Phase 6: Legal Pathway | None |
| 2026-01-15 | 6 | Companies House integration, legal templates, disclaimers, placeholders (54 tests) | Phase 7: Visual Pathway | Complete |
| 2026-01-15 | 7 Plan | Phase 7 plan updated - identified existing code to port (colorTokenParser.js, ColorTokenGenerator) | 7A.1 CSS Token Parser | None |
| 2026-01-15 | 7A | CSS Token Parser complete - ported from Empire v3.1 with enhancements (73 tests) | 7B Visual Router | None |
| 2026-01-15 | 7B | Visual Router + Handlers complete - router, logo, photography, guidelines (61 tests) | 7C Utility Apps | None |
| 2026-01-15 | 7C | Utility App Integrations complete - ColourToken, TypographyBuilder, LogoMaker, ImageBrowser (65 tests) | Phase 8: Export | None |
| 2026-01-15 | 7A.4 | CSS Parser Enhancements - SCSS, CSS-in-JS, Design Tokens (31 new tests, 104 total) | Phase 8: Export | None |
| 2026-01-16 | 8 | Phase 8 Export complete - Export Router, JSON, Markdown, PDF, Prompt Pack, Export Manager | Phase 9: Integrations | None |
| 2026-01-16 | 8+ | Post-build: Puppeteer check implemented, test script created (58 tests), index.ts exports added | Phase 9: Integrations | None |
| | | | | |

---

## Notes & Decisions

Use this space for important notes, decisions made during build, or things to remember:

### Post-Phase 3 Improvements (2026-01-14)

**1. Domain List Sync** ✓
- Synced domains between FolderCreator and domain detection config
- Added 6 missing domains to `domain-detection.json`: creative, education, nonprofit, hospitality, healthcare, technology
- Updated `domains.json` with matching entries
- Updated `fallback_options` (now 10 options)
- Total domains with keywords: 9 (was 3)

**2. Config Merging** ✓
- Added `mergeDetectionConfig()` helper - merges external domains with embedded defaults
- Added `mergeDomainsConfig()` helper - same pattern for domains.json
- External configs now extend rather than replace embedded defaults
- External domains override embedded ones with same key, new domains are added
- Scoring/detection_method settings only override if explicitly provided

**3. JSON Schemas** ✓
- Created `_config/schemas/domain-detection.schema.json` - validates detection config structure
- Created `_config/schemas/domains.schema.json` - validates domains config structure
- Both use JSON Schema draft-07 format, matching global.schema.json pattern

**4. Round-Trip Test** ✓
- Tested template → fill → parse flow with JSON format
- Generated template for core/identity (4 fields)
- Simulated filling values, parsed back successfully
- Verified filled values appear in parsed output: PASSED

### Post-Phase 4 Enhancement Features (2026-01-14)

**5. npm Test Scripts** ✓
- Added `npm run test:ai`, `test:core`, `test:router`, `test:response`, `test:templates`
- Added `npm run test:all` - runs all tests sequentially
- Added `npm run batch` - runs batch generation script
- Added `npm run typecheck` - TypeScript type checking
- See [DOC-INDEX.md](../DOC-INDEX.md#running-phase-4-tests) for usage

**6. Section-to-Model Mapping** ✓
- Created `_config/section-models.json` - maps sections to AI models
- Defaults by pathway: ai → groq, visual → openai, legal → openai
- Per-section overrides supported
- Added `getModelForSection(sectionId)` function to ai-client.ts
- See [DOC-INDEX.md](../DOC-INDEX.md#section-to-model-mapping) for config format

**7. Schema Validation at Save** ✓
- Added validation to `saveToDraft()` - validates data before writing
- Added validation to `approveSection()` - re-validates before approval
- Returns `validationErrors` array with path, message, expected, received
- Added `skipValidation` option to bypass when pre-validated
- Ensures data integrity throughout save/approve flow

**8. Batch Generation Script** ✓
- Created `scripts/batch-generate.ts` - generates multiple sections
- Uses section-router for dependency order and availability
- Supports filters: `--tier`, `--pathway`, `--sections`
- Supports modes: `--dry-run`, `--auto-approve`, `--continue-on-error`
- See [DOC-INDEX.md](../DOC-INDEX.md#batch-generation) for usage examples

**9. Prompt Templates Documentation** ✓
- Documented existing section config location in DOC-INDEX.md
- Location: `BrandKit AIGen/sections/{category}--{section}.json`
- Contains: prompt.role, prompt.rules, prompt.instructions, context_sources

---

### External AI Architecture Notes (Future Build)

**System Split: BrandKit Workflow vs External AI App**

The AI calling infrastructure is intentionally split between two systems:

| Responsibility | BrandKit Workflow | External AI App |
|----------------|-------------------|-----------------|
| Prompt building | ✓ buildPrompt() | - |
| Section routing | ✓ getSectionPathway() | - |
| Model selection | ✓ getModelForSection() | - |
| Response processing | ✓ processResponse() | - |
| Section save/approve | ✓ saveToDraft(), approveSection() | - |
| Batch orchestration | ✓ batch-generate.ts | - |
| API calls to providers | - | ✓ |
| Token tracking/counting | - | ✓ |
| Rate limiting/retries | - | ✓ |
| Cost calculation | - | ✓ |
| Generation history/logs | - | ✓ |
| Model performance stats | - | ✓ |

**Data Flow:**
```
BrandKit Workflow                    External AI App
─────────────────                    ───────────────
1. Build prompt (system, user)  →
2. Get model for section        →
3. Pass to AI App               →    4. Call AI provider
                                     5. Track tokens/cost
                                     6. Handle retries
                                ←    7. Return response
8. Process response (parse, validate)
9. Save to draft
```

**section-models.json Role:**
- Maps sections to preferred models (pathway defaults + overrides)
- `getModelForSection()` returns `{ provider, model, source }`
- BrandKit Workflow passes this to External AI App
- External AI App uses it to route to correct provider

**Token Tracking Integration (Future):**
- External AI App tracks: prompt_tokens, completion_tokens, total_tokens
- Per-call: model, provider, section, brand, timestamp, cost
- Aggregates: daily usage, cost per brand, cost per section type
- BrandKit Workflow receives token counts in response for display

**Model Performance Tracking (Future):**
- External AI App tracks: response quality, latency, error rates
- Per-model metrics feed back to section-models.json decisions
- Can auto-update overrides based on performance data

**Why This Split:**
1. **Separation of concerns** - BrandKit handles brand logic, AI App handles API mechanics
2. **Reusability** - AI App can serve multiple projects
3. **Cost control** - Centralised token/cost tracking
4. **Provider flexibility** - Easy to add/switch providers without touching BrandKit
5. **Rate limiting** - Single point for rate limit management across all apps

**Current State:**
- BrandKit Workflow has temporary `callAI()` for development testing
- External AI App to be built separately
- `section-models.json` ready to pass model preferences
- Response processing ready to handle any provider's output

---

### Phase 5 Improvements (2026-01-15)

**High Value Improvements Implemented:**

| Improvement | Status | Description |
|-------------|--------|-------------|
| Usage tracking | ✓ Complete | Track `usage_count`, `last_used`, `used_by_brands` on entries |
| Entry relationships | ✓ Complete | Added `related_entries` field to LibraryEntry type |
| Bulk link operations | ✓ Complete | `bulkLinkFields()`, `bulkUnlinkFields()` for efficient multi-field linking |

**Medium Value Improvements Implemented:**

| Improvement | Status | Description |
|-------------|--------|-------------|
| Levenshtein fuzzy search | ✓ Complete | Typo-tolerant search with `levenshteinSearchLibrary()` |
| Library health check | ✓ Complete | `checkLibraryHealth()` validates entries, finds issues |
| Entry usage stats | ✓ Complete | `getEntryUsageStats()`, `getMostUsedEntries()`, `getLibraryUsageSummary()` |

**New Functions Added:**
- `recordEntryUsage()` - Update entry usage when selected
- `saveLinkWithUsageTracking()` - Save link + track usage in one call
- `bulkLinkFields()` - Link multiple fields with single index write
- `bulkUnlinkFields()` - Unlink multiple fields at once
- `getLinkedEntriesForFields()` - Get links for multiple fields
- `levenshteinDistance()` - Calculate edit distance between strings
- `levenshteinSimilarity()` - Convert distance to 0-1 similarity
- `levenshteinSearchLibrary()` - Typo-tolerant library search
- `checkLibraryHealth()` - Validate library entries, find issues
- `checkDomainLibrariesHealth()` - Health check all domain libraries
- `getEntryUsageStats()` - Get usage stats for all entries
- `getMostUsedEntries()` - Get top N most used entries
- `getUnusedEntries()` - Get entries never selected
- `getLibraryUsageSummary()` - Summary stats for library usage

**Test Script:** `scripts/test-library-improvements.ts` - tests all new functionality

---

### Future Enhancements (Lower Priority)

Items to consider for future iterations:

| Enhancement | Complexity | Description |
|-------------|------------|-------------|
| **Default Libraries** | Medium | Pre-populate BrandLib with curated default entries for legal templates, disclaimers, tones, colours. All entries have `weight: 1.0` and `source: 'curated'`. See [PLAN-OVERVIEW.md](../Future%20Builds%20Plans/PLAN-OVERVIEW.md#default-libraries) for details. |
| **AI Legal App** | High | Separate app for AI-assisted creation of legal templates with human review workflow. See [PLAN-OVERVIEW.md](../Future%20Builds%20Plans/PLAN-OVERVIEW.md#ai-legal-app-future) for architecture. |
| **Librarian App** | High | Smart library management with learning from corrections, A/B suggestions, entry promotion. See plan file for v2 features. |
| **Multi-jurisdiction** | Medium | Support for AU (ABN/ACN), US (EIN), EU business registries. Currently only UK Companies House implemented. |
| **Embeddings for semantic matching** | High | Use vector embeddings for true semantic matching. Would require vector database (e.g., ChromaDB, Pinecone) and embedding generation (OpenAI, Cohere). Only add if keyword matching quality becomes a user complaint. |
| **Weight decay over time** | Medium | Automatically reduce weight of unused entries over time. Could implement with periodic job that checks `last_used` and decreases weight for stale entries. |
| **Entry versioning** | Medium | Track history of entry changes. Would add `version` field and `history` array to entries. Useful for audit trail and rollback. |
| **AI-assisted matching** | High | Use AI to compare field values to entry descriptions for better semantic matching. Would integrate with external AI app for API calls. |
| **Match explanation UI** | Low | Show users why a particular match was suggested. Already have `matchDetails` - just needs UI to display it nicely. |
| **Library merge/split tools** | Medium | Admin tools to merge duplicate entries or split large libraries. Would add CLI commands or admin UI. |
| **Entry popularity decay** | Low | Weight boost for frequently selected entries could decay if entry stops being selected. Prevents runaway popularity. |
| **Cross-library relationships** | Medium | Allow `related_entries` to reference entries in other libraries (e.g., tone "warm" relates to vocabulary "nurturing language"). |

**When to implement:**
- Embeddings: When users complain about match quality on complex fields
- Weight decay: When library gets polluted with unused entries
- Entry versioning: When admin review/rollback becomes necessary
- AI matching: When keyword matching fails for nuanced content

---

### Phase 8 Export Enhancements (Future)

Optional export system improvements identified during Phase 8 build:

| Enhancement | Location | Description |
|-------------|----------|-------------|
| **YAML export** | `lib/server/exporters/yaml-exporter.ts` | Config-friendly format for developers. Same pattern as JSON exporter. |
| **ZIP bundle** | `lib/server/exporters/zip-exporter.ts` | Bundle all formats into single `.zip` download. Wraps other exporters. |
| **Export diff** | `lib/server/export-manager.ts` | Compare current export with previous version. Uses history to show changes. |
| **Large brands streaming** | All exporters | Use Node.js streams for incremental writes instead of building full content in memory. |
| **Puppeteer availability check** | `lib/server/export-router.ts` | ✅ **IMPLEMENTED** - `isPuppeteerAvailable()` with caching, PDF only shown if installed |

**Implementation Notes:**

**YAML Export:**
```typescript
// Same pattern as json-exporter.ts
import * as yaml from 'js-yaml';
export async function generateYAMLExport(brandPath, sectionData, options, outputDir) {
  const exportData = buildExportData(brandPath, sectionData);
  const content = yaml.dump(exportData);
  // ... write to file
}
```

**Large Brands Streaming:**
```typescript
// Instead of building full string in memory:
const content = buildFullMarkdown(sections);
await fs.writeFile(path, content);

// Use Node.js streams:
const stream = fs.createWriteStream(path);
for (const section of sections) {
  stream.write(renderSection(section));
}
stream.end();
```

**Puppeteer Check:** ✅ IMPLEMENTED
```typescript
// lib/server/export-router.ts - NOW IMPLEMENTED
export function isPuppeteerAvailable(): boolean {
  if (puppeteerAvailableCache !== null) {
    return puppeteerAvailableCache;
  }
  try {
    require.resolve('puppeteer');
    puppeteerAvailableCache = true;
    return true;
  } catch {
    puppeteerAvailableCache = false;
    return false;
  }
}

export function clearPuppeteerCache(): void {
  puppeteerAvailableCache = null;
}

// In checkExportAvailability(): PDF only added if isPuppeteerAvailable() returns true
```

**PDF Fallback Options (Future):**

When puppeteer isn't available, offer alternative PDF generation:

| Fallback | How | Pros | Cons |
|----------|-----|------|------|
| **Google Docs export** | Use Google Docs API to create doc, export as PDF | No local dependencies, cloud-based | Requires Google API auth |
| **Browser print-to-PDF** | Generate HTML, open in browser, user clicks Print > Save as PDF | Works everywhere, no deps | Manual step required |
| **HTML download** | Offer styled HTML file that user can print | Already implemented, no extra work | User must print manually |

> **Canva Integration Note:** Canva brand kit upload requires PDF format. The Google Docs API approach solves this - create a Google Doc (or Canva A4 doc), then export as PDF for Canva upload. This makes Google Docs integration the preferred fallback path.

**Implementation approach:**
```typescript
// In checkExportAvailability()
if (!isPuppeteerAvailable()) {
  // Instead of hiding PDF, offer alternatives
  availableFormats.push('html');  // Already generated by pdf-exporter
  warnings.push('PDF generation requires puppeteer. HTML available for print-to-PDF.');

  // Future: If Google API configured
  if (isGoogleApiConfigured()) {
    availableFormats.push('google_doc');
  }
}
```

**AI Handoff Export (Future):**

Export brand schema for external AI tools (ChatGPT, Claude web, etc.) - copy to clipboard, paste to AI, get filled data back.

| Mode | What It Exports | Use Case |
|------|-----------------|----------|
| **Empty schema** | Section structure with field descriptions | "Fill in this brand section for [company]" |
| **Current + gaps** | Filled fields + empty placeholders | "Complete the missing fields" |
| **Single section** | Just one section schema | Quick per-section generation |
| **Full brand** | All sections in one prompt | Bulk fill from existing docs |

```typescript
// lib/server/exporters/ai-handoff-exporter.ts

interface AIHandoffOptions {
  mode: 'empty' | 'partial' | 'full';
  sections?: string[];           // Specific sections, or all
  includeInstructions?: boolean; // Add "fill in the blanks" prompt
  format: 'markdown' | 'json';   // MD for chat, JSON for structured
  copyToClipboard?: boolean;     // Direct clipboard copy
}

// Returns formatted text ready for AI
function generateAIHandoff(brandPath: string, options: AIHandoffOptions): string;

// Usage
const handoff = generateAIHandoff('/brands/acme', {
  mode: 'empty',
  sections: ['core/identity', 'voice/tone'],
  includeInstructions: true,
  format: 'markdown'
});
// → Copies to clipboard, ready to paste into ChatGPT/Claude
```

**Output example (markdown mode):**
```markdown
# Brand Section: Core Identity

Fill in the following fields for [Brand Name]:

## Required Fields
- **brandName**: The official brand name
- **tagline**: Short memorable phrase (max 10 words)
- **missionStatement**: Why the brand exists

## Optional Fields
- **foundedYear**: Year established
- **founders**: List of founder names
```

**When to implement:**
- YAML: When developers request config-friendly export format
- ZIP: When users want single-file download of all formats
- Export diff: When users need to track changes between exports
- Streaming: When brands become large enough to cause memory issues
- PDF fallbacks: When users without puppeteer need PDF output
- AI handoff: When users want to use external AI for bulk content generation
