# Phase 1: Foundation - Implementation Plan

> **STATUS: COMPLETE** - Completed 2026-01-13
> Reference: [BUILD-CHECKLIST.md](../Build%20Lists%20Detailed/BUILD-CHECKLIST.md) | [PROJECT-INVENTORY.md](../Build%20Lists%20Detailed/PROJECT-INVENTORY.md)

---

## Overview

Phase 1 establishes the foundational infrastructure that all other phases depend on:

| Component | Purpose |
|-----------|---------|
| **1.1 Global Config** | Central configuration for the BrandKit Workflow app |
| **1.2 Legal Structure Restructuring** | Move legal out of core/ into dedicated legal/ category |
| **1.3 Folder Creator App** | Separate utility for creating brand folder structures |
| **1.4 API Keys Manager App** | Separate utility for secure API key storage |
| **1.5 Brand Status Utilities** | Functions to track brand/section progress |

---

## Current State Analysis

### What Already Exists

| Component | Status | Location |
|-----------|--------|----------|
| BrandKit Workflow `_config/` | Exists with 5 JSON files | `BrandKit Workflow/_config/` |
| `paths.json` | Exists - links to BrandKit, AIGen, BrandLib | `BrandKit Workflow/_config/paths.json` |
| `settings.json` | Exists - workflow settings | `BrandKit Workflow/_config/settings.json` |
| BrandData folder | Exists but empty | `C:\Users\Business\BrandData\` |
| Content Pipeline brands | 3 brands exist with different structure | `C:\Users\Business\Content Pipeline\brands\` |
| FolderCreator app | Does NOT exist | Planned: `C:\Users\Business\FolderCreator\` |
| APIKeyManager app | Does NOT exist | Planned: `C:\Users\Business\APIKeyManager\` |
| Brand status utilities | Does NOT exist | Planned: `BrandKit Workflow/lib/server/` |

---

## Decisions Made

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Root path | `C:\Users\Business\` | All 5 projects live under this path |
| API key encryption | Simple file encryption (AES-256-GCM) | No external dependencies, sufficient for Phase 1 |
| Utility app mode | CLI + Library exports | Can run standalone AND import into BrandKit Workflow |
| Content Pipeline brands | Leave separate | Different system, BrandData starts fresh |

---

## 1.1 Global Config Setup

### Objective
Update existing `_config/` with a comprehensive `global.json` containing all project paths and system defaults.

### Files to Modify/Create

**File:** `BrandKit Workflow/_config/global.json`

```json
{
  "$schema": "./schemas/global.schema.json",
  "version": "1.0.0",
  "last_updated": "2026-01-13",

  "paths": {
    "brandkit": "C:\\Users\\Business\\BrandKit",
    "brandkit_aigen": "C:\\Users\\Business\\BrandKit AIGen",
    "brandlib": "C:\\Users\\Business\\BrandLib",
    "branddata": "C:\\Users\\Business\\BrandData",
    "folder_creator": "C:\\Users\\Business\\FolderCreator",
    "api_keys_manager": "C:\\Users\\Business\\APIKeyManager"
  },

  "defaults": {
    "region": "AU",
    "language": "en-AU",
    "icon_library": "phosphor",
    "icon_style": "regular",
    "date_format": "DD/MM/YYYY",
    "currency": "AUD"
  },

  "feature_flags": {
    "library_matching_enabled": true,
    "auto_domain_detection": true,
    "ai_generation_enabled": false
  }
}
```

### Tasks

- [x] Verify actual paths for all 5 projects (BrandKit, AIGen, BrandLib, BrandData, Workflow)
- [x] Create `global.json` with verified paths
- [x] Update existing `paths.json` to reference `global.json` or deprecate it
- [x] Create JSON schema for validation (`schemas/global.schema.json`)

### Dependencies
- None (first task)

---

## 1.2 Legal Structure Restructuring

### Objective
Move all legal-related content OUT of `core/` and into a dedicated `legal/` category. This creates a clean separation where:
- `core/` = brand identity essentials (identity, purpose, contact)
- `legal/` = all legal/compliance content (entity, compliance, disclaimers)

### Rationale
- Legal content has different workflows (often requires professional review)
- Legal sections have their own dependency chain
- Cleaner separation of concerns
- Phase 6 (Legal Pathway) depends on this structure

### Current Structure (To Change)

**BrandKit (`_system/`):**
```
core/
├── identity/
├── purpose/
├── contact/
└── legal/          ← MOVE to legal/core
    └── default/
        ├── schema.json
        ├── instructions.json
        └── example-wwas.json

legal/
├── entity/
├── compliance/
└── disclaimers/
```

**BrandKit AIGen (`sections/`):**
```
core--legal.json    ← RENAME to legal--core.json
legal--entity.json
legal--compliance.json
legal--disclaimers.json
```

**generation-order.json:**
```json
// Current
{"order": 3, "section": "core/legal", "depends_on": ["core/identity"]}
{"order": 8, "section": "legal/entity", "depends_on": ["core/legal"]}

// Change to
{"order": 3, "section": "legal/core", "depends_on": ["core/identity"]}
{"order": 8, "section": "legal/entity", "depends_on": ["legal/core"]}
```

### New Structure (Target)

**BrandKit (`_system/`):**
```
core/
├── identity/
├── purpose/
└── contact/

legal/
├── core/           ← MOVED from core/legal
│   └── default/
│       ├── schema.json
│       ├── instructions.json
│       └── example-wwas.json
├── entity/
├── compliance/
└── disclaimers/
```

**BrandKit AIGen (`sections/`):**
```
legal--core.json       ← RENAMED from core--legal.json
legal--entity.json
legal--compliance.json
legal--disclaimers.json
```

### Files to Modify

| Project | File | Change |
|---------|------|--------|
| BrandKit | `_system/core/legal/` | Move entire folder to `_system/legal/core/` |
| BrandKit AIGen | `sections/core--legal.json` | Rename to `sections/legal--core.json`, update `section` field |
| BrandKit AIGen | `_config/generation-order.json` | Change `core/legal` → `legal/core` in all references |
| BrandKit Workflow | `_config/workflow-steps.json` | Update any `core/legal` references |

### Migration Steps

```typescript
// 1. Move BrandKit folder
// FROM: BrandKit/_system/core/legal/
// TO:   BrandKit/_system/legal/core/

// 2. Rename AIGen section config
// FROM: BrandKit AIGen/sections/core--legal.json
// TO:   BrandKit AIGen/sections/legal--core.json

// 3. Update section field inside the renamed file
{
  "section": "legal/core"  // was "core/legal"
}

// 4. Update generation-order.json
// All instances of "core/legal" → "legal/core"

// 5. Update any dependency references
// "depends_on": ["core/legal"] → "depends_on": ["legal/core"]
```

### Verification

- [x] `BrandKit/_system/core/` contains only: identity, purpose, contact
- [x] `BrandKit/_system/legal/` contains: core, entity, compliance, disclaimers
- [x] `BrandKit AIGen/sections/legal--core.json` exists with correct section field
- [x] `BrandKit AIGen/sections/core--legal.json` no longer exists
- [x] `generation-order.json` has no references to `core/legal`
- [x] All `depends_on` arrays updated to reference `legal/core`

### Tasks

- [x] Create `BrandKit/_system/legal/core/` directory
- [x] Move contents from `BrandKit/_system/core/legal/default/` to `BrandKit/_system/legal/core/default/`
- [x] Delete empty `BrandKit/_system/core/legal/` directory
- [x] Rename `core--legal.json` to `legal--core.json`
- [x] Update `section` field in `legal--core.json`
- [x] Update `generation-order.json` (change `core/legal` to `legal/core`)
- [x] Update all `depends_on` references
- [x] Verify no broken references remain

### Dependencies
- Should be done AFTER 1.1 Global Config
- Should be done BEFORE 1.3 Folder Creator (so templates use correct structure)
- Should be done BEFORE Phase 4 (Section Generation)
- Should be done BEFORE Phase 6 (Legal Pathway)

### Rollback Strategy

If restructuring fails or needs to be reverted:

**1. Before Starting - Create Backups**
```bash
# Copy the folder
cp -r "BrandKit/_system/core/legal/" "BrandKit/_system/core/legal.backup/"

# Copy the AIGen config
cp "BrandKit AIGen/sections/core--legal.json" "BrandKit AIGen/sections/core--legal.json.backup"

# Copy generation order
cp "BrandKit AIGen/_config/generation-order.json" "BrandKit AIGen/_config/generation-order.json.backup"
```

**2. If Failure Occurs - Restore Steps**
```bash
# Restore folder
rm -rf "BrandKit/_system/legal/core/"
mv "BrandKit/_system/core/legal.backup/" "BrandKit/_system/core/legal/"

# Restore AIGen config
rm "BrandKit AIGen/sections/legal--core.json"
mv "BrandKit AIGen/sections/core--legal.json.backup" "BrandKit AIGen/sections/core--legal.json"

# Restore generation order
mv "BrandKit AIGen/_config/generation-order.json.backup" "BrandKit AIGen/_config/generation-order.json"
```

**3. Verification Before Proceeding**
- [x] All 4 files moved/renamed successfully
- [x] No references to `core/legal` remain in `generation-order.json`
- [x] `legal--core.json` contains `"section": "legal/core"`
- [x] No error when loading schemas

**4. Point of No Return**
- Once Phase 4+ begins using the new structure, rollback becomes complex
- Complete Phase 1.2 fully before proceeding to later phases
- Delete backup files only after verifying Phase 2 works correctly

---

## 1.3 Folder Creator App

### Objective
Create a standalone Node.js/TypeScript utility that creates brand folder structures in BrandData. Designed to be reusable by other apps in the ecosystem.

### Usage Modes
- **CLI:** `npx folder-creator create-brand --name "My Brand" --domain therapeutic`
- **Library:** `import { createBrandFolder } from '@brandkit/folder-creator'`

### Location
`C:\Users\Business\FolderCreator\`

### Folder Structure

```
FolderCreator/
├── src/
│   ├── index.ts                    # Library exports
│   ├── cli.ts                      # CLI entry point (uses commander or yargs)
│   ├── create-brand.ts             # Brand folder creation logic
│   ├── create-content.ts           # Future: content folder creation
│   ├── create-product.ts           # Future: product folder creation
│   ├── utils/
│   │   ├── paths.ts                # Path resolution utilities
│   │   ├── json-utils.ts           # JSON read/write helpers
│   │   └── validators.ts           # Input validation
│   └── types/
│       └── index.ts                # TypeScript type definitions
├── _config/
│   └── templates.json              # Folder structure templates
├── package.json
├── tsconfig.json
└── README.md
```

### Core Function: `createBrandFolder()`

**Input Parameters:**
```typescript
interface CreateBrandOptions {
  name: string;              // Brand name (will be slugified)
  domain?: string;           // Business domain (therapeutic, ecommerce, etc.)
  region?: string;           // Region code (AU, US, UK, etc.)
  brandDataPath?: string;    // Override default BrandData path
}
```

**Output Structure Created:**
```
BrandData/{brand-slug}/
├── _config/
│   └── brand.json              # Brand metadata
├── _meta/
│   └── progress.json           # Section tracking
├── brandkit/                   # Approved section data
├── draft/                      # Work-in-progress sections
├── context/
│   ├── sources.json            # Input source manifest
│   ├── website/                # Fetched website content
│   ├── documents/              # Uploaded documents
│   └── visual/                 # Uploaded visual assets
└── libraries/                  # Brand-specific library entries
```

### Key Files to Create

**1. `src/create-brand.ts`**
```typescript
export async function createBrandFolder(options: CreateBrandOptions): Promise<CreateBrandResult> {
  // 1. Validate inputs
  // 2. Generate slug from name
  // 3. Check if folder exists (error if so)
  // 4. Create folder structure
  // 5. Create brand.json with metadata
  // 6. Create progress.json with all sections as 'not_started'
  // 7. Create sources.json (empty manifest)
  // 8. Return success with path
}
```

**2. `_config/templates.json`**
```json
{
  "brand": {
    "folders": [
      "_config",
      "_meta",
      "brandkit",
      "draft",
      "context",
      "context/website",
      "context/documents",
      "context/visual",
      "context/visual/logos",
      "context/visual/colours",
      "context/visual/typography",
      "context/visual/photography",
      "libraries"
    ],
    "files": {
      "_config/brand.json": "brand-template",
      "_meta/progress.json": "progress-template",
      "context/sources.json": "sources-template"
    }
  }
}
```

**3. `_config/brand.json` template**
```json
{
  "id": "{{slug}}",
  "name": "{{name}}",
  "display_name": "{{displayName}}",
  "domain": "{{domain}}",
  "region": "{{region}}",
  "created_at": "{{timestamp}}",
  "updated_at": "{{timestamp}}",
  "status": "initialised",
  "core_complete": false
}
```

**4. `_meta/progress.json` template**
```json
{
  "brand_status": "initialised",
  "core_complete": false,
  "sections": {
    "core/identity": { "status": "not_started", "updated_at": null },
    "core/purpose": { "status": "not_started", "updated_at": null },
    "core/contact": { "status": "not_started", "updated_at": null },
    "audience/core": { "status": "not_started", "updated_at": null }
    // ... all sections from BrandKit AIGen generation-order.json
  }
}
```

### Tasks

- [x] Create `FolderCreator/` directory
- [x] Initialize npm project with TypeScript
- [x] Create `tsconfig.json` with Node.js settings
- [x] Create `_config/templates.json` with folder structure definitions
- [x] Create `src/types/index.ts` with TypeScript interfaces
- [x] Create `src/utils/paths.ts` for path resolution
- [x] Create `src/utils/json-utils.ts` for JSON file operations
- [x] Create `src/utils/validators.ts` for input validation
- [x] Create `src/create-brand.ts` with main logic
- [x] Create `src/cli.ts` as CLI entry point
- [x] Create `src/index.ts` for library exports
- [x] Add npm scripts for build and run
- [x] Test with sample brand creation

### Dependencies
- Node.js 18+
- TypeScript
- commander (for CLI)

---

## 1.4 API Keys Manager App

### Objective
Create a standalone utility for securely storing and retrieving API keys used by various apps in the ecosystem.

### Usage Modes
- **CLI:** `npx api-keys set canva "sk_abc123"` / `npx api-keys get canva`
- **Library:** `import { getKey, setKey } from '@brandkit/api-keys'`

### Location
`C:\Users\Business\APIKeyManager\`

### Folder Structure

```
APIKeyManager/
├── src/
│   ├── index.ts                    # Library exports
│   ├── cli.ts                      # CLI entry point
│   ├── get-key.ts                  # Retrieve API key
│   ├── set-key.ts                  # Store API key (encrypted)
│   ├── list-keys.ts                # List available services
│   ├── delete-key.ts               # Remove API key
│   └── utils/
│       ├── encryption.ts           # Encryption/decryption utilities
│       └── storage.ts              # File storage utilities
├── _config/
│   ├── services.json               # Supported services metadata
│   └── .keys.encrypted             # Encrypted key storage (gitignored)
├── package.json
├── tsconfig.json
├── .gitignore
└── README.md
```

### Core Functions

```typescript
// Get an API key for a service
export async function getKey(service: string): Promise<string | null>

// Store an API key for a service
export async function setKey(service: string, key: string): Promise<boolean>

// List all stored services
export async function listKeys(): Promise<string[]>

// Delete an API key
export async function deleteKey(service: string): Promise<boolean>

// Check if a key exists
export async function hasKey(service: string): Promise<boolean>
```

### Supported Services (Initial)

```json
{
  "services": {
    "canva": {
      "name": "Canva",
      "description": "Canva API for brand kit sync",
      "required_for": ["visual-export", "brand-sync"],
      "docs_url": "https://www.canva.dev/docs/connect/"
    },
    "unsplash": {
      "name": "Unsplash",
      "description": "Stock photography API",
      "required_for": ["image-browser"],
      "docs_url": "https://unsplash.com/documentation"
    },
    "pexels": {
      "name": "Pexels",
      "description": "Stock photography and video API",
      "required_for": ["image-browser"],
      "docs_url": "https://www.pexels.com/api/documentation/"
    },
    "google_fonts": {
      "name": "Google Fonts",
      "description": "Google Fonts API for typography",
      "required_for": ["typography-builder"],
      "docs_url": "https://developers.google.com/fonts"
    }
  }
}
```

### Security Approach

**Using:** Simple file encryption (AES-256-GCM)
- Use Node.js built-in `crypto` module
- AES-256-GCM encryption
- Machine-specific key derived from hostname + username
- Keys stored in `.keys.encrypted` (gitignored)
- No external dependencies

### Tasks

- [x] Create `APIKeyManager/` directory
- [x] Initialize npm project with TypeScript
- [x] Create `_config/services.json` with service definitions (8 services)
- [x] Create `src/utils/encryption.ts` with AES-256-GCM
- [x] Create `src/utils/storage.ts` for file operations
- [x] Create `src/get-key.ts`
- [x] Create `src/set-key.ts`
- [x] Create `src/list-keys.ts`
- [x] Create `src/delete-key.ts`
- [x] Create `src/services.ts` for service validation
- [x] Create `src/cli.ts` as CLI entry point
- [x] Create `src/index.ts` for library exports
- [x] Create `.gitignore` to exclude encrypted keys
- [x] Test with sample key storage/retrieval

### Dependencies
- Node.js 18+ (built-in crypto)
- TypeScript
- commander (for CLI)

---

## 1.5 Brand Status Utilities

### Objective
Create utility functions within BrandKit Workflow for reading and updating brand/section status.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\brand-status.ts`

### Functions to Create

```typescript
// Read brand status from progress.json
export async function getBrandStatus(brandPath: string): Promise<BrandStatus>

// Update a section's status
export async function updateSectionStatus(
  brandPath: string,
  section: string,
  status: 'not_started' | 'in_progress' | 'complete'
): Promise<void>

// Check if core sections are complete
export async function isCoreComplete(brandPath: string): Promise<boolean>

// Update overall brand status
export async function updateBrandStatus(
  brandPath: string,
  status: 'initialised' | 'in_progress' | 'complete'
): Promise<void>

// Get all brands with their statuses
export async function listBrands(brandDataPath: string): Promise<BrandSummary[]>

// Get section completion percentage
export async function getCompletionPercentage(brandPath: string): Promise<number>
```

### Type Definitions

```typescript
interface BrandStatus {
  brand_status: 'initialised' | 'in_progress' | 'complete';
  core_complete: boolean;
  sections: Record<string, SectionStatus>;
}

interface SectionStatus {
  status: 'not_started' | 'in_progress' | 'complete';
  updated_at: string | null;
}

interface BrandSummary {
  id: string;
  name: string;
  display_name: string;
  status: string;
  core_complete: boolean;
  completion_percentage: number;
  path: string;
}
```

### Tasks

- [x] Create `lib/server/` directory structure in BrandKit Workflow
- [x] Create `lib/server/types/brand.ts` with type definitions
- [x] Create `lib/server/brand-status.ts` with all functions (15 functions)
- [x] Create `lib/server/index.ts` to export utilities
- [x] Test with existing brand folder structure (test-brand)

### Dependencies
- Depends on: 1.1 Global Config (for paths), 1.3 Folder Creator (for brand structure)

---

## Implementation Order

```
1.1 Global Config Setup
         ↓
1.2 Legal Structure Restructuring
         ↓
1.3 Folder Creator App ←────────────────┐
         ↓                              │
1.4 API Keys Manager App (parallel) ────┤
         ↓                              │
1.5 Brand Status Utilities ─────────────┘
```

**Recommended sequence:**
1. Start with 1.1 (establishes paths everything else needs)
2. Do 1.2 (restructure legal before creating templates)
3. Build 1.3 (core utility, used by 1.5)
4. Build 1.4 (can be parallel, independent)
5. Build 1.5 (depends on brand folder structure from 1.3)

---

## Verification Plan

### 1.1 Global Config
- [x] `global.json` exists and validates against schema
- [x] All paths point to existing directories
- [x] Paths can be read by other apps

### 1.2 Legal Structure Restructuring
- [x] `BrandKit/_system/core/` contains only: identity, purpose, contact
- [x] `BrandKit/_system/legal/` contains: core, entity, compliance, disclaimers
- [x] `BrandKit AIGen/sections/legal--core.json` exists with correct section field
- [x] `BrandKit AIGen/sections/core--legal.json` no longer exists
- [x] `generation-order.json` has no references to `core/legal`
- [x] All `depends_on` arrays reference `legal/core` (not `core/legal`)

### 1.3 Folder Creator
- [x] Running `npm run create-brand -- --name "Test Brand"` creates correct structure
- [x] All folders created as specified (31 folders)
- [x] `brand.json`, `progress.json`, `sources.json` created with correct content
- [x] Duplicate brand name throws error
- [x] Slug generation handles special characters

### 1.4 API Keys Manager
- [x] Can store a key: `npm run set-key -- canva "sk_test_123"`
- [x] Can retrieve a key: `npm run get-key -- canva` returns `sk_test_123`
- [x] Can list keys: `npm run list-keys` shows `canva`
- [x] Can delete key: `npm run delete-key -- canva` removes it
- [x] Keys are encrypted in storage file

### 1.5 Brand Status Utilities
- [x] `getBrandStatus()` returns correct structure
- [x] `updateSectionStatus()` updates progress.json
- [x] `isCoreComplete()` returns true when core/identity, core/purpose, core/contact are complete
- [x] `listBrands()` returns all brands in BrandData

---

## Files to Create (Summary)

| File | Purpose |
|------|---------|
| `BrandKit Workflow/_config/global.json` | Central configuration |
| `BrandKit Workflow/_config/schemas/global.schema.json` | Config validation |
| `FolderCreator/package.json` | npm project setup |
| `FolderCreator/tsconfig.json` | TypeScript config |
| `FolderCreator/src/index.ts` | Library exports |
| `FolderCreator/src/cli.ts` | CLI entry point |
| `FolderCreator/src/create-brand.ts` | Main creation logic |
| `FolderCreator/src/utils/*.ts` | Utility functions |
| `FolderCreator/_config/templates.json` | Folder templates |
| `APIKeyManager/package.json` | npm project setup |
| `APIKeyManager/tsconfig.json` | TypeScript config |
| `APIKeyManager/src/index.ts` | Library exports |
| `APIKeyManager/src/cli.ts` | CLI entry point |
| `APIKeyManager/src/*.ts` | Key management functions |
| `APIKeyManager/_config/services.json` | Service definitions |
| `BrandKit Workflow/lib/server/brand-status.ts` | Status utilities |
| `BrandKit Workflow/lib/server/types/brand.ts` | Type definitions |

---

## Notes

- All questions have been clarified (see Decisions Made section above)
- Ready for implementation

---

## Build Report

> **Phase 1 completed: 2026-01-13**

### Summary

| Sub-Phase | Tasks | Status |
|-----------|-------|--------|
| 1.1 Global Config Setup | 4 | Complete |
| 1.2 Legal Structure Restructuring | 8 | Complete |
| 1.3 Folder Creator App | 13 | Complete |
| 1.4 API Keys Manager App | 14 | Complete |
| 1.5 Brand Status Utilities | 5 | Complete |
| **Total** | **44** | **Complete** |

### Files Created

**BrandKit Workflow:**
- `_config/global.json` - Central configuration with all project paths
- `_config/schemas/global.schema.json` - JSON Schema validation
- `lib/server/types/brand.ts` - Type definitions
- `lib/server/brand-status.ts` - 15 brand status functions
- `lib/server/index.ts` - Library exports
- `package.json` - npm project
- `tsconfig.json` - TypeScript config

**FolderCreator (`C:\Users\Business\FolderCreator\`):**
- `package.json`, `tsconfig.json`
- `_config/templates.json` - Folder structure templates
- `src/types/index.ts` - Type definitions
- `src/utils/paths.ts` - Path utilities, slugify
- `src/utils/json-utils.ts` - JSON read/write
- `src/utils/validators.ts` - Input validation
- `src/create-brand.ts` - Main brand creation logic
- `src/cli.ts` - CLI entry point
- `src/index.ts` - Library exports

**APIKeyManager (`C:\Users\Business\APIKeyManager\`):**
- `package.json`, `tsconfig.json`, `.gitignore`
- `_config/services.json` - 8 service definitions
- `src/utils/encryption.ts` - AES-256-GCM encryption
- `src/utils/storage.ts` - Encrypted file storage
- `src/services.ts` - Service validation
- `src/get-key.ts`, `src/set-key.ts`, `src/list-keys.ts`, `src/delete-key.ts`
- `src/cli.ts` - CLI entry point
- `src/index.ts` - Library exports

**BrandKit (restructured):**
- Moved `_system/core/legal/` → `_system/legal/core/`

**BrandKit AIGen (updated):**
- Renamed `sections/core--legal.json` → `sections/legal--core.json`
- Updated `_config/generation-order.json` references
- Updated `depends_on` in legal--entity, legal--compliance, legal--disclaimers

**BrandData:**
- Created `test-brand/` with 31 folders and 3 config files

### Test Results

| Test | Result |
|------|--------|
| FolderCreator: Create brand | Pass - 31 folders, 3 files created |
| APIKeyManager: Encryption verification | Pass |
| APIKeyManager: Store/retrieve key | Pass - exact match |
| APIKeyManager: List services | Pass - 8 services shown |
| APIKeyManager: Delete key | Pass |
| Brand Status: List brands | Pass - found test-brand |
| Brand Status: Get brand status | Pass - correct structure |
| Brand Status: Update section | Pass - status and timestamp updated |

### API Services Configured (11 total)

| Service | Purpose |
|---------|---------|
| anthropic | Claude AI for content generation |
| openai | GPT API (Tier 1) |
| groq | Fast AI inference |
| google_ai | Google Gemini API |
| canva | Brand kit sync |
| unsplash | Stock photography (access key) |
| unsplash_secret | Stock photography (secret key) |
| pexels | Stock photography/video |
| google_fonts | Typography builder |
| companies_house | UK company lookup |
| abn_lookup | AU business lookup |

### Keys Stored (Post-Build Update)

| Service | Status |
|---------|--------|
| openai | ✓ Stored |
| groq | ✓ Stored |
| google_ai | ✓ Stored |
| unsplash | ✓ Stored |
| unsplash_secret | ✓ Stored |
| anthropic | ○ Not stored |
| canva | ○ Not stored |
| pexels | ○ Not stored |
| google_fonts | ○ Not stored |
| companies_house | ○ Not stored |
| abn_lookup | ○ Not stored |

*Updated: 2026-01-13*

### Next Phase

**Phase 2: Input Gathering** - File uploads, website fetching, import parsing, template generation
