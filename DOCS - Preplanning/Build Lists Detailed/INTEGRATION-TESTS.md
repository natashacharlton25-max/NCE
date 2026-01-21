# BrandKit Workflow - Integration Tests

> Cross-phase verification tests that require multiple components to be complete.
> These tests validate that independently-built phases work together correctly.
> Updated: 2026-01-15 (Phase 6 Complete - 54 tests)
>
> **Quick Links:** [DOC-INDEX.md](../DOC-INDEX.md) | [BUILD-CHECKLIST.md](BUILD-CHECKLIST.md) | [PROJECT-INVENTORY.md](PROJECT-INVENTORY.md)

---

## How to Use

- Tests are grouped by the **primary phase** being tested
- Each test lists **dependencies** (phases/components that must be complete)
- Run these tests when all dependencies are ready
- `[ ]` = Not tested, `[x]` = Passed, `[!]` = Failed (add notes)

---

## Phase Status Summary

| Phase | Name | Backend Status | Tests | UI Dependency | Integration Status |
|-------|------|----------------|-------|---------------|-------------------|
| 1 | Foundation | **Complete** | - | Phase 10.2, 10.3, 10.10 | Awaiting UI |
| 2 | Input Gathering | **Complete** | - | Phase 10.4, 10.5 | Awaiting UI |
| 3 | Domain Detection | **Complete** | - | Phase 10.3 | Awaiting UI |
| 4 | AI Generation | **Complete** | - | Phase 10.5 | Awaiting UI |
| 5 | Library Matching | **Complete** | 71 | Phase 10.5 | Awaiting UI |
| 6 | Legal Pathway | **Complete** | 54 | Phase 10.7 | Awaiting UI |
| 7 | Visual Pathway | Not started | - | Phase 10.8 | - |
| 8 | Export | Not started | - | Phase 10.9 | - |
| 9 | Integrations | Not started | - | Phase 10.10, 10.11 | - |
| 10 | UI/App | Not started | - | N/A | - |

---

## Phase 1: Foundation

**Backend Status:** Complete (2026-01-13)
**Components:** 1.1 Global Config, 1.2 Legal Restructuring, 1.3 Folder Creator, 1.4 API Key Manager, 1.5 Brand Status

### Unit Tests (Completed)

| Test | Status | Notes |
|------|--------|-------|
| Global config loads correctly | [x] | Tested via CLI |
| FolderCreator creates brand folder structure | [x] | Created test-brand successfully |
| APIKeyManager encrypts/decrypts keys | [x] | All operations verified |
| Brand status functions read/write correctly | [x] | Tested with test-brand |

### Integration Tests (Require Phase 10)

**1.3 Folder Creator + UI (Requires 10.3)**

| Test | Status | Notes |
|------|--------|-------|
| Create brand via wizard → folder created in BrandData | [ ] | Requires 10.3 brand creation flow |
| Brand appears in dashboard after creation | [ ] | Requires 10.2 dashboard + 10.3 |
| Invalid brand name shows validation error | [ ] | Requires 10.3 + validation UI |
| Region/domain selection persists to brand.json | [ ] | Requires 10.3 wizard |

**1.4 API Key Manager + UI (Requires 10.10)**

| Test | Status | Notes |
|------|--------|-------|
| Set API key via settings → stored encrypted | [ ] | Requires 10.10 settings |
| API key available for integrations | [ ] | Requires 10.10 + 9.x integrations |
| Delete API key works | [ ] | Requires 10.10 settings |
| Verify command checks all configured services | [ ] | Requires 10.10 settings |

**1.5 Brand Status + UI (Requires 10.6)**

| Test | Status | Notes |
|------|--------|-------|
| Section status updates reflect in navigator | [ ] | Requires 10.6 section navigator |
| Progress percentage calculates correctly | [ ] | Requires 10.6 section navigator |
| Core complete unlocks dependent sections | [ ] | Requires 10.6 section navigator |
| Brand status (active/archived) affects dashboard display | [ ] | Requires 10.2 dashboard |

---

## Phase 2: Input Gathering

**Backend Status:** Complete (2026-01-14)
**Components:** 2.1 File Upload Handler, 2.2 Website Fetcher, 2.3 Import Parsers, 2.4 Template Generator

### Unit Tests (Completed)

| Test | Status | Notes |
|------|--------|-------|
| handleUpload() stores files correctly | [x] | Tested via CLI |
| fetchWebsite() retrieves and parses content | [x] | Tested with example.com |
| parseFile() extracts text from JSON/CSV/Text | [x] | All formats verified |
| generateTemplate() produces valid output | [x] | All 4 formats tested |

### Integration Tests (Require Phase 10)

**2.1 File Upload Handler + UI (Requires 10.4)**

| Test | Status | Notes |
|------|--------|-------|
| Upload PDF → stored in `context/documents/`, entry in sources.json | [ ] | Requires 10.4 upload dropzone |
| Upload PNG → stored in `context/images/`, entry in sources.json | [ ] | Requires 10.4 upload dropzone |
| Upload CSS → stored in `context/visual/css/`, entry in sources.json | [ ] | Requires 10.4 upload dropzone |
| Reject invalid file type (e.g., .exe) → shows error toast | [ ] | Requires 10.4 + 10.12 toasts |
| Reject file exceeding size limit → shows error toast | [ ] | Requires 10.4 + 10.12 toasts |
| Duplicate filename generates unique name | [ ] | Requires 10.4 upload dropzone |
| Sources list displays all uploaded files | [ ] | Requires 10.4 sources list |

**2.2 Website Fetcher + UI (Requires 10.4)**

| Test | Status | Notes |
|------|--------|-------|
| Fetch simple website → markdown in `context/website/` | [ ] | Requires 10.4 URL input |
| Extract social links and contact info → displayed in UI | [ ] | Requires 10.4 sources list |
| Handle website with no about page gracefully | [ ] | Requires 10.4 URL input |
| Handle timeout/unreachable site gracefully → shows error | [ ] | Requires 10.4 + 10.12 toasts |
| sources.json updated with fetch metadata | [ ] | Requires 10.4 URL input |

**2.3 Import Parser + UI (Requires 10.4)**

| Test | Status | Notes |
|------|--------|-------|
| Parse PDF → extracted text saved, source marked processed | [ ] | Requires 10.4 + parsing trigger |
| Parse DOCX → extracted text saved, source marked processed | [ ] | Requires 10.4 + parsing trigger |
| Parse valid JSON → content extracted | [ ] | Requires 10.4 + parsing trigger |
| Parse CSV → content converted to readable format | [ ] | Requires 10.4 + parsing trigger |
| Invalid file returns error, doesn't crash → shows toast | [ ] | Requires 10.4 + 10.12 toasts |

**2.4 Template Generator + UI (Requires 10.5)**

| Test | Status | Notes |
|------|--------|-------|
| Generate markdown template → download works | [ ] | Requires 10.5 section editor |
| Generate JSON template → download works | [ ] | Requires 10.5 section editor |
| Generate CSV template → download works | [ ] | Requires 10.5 section editor |
| Generate text template → download works | [ ] | Requires 10.5 section editor |
| Round-trip: Download JSON template → fill → upload → parsed | [ ] | Requires 10.4 + 10.5 |

---

## Phase 3: Domain Detection

**Backend Status:** Complete (2026-01-14)
**Components:** 3.1 Domain Detector, 3.2 Domain Selector UI Support

### Unit Tests (Completed)

| Test | Status | Notes |
|------|--------|-------|
| Load domain keywords from config | [x] | Default config embedded, external config support |
| Content scanning returns matches | [x] | loadContextContent() reads website + documents |
| Keyword scoring calculates correctly | [x] | 3/2/1/-5 for strong/moderate/weak/anti |
| Confidence threshold triggers suggestions | [x] | 0.6 threshold, needsUserInput flag |
| Therapeutic content detected correctly | [x] | 81.6% confidence, score 39 |
| Ecommerce content detected correctly | [x] | 83.2% confidence, score 33 |
| Anti-keywords block false positives | [x] | Spa content: therapeutic score 0 |
| Ambiguous content requests user input | [x] | 37.3% confidence, needsUserInput: true |
| User selection saves to detection-result.json | [x] | Includes libraries_enabled list |

### Integration Tests

**3.1 Domain Detector + Phase 2 Sources**

| Test | Status | Notes |
|------|--------|-------|
| Scan uploaded documents for domain keywords | [x] | Reads from context/documents/*.txt |
| Scan fetched website content for domain keywords | [x] | Reads from context/website/fetched-content.md |
| Combined content scoring returns best match | [x] | Combines all sources, sorts by score |

**3.2 Domain Selector + UI (Requires 10.3)**

| Test | Status | Notes |
|------|--------|-------|
| Auto-detected domain shown in wizard | [ ] | Requires 10.3 brand creation flow |
| Manual domain override persists to brand.json | [ ] | Requires 10.3 wizard |
| Low confidence shows fallback options | [ ] | Requires 10.3 + getFallbackOptions() |

---

## Phase 4: AI Generation

**Backend Status:** Complete (2026-01-14)
**Components:** 4.1 AI Prompt Builder, 4.2 Section Save/Load, 4.3 AI Client, 4.3b Core Sections Handler, 4.4 Section Router, 4.5 Response Processor

### Unit Tests (Completed)

**4.1 AI Prompt Builder**

| Test | Status | Notes |
|------|--------|-------|
| Load section schema for prompt | [x] | 50 sections discovered |
| Load section instructions for prompt | [x] | From BrandKit AIGen sections/ |
| Load context content (sources) | [x] | Website + documents + completed sections |
| Assemble complete prompt | [x] | ~1180 tokens for core/identity |
| Token estimation accurate | [x] | ~4 chars per token |

**4.2 Section Save/Load Utilities**

| Test | Status | Notes |
|------|--------|-------|
| Save to draft creates folder structure | [x] | draft/{category}/{section}/ |
| Load from draft returns correct data | [x] | data.json + metadata.json |
| Approve moves draft to brandkit | [x] | Updates progress.json |
| Edit copies brandkit to draft | [x] | For re-editing approved sections |
| Core complete triggers correctly | [x] | When all 3 core sections approved |

**4.3 AI Client**

| Test | Status | Notes |
|------|--------|-------|
| Groq API connection works | [x] | llama-3.3-70b-versatile, 0.9s |
| OpenAI API connection works | [x] | gpt-4o, 2.6s |
| Google AI connection works | [x] | gemini-2.5-flash |
| OpenRouter connection works | [x] | Various free models |
| JSON mode structured output | [x] | response_format: json_object |
| generateSection() full flow | [x] | Prompt → AI → Parse → Return |

**4.3b Core Sections Handler**

| Test | Status | Notes |
|------|--------|-------|
| Validate core/identity (brand_name required) | [x] | Returns errors for missing |
| Validate core/purpose (mission_statement required) | [x] | Returns errors for missing |
| Validate core/contact (website_url required) | [x] | Returns errors for missing |
| Detect [INFERRED] markers | [x] | Sets confidence to 0.5 |
| getCoreStatus returns correct status | [x] | not_started, in_progress, complete |
| getCoreSectionsNeeded identifies gaps | [x] | needsGeneration, needsValidation, needsApproval |

**4.4 Section Router**

| Test | Status | Notes |
|------|--------|-------|
| Route AI sections correctly | [x] | 35 sections |
| Route visual sections correctly | [x] | 10 sections |
| Route legal sections correctly | [x] | 4 sections |
| Check dependencies before generation | [x] | canGenerateSection() |
| Get sections ready for generation | [x] | getSectionsReadyForGeneration() |
| Progress tracking by pathway | [x] | getGenerationProgress() |
| Progress tracking by tier | [x] | 5 tiers |
| Transitive dependency resolution | [x] | getAllDependencies() |

**4.5 Response Processor**

| Test | Status | Notes |
|------|--------|-------|
| Parse direct JSON | [x] | extractMethod: 'direct' |
| Parse markdown code blocks | [x] | extractMethod: 'markdown_block' |
| Extract JSON from text | [x] | extractMethod: 'object_extract' |
| Repair trailing commas | [x] | extractMethod: 'repair' |
| Schema validation (valid data) | [x] | Returns valid: true |
| Schema validation (missing required) | [x] | Returns errors array |
| Extract [INFERRED] markers | [x] | Counts, cleans, extracts reasons |
| Extract [INFERRED: reason] format | [x] | Parses reason from marker |
| Analyze partialness | [x] | completeness %, missing fields |
| Full processResponse() pipeline | [x] | Confidence scoring |

### Integration Tests

**4.1 AI Prompt Builder + Phase 2 Sources**

| Test | Status | Notes |
|------|--------|-------|
| Parsed document content included in prompt | [x] | gatherContext() reads .txt files |
| Website content included in prompt | [x] | Reads fetched-content.md |
| Previously completed sections included | [x] | loadCompletedSections() |

**4.4 Section Router + Phase 6/7 (Pathway Routing)**

| Test | Status | Notes |
|------|--------|-------|
| Visual sections route to Phase 7 pathway | [x] | isVisualSection() returns true |
| Legal sections route to Phase 6 pathway | [x] | isLegalSection() returns true |
| Pathway categories configurable | [x] | PATHWAY_CATEGORIES constant |

**4.2/4.3 Section Save/Load + AI Generation**

| Test | Status | Notes |
|------|--------|-------|
| Generate + save to draft in one call | [x] | generateAndSaveToDraft() |
| Draft data survives reload | [x] | loadFromDraft() after save |
| Approval updates progress.json | [x] | status: 'complete' |

**4.x Full Pipeline Test**

| Test | Status | Notes |
|------|--------|-------|
| buildPrompt → callAI → parseJSON → validate | [x] | End-to-end generation |
| Context from Phase 2 → AI generation | [x] | Sources included in prompt |
| Inferred markers → cleaned data | [x] | extractInferredFields() |

**4.x Enhancement Features**

| Test | Status | Notes |
|------|--------|-------|
| npm run test:all executes all tests | [x] | package.json scripts |
| getModelForSection() returns model for section | [x] | Uses section-models.json |
| getModelForSection() returns pathway default | [x] | Falls back by pathway |
| saveToDraft() validates against schema | [x] | Returns validationErrors |
| saveToDraft() with skipValidation bypasses | [x] | skipValidation: true |
| approveSection() validates before approval | [x] | Re-validates draft data |
| batch-generate.ts --dry-run shows sections | [x] | No AI calls made |
| batch-generate.ts respects --tier filter | [x] | Filters by tier 1-5 |
| batch-generate.ts respects --pathway filter | [x] | ai/visual/legal |

> **See Also:** [DOC-INDEX.md](../DOC-INDEX.md#batch-generation) for batch generation usage

### Integration Tests (Require Phase 10 UI)

**4.x Section Editor + UI (Requires 10.5)**

| Test | Status | Notes |
|------|--------|-------|
| Save to draft persists correctly | [ ] | Requires 10.5 section editor |
| Approve moves draft to brandkit | [ ] | Requires 10.5 save/approve buttons |
| Edit approved section copies to draft | [ ] | Requires 10.5 edit button |
| AI generation triggered from UI | [ ] | Requires 10.5 generate button |
| Inferred fields highlighted in editor | [ ] | Requires 10.5 field renderer |
| Response confidence shown | [ ] | Requires 10.5 UI indicator |

---

## Phase 5: Library Matching

**Backend Status:** Complete (2026-01-15) - 71 tests total
**Components:** 5A.1 Library Matcher, 5A.2 Library Linker, 5A.3 Library Browser | 5B.1 Creator, 5B.2 Editor, 5B.3 Matcher Update | 5.3 Improvements

### Unit Tests (Phase 5A Completed)

**5A.1 Library Matcher**

| Test | Status | Notes |
|------|--------|-------|
| Load libraries by domain | [x] | getAvailableLibraries() - 9 for therapeutic |
| Load universal libraries | [x] | Included via includes_universal flag |
| Load field mappings | [x] | getFieldMappings() from field-mappings.json |
| Match field values to entries | [x] | matchFieldsToLibrary() with 4 algorithms |
| Score matches correctly | [x] | semantic, keyword, category, exact |
| Negative scoring for 'avoid' terms | [x] | Penalty from entry.avoid array |
| Generate match hash | [x] | SHA256 for idempotency |
| Get match intent from confidence | [x] | auto_link ≥0.95, suggest ≥0.70, manual_only |
| Library caching (5min TTL) | [x] | In-memory cache, 0ms on cache hit |

**5A.2 Library Linker**

| Test | Status | Notes |
|------|--------|-------|
| Save link selection | [x] | saveLinkSelection() to brand's libraries/index.json |
| Track history on replacement | [x] | previous_entry_id tracked |
| Support linked_by types | [x] | user_selected, ai_matched, user_agreed |
| Get link statistics | [x] | getLinkStats() returns counts by library/type |

**5A.3 Library Browser**

| Test | Status | Notes |
|------|--------|-------|
| Browse with pagination | [x] | Page 1/10, 49 entries |
| Filter by search | [x] | Found 2 for "warm" |
| Filter by domain | [x] | Universal + domain-specific |
| Fuzzy search | [x] | Found 3 for "friendly supportive" |
| Get categories | [x] | 176 categories in tone library |
| Get related entries | [x] | 3 related to "warm" tone |

### Integration Tests

**5A.1 Library Matcher + Phase 3 Domain**

| Test | Status | Notes |
|------|--------|-------|
| Detected domain loads correct library set | [x] | therapeutic = 9 libraries |
| Universal libraries always loaded | [x] | Included via includes_universal |
| Domain-specific libraries added | [x] | vocabulary, content-warnings, etc. |

**5A.1 Library Matcher + Phase 4 AI Generation**

| Test | Status | Notes |
|------|--------|-------|
| AI-generated content matched to library | [x] | "warm and friendly" → Warm (50%) |
| Match includes confidence score | [x] | 0-1 confidence with intent |
| Match includes hash for idempotency | [x] | 16-char hex hash |

**5A.2 Library Linker + Brand Index**

| Test | Status | Notes |
|------|--------|-------|
| Selection saved to brand index | [x] | libraries/index.json created |
| Multiple selections tracked | [x] | Each field has own entry |
| Link stats calculated correctly | [x] | byLibrary, byLinkedBy counts |

**5B.x Library Creator + Editor - Weighting System (Complete)**

> Architecture revised 2026-01-15: Single library with weight/source instead of brand-local → promote

| Test | Status | Notes |
|------|--------|-------|
| LibraryEntry type has weight/source | [x] | EntrySource type + weight fields added |
| Create entry with weight 0.5 (suggested) | [x] | getDefaultWeight('suggested') = 0.5 |
| Create entry with weight 0.8 (contributed) | [x] | getDefaultWeight('contributed') = 0.8 |
| Validate entry schema | [x] | ID format, required fields, source validation |
| Update entry weight | [x] | updateEntryWeight() with bounds 0.1-1.0 |
| Edit entry content | [x] | editLibraryEntry() with protected fields |
| Matcher factors weight into confidence | [x] | calculateWeightedConfidence() = matchScore × weight |
| Higher-weight entries ranked first | [x] | Sorting by confidence includes weight factor |

**5.3 Library Improvements (Complete)**

> Phase 5.3 adds: Usage tracking, entry relationships, bulk operations, Levenshtein fuzzy search, health check, usage stats

| Test | Status | Notes |
|------|--------|-------|
| Record entry usage | [x] | recordEntryUsage() updates usage_count, last_used |
| Save link with usage tracking | [x] | saveLinkWithUsageTracking() combined operation |
| Bulk link multiple fields | [x] | bulkLinkFields() single index write |
| Bulk unlink multiple fields | [x] | bulkUnlinkFields() efficient removal |
| Get links for multiple fields | [x] | getLinkedEntriesForFields() batch query |
| Levenshtein distance calculation | [x] | levenshteinDistance() edit distance |
| Levenshtein similarity (0-1) | [x] | levenshteinSimilarity() normalized score |
| Levenshtein search library | [x] | levenshteinSearchLibrary() typo-tolerant fuzzy |
| Library health check | [x] | checkLibraryHealth() validates entries |
| Domain libraries health check | [x] | checkDomainLibrariesHealth() bulk validation |
| Entry usage statistics | [x] | getEntryUsageStats() for single entry |
| Get most used entries | [x] | getMostUsedEntries() sorted by usage |
| Get unused entries | [x] | getUnusedEntries() for cleanup |
| Library usage summary | [x] | getLibraryUsageSummary() aggregate stats |
| Health check detects missing descriptions | [x] | Severity warning for empty description |
| Health check detects missing keywords | [x] | Severity warning for no keywords |
| Health check detects low weight | [x] | Severity warning for weight < 0.5 |
| Health check detects duplicate IDs | [x] | Severity error for ID collision |
| Levenshtein finds similar despite typos | [x] | "frindly" matches "friendly" |
| Usage tracking across brands | [x] | used_by_brands array updated |
| Related entries field support | [x] | related_entries array on LibraryEntry |
| Bulk operations maintain index consistency | [x] | Single save after all operations |

**5.x Library + UI (Requires 10.5)**

| Test | Status | Notes |
|------|--------|-------|
| Library suggestions shown in section editor | [ ] | Requires 5A + 10.5 |
| User can browse and select library entry | [ ] | Requires 5A.3 + 10.5 |
| Selected entries populate fields | [ ] | Requires 5A.2 + 10.5 |
| New entries created with weight | [ ] | Requires 5B.1 + 10.5 |
| Weight-based suggestion ordering | [ ] | Requires 5B.3 + 10.5 |

---

## Phase 6: Legal Pathway

**Backend Status:** Complete (2026-01-15) - 54 tests passing
**Components:** 6.1 Companies House Integration, 6.2 Website Legal Templates, 6.3 Domain Disclaimers, 6.4 Placeholder System
**Test Script:** `scripts/test-legal.ts`

> **Scope Change (2026-01-15):** Simplified from complex multi-jurisdiction system to practical UK-focused business details and website legal templates. Future: Dedicated AI Legal App for custom content creation.

### Unit Tests (Complete: 54/54)

**6.1 Companies House Integration (12 tests)**

| Test | Status | Notes |
|------|--------|-------|
| Pad company numbers to 8 digits | [x] | '123' → '00000123' |
| Preserve Scottish company prefix | [x] | 'SC123456' unchanged |
| Generate Companies House URL | [x] | Correct format |
| Search returns companies | [x] | Tested with "Tesco" |
| Search handles short queries | [x] | Returns error for <2 chars |
| Get company details | [x] | TESCO PLC verified |
| Details cached for 24 hours | [x] | Second call returns cached |
| Invalid company returns error | [x] | 99999999 not found |
| Save business details | [x] | brand/_config/business.json |
| Load business details | [x] | Reads back correctly |
| Create manual business details | [x] | Fallback for non-UK |
| Detect manual entry source | [x] | isManualEntry() works |

**6.2 Website Legal Templates (11 tests)**

| Test | Status | Notes |
|------|--------|-------|
| Get available templates | [x] | 6 templates available |
| Get template info | [x] | Title, version, placeholders |
| Get all templates info | [x] | Returns array of info objects |
| Get raw template | [x] | Contains placeholders |
| Generate cookie policy | [x] | With placeholders replaced |
| Generate privacy policy | [x] | GDPR-compliant |
| Template version tracked | [x] | template_version field |
| Generation timestamp tracked | [x] | generated_at field |
| Save legal content | [x] | brand/legal/ folder |
| Load legal content | [x] | Reads back correctly |
| Get all legal content | [x] | Lists all generated docs |

**6.3 Domain Disclaimers (12 tests)**

| Test | Status | Notes |
|------|--------|-------|
| Get all disclaimer types | [x] | 16 types |
| Get specific disclaimer | [x] | By type ID |
| Get supported domains | [x] | 6 domains |
| Therapeutic domain disclaimers | [x] | Includes not_therapy, crisis |
| Ecommerce domain disclaimers | [x] | Includes shipping, returns |
| Get footer disclaimers | [x] | Short versions |
| Get disclaimers by placement | [x] | footer, checkout, forms |
| Domain-specific detection | [x] | hasDomainSpecificDisclaimers() |
| Generate disclaimers page | [x] | Full markdown page |
| Page has crisis resources | [x] | UK helplines included |
| Save disclaimers page | [x] | brand/legal/disclaimers.json |
| Load disclaimers page | [x] | Reads back correctly |

**6.4 Placeholder System (8 tests)**

| Test | Status | Notes |
|------|--------|-------|
| Load placeholder values | [x] | From business.json + brand.json |
| Replace simple placeholders | [x] | {{business_name}} etc. |
| Conditional renders when true | [x] | {{#if vat_number}}... |
| Conditional hidden when false | [x] | Empty when var missing |
| Conditional else works | [x] | {{#else}} fallback |
| Find missing placeholders | [x] | Returns array |
| Validate template placeholders | [x] | Required/optional check |
| Format address single line | [x] | Proper formatting |

**Helper Functions (11 tests)**

| Test | Status | Notes |
|------|--------|-------|
| Company type display | [x] | 'ltd' → 'Private Limited Company' |
| Company status display | [x] | 'active' → 'Active' |
| Is company active check | [x] | Boolean return |
| Clear company cache | [x] | Cache emptied |
| Generate footer text | [x] | Combined disclaimer text |

### Integration Tests

**6.1 Companies House + Phase 1 Brand Config**

| Test | Status | Notes |
|------|--------|-------|
| Business details save to brand folder | [x] | brand/_config/business.json |
| Brand region used for jurisdiction hint | [x] | UK focus currently |

**6.3 Disclaimers + Phase 3 Domain Detection**

| Test | Status | Notes |
|------|--------|-------|
| Detected domain selects disclaimers | [x] | getDisclaimersFromDetection() |
| Therapeutic domain gets crisis resources | [x] | UK helplines (Samaritans, etc.) |

**6.x Legal Pathway + UI (Requires 10.7)**

| Test | Status | Notes |
|------|--------|-------|
| Companies House search UI | [ ] | Awaiting 10.7 |
| Legal template preview | [ ] | Awaiting 10.7 |
| Disclaimer selection UI | [ ] | Awaiting 10.7 |

### Future Enhancements (Separate App)

| Enhancement | Description | Priority |
|-------------|-------------|----------|
| AI Legal App | Dedicated app for AI-assisted legal content creation | Future |
| Multi-jurisdiction | AU (ABN Lookup), US, EU support | Future |
| Library of clauses | Optional clauses to add to templates | Future |
| ICO registration lookup | Auto-fill ICO number | Low |
| VAT validation | HMRC API for VAT number validation | Low |

---

## Phase 7: Visual Pathway

**Backend Status:** Not started
**Components:** 7.1 Visual Section Router, 7.2 CSS Token Parser, 7.3-7.6 Utility App Integrations, 7.7 Visual Guidelines Generator

### Unit Tests (Pending Build)

| Test | Status | Notes |
|------|--------|-------|
| Determine input source correctly | [ ] | Requires 7.1 |
| Parse colour tokens from CSS | [ ] | Requires 7.2 |
| Parse typography tokens from CSS | [ ] | Requires 7.2 |
| Extract hex/rgb/hsl values | [ ] | Requires 7.2 |
| Infer colour roles | [ ] | Requires 7.2 |

### Integration Tests

**7.2 CSS Token Parser + Phase 2 Upload**

| Test | Status | Notes |
|------|--------|-------|
| Uploaded CSS triggers token parsing | [ ] | Requires 2.1 + 7.2 |
| Parsed tokens populate visual sections | [ ] | Requires 7.2 + 4.3 |

**7.3-7.6 Utility Apps + UI (Requires 10.8)**

| Test | Status | Notes |
|------|--------|-------|
| ColourToken app integration works | [ ] | Requires 7.3 + 10.8 |
| TypographyBuilder app integration works | [ ] | Requires 7.4 + 10.8 |
| LogoMaker app integration works | [ ] | Requires 7.5 + 10.8 |
| ImageBrowser app integration works | [ ] | Requires 7.6 + 10.8 |

**7.7 Visual Guidelines + UI (Requires 10.8)**

| Test | Status | Notes |
|------|--------|-------|
| Generate colour do's/don'ts | [ ] | Requires 7.7 + 10.8 |
| Generate typography do's/don'ts | [ ] | Requires 7.7 + 10.8 |
| Generate logo do's/don'ts | [ ] | Requires 7.7 + 10.8 |

---

## Phase 8: Export

**Backend Status:** Not started
**Components:** 8.1 Export Router, 8.2 JSON Export, 8.3 Markdown Export, 8.4 PDF Export, 8.5 Prompt Pack Export

### Unit Tests (Pending Build)

| Test | Status | Notes |
|------|--------|-------|
| Export complete brand as JSON | [ ] | Requires 8.2 |
| Export individual sections as JSON | [ ] | Requires 8.2 |
| Format sections as markdown | [ ] | Requires 8.3 |
| Generate PDF from markdown | [ ] | Requires 8.4 |
| Apply brand styling to PDF | [ ] | Requires 8.4 |
| Generate AI prompt format | [ ] | Requires 8.5 |

### Integration Tests

**8.1 Export Router + Phase 1 Brand Status**

| Test | Status | Notes |
|------|--------|-------|
| Validate minimum requirements before export | [ ] | Requires 1.5 + 8.1 |
| Section selection filters correctly | [ ] | Requires 8.1 |

**8.x Export + UI (Requires 10.9)**

| Test | Status | Notes |
|------|--------|-------|
| JSON export downloads valid file | [ ] | Requires 8.2 + 10.9 |
| Markdown export downloads valid file | [ ] | Requires 8.3 + 10.9 |
| PDF export downloads with brand styling | [ ] | Requires 8.4 + 10.9 |
| Prompt pack export downloads correctly | [ ] | Requires 8.5 + 10.9 |
| Export history displays correctly | [ ] | Requires 8.x + 10.9 |

---

## Phase 9: Integrations

**Backend Status:** Not started
**Components:** 9.1 Canva Integration, 9.2 Stock Photo APIs, 9.3 Google Fonts API, 9.4 External Registries, 9.5 Integration Manager

### Unit Tests (Pending Build)

| Test | Status | Notes |
|------|--------|-------|
| Canva OAuth token exchange | [ ] | Requires 9.1 |
| Unsplash API search | [ ] | Requires 9.2 |
| Pexels API search | [ ] | Requires 9.2 |
| Google Fonts API search | [ ] | Requires 9.3 |
| Companies House API lookup | [ ] | Requires 9.4 |
| ABN Lookup API | [ ] | Requires 9.4 |

### Integration Tests

**9.1 Canva + Phase 7 Visual**

| Test | Status | Notes |
|------|--------|-------|
| Sync colours to Canva | [ ] | Requires 7.x + 9.1 |
| Sync typography to Canva | [ ] | Requires 7.x + 9.1 |
| Sync logos to Canva | [ ] | Requires 7.x + 9.1 |

**9.2 Stock Photos + Phase 7 Visual**

| Test | Status | Notes |
|------|--------|-------|
| Search Unsplash from ImageBrowser | [ ] | Requires 7.6 + 9.2 |
| Search Pexels from ImageBrowser | [ ] | Requires 7.6 + 9.2 |

**9.x Integrations + UI (Requires 10.10, 10.11)**

| Test | Status | Notes |
|------|--------|-------|
| Canva OAuth flow completes | [ ] | Requires 9.1 + 10.11 |
| Test connection works for each service | [ ] | Requires 9.5 + 10.11 |
| Integration status panel reflects actual state | [ ] | Requires 9.5 + 10.11 |
| API keys manageable via settings | [ ] | Requires 1.4 + 10.10 |

---

## Phase 10: UI/App

**Backend Status:** Not started
**Components:** 10.1-10.12 (All UI components)

### Foundation Tests (10.1)

| Test | Status | Notes |
|------|--------|-------|
| SvelteKit app runs without errors | [ ] | |
| Tailwind CSS working with custom config | [ ] | |
| Phosphor Icons rendering correctly | [ ] | |
| Base UI components render correctly | [ ] | |
| Stores update and propagate correctly | [ ] | |
| API routes respond correctly | [ ] | |

### Dashboard Tests (10.2)

| Test | Status | Notes |
|------|--------|-------|
| Brand list displays correctly | [ ] | |
| Stats cards show correct counts | [ ] | |
| Quick actions navigate correctly | [ ] | |
| Integration status shows correctly | [ ] | |

### Brand Creation Flow Tests (10.3)

| Test | Status | Notes |
|------|--------|-------|
| Wizard progresses through all steps | [ ] | |
| Validation works on each step | [ ] | |
| Brand folder created successfully | [ ] | Uses Phase 1.3 |
| Redirects to brand page after creation | [ ] | |

### Input Gathering Tests (10.4)

| Test | Status | Notes |
|------|--------|-------|
| File upload works for all supported types | [ ] | Uses Phase 2.1 |
| URL fetch retrieves content | [ ] | Uses Phase 2.2 |
| Sources list displays uploaded files | [ ] | |
| Files stored in correct context folders | [ ] | |

### Section Editor Tests (10.5)

| Test | Status | Notes |
|------|--------|-------|
| Schema loads correctly for section | [ ] | |
| Fields render based on schema type | [ ] | |
| Sidebar helper shows relevant info | [ ] | |
| Save draft persists to correct location | [ ] | Uses Phase 4.3 |
| Approve section moves to brandkit folder | [ ] | Uses Phase 4.3 |
| Template download works | [ ] | Uses Phase 2.4 |

### Section Navigator Tests (10.6)

| Test | Status | Notes |
|------|--------|-------|
| All sections grouped correctly | [ ] | |
| Status indicators match actual status | [ ] | Uses Phase 1.5 |
| Locked sections show dependencies | [ ] | |
| Progress percentage calculates correctly | [ ] | Uses Phase 1.5 |

### Legal Pathway UI Tests (10.7)

| Test | Status | Notes |
|------|--------|-------|
| Jurisdiction selector works | [ ] | Uses Phase 6.2 |
| Registry search returns results | [ ] | Uses Phase 6.4 |
| Legal sections navigate correctly | [ ] | |
| Review panel shows items correctly | [ ] | Uses Phase 6.3 |

### Visual Pathway UI Tests (10.8)

| Test | Status | Notes |
|------|--------|-------|
| Colour palette displays correctly | [ ] | Uses Phase 7.x |
| Typography preview renders fonts | [ ] | Uses Phase 7.x |
| Logo gallery shows uploaded logos | [ ] | |
| App launchers work (external and internal) | [ ] | Uses Phase 7.3-7.6 |

### Export Interface Tests (10.9)

| Test | Status | Notes |
|------|--------|-------|
| Format selection works | [ ] | Uses Phase 8.1 |
| Section selection filters correctly | [ ] | |
| Export generates correct file | [ ] | Uses Phase 8.x |
| Export history displays correctly | [ ] | |

### Settings Tests (10.10)

| Test | Status | Notes |
|------|--------|-------|
| Settings save and persist | [ ] | |
| API keys encrypt and store | [ ] | Uses Phase 1.4 |
| Feature flags toggle correctly | [ ] | |

### Integration Status Tests (10.11)

| Test | Status | Notes |
|------|--------|-------|
| Status displays correctly for each service | [ ] | Uses Phase 9.5 |
| Test connection works | [ ] | Uses Phase 9.5 |
| OAuth flow completes for Canva | [ ] | Uses Phase 9.1 |

### Toast Notifications Tests (10.12)

| Test | Status | Notes |
|------|--------|-------|
| Toast appears when triggered | [ ] | |
| Toast auto-dismisses after duration | [ ] | |
| Close button works | [ ] | |
| Multiple toasts stack correctly | [ ] | |

---

## End-to-End Workflows

**Dependencies:** Most phases complete

### Complete Brand Creation Flow

| Test | Status | Notes |
|------|--------|-------|
| Create brand → Upload files → Detect domain → Generate sections → Export | [ ] | Full flow |
| Create brand → Fetch website → Parse → Use in AI generation | [ ] | Website-based flow |
| Create brand → Download templates → Fill offline → Upload → Parse | [ ] | Template-based flow |

### Visual Brand Flow

| Test | Status | Notes |
|------|--------|-------|
| Upload CSS → Parse tokens → Display palette → Export brand | [ ] | CSS-first flow |
| Use ColourToken app → Save → Display in visual sections | [ ] | App-based flow |

### Legal Compliance Flow

| Test | Status | Notes |
|------|--------|-------|
| Set jurisdiction → Lookup registry → Generate legal sections → Review | [ ] | Full legal flow |

---

## Test Session Log

| Date | Tests Run | Passed | Failed | Notes |
|------|-----------|--------|--------|-------|
| 2026-01-13 | Phase 1 unit tests | 4 | 0 | All CLI tests passed |
| 2026-01-14 | Phase 2 unit tests | 4 | 0 | All CLI tests passed |
| 2026-01-14 | Phase 3 unit tests | 9 | 0 | Domain detection all passed |
| 2026-01-14 | Phase 4.1-4.2 unit tests | 10 | 0 | Prompt builder + section utils |
| 2026-01-14 | Phase 4.3 AI client | 6 | 0 | Multi-provider, Groq/OpenAI/Google/OpenRouter |
| 2026-01-14 | Phase 4.3b core sections | 6 | 0 | Validation, status, inferred detection |
| 2026-01-14 | Phase 4.4 section router | 8 | 0 | Pathways, dependencies, progress |
| 2026-01-14 | Phase 4.5 response processor | 13 | 0 | Parse, validate, inferred, partialness |
| 2026-01-14 | Phase 4.x enhancements | 9 | 0 | Model mapping, validation, batch gen |
| 2026-01-15 | Phase 5A.1 library matcher | 15 | 0 | 4 match types, hash, intent, caching |
| 2026-01-15 | Phase 5A.2 library linker | 4 | 0 | Save, history, linked_by, stats |
| 2026-01-15 | Phase 5A.3 library browser | 6 | 0 | Pagination, search, fuzzy, categories |
| 2026-01-15 | Phase 5B creator/editor | 28 | 0 | Weight constants, validation, confidence |
| 2026-01-15 | Phase 5.3 improvements | 22 | 0 | Usage tracking, Levenshtein, health check |
| | | | | |

---

## Notes

- **Phases 1, 2, 3, 4, 5 Backend Complete** - All unit tests pass via CLI (71 Phase 5 tests)
- Integration tests require Phase 10 UI to be complete
- Failed tests should be investigated before marking phase as "production ready"
- **See [DOC-INDEX.md](../DOC-INDEX.md)** for test commands, usage examples, and configuration
- Some tests require external services (Canva, ABN Lookup, etc.) - may need test accounts
- End-to-end workflows should be tested last, after all phases are complete

### Phase 4 Test Scripts

```bash
# Run all Phase 4 tests
npx tsx scripts/test-ai.ts           # AI provider comparison
npx tsx scripts/test-core-sections.ts # Core sections validation
npx tsx scripts/test-section-router.ts # Routing + dependencies
npx tsx scripts/test-response-processor.ts # Response processing
```

### Phase 5A Test Scripts

```bash
# Run all Phase 5A tests (21 tests)
npx tsx scripts/test-library-matcher.ts
```

### Phase 5B Test Scripts

```bash
# Run all Phase 5B tests (28 tests)
npx tsx scripts/test-library-creator.ts
```

### Phase 5.3 Improvements Test Scripts

```bash
# Run all Phase 5.3 improvement tests (22 tests)
npx tsx scripts/test-library-improvements.ts
```

### Phase 4 Key Integration Points

1. **Phase 2 → Phase 4**: Context gathering includes parsed documents and fetched website content
2. **Phase 3 → Phase 4**: Detected domain could influence prompt context (future enhancement)
3. **Phase 4 → Phase 6/7**: Section router identifies visual/legal sections for alternate pathways
4. **Phase 4 → Phase 5**: Generated content can be matched against libraries (next phase)

### Phase 5A Key Integration Points

1. **Phase 3 → Phase 5A**: Detected domain determines available libraries
2. **Phase 4 → Phase 5A**: AI-generated field values matched to library entries
3. **Phase 5A → Brand**: Link selections saved to brand's libraries/index.json
4. **Phase 5A → Phase 5B**: Brand-local entries created (5B.1), then promoted to BrandLib (5B.2)
