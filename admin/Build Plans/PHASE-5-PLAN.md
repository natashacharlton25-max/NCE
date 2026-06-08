# Phase 5: Library System - Implementation Plan

> Detailed implementation plan for Phase 5 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [PHASE-4-PLAN.md](./PHASE-4-PLAN.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)

---

## ⚠️ Architecture Update (2026-01-14)

**Phase 5 has been split into two sub-phases based on CTO review:**

| Sub-Phase | Type | Purpose |
|-----------|------|---------|
| **5A: Passive** | Read-only | Matching, browsing, saving selections (to brand only) |
| **5B: Mutating** | Write | Creating entries, promoting to BrandLib |

**Key changes from original plan:**
- 5A built first (safe, read-only operations)
- 5B built after 5A is tested and stable
- BrandLib writes require explicit promote step (not automatic)
- Added: match_hash, match_intent, negative scoring, library caching
- Added: lock_after_link, detect_candidate config options
- Added: history trail instead of replacement

**See:**
- [BUILD-CHECKLIST.md](../Build%20Lists%20Detailed/BUILD-CHECKLIST.md#phase-5-library-matching) for task breakdown
- [LIBRARIAN-APP.md](../Build%20Lists%20Detailed/LIBRARIAN-APP.md) for future v2 features

---

## Overview

Phase 5 implements the library system - matching AI-generated/user content against BrandLib entries and allowing users to select, browse, or create library entries.

| Component | Purpose |
|-----------|---------|
| **5A.1 Library Matcher** | Match field values to library entries with confidence scores |
| **5A.2 Save Link Selection** | Save user's library selections with history |
| **5A.3 Browse Library** | Browse/search library entries |
| **5B.1 Create Brand-Local Entry** | Create new entries in brand's custom_entries |
| **5B.2 Promote to BrandLib** | Promote entries to shared library (explicit action) |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| Domain detection result | Phase 3 | Required - determines available libraries |
| Section data (draft/brandkit) | Phase 4 | Required - content to match against |
| BrandLib access | Config | Required - library data location |

---

## Library Architecture

### BrandLib Structure

```
BrandLib/
├── _config/
│   ├── index.json              # Registry of all libraries
│   ├── domains.json            # Domain definitions
│   └── field-mappings.json     # Maps BrandKit fields to libraries
├── voice/
│   ├── tone/
│   │   ├── schema.json
│   │   ├── default.json        # 50 tone definitions
│   │   └── instructions.json
│   ├── vocabulary/
│   └── sentence-starters/
├── content/
│   ├── emoji/
│   ├── cta-patterns/
│   ├── hashtags/
│   └── ... (14 libraries)
├── safety/
│   ├── content-warnings/
│   └── crisis-resources/
├── compliance/
│   ├── regulatory/
│   └── accessibility/
├── outcomes/
│   └── client-outcomes/
└── ops/
    ├── response-times/
    ├── asset-locations/
    └── geographic/
```

### Library Entry Structure

Each library entry follows this pattern:

```json
{
  "id": "warm",
  "name": "Warm",
  "description": "Friendly, approachable, and genuinely caring.",
  "domains": [],                          // Empty = universal
  "when_to_use": ["welcome messages", "support responses"],
  "characteristics": ["friendly", "approachable", "caring"],
  "example_phrases": ["We're so glad you're here"],
  "avoid": ["cold language", "corporate jargon"],
  "metadata": {
    "created": "2026-01-01",
    "source": "system",
    "usage_count": 0
  }
}
```

### Field Mappings

From `BrandLib/_config/field-mappings.json`:

```json
{
  "mappings": {
    "universal": {
      "tone": {
        "library": "universal/tone",
        "fields": [
          {
            "section": "voice/tone",
            "field": "primary_tone",
            "match_type": "semantic",
            "required": true
          },
          {
            "section": "voice/tone",
            "field": "secondary_tones",
            "match_type": "semantic",
            "multi_select": true
          }
        ]
      }
    },
    "therapeutic": {
      "vocabulary": {
        "library": "therapeutic/vocabulary",
        "fields": [
          {
            "section": "voice/vocabulary",
            "field": "preferred_terms",
            "match_type": "keyword",
            "multi_select": true
          }
        ]
      }
    }
  }
}
```

### Match Types

| Type | Description | Algorithm |
|------|-------------|-----------|
| `semantic` | AI compares meaning and intent | Embeddings or AI comparison |
| `keyword` | Looks for matching words/phrases | String matching, stemming |
| `category` | Matches against library categories | Exact category match |
| `exact` | Exact or near-exact string match | String equality |

---

## Brand's Library Storage

Each brand stores library selections and custom entries:

**File:** `BrandData/{brand}/libraries/index.json`

```json
{
  "version": "1.0.0",
  "last_updated": "2026-01-13T10:30:00Z",
  "domain": "therapeutic",
  "enabled_libraries": [
    "universal/tone",
    "universal/emoji",
    "universal/cta-patterns",
    "therapeutic/vocabulary",
    "therapeutic/content-warnings",
    "therapeutic/crisis-resources"
  ],
  "linked_entries": [
    {
      "id": "link_abc123",
      "library": "universal/tone",
      "entry_id": "warm",
      "field": "voice/tone.primary_tone",
      "linked_by": "user_selected",
      "confidence": 0.92,
      "linked_at": "2026-01-13T10:30:00Z"
    },
    {
      "id": "link_def456",
      "library": "universal/tone",
      "entry_id": "encouraging",
      "field": "voice/tone.secondary_tones",
      "linked_by": "ai_matched",
      "confidence": 0.85,
      "linked_at": "2026-01-13T10:32:00Z"
    }
  ],
  "custom_entries": [
    {
      "id": "custom_xyz789",
      "library": "therapeutic/vocabulary",
      "entry": {
        "id": "custom-term-1",
        "preferred": "wellbeing journey",
        "instead_of": "treatment",
        "context": "General references to client progress"
      },
      "created_by": "user",
      "created_at": "2026-01-13T11:00:00Z",
      "promoted_to_brandlib": false
    }
  ]
}
```

---

## 5.1 Library Matcher

### Objective
Match section field values against library entries, returning matches with confidence scores and allowing user selection.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\library-matcher.ts`

### Core Functions

```typescript
interface MatchOptions {
  brandPath: string;           // Path to brand folder
  section: string;             // Section being matched (e.g., "voice/tone")
  fieldData: object;           // Field values to match
  domain: string;              // Brand's domain
}

interface LibraryMatch {
  field: string;               // Field name matched
  library: string;             // Library path (e.g., "universal/tone")
  entry: LibraryEntry;         // Matched library entry
  confidence: number;          // 0-1 confidence score
  matchType: MatchType;        // How it was matched
  matchDetails: string;        // Explanation of match
}

interface MatchResult {
  success: boolean;
  matches: LibraryMatch[];     // Matches found
  noMatchFields: string[];     // Fields with no good match
  suggestedEntries: LibraryEntry[];  // Entries to consider
}

// Main matching function
export async function matchFieldsToLibrary(options: MatchOptions): Promise<MatchResult>

// Get available libraries for domain
export async function getAvailableLibraries(domain: string): Promise<LibraryInfo[]>

// Load library entries
export async function loadLibraryEntries(libraryPath: string): Promise<LibraryEntry[]>

// Get field mappings for section
export async function getFieldMappings(section: string, domain: string): Promise<FieldMapping[]>

// Match single field to library
export async function matchField(
  fieldValue: any,
  library: LibraryEntry[],
  matchType: MatchType
): Promise<LibraryMatch | null>

// Save user's library selection
export async function saveLinkSelection(
  brandPath: string,
  selection: LibrarySelection
): Promise<void>
```

### Match Fields to Library

```typescript
export async function matchFieldsToLibrary(options: MatchOptions): Promise<MatchResult> {
  const { brandPath, section, fieldData, domain } = options;

  // 1. Load field mappings for this section
  const mappings = await getFieldMappings(section, domain);

  if (mappings.length === 0) {
    return {
      success: true,
      matches: [],
      noMatchFields: Object.keys(fieldData),
      suggestedEntries: []
    };
  }

  // 2. Get available libraries for domain
  const availableLibraries = await getAvailableLibraries(domain);

  const matches: LibraryMatch[] = [];
  const noMatchFields: string[] = [];

  // 3. Process each field with a mapping
  for (const mapping of mappings) {
    const fieldValue = fieldData[mapping.field];

    if (!fieldValue) {
      noMatchFields.push(mapping.field);
      continue;
    }

    // 4. Check if library is available for this domain
    const libraryAvailable = availableLibraries.some(
      lib => lib.path === mapping.library
    );

    if (!libraryAvailable) {
      noMatchFields.push(mapping.field);
      continue;
    }

    // 5. Load library entries
    const entries = await loadLibraryEntries(mapping.library);

    // 6. Find matches based on match type
    const match = await matchField(fieldValue, entries, mapping.match_type);

    if (match && match.confidence >= 0.7) {  // Threshold from config
      matches.push({
        field: mapping.field,
        library: mapping.library,
        entry: match.entry,
        confidence: match.confidence,
        matchType: mapping.match_type,
        matchDetails: match.details
      });
    } else {
      noMatchFields.push(mapping.field);
    }
  }

  // 7. Get suggested entries for unmatched fields
  const suggestedEntries = await getSuggestedEntries(noMatchFields, mappings, availableLibraries);

  return {
    success: true,
    matches,
    noMatchFields,
    suggestedEntries
  };
}
```

### Match Type Implementations

```typescript
type MatchType = 'semantic' | 'keyword' | 'category' | 'exact';

async function matchField(
  fieldValue: any,
  entries: LibraryEntry[],
  matchType: MatchType
): Promise<{ entry: LibraryEntry; confidence: number; details: string } | null> {
  switch (matchType) {
    case 'semantic':
      return semanticMatch(fieldValue, entries);
    case 'keyword':
      return keywordMatch(fieldValue, entries);
    case 'category':
      return categoryMatch(fieldValue, entries);
    case 'exact':
      return exactMatch(fieldValue, entries);
    default:
      return null;
  }
}
```

### Semantic Matching

```typescript
async function semanticMatch(
  fieldValue: string,
  entries: LibraryEntry[]
): Promise<{ entry: LibraryEntry; confidence: number; details: string } | null> {
  // For now: keyword-based semantic approximation
  // Future: Use embeddings or AI for true semantic matching

  const normalizedValue = fieldValue.toLowerCase();
  let bestMatch: LibraryEntry | null = null;
  let bestScore = 0;
  let matchDetails = '';

  for (const entry of entries) {
    let score = 0;
    const matches: string[] = [];

    // Check name match
    if (normalizedValue.includes(entry.name.toLowerCase())) {
      score += 0.5;
      matches.push(`name: "${entry.name}"`);
    }

    // Check characteristics match
    for (const char of entry.characteristics || []) {
      if (normalizedValue.includes(char.toLowerCase())) {
        score += 0.1;
        matches.push(`characteristic: "${char}"`);
      }
    }

    // Check description keywords
    const descWords = (entry.description || '').toLowerCase().split(/\s+/);
    const valueWords = normalizedValue.split(/\s+/);
    const commonWords = valueWords.filter(w => descWords.includes(w) && w.length > 3);
    if (commonWords.length > 0) {
      score += commonWords.length * 0.05;
      matches.push(`keywords: ${commonWords.join(', ')}`);
    }

    // Check example phrases
    for (const phrase of entry.example_phrases || []) {
      if (normalizedValue.includes(phrase.toLowerCase())) {
        score += 0.2;
        matches.push(`phrase match`);
      }
    }

    // Normalize score to 0-1
    score = Math.min(1, score);

    if (score > bestScore) {
      bestScore = score;
      bestMatch = entry;
      matchDetails = `Matched on: ${matches.join(', ')}`;
    }
  }

  if (bestMatch && bestScore >= 0.5) {
    return {
      entry: bestMatch,
      confidence: bestScore,
      details: matchDetails
    };
  }

  return null;
}
```

### Keyword Matching

```typescript
async function keywordMatch(
  fieldValue: string | string[],
  entries: LibraryEntry[]
): Promise<{ entry: LibraryEntry; confidence: number; details: string } | null> {
  const values = Array.isArray(fieldValue) ? fieldValue : [fieldValue];
  const normalizedValues = values.map(v => v.toLowerCase());

  let bestMatch: LibraryEntry | null = null;
  let bestScore = 0;
  let matchDetails = '';

  for (const entry of entries) {
    let matchCount = 0;
    const matches: string[] = [];

    // Check against entry keywords/terms
    const entryKeywords = [
      entry.name?.toLowerCase(),
      ...(entry.keywords || []).map(k => k.toLowerCase()),
      ...(entry.preferred_terms || []).map(t => t.term?.toLowerCase())
    ].filter(Boolean);

    for (const value of normalizedValues) {
      for (const keyword of entryKeywords) {
        if (value.includes(keyword) || keyword.includes(value)) {
          matchCount++;
          matches.push(keyword);
        }
      }
    }

    const score = matchCount / Math.max(normalizedValues.length, 1);

    if (score > bestScore) {
      bestScore = score;
      bestMatch = entry;
      matchDetails = `Keywords matched: ${matches.join(', ')}`;
    }
  }

  if (bestMatch && bestScore >= 0.5) {
    return {
      entry: bestMatch,
      confidence: Math.min(1, bestScore),
      details: matchDetails
    };
  }

  return null;
}
```

### Category Matching

```typescript
async function categoryMatch(
  fieldValue: string,
  entries: LibraryEntry[]
): Promise<{ entry: LibraryEntry; confidence: number; details: string } | null> {
  const normalizedValue = fieldValue.toLowerCase();

  for (const entry of entries) {
    // Check category field
    if (entry.category?.toLowerCase() === normalizedValue) {
      return {
        entry,
        confidence: 1.0,
        details: `Exact category match: "${entry.category}"`
      };
    }

    // Check when_to_use as categories
    for (const usage of entry.when_to_use || []) {
      if (usage.toLowerCase() === normalizedValue) {
        return {
          entry,
          confidence: 0.9,
          details: `Usage category match: "${usage}"`
        };
      }
    }
  }

  return null;
}
```

### Exact Matching

```typescript
async function exactMatch(
  fieldValue: string,
  entries: LibraryEntry[]
): Promise<{ entry: LibraryEntry; confidence: number; details: string } | null> {
  const normalizedValue = fieldValue.toLowerCase().trim();

  for (const entry of entries) {
    // Exact ID match
    if (entry.id.toLowerCase() === normalizedValue) {
      return {
        entry,
        confidence: 1.0,
        details: 'Exact ID match'
      };
    }

    // Exact name match
    if (entry.name.toLowerCase() === normalizedValue) {
      return {
        entry,
        confidence: 1.0,
        details: 'Exact name match'
      };
    }

    // Near-exact (contains)
    if (entry.name.toLowerCase().includes(normalizedValue) ||
        normalizedValue.includes(entry.name.toLowerCase())) {
      return {
        entry,
        confidence: 0.9,
        details: `Near-exact match: "${entry.name}"`
      };
    }
  }

  return null;
}
```

### Get Available Libraries

```typescript
interface LibraryInfo {
  id: string;
  name: string;
  path: string;
  description: string;
  entryCount: number;
}

export async function getAvailableLibraries(domain: string): Promise<LibraryInfo[]> {
  // 1. Load domains config
  const domainsConfig = await loadDomainsConfig();

  // 2. Get universal libraries (always included)
  const universalLibraries = domainsConfig.domains.universal.libraries || [];

  // 3. Get domain-specific libraries
  const domainLibraries = domainsConfig.domains[domain]?.libraries || [];

  // 4. Load library index
  const libraryIndex = await loadLibraryIndex();

  // 5. Build list of available libraries
  const available: LibraryInfo[] = [];

  const allLibraryIds = [...new Set([...universalLibraries, ...domainLibraries])];

  for (const category of Object.values(libraryIndex.libraries)) {
    for (const lib of category) {
      if (allLibraryIds.includes(lib.id) || universalLibraries.includes(lib.id)) {
        const entries = await loadLibraryEntries(lib.path);
        available.push({
          id: lib.id,
          name: lib.name,
          path: lib.path,
          description: lib.description,
          entryCount: entries.length
        });
      }
    }
  }

  return available;
}
```

### Save Library Selection

```typescript
interface LibrarySelection {
  library: string;
  entryId: string;
  field: string;
  linkedBy: 'user_selected' | 'ai_matched' | 'user_agreed';
  confidence: number;
}

export async function saveLinkSelection(
  brandPath: string,
  selection: LibrarySelection
): Promise<void> {
  const indexPath = path.join(brandPath, 'libraries', 'index.json');

  // 1. Load or create index
  let index: BrandLibraryIndex;
  try {
    index = JSON.parse(await fs.readFile(indexPath, 'utf-8'));
  } catch {
    index = {
      version: '1.0.0',
      last_updated: new Date().toISOString(),
      domain: '',
      enabled_libraries: [],
      linked_entries: [],
      custom_entries: []
    };
  }

  // 2. Check if link already exists for this field
  const existingIndex = index.linked_entries.findIndex(
    e => e.field === selection.field
  );

  // 3. Create new link entry
  const linkEntry = {
    id: `link_${generateId()}`,
    library: selection.library,
    entry_id: selection.entryId,
    field: selection.field,
    linked_by: selection.linkedBy,
    confidence: selection.confidence,
    linked_at: new Date().toISOString()
  };

  // 4. Add or replace
  if (existingIndex >= 0) {
    index.linked_entries[existingIndex] = linkEntry;
  } else {
    index.linked_entries.push(linkEntry);
  }

  // 5. Update timestamp
  index.last_updated = new Date().toISOString();

  // 6. Save
  await fs.mkdir(path.dirname(indexPath), { recursive: true });
  await fs.writeFile(indexPath, JSON.stringify(index, null, 2));
}
```

### Browse Library

```typescript
interface BrowseOptions {
  library: string;
  domain: string;
  search?: string;
  category?: string;
  page?: number;
  limit?: number;
}

interface BrowseResult {
  entries: LibraryEntry[];
  total: number;
  page: number;
  totalPages: number;
}

export async function browseLibrary(options: BrowseOptions): Promise<BrowseResult> {
  const { library, domain, search, category, page = 1, limit = 20 } = options;

  // 1. Load all entries
  let entries = await loadLibraryEntries(library);

  // 2. Filter by domain (include universal + domain-specific)
  entries = entries.filter(entry => {
    const entryDomains = entry.domains || [];
    return entryDomains.length === 0 ||  // Universal
           entryDomains.includes('universal') ||
           entryDomains.includes(domain);
  });

  // 3. Filter by search
  if (search) {
    const searchLower = search.toLowerCase();
    entries = entries.filter(entry =>
      entry.name.toLowerCase().includes(searchLower) ||
      entry.description?.toLowerCase().includes(searchLower) ||
      entry.characteristics?.some(c => c.toLowerCase().includes(searchLower))
    );
  }

  // 4. Filter by category
  if (category) {
    entries = entries.filter(entry =>
      entry.category === category ||
      entry.when_to_use?.includes(category)
    );
  }

  // 5. Paginate
  const total = entries.length;
  const totalPages = Math.ceil(total / limit);
  const start = (page - 1) * limit;
  entries = entries.slice(start, start + limit);

  return {
    entries,
    total,
    page,
    totalPages
  };
}
```

### Tasks

- [x] Create `lib/server/library-matcher.ts` - 2026-01-15
- [x] Create `lib/server/types/library.ts` with interfaces - 2026-01-15
- [x] Implement `getAvailableLibraries()` based on domain - 2026-01-15
- [x] Implement `loadLibraryEntries()` from BrandLib paths - 2026-01-15
- [x] Implement `getFieldMappings()` from field-mappings.json - 2026-01-15
- [x] Implement `matchFieldsToLibrary()` main function - 2026-01-15
- [x] Implement `semanticMatch()` algorithm - 2026-01-15
- [x] Implement `keywordMatch()` algorithm - 2026-01-15
- [x] Implement `categoryMatch()` algorithm - 2026-01-15
- [x] Implement `exactMatch()` algorithm - 2026-01-15
- [x] Implement `saveLinkSelection()` to brand's index (library-linker.ts) - 2026-01-15
- [x] Implement `browseLibrary()` for UI (library-browser.ts) - 2026-01-15
- [x] Test with tone library matching - 21/21 tests passing

---

## 5.2 Library Entry Creator (REVISED - Weighting System)

> **Architecture Change (2026-01-15):** Simplified from two-step (brand-local → promote) to single library with entry weighting.

### Objective
Create and manage library entries with weight/source tracking. All entries go to BrandLib with trust indicators.

### Location
- `C:\Users\Business\BrandKit Workflow\lib\server\library-creator.ts` - Entry creation
- `C:\Users\Business\BrandKit Workflow\lib\server\library-editor.ts` - Weight/content updates

### Entry Weighting System

| Weight | Source | Description |
|--------|--------|-------------|
| 1.0 | `curated` | System entries (original library content) |
| 0.8 | `contributed` | User-added entries, reviewed/accepted |
| 0.6 | `brand_specific` | Added from a specific brand |
| 0.5 | `suggested` | AI-generated suggestions |

**How Weighting Works:**
1. `finalConfidence = matchScore × entryWeight`
2. Higher-weight entries preferred in suggestions
3. If user rejects suggestion, fallback to lower-weight alternatives

### Type Changes Required

```typescript
// Add to types/library.ts

type EntrySource = 'curated' | 'contributed' | 'brand_specific' | 'suggested';

interface LibraryEntry {
  // ... existing fields ...
  weight: number;           // 0-1, default 1.0 for curated
  source: EntrySource;      // Origin of the entry
  source_brand?: string;    // Brand slug if brand_specific
  created_at?: string;      // ISO timestamp
  updated_at?: string;      // ISO timestamp
}
```

### Core Functions

```typescript
// library-creator.ts
interface CreateEntryOptions {
  library: string;             // Target library (e.g., "voice/vocabulary")
  entry: Partial<LibraryEntry>;
  source: EntrySource;         // 'curated' | 'contributed' | 'brand_specific' | 'suggested'
  sourceBrand?: string;        // Brand slug if brand_specific
}

interface CreateEntryResult {
  success: boolean;
  entryId: string;
  entry: LibraryEntry;
  error?: string;
}

// Create new library entry with weight
export async function createLibraryEntry(options: CreateEntryOptions): Promise<CreateEntryResult>

// Validate entry against library schema
export async function validateEntry(library: string, entry: Partial<LibraryEntry>): Promise<ValidationResult>

// library-editor.ts
// Update entry weight (after user selection/rejection)
export async function updateEntryWeight(
  library: string,
  entryId: string,
  newWeight: number,
  reason?: string
): Promise<{ success: boolean; error?: string }>

// Edit entry content
export async function editLibraryEntry(
  library: string,
  entryId: string,
  updates: Partial<LibraryEntry>
): Promise<{ success: boolean; error?: string }>

// library-matcher.ts update
// Factor weight into confidence calculation
function calculateFinalConfidence(matchScore: number, entryWeight: number): number {
  return matchScore * entryWeight;
}
```

### Create Library Entry

```typescript
export async function createLibraryEntry(options: CreateEntryOptions): Promise<CreateEntryResult> {
  const { brandPath, library, entry, domain, addToBrandLib, source } = options;

  // 1. Validate entry
  const validation = await validateEntry(library, entry);
  if (!validation.valid) {
    throw new Error(`Invalid entry: ${validation.errors.join(', ')}`);
  }

  // 2. Generate full entry with defaults
  const fullEntry: LibraryEntry = {
    id: entry.id || `custom-${generateId()}`,
    name: entry.name || '',
    description: entry.description || '',
    domains: [domain],
    ...entry,
    metadata: {
      created: new Date().toISOString(),
      source: source,
      source_brand: path.basename(brandPath),
      usage_count: 0
    }
  };

  const savedTo: ('brand' | 'brandlib')[] = [];

  // 3. Always save to brand's custom entries
  await addToBrandCustom(brandPath, library, fullEntry);
  savedTo.push('brand');

  // 4. Optionally add to BrandLib
  if (addToBrandLib) {
    await addToBrandLibrary(library, fullEntry, domain);
    savedTo.push('brandlib');

    // Mark as promoted in brand's index
    await markAsPromoted(brandPath, fullEntry.id);
  }

  return {
    success: true,
    entryId: fullEntry.id,
    savedTo,
    entry: fullEntry
  };
}
```

### Add to Brand Custom Entries

```typescript
export async function addToBrandCustom(
  brandPath: string,
  library: string,
  entry: LibraryEntry
): Promise<void> {
  const indexPath = path.join(brandPath, 'libraries', 'index.json');

  // 1. Load or create index
  let index: BrandLibraryIndex;
  try {
    index = JSON.parse(await fs.readFile(indexPath, 'utf-8'));
  } catch {
    index = {
      version: '1.0.0',
      last_updated: new Date().toISOString(),
      domain: '',
      enabled_libraries: [],
      linked_entries: [],
      custom_entries: []
    };
  }

  // 2. Add custom entry
  const customEntry = {
    id: `custom_${generateId()}`,
    library,
    entry,
    created_by: entry.metadata?.source || 'user',
    created_at: new Date().toISOString(),
    promoted_to_brandlib: false
  };

  index.custom_entries.push(customEntry);
  index.last_updated = new Date().toISOString();

  // 3. Save
  await fs.writeFile(indexPath, JSON.stringify(index, null, 2));
}
```

### Add to BrandLib

```typescript
export async function addToBrandLibrary(
  library: string,
  entry: LibraryEntry,
  domain: string
): Promise<void> {
  // 1. Get BrandLib path from global config
  const globalConfig = await loadGlobalConfig();
  const brandLibPath = globalConfig.paths.brandlib;

  // 2. Construct library file path
  const libraryFilePath = path.join(brandLibPath, library, 'default.json');

  // 3. Load existing library data
  const libraryData = JSON.parse(await fs.readFile(libraryFilePath, 'utf-8'));

  // 4. Ensure entry has domain tag
  entry.domains = entry.domains || [];
  if (!entry.domains.includes(domain)) {
    entry.domains.push(domain);
  }

  // 5. Get the entries array (varies by library structure)
  const entriesKey = getEntriesKey(libraryData);  // 'tones', 'entries', 'terms', etc.

  if (!libraryData[entriesKey]) {
    libraryData[entriesKey] = [];
  }

  // 6. Check for duplicate ID
  const existingIndex = libraryData[entriesKey].findIndex(e => e.id === entry.id);
  if (existingIndex >= 0) {
    // Update existing
    libraryData[entriesKey][existingIndex] = entry;
  } else {
    // Add new
    libraryData[entriesKey].push(entry);
  }

  // 7. Save library file
  await fs.writeFile(libraryFilePath, JSON.stringify(libraryData, null, 2));

  // 8. Update domains index if new domain
  await ensureDomainExists(domain);
}

function getEntriesKey(libraryData: object): string {
  // Find the array property in the library data
  const possibleKeys = ['tones', 'entries', 'terms', 'patterns', 'items', 'resources'];
  for (const key of possibleKeys) {
    if (Array.isArray(libraryData[key])) {
      return key;
    }
  }
  return 'entries';  // Default
}
```

### Ensure Domain Exists

```typescript
async function ensureDomainExists(domain: string): Promise<void> {
  const globalConfig = await loadGlobalConfig();
  const domainsPath = path.join(globalConfig.paths.brandlib, '_config', 'domains.json');

  const domainsConfig = JSON.parse(await fs.readFile(domainsPath, 'utf-8'));

  // Check if domain exists
  if (!domainsConfig.domains[domain]) {
    // Add new domain
    domainsConfig.domains[domain] = {
      name: formatDomainName(domain),
      description: `Auto-created domain: ${domain}`,
      indicators: [],
      libraries: [],
      includes_universal: true,
      created: new Date().toISOString()
    };

    await fs.writeFile(domainsPath, JSON.stringify(domainsConfig, null, 2));
  }
}

function formatDomainName(domain: string): string {
  return domain
    .split('-')
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(' ');
}
```

### Detect Library Candidates

```typescript
interface LibraryCandidate {
  field: string;
  value: any;
  suggestedLibrary: string;
  confidence: number;
  reason: string;
}

export async function detectLibraryCandidate(
  section: string,
  fieldData: object,
  domain: string
): Promise<LibraryCandidate[]> {
  const candidates: LibraryCandidate[] = [];

  // 1. Get field mappings for this section
  const mappings = await getFieldMappings(section, domain);

  for (const [field, value] of Object.entries(fieldData)) {
    // 2. Check if field has a library mapping
    const mapping = mappings.find(m => m.field === field);

    if (mapping) {
      // 3. Check if value looks like it could be a library entry
      if (typeof value === 'string' && value.length > 10) {
        // 4. Check if value matches existing entries
        const entries = await loadLibraryEntries(mapping.library);
        const existingMatch = await matchField(value, entries, mapping.match_type);

        if (!existingMatch || existingMatch.confidence < 0.7) {
          // This looks like a new entry candidate
          candidates.push({
            field,
            value,
            suggestedLibrary: mapping.library,
            confidence: 0.8,
            reason: 'No existing match found - could be added as new entry'
          });
        }
      }
    }

    // 5. Special detection for vocabulary-like content
    if (section === 'voice/vocabulary') {
      if (field === 'preferred_terms' || field === 'terms_to_avoid') {
        const terms = Array.isArray(value) ? value : [value];
        for (const term of terms) {
          if (typeof term === 'object' && term.term) {
            candidates.push({
              field,
              value: term,
              suggestedLibrary: `${domain}/vocabulary`,
              confidence: 0.9,
              reason: 'Domain-specific vocabulary term'
            });
          }
        }
      }
    }
  }

  return candidates;
}
```

### Validate Entry

```typescript
interface ValidationResult {
  valid: boolean;
  errors: string[];
  warnings: string[];
}

export async function validateEntry(
  library: string,
  entry: Partial<LibraryEntry>
): Promise<ValidationResult> {
  const errors: string[] = [];
  const warnings: string[] = [];

  // 1. Load library schema
  const globalConfig = await loadGlobalConfig();
  const schemaPath = path.join(globalConfig.paths.brandlib, library, 'schema.json');

  let schema;
  try {
    schema = JSON.parse(await fs.readFile(schemaPath, 'utf-8'));
  } catch {
    warnings.push('Could not load schema for validation');
    schema = null;
  }

  // 2. Basic required fields
  if (!entry.name || entry.name.trim() === '') {
    errors.push('Entry name is required');
  }

  // 3. ID format
  if (entry.id && !/^[a-z0-9-]+$/.test(entry.id)) {
    errors.push('Entry ID must be lowercase alphanumeric with hyphens only');
  }

  // 4. Schema-specific validation
  if (schema) {
    const requiredFields = schema.required || [];
    for (const field of requiredFields) {
      if (!entry[field]) {
        errors.push(`Required field missing: ${field}`);
      }
    }
  }

  // 5. Library-specific validation
  if (library.includes('tone')) {
    if (!entry.characteristics || entry.characteristics.length === 0) {
      warnings.push('Tone entries should have characteristics');
    }
    if (!entry.example_phrases || entry.example_phrases.length === 0) {
      warnings.push('Tone entries should have example phrases');
    }
  }

  if (library.includes('vocabulary')) {
    if (!entry.preferred_terms && !entry.terms_to_avoid) {
      errors.push('Vocabulary entries need preferred_terms or terms_to_avoid');
    }
  }

  return {
    valid: errors.length === 0,
    errors,
    warnings
  };
}
```

### Tasks (5B with Weighting) ✓ COMPLETE

**5B.1 Library Creator** - 2026-01-15
- [x] Update `lib/server/types/library.ts` - Add weight/source fields
- [x] Create `lib/server/library-creator.ts`
- [x] Implement `createLibraryEntry()` with weight/source
- [x] Implement `validateEntry()` with schema support
- [x] Test entry creation with different weights

**5B.2 Library Editor** - 2026-01-15
- [x] Create `lib/server/library-editor.ts`
- [x] Implement `updateEntryWeight()` for weight adjustments
- [x] Implement `editLibraryEntry()` for content updates
- [x] Test weight updates

**5B.3 Matcher Update** - 2026-01-15
- [x] Update `library-matcher.ts` to factor weight into confidence
- [x] Add `calculateWeightedConfidence()` function
- [x] Sort suggestions by weight (higher = preferred)
- [x] Test weighted matching - 28/28 tests passing

---

## 5.3 Phase 5 Improvements ✓ COMPLETE (2026-01-15)

Additional enhancements added after core Phase 5 completion.

### 5.3.1 Usage Tracking

Track when library entries are selected to identify popular entries.

| Type Addition | Description |
|--------------|-------------|
| `usage_count` | Number of times entry selected |
| `last_used` | ISO timestamp of last selection |
| `used_by_brands` | Array of brand slugs using this entry |

**Functions:**
- `recordEntryUsage(library, entryId, brandSlug)` - Increment usage on selection
- `saveLinkWithUsageTracking(brandPath, selection, brandSlug)` - Save link + track usage

### 5.3.2 Entry Relationships

Allow entries to reference related entries for better suggestions.

| Type Addition | Description |
|--------------|-------------|
| `related_entries` | Array of related entry IDs (e.g., ["nurturing", "caring"]) |

### 5.3.3 Bulk Link Operations

Efficiently link/unlink multiple fields with single index write.

**Functions:**
- `bulkLinkFields(brandPath, operations, brandSlug?)` - Link multiple fields at once
- `bulkUnlinkFields(brandPath, fields)` - Unlink multiple fields at once
- `getLinkedEntriesForFields(brandPath, fields)` - Get links for multiple fields

### 5.3.4 Levenshtein Fuzzy Search

Typo-tolerant search using Levenshtein distance algorithm.

**Functions:**
- `levenshteinDistance(a, b)` - Calculate edit distance between strings
- `levenshteinSimilarity(a, b)` - Convert distance to 0-1 similarity score
- `levenshteinSearchLibrary(library, query, options)` - Search with typo tolerance

**Example:** "warme" finds "warm" with 80% similarity.

### 5.3.5 Library Health Check

Validate library entries and identify issues.

**Functions:**
- `checkLibraryHealth(library)` - Check single library for issues
- `checkDomainLibrariesHealth(domain)` - Check all domain libraries

**Issue Types:**
| Severity | Code | Description |
|----------|------|-------------|
| error | DUPLICATE_ID | Duplicate entry IDs |
| warning | NO_DESCRIPTION | Entry missing description |
| warning | MISSING_RELATED | Related entry doesn't exist |
| info | NO_KEYWORDS | Entry missing keywords |
| info | LOW_WEIGHT | Entry weight < 0.5 |

### 5.3.6 Entry Usage Stats

Get statistics on entry usage across libraries.

**Functions:**
- `getEntryUsageStats(library)` - Usage stats for all entries
- `getMostUsedEntries(library, limit)` - Top N most used entries
- `getUnusedEntries(library)` - Entries never selected
- `getLibraryUsageSummary(library)` - Summary with totals and averages

### Tasks (5.3 Improvements) ✓ COMPLETE

- [x] Add `usage_count`, `last_used`, `used_by_brands` to LibraryEntry type
- [x] Add `related_entries` to LibraryEntry type
- [x] Implement `recordEntryUsage()` in library-linker.ts
- [x] Implement `saveLinkWithUsageTracking()` in library-linker.ts
- [x] Implement `bulkLinkFields()` and `bulkUnlinkFields()` in library-linker.ts
- [x] Implement Levenshtein functions in library-browser.ts
- [x] Implement health check functions in library-browser.ts
- [x] Implement usage stats functions in library-browser.ts
- [x] Create test script `scripts/test-library-improvements.ts` - 22/22 tests passing
- [x] Update `lib/server/index.ts` with new exports
- [x] Update documentation (BUILD-CHECKLIST.md, DOC-INDEX.md)

---

## Implementation Order

```
5.1 Library Matcher ✓ COMPLETE
         ↓
5.2 Library Entry Creator (with weighting) ✓ COMPLETE
    ├── 5B.1 Types + Creator
    ├── 5B.2 Editor
    └── 5B.3 Matcher Update
         ↓
5.3 Improvements ✓ COMPLETE
    ├── Usage Tracking
    ├── Entry Relationships
    ├── Bulk Link Operations
    ├── Levenshtein Fuzzy Search
    ├── Library Health Check
    └── Entry Usage Stats
```

**Status:**
- 5.1 (5A) complete with 21/21 tests passing
- 5.2 (5B) complete with 28/28 tests passing
- 5.3 (Improvements) complete with 22/22 tests passing
- **Phase 5 COMPLETE: 71 total tests**

---

## Verification Plan

### 5.1 Library Matcher ✓ COMPLETE
- [x] `getAvailableLibraries('therapeutic')` returns universal + therapeutic libraries (9 libs)
- [x] `getAvailableLibraries('ecommerce')` returns universal only (no ecommerce libs yet)
- [x] Semantic match finds "warm" tone for "warm and friendly" (50% confidence)
- [x] Keyword match finds vocabulary entry for exact term
- [x] Category match finds CTA pattern for category
- [x] Exact match finds geographic entry for region code
- [x] Confidence threshold configurable (auto_link ≥0.95, suggest ≥0.70, manual_only)
- [x] `saveLinkSelection()` updates brand's libraries/index.json
- [x] `browseLibrary()` returns paginated, filtered results (Page 1/10)

### 5.2 Library Entry Creator (Weighting System) ✓ COMPLETE
- [x] LibraryEntry type has weight (0-1) and source fields
- [x] `createLibraryEntry()` adds entry with weight/source
- [x] `updateEntryWeight()` adjusts weight after user action
- [x] `editLibraryEntry()` updates entry content
- [x] Matcher factors weight into final confidence
- [x] Higher-weight entries ranked first in suggestions
- [x] Test script passes all 5B tests (28/28)

---

## Files Summary

| File | Phase | Status |
|------|-------|--------|
| `lib/server/library-matcher.ts` | 5A/5B | ✓ Complete (+ calculateWeightedConfidence) |
| `lib/server/library-linker.ts` | 5A/5.3 | ✓ Complete (+ usage tracking, bulk ops) |
| `lib/server/library-browser.ts` | 5A/5.3 | ✓ Complete (+ Levenshtein, health check, stats) |
| `lib/server/types/library.ts` | 5A/5B/5.3 | ✓ Complete (+ usage, relationships, bulk types) |
| `lib/server/library-creator.ts` | 5B | ✓ Complete |
| `lib/server/library-editor.ts` | 5B | ✓ Complete |
| `scripts/test-library-matcher.ts` | 5A | ✓ Complete (21 tests) |
| `scripts/test-library-creator.ts` | 5B | ✓ Complete (28 tests) |
| `scripts/test-library-improvements.ts` | 5.3 | ✓ Complete (22 tests) |

---

## Integration Points

### With Phase 3 (Domain Detection)
- Domain determines available libraries
- `getAvailableLibraries()` uses domain from detection result

### With Phase 4 (Section Generation)
- `matchFieldsToLibrary()` called after section generation
- Matches shown to user for selection
- Candidates detected for AI-generated content

### With BrandLib
- Reads from: `_config/index.json`, `_config/domains.json`, `_config/field-mappings.json`
- Reads from: `{library}/default.json` for entries
- Writes to: `{library}/default.json` when adding entries
- Writes to: `_config/domains.json` when new domain created

---

## Notes

- Match confidence threshold (0.7) is configurable
- Semantic matching is simplified for Phase 1 (no embeddings)
- All library writes are to default.json (single source of truth)
- Domain tags allow entries to appear for multiple domains
- Empty domains array means universal (all domains)
- ✓ Usage tracking implemented (5.3.1)
- ✓ Levenshtein fuzzy search added for typo tolerance (5.3.4)
- ✓ Library health check identifies data quality issues (5.3.5)

### Future Enhancements (Lower Priority)
- Embeddings for semantic matching (when keyword matching fails for nuanced content)
- Weight decay over time (when library gets polluted with unused entries)
- Entry versioning (when admin review/rollback becomes necessary)
- AI-assisted matching (integrate with external AI app)
- Cross-library relationships (reference entries in other libraries)
