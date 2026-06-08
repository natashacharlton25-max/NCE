# BrandKit Workflow - Documentation Index

> Central guide for navigating documentation during development.
> Read this FIRST before starting any phase.

---

## Quick Reference

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [PLAN-OVERVIEW.md](PLAN-OVERVIEW.md) | System architecture | Understanding the big picture |
| [BUILD-CHECKLIST.md](Build%20Lists%20Detailed/BUILD-CHECKLIST.md) | Task tracking | During and after each task |
| [PROJECT-INVENTORY.md](Build%20Lists%20Detailed/PROJECT-INVENTORY.md) | File/folder reference | When creating or finding files |
| [INTEGRATION-TESTS.md](Build%20Lists%20Detailed/INTEGRATION-TESTS.md) | Test cases | After completing a phase |
| [EXTENSIBILITY-GUIDE.md](EXTENSIBILITY-GUIDE.md) | How to extend | When adding new features |
| [LIBRARIAN-APP.md](Build%20Lists%20Detailed/LIBRARIAN-APP.md) | Future v2 library features | Planning smart library features |
| Phase plans (PHASE-X-PLAN.md) | Detailed implementation | Before starting a phase |

---

## Before Starting a Phase

### 1. Read These Documents (in order)

1. **Phase Plan** - `DOCS/Build Plans/PHASE-{N}-PLAN.md`
   - Understand objectives, tasks, and dependencies
   - Check "Existing Configuration" section for what already exists
   - Review "Files to Create/Modify" list

2. **BUILD-CHECKLIST.md** - `DOCS/Build Lists Detailed/`
   - Find the phase section (e.g., "## Phase 4: AI Generation")
   - Review all tasks and their current status
   - Check previous phase notes for context

3. **PROJECT-INVENTORY.md** - `DOCS/Build Lists Detailed/`
   - Verify folder structure matches expectations
   - Check what files already exist

### 2. Check Dependencies

- Verify previous phase is marked complete in BUILD-CHECKLIST.md
- Run any existing tests to ensure nothing is broken
- Check if required config files exist

---

## During Development

### Task Completion Workflow

For each task completed:

1. **Mark task done** in BUILD-CHECKLIST.md
   - Change `[ ]` to `[x]`
   - Add notes in the Notes column if needed

2. **Update PROJECT-INVENTORY.md** if you:
   - Created new files
   - Created new folders
   - Changed folder structure

3. **Add to INTEGRATION-TESTS.md** if you:
   - Created testable functionality
   - Found edge cases worth documenting

---

## After Completing a Sub-Phase (e.g., 4.1, 4.2)

### Checklist

- [ ] All tasks marked `[x]` in BUILD-CHECKLIST.md
- [ ] Build Notes section filled in with:
  - Completion date
  - Location of new files
  - Key decisions made
  - Usage examples (if applicable)
- [ ] Files created/modified listed
- [ ] PROJECT-INVENTORY.md updated with new files

---

## After Completing a Full Phase (e.g., Phase 4)

### 1. Update BUILD-CHECKLIST.md

**Progress Summary table:**
```markdown
| Phase 4: AI Generation | Complete | 100% | All sub-phases done |
```

**Session Log:**
```markdown
| 2026-01-XX | 4.1 | Task description | Next steps | Blockers |
```

### 2. Update PROJECT-INVENTORY.md

**Build Progress table:**
```markdown
| BrandKit Workflow | Phase 4 Complete | Phases 1-4 done: ... |
```

**Last updated line:**
```markdown
*Last updated: 2026-01-XX - Phase 4 Complete*
```

### 3. Update Phase Plan

In `DOCS/Build Plans/PHASE-{N}-PLAN.md`:
- Mark all task checkboxes as `[x]`
- Add "Build Notes" section if not present
- Document any deviations from plan

### 4. Run Integration Tests

Check `DOCS/Build Lists Detailed/INTEGRATION-TESTS.md`:
- Run all tests for the completed phase
- Document results
- Add new test cases discovered during development

### 5. Review for Improvements

Before moving to next phase, consider:
- Are there any TODOs or FIXMEs left in code?
- Any config files that should be synced?
- Any schemas that should be added?
- Any round-trip or integration tests to add?

Document recommendations in BUILD-CHECKLIST.md under "Notes & Decisions".

---

## Document Locations

```
BrandKit Workflow/DOCS/
├── DOC-INDEX.md              # THIS FILE - read first
├── PLAN-OVERVIEW.md          # System architecture
├── EXTENSIBILITY-GUIDE.md    # How to extend
├── Build Plans/              # Phase implementation plans
│   ├── PHASE-1-PLAN.md
│   ├── PHASE-2-PLAN.md
│   ├── PHASE-3-PLAN.md
│   ├── PHASE-4-PLAN.md
│   ├── PHASE-5-PLAN.md       # Updated with 5A/5B architecture
│   ├── ...
│   └── PHASE-10-PLAN.md
└── Build Lists Detailed/     # Tracking and reference
    ├── BUILD-CHECKLIST.md    # Task tracking (UPDATE OFTEN)
    ├── PROJECT-INVENTORY.md  # File/folder reference
    ├── INTEGRATION-TESTS.md  # Test cases
    └── LIBRARIAN-APP.md      # Future v2 library features (separate app)
```

---

## Config File Locations

| Config | Location | Purpose |
|--------|----------|---------|
| global.json | `_config/` | Central paths and defaults |
| schemas/*.json | `_config/schemas/` | JSON validation schemas |
| section-models.json | `_config/` | Section-to-model AI mapping |
| domain-detection.json | `BrandKit AIGen/_config/` | Domain detection keywords |
| domains.json | `BrandLib/_config/` | Domain library mappings |
| generation-order.json | `BrandKit AIGen/_config/` | Section generation sequence |
| templates.json | `FolderCreator/_config/` | Brand folder structure |
| services.json | `APIKeyManager/_config/` | Supported API services |
| .env | `BrandKit Workflow/` | API keys (AI providers, COMPANIES_HOUSE, image APIs) |

---

## Common Tasks Reference

### Adding a New Domain

1. Add to `BrandKit AIGen/_config/domain-detection.json` (keywords)
2. Add to `BrandLib/_config/domains.json` (libraries)
3. Add to `FolderCreator/src/utils/validators.ts` (KNOWN_DOMAINS)
4. Update fallback_options in domain-detection.json
5. Document in BUILD-CHECKLIST.md Notes & Decisions

### Adding a New Section

1. Create schema in `BrandKit/_system/{category}/{section}/default/`
2. Create AI config in `BrandKit AIGen/sections/{category}--{section}.json`
3. Add to `BrandKit AIGen/_config/generation-order.json`
4. Update progress.json template in `FolderCreator/_config/templates.json`

### Adding a New Library

1. Create library file in `BrandLib/{domain}/{library}.json`
2. Add to domain's libraries array in `BrandLib/_config/domains.json`
3. Add field mapping in `BrandLib/_config/field-mappings.json`

### Configuring AI Providers

1. Add API key to `.env` file:
   - `GROQ_API_KEY` - Groq (llama-3.3-70b-versatile) - fastest, recommended
   - `OPENAI_API_KEY` - OpenAI (gpt-4o)
   - `GOOGLE_AI_API_KEY` - Google AI (gemini-2.5-flash)
   - `OPENROUTER_API_KEY` - OpenRouter (various free models)
2. Provider configs in `lib/server/types/ai-client.ts`
3. Test with `npx tsx scripts/test-ai.ts`

### Running Phase 4 Tests

Phase 4 components have dedicated test scripts:

```bash
# Test AI connection and model comparison
npx tsx scripts/test-ai.ts

# Test core sections handler (validation, generation)
npx tsx scripts/test-core-sections.ts

# Test section router (pathways, dependencies)
npx tsx scripts/test-section-router.ts

# Test response processor (parsing, validation, inferred)
npx tsx scripts/test-response-processor.ts
```

### Using Phase 4 Functions

```typescript
import {
  // Prompt building (4.1)
  buildPrompt,

  // Section save/load (4.2)
  saveToDraft, loadFromDraft, approveSection,

  // AI calls (4.3)
  callAI, generateSection, generateAndSaveToDraft,

  // Core sections (4.3b)
  validateCoreSection, generateCoreSections, getCoreStatus,

  // Routing (4.4)
  getSectionPathway, canGenerateSection, getSectionsReadyForGeneration,

  // Response processing (4.5)
  parseJSON, processResponse, extractInferredFields,

  // Model selection
  getModelForSection
} from '$lib/server';
```

### Prompt Templates

Prompt templates are stored per-section in `BrandKit AIGen/sections/`:
- `{category}--{section}.json` (e.g., `core--identity.json`)
- Contains: `prompt.role`, `prompt.rules`, `prompt.instructions`
- Also includes: `context_sources`, `fields` with extraction hints
- Loaded by `prompt-builder.ts:loadSectionConfig()`

### Section-to-Model Mapping

Configure which AI models to use for each section in `_config/section-models.json`:

```json
{
  "defaults": {
    "ai": "groq/llama-3.3-70b-versatile",
    "visual": "openai/gpt-4o",
    "legal": "openai/gpt-4o"
  },
  "overrides": {
    "voice/vocabulary": "openai/gpt-4o"
  }
}
```

Use `getModelForSection(sectionId)` to get the recommended model.

### Batch Generation

Generate multiple sections in dependency order:

```bash
# Generate all available sections for a brand
npx tsx scripts/batch-generate.ts test-brand

# Generate only tier 1 (foundational) sections
npx tsx scripts/batch-generate.ts test-brand --tier 1

# Generate only AI pathway sections
npx tsx scripts/batch-generate.ts test-brand --pathway ai

# Dry run to see what would be generated
npx tsx scripts/batch-generate.ts test-brand --dry-run

# Generate and auto-approve
npx tsx scripts/batch-generate.ts test-brand --auto-approve
```

### Running Phase 5 Tests

Phase 5 components have dedicated test scripts:

```bash
# Test library matching (5A) - 21 tests
npx tsx scripts/test-library-matcher.ts

# Test library creator/editor (5B) - 28 tests
npx tsx scripts/test-library-creator.ts

# Test Phase 5 improvements (usage tracking, Levenshtein, health check)
npx tsx scripts/test-library-improvements.ts
```

### Running Phase 6 Tests

Phase 6 (Legal Pathway) test script:

```bash
# Test all Phase 6 functionality - 54 tests
npx tsx scripts/test-legal.ts
```

Covers:
- Companies House API (search, details, caching)
- Legal templates (cookie, privacy, GDPR, terms, footer)
- Domain disclaimers (therapeutic, ecommerce, professional)
- Placeholder system (replacement, conditionals, validation)

### Using Phase 5 Functions

```typescript
import {
  // Library Matching (5A.1)
  matchFieldsToLibrary, getAvailableLibraries, loadLibraryEntries,
  generateMatchHash, getMatchIntent, calculateWeightedConfidence,

  // Library Linking (5A.2)
  saveLinkSelection, getLinkedEntries, getLinkStats,

  // Library Browsing (5A.3)
  browseLibrary, browseLibraryWithDetails, fuzzySearchLibrary,

  // Library Creation (5B.1)
  createLibraryEntry, validateEntry, generateEntryId,
  getEntryTemplate, getWeightDefaults,

  // Library Editing (5B.2)
  updateEntryWeight, boostEntryWeight, reduceEntryWeight,
  editLibraryEntry, promoteEntrySource, deleteLibraryEntry,

  // Weight Constants
  DEFAULT_ENTRY_WEIGHTS, getDefaultWeight, WEIGHT_BOUNDS,

  // Phase 5 Improvements: Usage Tracking
  recordEntryUsage, saveLinkWithUsageTracking,

  // Phase 5 Improvements: Bulk Operations
  bulkLinkFields, bulkUnlinkFields, getLinkedEntriesForFields,

  // Phase 5 Improvements: Levenshtein Fuzzy Search
  levenshteinDistance, levenshteinSimilarity, levenshteinSearchLibrary,

  // Phase 5 Improvements: Library Health Check
  checkLibraryHealth, checkDomainLibrariesHealth,

  // Phase 5 Improvements: Entry Usage Stats
  getEntryUsageStats, getMostUsedEntries, getUnusedEntries, getLibraryUsageSummary
} from '$lib/server';
```

### Entry Weighting System

Entries have weight (0-1) based on their source:

| Weight | Source | Description |
|--------|--------|-------------|
| 1.0 | `curated` | Original library content |
| 0.8 | `contributed` | User-added, reviewed |
| 0.6 | `brand_specific` | From a specific brand |
| 0.5 | `suggested` | AI-generated |

**Confidence formula:** `finalConfidence = matchScore × entryWeight`

### Using Phase 6 Functions

```typescript
import {
  // Companies House (6.1)
  searchCompanies, getCompanyDetails, getCompaniesHouseUrl,
  padCompanyNumber, saveBusinessDetails, loadBusinessDetails,
  createManualBusinessDetails, clearCompanyCache,
  getCompanyTypeLabel, getCompanyStatusLabel,

  // Legal Templates (6.2)
  generateLegalContent, getAvailableTemplates, getTemplateInfo,
  saveLegalContent, loadLegalContent,

  // Disclaimers (6.3)
  getDisclaimersForDomain, getDisclaimersByPlacement,
  getDisclaimer, getAllDisclaimerTypes,
  generateDisclaimersPage, getFooterDisclaimers,

  // Placeholders (6.4)
  loadPlaceholderValues, replacePlaceholders,
  processConditionals, findMissingPlaceholders,
  validateTemplatePlaceholders, formatAddressSingleLine,

  // Types
  type LegalTemplateType, type DisclaimerType,
  type CompanyDetails, type StoredBusinessDetails
} from '$lib/server';
```

### Legal Template Types

| Template | Description |
|----------|-------------|
| `cookie_policy` | What cookies are used, consent info |
| `privacy_policy` | GDPR-compliant data handling |
| `gdpr_notice` | Data controller, lawful basis, rights |
| `privacy_notice` | Short form - how data is processed |
| `terms_of_service` | Website usage rules |
| `footer_legal` | Company registration statement |

### Domain Disclaimers

| Domain | Included Disclaimers |
|--------|---------------------|
| Universal | copyright, affiliate, testimonial, general_liability |
| Therapeutic | not_medical_advice, not_therapy, crisis_resources, confidentiality |
| Ecommerce | shipping, returns, pricing, product_accuracy |
| Professional | results_vary, not_legal_advice, not_financial_advice, liability_limit |

---

## Phase 7: Visual Pathway (Complete)

Phase 7 handles visual sections (colours, typography, logos, photography) separately from AI-generated text sections.

### Phase 7 Structure

| Sub-Phase | Focus | Source | Status |
|-----------|-------|--------|--------|
| **7A** | CSS Token Parser | Port from Empire v3.1 | ✅ Complete (73 tests) |
| **7B** | Visual Router + Handlers | New code | ✅ Complete (61 tests) |
| **7C** | Utility App Integrations | New code | ✅ Complete (65 tests) |

### Running Phase 7 Tests

```bash
# Test CSS token parser (7A) - 73 tests
npx tsx scripts/test-css-parser.ts

# Test Visual Router + Handlers (7B) - 61 tests
npx tsx scripts/test-visual.ts

# Test Utility App Integrations (7C) - 65 tests
npx tsx scripts/test-integrations.ts
```

### Using Phase 7A Functions (CSS Parsing)

```typescript
import {
  // CSS parsing
  parseCSSTokens, parseCSSColorTokens, parseCSSFontTokens,
  groupColorsByFamily, extractBrandPalette,

  // Color utilities
  detectColorFormat, convertToHex, inferColorRole,

  // Accessibility
  calculateContrastRatio, checkWCAGCompliance, checkColorAccessibility,

  // Types
  type ParsedColor, type ParsedFont, type ColorFamily,
  type CSSParseResult, type BrandPalette
} from '$lib/server';

// Parse CSS file
const result = parseCSSTokens(cssContent);
console.log(result.colors);        // Parsed colors with roles
console.log(result.colorFamilies); // Grouped by family (Primary-100, 500, 900)
console.log(result.fonts);         // Parsed fonts with sources

// Check accessibility
const a11y = checkColorAccessibility('#000000', '#FFFFFF');
console.log(a11y.normalText.aa);   // true (passes WCAG AA)
```

### Phase 7A.4: CSS Parser Enhancements ✅

Extended parsers for SCSS, CSS-in-JS, and Design Token formats:

```typescript
import {
  // SCSS parsing
  parseSCSSVariables,    // Parse $variable: value; syntax

  // CSS-in-JS parsing
  parseCSSInJS,          // Parse styled-components, emotion themes

  // Design Token parsing
  parseDesignTokens,     // Parse Style Dictionary JSON

  // Auto-detection
  parseAnyFormat,        // Auto-detect and route to correct parser
  detectFormat,          // Detect format without parsing

  // Types
  type DesignToken,
  type DesignTokenParseResult,
  type DetectedFormat
} from '$lib/server';

// Parse SCSS variables
const scss = parseSCSSVariables(`
  $primary-color: #8FA68A;
  $font-heading: 'Playfair Display', serif;
  $secondary: $primary-color; // Warns: unresolved reference
`);
console.log(scss.colors[0].format);  // 'scss-variable'
console.log(scss.fonts[0].source);   // 'scss'

// Parse CSS-in-JS theme
const jsTheme = parseCSSInJS(`
  export const PRIMARY = '#8FA68A';
  const colors = { primary: '#8FA68A', accent: 'rgb(200, 150, 100)' };
`);
console.log(jsTheme.colors[0].format);  // 'js-object'

// Parse Design Tokens (Style Dictionary / DTCG format)
const tokens = parseDesignTokens(JSON.stringify({
  colors: {
    primary: { '500': { value: '#8FA68A' } },
    semantic: { brand: { value: '{colors.primary.500}' } }  // Reference warning
  },
  font: { family: { heading: { $value: 'Playfair Display' } } }
}));
console.log(tokens.tokens[0].path);  // ['colors', 'primary', '500']
console.log(tokens.colors[0].shade); // 500
console.log(tokens.meta.sourceFormat); // 'style-dictionary'

// Auto-detect format and parse
const auto = parseAnyFormat(content, 'theme.scss');  // Routes to SCSS parser
const format = detectFormat(content);  // 'css' | 'scss' | 'css-in-js' | 'design-tokens'
```

**Test Coverage:** 104 tests (73 original + 31 new)

### Using Phase 7B Functions (Visual Router + Handlers)

```typescript
import {
  // Visual Router (7B.1)
  routeVisualSection, checkAvailableSources, isVisualSection,
  getVisualSectionsReady, getNextVisualSection,

  // Logo Handler (7B.2)
  uploadLogo, getLogoStatus, hasMainLogo, getMissingVariants,
  requestLogoGeneration, getLogoSectionData,

  // Photography Handler (7B.3)
  getDefaultPhotographyStyle, getPhotographyStyleForBrand,
  uploadPhotographyImage, launchImageBrowser,
  saveSelectedImages, getPhotographySectionData,

  // Guidelines Generator (7B.4)
  generateColorGuidelines, generateTypographyGuidelines,
  generateLogoGuidelines, generatePhotographyGuidelines,
  generateAllVisualGuidelines,

  // Types
  type VisualSectionId, type VisualRouteDecision,
  type LogoStatus, type PhotographyStyle
} from '$lib/server';

// Route a visual section
const decision = await routeVisualSection({
  brandPath: '/path/to/brand',
  section: 'visual/colours'
});
console.log(decision.handler);           // 'css_parser' or 'builder_app'
console.log(decision.recommendedSource); // Best input source

// Get logo status
const logoStatus = await getLogoStatus(brandPath);
console.log(logoStatus.hasMain);         // true if main logo uploaded
console.log(logoStatus.missingVariants); // ['transparent', 'favicon', ...]

// Generate color guidelines with accessibility
const guidelines = await generateColorGuidelines(brandPath, colors, families);
console.log(guidelines.accessibility.passingCombinations); // WCAG-compliant pairs
console.log(guidelines.colorPsychology);                   // Domain-based notes
```

### Using Phase 7C Functions (Utility App Integrations)

```typescript
import {
  // ColourToken Integration (7C.1)
  launchColourToken, handleColourTokenComplete, isColourTokenComplete,
  convertToColoursSection, saveColourTokenResult, generateCSSVariables,
  generateTailwindConfig, cleanupColourToken,

  // TypographyBuilder Integration (7C.2)
  launchTypographyBuilder, handleTypographyBuilderComplete,
  isTypographyBuilderComplete, convertToTypographySection,
  saveTypographyBuilderResult, generateGoogleFontsImportUrl,
  cleanupTypographyBuilder,

  // LogoMaker Integration (7C.3)
  launchLogoMaker, handleLogoMakerComplete, isLogoMakerComplete,
  convertToLogosSection, saveLogoMakerResult, loadLogoMakerResult,
  getExistingVariants, getMissingVariants, generateLogoPreviewHTML,

  // ImageBrowser Integration (7C.4)
  launchImageBrowser, handleImageBrowserComplete, isImageBrowserComplete,
  convertToPhotographySection, saveImageBrowserResult, loadImageBrowserResult,
  generateAttributionHTML, generateAttributionJSON,

  // Types
  type ColourTokenInput, type ColourTokenOutput,
  type TypographyBuilderInput, type TypographyBuilderOutput,
  type LogoMakerInput, type LogoMakerOutput,
  type ImageBrowserInput, type ImageBrowserOutput
} from '$lib/server';

// Launch ColourToken app for palette creation
const launch = await launchColourToken(brandPath, {
  mode: 'create',
  colours: { primary: '#8FA68A' }
});
console.log(launch.inputFile);  // Path to input JSON for app

// After app completes, handle the output
const output = await handleColourTokenComplete(brandPath);
const coloursSection = convertToColoursSection(output);
const cssVars = generateCSSVariables(output.palette, 'brand');
```

### Phase 7 APIs

| API | Purpose |
|-----|---------|
| Colour Pizza API | Get colour names, palette suggestions |
| Background Remover API | Auto-generate transparent logo variants |
| Google Fonts API | Font metadata for TypographyBuilder |
| Unsplash/Pexels API | Stock photo search for ImageBrowser |

### Visual Sections

| Section | Input Sources |
|---------|---------------|
| `visual/logos` | File upload, LogoMaker App |
| `visual/colours` | CSS upload, ColourToken App |
| `visual/typography` | CSS upload, TypographyBuilder App |
| `visual/photography` | Image upload, ImageBrowser App |
| `visual/graphics` | Config UI |
| `visual/icons` | Config UI (Phosphor icons - 1,512 available) |

### Logo Variants

| Variant | Required | Description |
|---------|----------|-------------|
| `main` | Yes | Primary logo in full colour |
| `transparent` | Recommended | Logo with transparent background |
| `dark` | Recommended | Logo for dark backgrounds |
| `favicon` | Recommended | Browser tab icon |
| `light` | Optional | Logo for light backgrounds |
| `icon` | Optional | Square app icon |
| `wordmark` | Optional | Text-only version |

### Domain Photography Defaults

| Domain | Mood | Subjects |
|--------|------|----------|
| Therapeutic | calm, peaceful, authentic | people, nature, quiet spaces |
| Ecommerce | clean, professional, aspirational | products, lifestyle |
| Professional | professional, trustworthy, modern | people, office, teamwork |
| Creative | creative, bold, artistic | abstract, artistic compositions |

---

## Phase 8: Export System (Complete)

Phase 8 provides comprehensive export functionality for sharing brand guidelines in various formats.

### Phase 8 Structure

| Sub-Phase | Focus | Status |
|-----------|-------|--------|
| **8.1** | Export Router | ✅ Complete |
| **8.2** | JSON Export | ✅ Complete |
| **8.3** | Markdown Export | ✅ Complete |
| **8.4** | PDF Export | ✅ Complete |
| **8.5** | Prompt Pack Export | ✅ Complete |
| **8.6** | Export Manager | ✅ Complete |

**Test Script:** `scripts/test-export.ts` - 58 tests, 100% pass rate

**Puppeteer Check:** `isPuppeteerAvailable()` checks at runtime - PDF format only shown if puppeteer installed

### Export Formats

| Format | Use Case | Output |
|--------|----------|--------|
| **JSON** | API/developer use | `{brand}-brandkit.json` |
| **Markdown** | Documentation | `{brand}-guide.md` |
| **PDF** | Client sharing | `{brand}-guidelines.pdf` (or .html) |
| **Prompt Pack** | AI assistants | `{brand}-prompts/` folder |

### Using Phase 8 Functions

```typescript
import {
  // Export Router (8.1)
  checkExportAvailability, executeExport, exportFormat,
  getExportHistory, getLatestExport, cleanupOldExports,
  getExportSize, formatFileSize,
  isPuppeteerAvailable, clearPuppeteerCache,

  // JSON Exporter (8.2)
  generateJSONExport, parseJSONExport, getJSONExportSummary,

  // Markdown Exporter (8.3)
  generateMarkdownExport,

  // PDF Exporter (8.4)
  generatePDFExport,

  // Prompt Pack Exporter (8.5)
  generatePromptPackExport,

  // Export Manager (8.6)
  ExportManager, exportManager,
  quickExportAll, quickExport, getExportSummary, reexportLast,

  // Types
  type ExportFormat, type ExportOptions, type ExportResult,
  type MultiExportResult, type ExportRecord
} from '$lib/server';
```

### Quick Export Examples

```typescript
// Export all available formats
const result = await quickExportAll('/path/to/brand');

// Export specific formats
const result = await exportManager.export(brandPath, ['json', 'markdown'], {
  includeDrafts: false,
  includeLibraryRefs: true
});

// Export single format with options
await exportManager.exportJSON(brandPath, { prettyPrint: true });
await exportManager.exportMarkdown(brandPath, { includeTableOfContents: true });
await exportManager.exportPDF(brandPath, { template: 'modern' });
await exportManager.exportPromptPack(brandPath, { targetAI: 'claude' });

// Check what can be exported
const availability = await checkExportAvailability(brandPath);
console.log(availability.availableFormats);  // ['json', 'markdown', 'pdf', 'prompt_pack']
console.log(availability.completedSections); // Sections ready to export

// Get export summary for a brand
const summary = await exportManager.getSummary(brandPath);
console.log(summary.totalExports);      // Number of exports
console.log(summary.totalSizeFormatted); // '1.5 MB'
console.log(summary.latestFiles);        // Most recent export files

// Re-export using last settings
await exportManager.reexport(brandPath);

// Batch export multiple brands
const batchResult = await exportManager.batchExport({
  brandPaths: ['/brand1', '/brand2'],
  formats: ['json', 'markdown']
});

// Cleanup old exports (keep latest 5)
await exportManager.cleanup(brandPath);
```

### Export Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `includeAllCompleted` | boolean | true | Include all completed sections |
| `includeDrafts` | boolean | false | Include draft sections (marked as DRAFT) |
| `includeLibraryRefs` | boolean | false | Include library reference data |
| `sectionsToInclude` | string[] | - | Specific sections only |
| `outputPath` | string | - | Override default exports folder |
| `trigger` | string | 'manual' | Export trigger type |

### PDF Templates

| Template | Style |
|----------|-------|
| `modern` | Clean, Inter font, accent borders |
| `classic` | Elegant, Georgia font, traditional |
| `minimal` | Simple, Helvetica, minimal styling |

### Prompt Pack Contents

| File | Purpose |
|------|---------|
| `system-prompt.md` | Full AI assistant system prompt |
| `voice-guide.md` | Voice & tone reference |
| `quick-reference.md` | Key points summary |
| `writing-checklist.md` | Content review checklist |
| `example-prompts.md` | Content type-specific prompts |

### Export History

Exports are tracked in `brand/exports/_history.json`:

```json
{
  "exports": [
    {
      "id": "exp_001",
      "date": "2026-01-16T10:30:00Z",
      "formats": ["json", "markdown"],
      "files": [...],
      "sections_included": ["core/identity", "voice/tone", ...],
      "trigger": "manual",
      "success": true
    }
  ],
  "latest": "exp_001",
  "totalExports": 1
}
```

---

## Troubleshooting

### "File not found" errors
- Check PROJECT-INVENTORY.md for correct paths
- Verify global.json has correct paths

### "Type errors" in TypeScript
- Run `npx tsc --noEmit` to check compilation
- Check lib/server/types/ for type definitions

### Tests failing
- Check INTEGRATION-TESTS.md for expected behavior
- Verify test-brand exists in BrandData

---

*Last updated: 2026-01-16 - Phase 8 Complete (Export System - JSON, Markdown, PDF, Prompt Pack)*
