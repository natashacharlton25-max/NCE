# Librarian App - Build Checklist

> **Status:** Ready to build - Foundation for all automation pipelines
> **Priority:** HIGH - Everything else depends on this
> **Location:** `C:\Users\Business\Librarian\`
> **Last Updated:** 2026-01-16
> **Related Docs:** [LIBRARIAN-ARCHITECTURE.md](./LIBRARIAN-ARCHITECTURE.md), [LIBRARIAN-SERVICE.md](./LIBRARIAN-SERVICE.md), [AUTOMATION-PIPELINE-SPEC.md](./AUTOMATION-PIPELINE-SPEC.md)

---

## Architecture: CEO + Domain Librarians

```
LibrarianCEO (Unified API)
    │
    ├── BrandLibrarian    (identity, voice, values, themes)
    ├── PromptLibrarian   (templates, variables, chains)
    ├── ContentLibrarian  (drafts, generated, history)
    ├── VisualLibrarian   (colours, fonts, logos, images)
    └── LegalLibrarian    (entities, disclaimers, compliance)
```

**Why this pattern:**
- Each librarian has deep knowledge of its domain
- Domain-specific validation, search, relationships
- Easy to add new librarians without touching others
- Services only talk to CEO, never directly to domain librarians

---

## Code to Reuse from BrandKit Workflow

| File | Moves To | Domain |
|------|----------|--------|
| `library-matcher.ts` | BrandLibrarian methods | brand |
| `library-linker.ts` | ContentLibrarian methods | content |
| `library-browser.ts` | BaseLibrarian methods | shared |
| `library-creator.ts` | BaseLibrarian methods | shared |
| `library-editor.ts` | BaseLibrarian methods | shared |
| `domain-detector.ts` | BrandLibrarian methods | brand |
| `types/library.ts` | `src/types/index.ts` | shared |

### Existing Features to Incorporate

| Feature | Status | From File |
|---------|--------|-----------|
| Semantic matching (name + characteristics) | ✅ Done | `library-matcher.ts` |
| Negative scoring (avoid terms) | ✅ Done | `library-matcher.ts` |
| Entry weighting (0.5-1.0) | ✅ Done | `library-creator.ts` |
| Source tracking (curated, contributed, brand_specific, suggested) | ✅ Done | `types/library.ts` |
| Usage tracking (usage_count, last_used, used_by_brands) | ✅ Done | `library-editor.ts` |
| Link history | ✅ Done | `library-linker.ts` |
| Levenshtein fuzzy search | ✅ Done | `library-browser.ts` |
| Library health check | ✅ Done | `library-browser.ts` |
| 5-minute cache | ✅ Done | `library-matcher.ts` |

---

## Build Checklist

### Phase L1: Foundation (Core Classes)

- [ ] **L1.1 Create Project Structure**
  ```
  Librarian/
  ├── src/
  │   ├── index.ts
  │   ├── ceo.ts
  │   ├── base.ts
  │   ├── registry.ts
  │   ├── contracts.ts
  │   ├── domains/
  │   └── types/
  ├── _config/
  ├── analytics/
  ├── scripts/
  ├── package.json
  └── tsconfig.json
  ```

- [ ] **L1.2 Types (`src/types/index.ts`)**
  - Copy from `types/library.ts`
  - Add: `LibraryConfig`, `ServiceContract`, `SearchQuery`, `SearchResult`
  - Add: `DomainLibrarian` interface

- [ ] **L1.3 BaseLibrarian (`src/base.ts`)**
  - Abstract class all domain librarians extend
  - Core methods: `load()`, `save()`, `list()`, `search()`, `exists()`
  - Built-in: validation, logging, caching

- [ ] **L1.4 Registry (`src/registry.ts`)**
  - Load `_config/registry.json`
  - `getLibraryPath(domain, type)` → file path
  - `listDomains()` → all domains
  - `getDomainConfig(domain)` → domain settings

- [ ] **L1.5 Contracts (`src/contracts.ts`)**
  - Load `_config/contracts.json`
  - `checkPermission(app, domain, action)` → boolean
  - `getAppContract(app)` → permissions list

### Phase L2: Domain Librarians

- [ ] **L2.1 BrandLibrarian (`src/domains/brand.ts`)**
  - Types managed: `identity`, `voice`, `values`, `audience`, `themes`
  - Special methods:
    - `getBrandsByDomain(domain)` - from domain-detector
    - `getActiveBrands()` - status filter
    - `validateBrandComplete(brandId)` - section check
    - `matchEntries(fieldData, domain)` - from library-matcher

- [ ] **L2.2 PromptLibrarian (`src/domains/prompt.ts`)**
  - Types managed: `template`, `variables`, `examples`, `chains`
  - Special methods:
    - `getTemplateForContentType(contentType)`
    - `assemblePrompt(templateId, variables)` - inject variables
    - `getChain(chainId)` - multi-step prompts
    - `tokenizePrompt(prompt)` - count tokens

- [ ] **L2.3 ContentLibrarian (`src/domains/content.ts`)**
  - Types managed: `draft`, `approved`, `generated`, `history`, `export`
  - Special methods:
    - `getRecentContent(brandId, days)`
    - `getContentByTheme(brandId, themeId)`
    - `markApproved(brandId, contentId)` - move draft → approved
    - `recordGeneration(brandId, content)` - log to history

- [ ] **L2.4 VisualLibrarian (`src/domains/visual.ts`)**
  - Types managed: `palette`, `typography`, `logo`, `images`, `templates`
  - Special methods:
    - `findMatchingPalette(keywords, mood)`
    - `getUnusedImages(brandId)` - not yet used
    - `trackImageUsage(brandId, imageId)` - mark used

- [ ] **L2.5 LegalLibrarian (`src/domains/legal.ts`)**
  - Types managed: `entity`, `disclaimers`, `compliance`, `terms`
  - Special methods:
    - `getDisclaimersForIndustry(industry)`
    - `validateCompliance(brandId)` - check requirements
    - `getEntityDetails(companyNumber)` - Companies House

### Phase L3: LibrarianCEO

- [ ] **L3.1 CEO Core (`src/ceo.ts`)**
  - Register all domain librarians
  - Route `load/save/list/search` to correct domain
  - Contract checking on every operation
  - Usage logging on every operation

- [ ] **L3.2 Cross-Domain Methods**
  - `loadBrandWithRelations(brandId)` - brand + visual + legal
  - `searchAll(query)` - search all domains
  - `validateBrand(brandId)` - check all required data exists

- [ ] **L3.3 Analytics Methods**
  - `getUsageStats(domain)` - from analytics files
  - `checkStockLevels()` - library health
  - `buildStock(domain, count)` - AI generation trigger

### Phase L4: Configuration

- [ ] **L4.1 Registry Config (`_config/registry.json`)**
  ```json
  {
    "domains": {
      "brand": {
        "basePath": "C:/Users/Business/BrandData",
        "types": {
          "identity": "{brandId}/_config/brand.json",
          "themes": "{brandId}/_config/themes.json"
        }
      }
    }
  }
  ```

- [ ] **L4.2 Contracts Config (`_config/contracts.json`)**
  ```json
  {
    "contracts": {
      "brandkit-workflow": {
        "brand/*": "read-write",
        "prompt/*": "read"
      },
      "content-pipeline": {
        "content/*": "read-write",
        "prompt/*": "read",
        "brand/*": "read"
      }
    }
  }
  ```

- [ ] **L4.3 Stock Thresholds (`_config/stock-thresholds.json`)**

### Phase L5: Testing

- [ ] **L5.1 Test BaseLibrarian** - CRUD operations
- [ ] **L5.2 Test Each Domain Librarian** - domain-specific methods
- [ ] **L5.3 Test CEO Routing** - requests go to correct domain
- [ ] **L5.4 Test Contracts** - permissions enforced
- [ ] **L5.5 Test Cross-Domain** - multi-librarian queries

### Phase L6: Integration

- [ ] **L6.1 Create Facade in BrandKit Workflow**
  ```typescript
  // lib/server/librarian.ts
  import { librarianCEO } from '@services/librarian';
  export { librarianCEO };
  ```

- [ ] **L6.2 Update BrandKit Workflow Imports**
  ```typescript
  // Before
  import { matchFieldsToLibrary } from '$lib/server/library-matcher';

  // After
  import { librarianCEO } from '$lib/server/librarian';
  await librarianCEO.brand.matchEntries(fieldData, domain);
  ```

- [ ] **L6.3 Verify All Tests Still Pass**

---

## File Structure

```
Librarian/
├── src/
│   ├── index.ts                    # Single export: librarianCEO
│   ├── ceo.ts                      # LibrarianCEO class
│   ├── base.ts                     # BaseLibrarian abstract class
│   ├── registry.ts                 # Library registry
│   ├── contracts.ts                # Permission checking
│   │
│   ├── domains/
│   │   ├── brand.ts                # BrandLibrarian
│   │   ├── prompt.ts               # PromptLibrarian
│   │   ├── content.ts              # ContentLibrarian
│   │   ├── visual.ts               # VisualLibrarian
│   │   └── legal.ts                # LegalLibrarian
│   │
│   └── types/
│       └── index.ts                # All type definitions
│
├── _config/
│   ├── registry.json               # Domain → path mappings
│   ├── contracts.json              # App → permission mappings
│   └── stock-thresholds.json       # Stock level alerts
│
├── analytics/
│   ├── usage-by-domain.json
│   ├── usage-by-entry.json
│   └── usage-by-app.json
│
├── scripts/
│   ├── test-base.ts
│   ├── test-brand.ts
│   ├── test-prompt.ts
│   ├── test-content.ts
│   ├── test-visual.ts
│   ├── test-legal.ts
│   ├── test-ceo.ts
│   └── test-contracts.ts
│
├── package.json
├── tsconfig.json
└── README.md
```

---

## Future Features (After Core Complete)

These can be added to domain librarians after the foundation is working:

### Learning & Smart Suggestions

| Feature | Where | What it Does |
|---------|-------|--------------|
| Correction tracking | BrandLibrarian | Remember when users reject a suggestion and pick something else |
| Auto-boost weights | BrandLibrarian | After 5 similar corrections, boost the preferred entry |
| "Others chose this" | BrandLibrarian | Show "3 wellness brands chose this tone" |
| A/B alternatives | BrandLibrarian | When user says "not right", offer 2-3 alternatives |

### Analytics

| Feature | Where | What it Does |
|---------|-------|--------------|
| Usage tracking | All librarians | Log every access with app, time, entry |
| Stock levels | LibrarianCEO | Warn when library is running low on entries |
| Domain stats | LibrarianCEO | Most-used entries, common corrections |

---

## Integration with Other Services

```
Content Pipeline                     Librarian CEO
───────────────                     ─────────────
Pick today's theme ────────────────▶ brand.load('themes', brandId)

Build prompt ──────────────────────▶ loadBrandWithRelations(brandId)
                                    prompt.assemblePrompt(template, vars)

Save generated content ────────────▶ content.save('generated', brandId, content)

Fetch images ──────────────────────▶ visual.getUnusedImages(brandId)
```

---

## Files to Reference (Existing Code)

| File | Purpose | Moves To |
|------|---------|----------|
| `lib/server/library-matcher.ts` | Matching logic | BrandLibrarian |
| `lib/server/library-linker.ts` | Link storage | ContentLibrarian |
| `lib/server/library-browser.ts` | Browse/search | BaseLibrarian |
| `lib/server/library-creator.ts` | Entry creation | BaseLibrarian |
| `lib/server/library-editor.ts` | Weight updates | BaseLibrarian |
| `lib/server/types/library.ts` | Type definitions | `src/types/index.ts` |
| `scripts/test-library-*.ts` | Test suites (71 tests) | `scripts/` |

---

*Ready to build: Start with L1.1 Project Structure*
