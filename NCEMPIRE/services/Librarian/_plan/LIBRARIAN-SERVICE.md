# Librarian Service

> Central gatekeeper for ALL library data access across all systems.
> **Status:** Planning | **Priority:** HIGH (Foundation for automation pipelines)
> **Location:** `C:\Users\Business\Librarian\`
> **Related Docs:** [LIBRARIAN-ARCHITECTURE.md](./LIBRARIAN-ARCHITECTURE.md), [LIBRARIAN-APP.md](./LIBRARIAN-APP.md), [AUTOMATION-PIPELINE-SPEC.md](./AUTOMATION-PIPELINE-SPEC.md)

---

## Overview

The Librarian service is the single entry point for all library operations across the entire system. Apps request data from **LibrarianCEO** - they never access library files directly. The CEO coordinates multiple **domain-specific librarians** (Brand, Prompt, Content, Visual, Legal), each with deep knowledge of their data types.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              LibrarianCEO                                    │
│                    (Unified API - all services talk to this)                 │
├─────────────────────────────────────────────────────────────────────────────┤
│  BrandLibrarian  │  PromptLibrarian  │  ContentLibrarian  │  VisualLibrarian │  LegalLibrarian  │
│  (identity,      │  (templates,      │  (drafts,          │  (colours,       │  (entities,      │
│   voice, values) │   variables)      │   generated)       │   fonts, logos)  │   disclaimers)   │
└─────────────────────────────────────────────────────────────────────────────┘
                                        │
                                   File Manager
```

### Current State (BrandKit Workflow)

Library functionality is currently embedded in BrandKit Workflow:

```
BrandKit Workflow/lib/server/
├── library-matcher.ts      # Matching entries to fields
├── library-linker.ts       # Saving link selections
├── library-browser.ts      # Browse/search functionality
├── library-creator.ts      # Creating new entries
├── library-editor.ts       # Editing entries/weights
├── domain-detector.ts      # Domain classification
└── types/library.ts        # Type definitions
```

### Target State (Multi-Librarian Architecture)

```
Librarian/
├── src/
│   ├── index.ts                    # Exports LibrarianCEO
│   ├── ceo.ts                      # LibrarianCEO class
│   ├── base.ts                     # BaseLibrarian abstract class
│   ├── registry.ts                 # Library registry management
│   ├── contracts.ts                # Permission checking
│   │
│   ├── domains/                    # Domain-specific librarians
│   │   ├── brand.ts                # BrandLibrarian
│   │   ├── prompt.ts               # PromptLibrarian
│   │   ├── content.ts              # ContentLibrarian
│   │   ├── visual.ts               # VisualLibrarian
│   │   └── legal.ts                # LegalLibrarian
│   │
│   ├── scripts/                    # Shared operations
│   │   ├── track-usage.ts          # Log entry access
│   │   ├── check-stock.ts          # Monitor stock levels
│   │   ├── build-stock.ts          # Trigger AI generation
│   │   └── detect-domain.ts        # Domain classification
│   │
│   └── types/
│       └── index.ts                # All type definitions
│
├── _config/
│   ├── registry.json               # Library locations
│   ├── contracts.json              # App permissions
│   └── stock-thresholds.json       # Stock level triggers
│
├── analytics/
│   ├── usage-by-domain.json
│   ├── usage-by-entry.json
│   └── usage-by-app.json
│
├── package.json
└── tsconfig.json
```

---

## Extraction Mapping

### Files to Extract

| Current Location | Target Location | Changes Needed |
|------------------|-----------------|----------------|
| `lib/server/library-matcher.ts` | `src/scripts/match-entries.ts` | Add contract check, logging |
| `lib/server/library-browser.ts` | `src/scripts/search-library.ts` | Add contract check, logging |
| `lib/server/library-linker.ts` | `src/scripts/fetch-entries.ts` | Merge with usage tracking |
| `lib/server/library-creator.ts` | `src/scripts/create-entry.ts` | Add contract check |
| `lib/server/library-editor.ts` | `src/scripts/edit-entry.ts` | Add contract check |
| `lib/server/domain-detector.ts` | `src/scripts/detect-domain.ts` | Minimal changes |
| `lib/server/types/library.ts` | `src/types/index.ts` | Add contract types |

### Functions to Extract

| Current Function | Target Script | Contract Required |
|------------------|---------------|-------------------|
| `matchFieldsToLibrary()` | `match-entries.ts` | `library:read` |
| `browseLibrary()` | `search-library.ts` | `library:read` |
| `fuzzySearchLibrary()` | `search-library.ts` | `library:read` |
| `saveLinkSelection()` | `fetch-entries.ts` | `library:write` |
| `createLibraryEntry()` | `create-entry.ts` | `library:write` |
| `editLibraryEntry()` | `edit-entry.ts` | `library:write` |
| `deleteLibraryEntry()` | `delete-entry.ts` | `library:delete` |
| `updateEntryWeight()` | `edit-entry.ts` | `library:write` |
| `recordEntryUsage()` | `track-usage.ts` | `library:read` |
| `detectDomain()` | `detect-domain.ts` | `domain:detect` |

---

## New Functionality

### 1. Registry System

```typescript
// _config/registry.json
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

// src/registry.ts
export function getLibraryPath(libraryName: string): string {
  const registry = loadRegistry();
  const library = registry.libraries[libraryName];
  if (!library) throw new Error(`Unknown library: ${libraryName}`);
  return library.path;
}

export function listLibraries(): LibraryInfo[] {
  const registry = loadRegistry();
  return Object.entries(registry.libraries).map(([name, info]) => ({
    name,
    ...info
  }));
}
```

### 2. Contract System

```typescript
// _config/contracts.json
{
  "contracts": {
    "brandkit-workflow": {
      "brand/voice": "read",
      "brand/visual": "read",
      "brand/*": "read",
      "prompts/*": "read"
    },
    "content-pipeline": {
      "content/*": "read-write",
      "prompts/*": "read"
    },
    "librarian-admin": {
      "*": "read-write-delete"
    }
  }
}

// src/contracts.ts
export function checkPermission(
  app: string,
  library: string,
  operation: 'read' | 'write' | 'delete'
): boolean {
  const contracts = loadContracts();
  const appContract = contracts.contracts[app];
  if (!appContract) return false;

  // Check exact match
  if (appContract[library]) {
    return appContract[library].includes(operation);
  }

  // Check wildcard
  const [category] = library.split('/');
  if (appContract[`${category}/*`]) {
    return appContract[`${category}/*`].includes(operation);
  }

  // Check global wildcard
  if (appContract['*']) {
    return appContract['*'].includes(operation);
  }

  return false;
}
```

### 3. Analytics Tracking

```typescript
// src/scripts/track-usage.ts
export async function trackLibraryAccess(
  app: string,
  library: string,
  operation: string,
  entryIds?: string[]
): Promise<void> {
  const timestamp = new Date().toISOString();

  // Update usage-by-library
  await updateAnalytics('usage-by-library', library, {
    lastAccess: timestamp,
    totalAccess: increment(),
    accessByApp: { [app]: increment() }
  });

  // Update usage-by-entry (if specific entries)
  if (entryIds) {
    for (const entryId of entryIds) {
      await updateAnalytics('usage-by-entry', `${library}/${entryId}`, {
        lastAccess: timestamp,
        totalAccess: increment(),
        accessByApp: { [app]: increment() }
      });
    }
  }

  // Update usage-by-app
  await updateAnalytics('usage-by-app', app, {
    lastAccess: timestamp,
    totalAccess: increment(),
    accessByLibrary: { [library]: increment() }
  });
}
```

### 4. Stock Monitoring

```typescript
// src/scripts/check-stock.ts
export async function checkStockLevels(): Promise<StockReport> {
  const thresholds = loadStockThresholds();
  const report: StockReport = { healthy: [], low: [], critical: [] };

  for (const library of listLibraries()) {
    for (const category of library.categories) {
      const entries = await loadLibraryEntries(library.name, category);
      const count = entries.length;
      const threshold = thresholds[`${library.name}/${category}`] || thresholds.default;

      if (count >= threshold.healthy) {
        report.healthy.push({ library: `${library.name}/${category}`, count });
      } else if (count >= threshold.low) {
        report.low.push({ library: `${library.name}/${category}`, count });
      } else {
        report.critical.push({ library: `${library.name}/${category}`, count });
      }
    }
  }

  return report;
}

// src/scripts/build-stock.ts
export async function buildStock(
  library: string,
  count: number
): Promise<BuildResult> {
  // Request AI generation via AICaller service
  const prompt = await PromptBuilder.build('system/library-entry-generation', {
    library,
    existingEntries: await getExistingSamples(library, 5),
    count
  });

  const response = await AICaller.call(prompt, { responseFormat: 'json' });
  const newEntries = await DataParser.parse(response, 'ai-to-json');

  // Validate and add entries
  const results = [];
  for (const entry of newEntries) {
    const validation = await validateEntry(entry, library);
    if (validation.valid) {
      await createEntry(library, entry, 'suggested');
      results.push({ entry: entry.id, status: 'created' });
    } else {
      results.push({ entry: entry.id, status: 'invalid', errors: validation.errors });
    }
  }

  return { library, requested: count, created: results.filter(r => r.status === 'created').length, results };
}
```

---

## Public API (via LibrarianCEO)

```typescript
// src/index.ts - Single export point
export { librarianCEO } from './ceo';

// Services import and use like this:
import { librarianCEO } from '@services/librarian';

// --- Core Methods (route to domain librarians) ---

// Load data by domain/type/id
await librarianCEO.load('brand', 'identity', 'test-brand');
await librarianCEO.load('prompt', 'template', 'long-form-article');
await librarianCEO.load('visual', 'palette', 'modern-minimal');

// Save data
await librarianCEO.save('content', 'draft', 'test-brand/2026-01-16', draftData);

// List entries
await librarianCEO.list('brand', 'themes');  // ['theme-1', 'theme-2', ...]

// Search within domain
await librarianCEO.search('brand', { text: 'wellness', type: 'identity' });

// --- Cross-Domain Methods (CEO exclusive) ---

// Load brand with all related data
await librarianCEO.loadBrandWithRelations('test-brand');
// Returns: { brand, visual, voice, legal }

// Search across all domains
await librarianCEO.searchAll('wellness');
// Returns: { brand: [...], prompt: [...], content: [...], ... }

// --- Domain-Specific Methods (delegated) ---

// Brand domain
await librarianCEO.brand.getActivebrands();
await librarianCEO.brand.validateBrandComplete('test-brand');

// Prompt domain
await librarianCEO.prompt.assemblePrompt('template-id', variables);
await librarianCEO.prompt.getChain('outline-expand-refine');

// Content domain
await librarianCEO.content.getRecentContent('test-brand', 7);
await librarianCEO.content.markApproved('test-brand', 'content-123');

// --- Analytics & Admin ---

await librarianCEO.getUsageStats('brand');
await librarianCEO.checkStockLevels();
await librarianCEO.buildStock('brand/tone', 10);  // Generate 10 tone entries
```

---

## Migration Steps

### Step 1: Create Librarian Shell (Week 1)

```bash
# Create project
mkdir C:\Users\Business\Librarian
cd C:\Users\Business\Librarian
npm init -y

# Install dependencies
npm install typescript ts-node
npm install --save-dev @types/node

# Create folder structure
mkdir src src/scripts src/types _config analytics
```

### Step 2: Copy Type Definitions

```typescript
// Copy from lib/server/types/library.ts
// Add new types for contracts and analytics
```

### Step 3: Create Registry & Contracts

```bash
# Create config files
# _config/registry.json - library locations
# _config/contracts.json - app permissions
# _config/stock-thresholds.json - stock levels
```

### Step 4: Extract Library Functions

```bash
# Copy and modify each file:
# 1. library-matcher.ts → match-entries.ts
# 2. library-browser.ts → search-library.ts
# 3. library-linker.ts → fetch-entries.ts
# 4. library-creator.ts → create-entry.ts
# 5. library-editor.ts → edit-entry.ts
# 6. domain-detector.ts → detect-domain.ts

# Add to each:
# - Contract checking at start
# - Usage tracking at end
# - Logging
```

### Step 5: Create Public API

```typescript
// src/index.ts
// Unified interface that routes to scripts
// All methods check contracts first
```

### Step 6: Update BrandKit Workflow

```typescript
// Before (direct access)
import { matchFieldsToLibrary } from '$lib/server/library-matcher';
const result = await matchFieldsToLibrary({ brandPath, section, fieldData, domain });

// After (via Librarian)
import { Librarian } from '@services/librarian';
const result = await Librarian.matchEntries('brandkit-workflow', {
  brandPath, section, fieldData, domain
});
```

### Step 7: Test & Verify

```bash
# Run existing tests through new API
# Verify same results
# Check analytics recording
# Test contract enforcement
```

---

## Integration with Current Build

### During Migration (Both Systems Running)

```typescript
// lib/server/library-matcher.ts - Add forwarding
import { Librarian } from '@services/librarian';

export async function matchFieldsToLibrary(options: MatchOptions): Promise<MatchResult[]> {
  // Check if Librarian service is available
  if (process.env.USE_LIBRARIAN_SERVICE === 'true') {
    return Librarian.matchEntries('brandkit-workflow', options);
  }

  // Otherwise use local implementation
  return localMatchFieldsToLibrary(options);
}
```

### Feature Flag

```json
// _config/global.json
{
  "feature_flags": {
    "use_librarian_service": false  // Toggle when ready
  }
}
```

### Gradual Migration

1. **Phase A:** Create Librarian, run both systems
2. **Phase B:** Enable flag for dev/test
3. **Phase C:** Enable flag for production
4. **Phase D:** Remove local implementations

---

## Dependencies

| Service | Dependency Type | Purpose |
|---------|-----------------|---------|
| **AICaller** | Optional | For stock generation |
| **PromptBuilder** | Optional | For stock generation prompts |
| **DataParser** | Optional | For parsing AI responses |
| **APIKeyManager** | Required | For AI API keys (stock gen) |

---

## Testing

### Test Script Location

```
Librarian/
├── scripts/
│   ├── test-contracts.ts       # Contract enforcement
│   ├── test-registry.ts        # Registry operations
│   ├── test-crud.ts            # CRUD operations
│   └── test-analytics.ts       # Analytics tracking
```

### Key Test Cases

1. **Contract Enforcement**
   - App with read permission can read
   - App without permission is rejected
   - Wildcard permissions work

2. **Registry**
   - Can find library by name
   - Unknown library throws error
   - Categories are correct

3. **CRUD Operations**
   - Create entry adds to library
   - Read entry returns correct data
   - Update entry modifies correctly
   - Delete entry removes (if permitted)

4. **Analytics**
   - Access is tracked
   - Usage counts increment
   - Per-app tracking works

---

## Notes

- Registry is the source of truth for library locations
- Contracts are checked on EVERY operation
- Analytics are recorded on EVERY operation
- Stock monitoring can run as scheduled job
- Stock building requires AICaller service

---

*Last updated: 2026-01-16*
