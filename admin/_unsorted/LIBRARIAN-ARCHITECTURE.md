# Librarian Architecture

> **Purpose:** Define the Librarian service - central data access for ALL read requests
> **Created:** 2026-01-16
> **Status:** Architecture Planning
> **Related Docs:** [LIBRARIAN-SERVICE.md](./LIBRARIAN-SERVICE.md) (extraction details), [LIBRARIAN-APP.md](./LIBRARIAN-APP.md) (build checklist), [AUTOMATION-PIPELINE-SPEC.md](./AUTOMATION-PIPELINE-SPEC.md), [../Services/SERVICE-MAP.md](../Services/SERVICE-MAP.md)

---

## Core Principle

**Librarian handles ALL data read requests.** If anything needs data, it goes through Librarian.

---

## Overview

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                              LIBRARIAN CEO                                           │
│                         (Single Entry Point - Routes Requests)                       │
│                                                                                      │
│  • App asks: "get mind-the-box background colour token"                             │
│  • CEO checks permissions in rules → YES                                            │
│  • CEO routes to correct library worker                                             │
│  • Has registry of all library apps and their workers                               │
└─────────────────────────────────────────┬───────────────────────────────────────────┘
                                          │
         ┌────────────────────────────────┼────────────────────────────────┐
         │                                │                                │
         ▼                                ▼                                ▼
┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
│  BRAND LIBRARY      │      │  TONE LIBRARY       │      │  COLOUR LIBRARY     │
│     WORKER          │      │     WORKER          │      │     WORKER          │
│                     │      │                     │      │                     │
│ Knows how to fetch  │      │ Knows how to fetch  │      │ Knows how to fetch  │
│ brand data          │      │ tone data           │      │ colour data         │
└──────────┬──────────┘      └──────────┬──────────┘      └──────────┬──────────┘
           │                            │                            │
           ▼                            ▼                            ▼
┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
│ /apps/brand-library │      │ /apps/tone-library  │      │ /apps/colour-library│
│   /data/            │      │   /data/            │      │   /data/            │
│   schema.json       │      │   schema.json       │      │   schema.json       │
└─────────────────────┘      └─────────────────────┘      └─────────────────────┘
```

### Request Flow

```
1. App → LibrarianCEO: "get colourTokens, mind-the-box, background"
2. LibrarianCEO checks rules → content-creator can read colourTokens ✓
3. LibrarianCEO → ColourLibraryWorker: "fetch mind-the-box background"
4. ColourLibraryWorker reads from /apps/colour-library/data/mind-the-box.json
5. Returns data back up the chain → App
```

---

## Library Management

### Libraries Live in Their Own Apps

Each library is its own app with its own data and rules. Librarian is a service that indexes and provides access across all of them.

```
/apps/
  # Functional apps
  /brand-manager/
    actions.json
    index.js

  /content-creator/
    actions.json
    index.js

  /content-designer/
    actions.json
    index.js

  # Library apps
  /brand-library/
    /data/
      be-you-love-wins.json
      frequency-studio.json
      mind-the-box.json
    schema.json
    index.js

  /tone-library/
    /data/
      warm-professional.json
      casual-friendly.json
      academic-formal.json
    schema.json
    index.js

  /colour-library/
    /data/
      be-you-love-wins-tokens.json
      frequency-tokens.json
    schema.json
    index.js

  /framework-library/
    /data/
      boundaries.json
      attachment-styles.json
      nervous-system.json
    schema.json
    index.js

/services/
  /librarian/
    /rules/
      brand-manager.rules.json
      content-creator.rules.json
      content-designer.rules.json
    registry.json          # Indexes all library apps
    /scripts/
```

### Why Libraries as Separate Apps

- Each library can have its own schema, validation, versioning
- Libraries can evolve independently
- Clear ownership (brand-manager writes to brand-library)
- Library apps could have their own UI for browsing/editing data directly
- Keeps data with its own rules rather than dumped in one place

### Librarian Registry Indexes All Library Apps

```json
// services/librarian/registry.json
{
  "libraries": {
    "brandProfiles": {
      "app": "brand-library",
      "source": "/apps/brand-library/data/",
      "read": ["brand-manager", "content-creator", "content-designer"],
      "write": ["brand-manager"],
      "schema": "/apps/brand-library/schema.json"
    },
    "tonePatterns": {
      "app": "tone-library",
      "source": "/apps/tone-library/data/",
      "read": ["content-creator", "content-designer"],
      "write": ["brand-manager"],
      "schema": "/apps/tone-library/schema.json"
    },
    "colourTokens": {
      "app": "colour-library",
      "source": "/apps/colour-library/data/",
      "read": ["content-designer", "brand-manager"],
      "write": ["brand-manager"],
      "schema": "/apps/colour-library/schema.json"
    },
    "frameworks": {
      "app": "framework-library",
      "source": "/apps/framework-library/data/",
      "read": ["content-creator"],
      "write": ["brand-manager"],
      "schema": "/apps/framework-library/schema.json"
    }
  }
}

### Per-App Rules

Each app has a rules file defining what it can access and how:

```json
// librarian/rules/content-creator.rules.json
{
  "appId": "content-creator",
  "permissions": {
    "brandProfiles": { "read": true, "write": false },
    "tonePatterns": { "read": true, "write": false },
    "frameworks": { "read": true, "write": false },
    "colourTokens": { "read": false, "write": false }
  },
  "cacheStrategy": "session",
  "defaults": {
    "tonePatterns": "warm-professional"
  }
}
```

### How Apps Use Librarian

Apps don't know where libraries live. They just ask:

```javascript
const lib = new Librarian('content-creator')

// Librarian checks permissions, knows path, loads data
const brand = await lib.get('brandProfiles', 'be-you-love-wins')

// Denied - content-creator can't write to brandProfiles
await lib.save('brandProfiles', 'be-you-love-wins', data) // Error
```

---

## File Data Outside Libraries

Not everything is library data. You'll have:

- User uploads (images, docs)
- Generated outputs (PDFs, workbooks)
- Temp files during processing
- Assets (logos, fonts)
- Config files

### Separate File Handler Service

```
/services/
  /librarian/            # Structured JSON library data
  /file-handler/         # Unstructured files and assets
    /rules/
    /scripts/
    registry.json
```

```json
// file-handler/registry.json
{
  "locations": {
    "uploads": {
      "source": "/files/uploads/",
      "read": ["all"],
      "write": ["brand-manager", "content-creator"]
    },
    "outputs": {
      "source": "/files/outputs/",
      "read": ["all"],
      "write": ["content-creator", "content-designer"]
    },
    "assets": {
      "source": "/files/assets/",
      "read": ["all"],
      "write": ["brand-manager"]
    },
    "temp": {
      "source": "/files/temp/",
      "read": ["all"],
      "write": ["all"],
      "autoClean": true
    }
  }
}
```

### File Handler Usage

```javascript
import { FileHandler } from '/services/file-handler/index.js'

const files = new FileHandler('content-creator')

// Save generated output
const outputPath = await files.save('outputs', {
  brandId: 'be-you-love-wins',
  type: 'workbook',
  format: 'pdf',
  data: pdfBuffer
})

// Get asset
const logo = await files.get('assets', 'be-you-love-wins/logo.svg')

// Clean temp after processing
await files.clean('temp', sessionId)
```

### Full Directory Structure

```
/apps/
  # Functional apps
  /brand-manager/
  /content-creator/
  /content-designer/

  # Library apps (each has own data, schema, worker)
  /brand-library/
    /data/
    schema.json
    index.js
  /tone-library/
    /data/
    schema.json
    index.js
  /colour-library/
    /data/
    schema.json
    index.js
  /typography-library/
    /data/
    schema.json
    index.js
  /framework-library/
    /data/
    schema.json
    index.js
  /vocabulary-library/
    /data/
    schema.json
    index.js

/files/                  # Unstructured files (managed by File Handler)
  /uploads/
  /outputs/
  /assets/
  /temp/

/services/
  /librarian/            # CEO + rules + registry
    /rules/
    registry.json
  /file-handler/         # Unstructured files
  /prompt-master/        # Prompt orchestration
  /ai-router/            # AI API calls
  /data-parser/          # Response validation
```

---

## Key Distinction

| Librarian | File Handler |
|-----------|--------------|
| Structured JSON data | Binary files, documents, images |
| Schema-validated | Format-validated (file type, size) |
| Version controlled | Timestamped / session-based |
| Permanent reference data | Often generated or temporary |
| Small, frequent reads | Larger files, less frequent |

Both follow same pattern—apps don't access directly, they request through the service which checks permissions and knows locations.

---

## Why Single Librarian?

| Aspect | Multiple Librarians | Single Librarian |
|--------|---------------------|------------------|
| **Complexity** | More files, more coordination | One service, one API |
| **Permissions** | Duplicated per librarian | Central registry |
| **Caching** | Each has own cache | Unified cache |
| **Extensions** | Add new librarian class | Add entry to registry |
| **Testing** | Test each separately | Test one service |

---

## Librarian CEO

The CEO is the single entry point. Services never talk to individual librarians directly.

```typescript
// LibrarianCEO - the unified API
class LibrarianCEO {
  private librarians: Map<string, BaseLibrarian>;
  private registry: LibraryRegistry;
  private logger: Logger;

  constructor() {
    // Register all domain librarians
    this.register('brand', new BrandLibrarian());
    this.register('prompt', new PromptLibrarian());
    this.register('content', new ContentLibrarian());
    this.register('visual', new VisualLibrarian());
    this.register('legal', new LegalLibrarian());
  }

  // Core methods - route to correct librarian
  async load(domain: string, type: string, id: string): Promise<any> {
    const librarian = this.getLibrarian(domain);
    return librarian.load(type, id);
  }

  async save(domain: string, type: string, id: string, data: any): Promise<void> {
    const librarian = this.getLibrarian(domain);
    return librarian.save(type, id, data);
  }

  async list(domain: string, type: string, filter?: Filter): Promise<string[]> {
    const librarian = this.getLibrarian(domain);
    return librarian.list(type, filter);
  }

  async search(domain: string, query: SearchQuery): Promise<SearchResult[]> {
    const librarian = this.getLibrarian(domain);
    return librarian.search(query);
  }

  // Cross-domain queries (CEO exclusive)
  async loadBrandWithRelations(brandId: string): Promise<FullBrandData> {
    const brand = await this.load('brand', 'identity', brandId);
    const visual = await this.load('visual', 'palette', brand.visualId);
    const voice = await this.load('brand', 'voice', brandId);
    const legal = await this.load('legal', 'entity', brand.legalId);

    return { brand, visual, voice, legal };
  }

  // Multi-domain search
  async searchAll(query: string): Promise<Record<string, SearchResult[]>> {
    const results = {};
    for (const [name, librarian] of this.librarians) {
      results[name] = await librarian.search({ text: query });
    }
    return results;
  }
}
```

**Usage from services:**

```typescript
// Import the CEO, not individual librarians
import { librarianCEO } from '@/services/librarian';

// Theme selector service
async function selectTheme(brandId: string) {
  const themes = await librarianCEO.load('brand', 'themes', brandId);
  const history = await librarianCEO.load('content', 'history', brandId);
  // ...
}

// Prompt builder service
async function buildPrompt(brandId: string, contentType: string) {
  const brand = await librarianCEO.loadBrandWithRelations(brandId);
  const template = await librarianCEO.load('prompt', 'template', contentType);
  // ...
}
```

---

## Domain Librarians

Each librarian knows its domain deeply.

### Brand Librarian

```typescript
class BrandLibrarian extends BaseLibrarian {
  domain = 'brand';

  // Types this librarian manages
  types = {
    identity: {
      path: (id) => `BrandData/${id}/_config/brand.json`,
      schema: brandIdentitySchema,
      indexed: ['name', 'domain', 'website']
    },
    voice: {
      path: (id) => `BrandData/${id}/brandkit/voice/`,
      schema: voiceSchema,
      indexed: ['tone', 'personality']
    },
    values: {
      path: (id) => `BrandData/${id}/brandkit/values/`,
      schema: valuesSchema,
      indexed: ['keywords']
    },
    audience: {
      path: (id) => `BrandData/${id}/brandkit/audience/`,
      schema: audienceSchema,
      indexed: ['segments']
    },
    themes: {
      path: (id) => `BrandData/${id}/_config/themes.json`,
      schema: themesSchema,
      indexed: ['keywords', 'mood']
    }
  };

  // Domain-specific methods
  async getBrandsByDomain(domain: string): Promise<Brand[]> {
    // Search brands with this business domain
  }

  async getActivebrands(): Promise<Brand[]> {
    // Brands with status = active
  }

  async validateBrandComplete(brandId: string): Promise<ValidationResult> {
    // Check all required sections exist
  }
}
```

### Prompt Librarian

```typescript
class PromptLibrarian extends BaseLibrarian {
  domain = 'prompt';

  types = {
    template: {
      path: (id) => `Library/prompts/templates/${id}.md`,
      schema: templateSchema,
      indexed: ['content_type', 'use_case']
    },
    variables: {
      path: (id) => `Library/prompts/variables/${id}.json`,
      schema: variablesSchema,
      indexed: ['name']
    },
    examples: {
      path: (id) => `Library/prompts/examples/${id}/`,
      schema: examplesSchema,
      indexed: ['quality', 'content_type']
    },
    chains: {
      path: (id) => `Library/prompts/chains/${id}.json`,
      schema: chainsSchema,
      indexed: ['name', 'steps']
    }
  };

  // Domain-specific methods
  async getTemplateForContentType(contentType: string): Promise<Template> {
    // Find best template for this content type
  }

  async assemblePrompt(templateId: string, variables: Record<string, any>): Promise<string> {
    // Load template, inject variables, return assembled prompt
  }

  async getChain(chainId: string): Promise<PromptChain> {
    // Multi-step prompt chains (outline → expand → refine)
  }
}
```

### Content Librarian

```typescript
class ContentLibrarian extends BaseLibrarian {
  domain = 'content';

  types = {
    draft: {
      path: (id, brandId) => `BrandData/${brandId}/draft/${id}/`,
      schema: draftSchema,
      indexed: ['status', 'content_type', 'created']
    },
    approved: {
      path: (id, brandId) => `BrandData/${brandId}/brandkit/${id}/`,
      schema: approvedSchema,
      indexed: ['section', 'version']
    },
    generated: {
      path: (id, brandId) => `BrandData/${brandId}/content/${id}/`,
      schema: generatedSchema,
      indexed: ['theme', 'date', 'status']
    },
    history: {
      path: (brandId) => `BrandData/${brandId}/_meta/content-history.json`,
      schema: historySchema,
      indexed: ['date', 'theme']
    },
    export: {
      path: (id, brandId) => `BrandData/${brandId}/exports/${id}/`,
      schema: exportSchema,
      indexed: ['format', 'date']
    }
  };

  // Domain-specific methods
  async getRecentContent(brandId: string, days: number): Promise<Content[]> {
    // Content from last N days
  }

  async getContentByTheme(brandId: string, themeId: string): Promise<Content[]> {
    // All content for a theme
  }

  async markApproved(brandId: string, contentId: string): Promise<void> {
    // Move from draft to approved
  }
}
```

### Visual Librarian

```typescript
class VisualLibrarian extends BaseLibrarian {
  domain = 'visual';

  types = {
    palette: {
      path: (id) => `Library/colours/${id}.json`,
      schema: paletteSchema,
      indexed: ['primary', 'mood', 'industry']
    },
    typography: {
      path: (id) => `Library/typography/${id}.json`,
      schema: typographySchema,
      indexed: ['style', 'font_family']
    },
    logo: {
      path: (id, brandId) => `BrandData/${brandId}/assets/logos/`,
      schema: logoSchema,
      indexed: ['variant', 'format']
    },
    images: {
      path: (id, brandId) => `BrandData/${brandId}/assets/images/`,
      schema: imageSchema,
      indexed: ['source', 'keywords', 'used_date']
    },
    templates: {
      path: (id) => `Library/templates/${id}/`,
      schema: templateSchema,
      indexed: ['type', 'platform']
    }
  };

  // Domain-specific methods
  async findMatchingPalette(keywords: string[], mood: string): Promise<Palette[]> {
    // Find palettes that match brand
  }

  async getUnusedImages(brandId: string): Promise<Image[]> {
    // Images not yet used in content
  }
}
```

### Legal Librarian

```typescript
class LegalLibrarian extends BaseLibrarian {
  domain = 'legal';

  types = {
    entity: {
      path: (id) => `BrandData/${id}/_config/legal.json`,
      schema: entitySchema,
      indexed: ['company_number', 'jurisdiction']
    },
    disclaimers: {
      path: (id) => `Library/legal/disclaimers/${id}.json`,
      schema: disclaimerSchema,
      indexed: ['industry', 'jurisdiction', 'type']
    },
    compliance: {
      path: (id) => `Library/legal/compliance/${id}.json`,
      schema: complianceSchema,
      indexed: ['regulation', 'jurisdiction']
    },
    terms: {
      path: (id) => `Library/legal/terms/${id}.md`,
      schema: termsSchema,
      indexed: ['type', 'version']
    }
  };

  // Domain-specific methods
  async getDisclaimersForIndustry(industry: string): Promise<Disclaimer[]> {
    // Industry-specific disclaimers
  }

  async validateCompliance(brandId: string): Promise<ComplianceResult> {
    // Check brand meets legal requirements
  }
}
```

---

## Data Flow Example

**When Prompt Builder needs brand data:**

```
┌─────────────────┐
│ Prompt Builder  │
│    Service      │
└────────┬────────┘
         │
         │ librarianCEO.loadBrandWithRelations(brandId)
         ▼
┌─────────────────┐
│  LibrarianCEO   │
└────────┬────────┘
         │
         ├──────────────────────┬──────────────────────┐
         │                      │                      │
         ▼                      ▼                      ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│ BrandLibrarian  │  │ VisualLibrarian │  │ LegalLibrarian  │
│                 │  │                 │  │                 │
│ load(identity)  │  │ load(palette)   │  │ load(entity)    │
│ load(voice)     │  │                 │  │                 │
│ load(values)    │  │                 │  │                 │
└────────┬────────┘  └────────┬────────┘  └────────┬────────┘
         │                    │                    │
         └────────────────────┼────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │   File Manager  │
                    │   (Reads JSON)  │
                    └─────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │ Combined Result │
                    │ to Prompt       │
                    │ Builder         │
                    └─────────────────┘
```

---

## Directory Structure

```
lib/
└── server/
    └── librarian/
        ├── index.ts              # Exports LibrarianCEO
        ├── ceo.ts                # LibrarianCEO class
        ├── base.ts               # BaseLibrarian abstract class
        ├── types.ts              # Shared types
        ├── registry.ts           # Library registry management
        │
        └── domains/
            ├── brand.ts          # BrandLibrarian
            ├── prompt.ts         # PromptLibrarian
            ├── content.ts        # ContentLibrarian
            ├── visual.ts         # VisualLibrarian
            └── legal.ts          # LegalLibrarian
```

---

## Build Order

| Step | What | Why |
|------|------|-----|
| 1 | BaseLibrarian + types | Foundation all librarians extend |
| 2 | LibrarianCEO scaffold | Entry point, even if basic |
| 3 | BrandLibrarian | Most used, validates pattern |
| 4 | PromptLibrarian | Needed for content generation |
| 5 | ContentLibrarian | Stores generated content |
| 6 | VisualLibrarian | Colour/font/image management |
| 7 | LegalLibrarian | Compliance requirements |
| 8 | CEO advanced methods | Cross-domain queries |
| 9 | Caching layer | Performance optimization |
| 10 | Search indexing | Fast lookups |

---

## Design Decisions (Confirmed)

### 1. Registry Location: Both Code + JSON ✅

The "registry" is like an address book - it tells the system where each library lives.

- **Source of truth**: Defined in TypeScript code (type-safe, version controlled)
- **Also exported to**: JSON file (`_config/registry.json`) for easy viewing
- If you want to see where things are stored, check the JSON file

### 2. Permission Model: Read Default, Write Explicit ✅

Most apps need to read data from most places, but writing is restricted.

| Permission | Default | Example |
|------------|---------|---------|
| **Read** | ✅ All apps can read all domains | Content pipeline reads brand data |
| **Write** | ❌ Must be explicitly granted | Only BrandKit can write to brand domain |

```json
// Example: What content-pipeline is allowed to do
{
  "content-pipeline": {
    "brand/*": "read",        // Can read brand data (not write)
    "content/*": "read-write" // Can read AND write its own content
  }
}
```

### 3. Caching: 5-Minute Memory Cache ✅

When data is read, keep it in memory for 5 minutes. Like keeping a book open on your desk instead of walking to the shelf each time.

- **Speeds up**: Repeated requests don't hit the disk
- **Stays fresh**: After 5 min, next request gets fresh data
- **Auto-clears on write**: If you save something, cache clears so next read gets the new version

Already working in `library-matcher.ts` - same pattern for all librarians.

---

*Design confirmed. Ready to build the Librarian system.*
