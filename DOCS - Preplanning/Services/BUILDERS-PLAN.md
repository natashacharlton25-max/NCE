# Builders Service Plan

> **Purpose:** Create file/folder structures and assemble data.
> **Level:** Processing Layer
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Builders create folder structures and know where files are stored. They have the registry of all file locations. They create folders directly (mkdir) - no Writer needed for structure.

**Key Principle:** Builders know WHERE things go. They create folders directly and provide paths to other services.

---

## Builder vs Archivist

| Builder | Archivist |
|---------|-----------|
| Knows WHERE files go (registry) | Validates WHAT gets stored |
| Provides paths via `getPath()` | Orchestrates storage flow |
| Creates/ensures folders exist | Catalogs storage |
| Doesn't validate data | Doesn't know file locations |
| Doesn't write data | Uses Builder for paths |

**Flow:**
```
Archivist.store(data) → validate → Builder.getPath() → Builder.ensureDir() → Writer.write()
```

Builder is called BY Archivist during storage - it provides the path and makes sure the folder exists.

---

## Builder Types

| Builder | Creates | Purpose |
|---------|---------|---------|
| **FolderBuilder** | Directory structures | Create brand folders |
| **PromptBuilder** | AI prompts | Assemble prompts for AI calls |
| **ConfigBuilder** | Config files | Create/update config files |
| **ExportBuilder** | Export packages | Assemble export bundles |

---

## File Locations Registry

**Builders own the registry of where files are stored.** When any service needs to know where a file lives, it asks Builders.

### Registry Structure

```json
// services/builders/registry.json
{
  "locations": {
    "uploads": {
      "source": "/files/uploads/",
      "pattern": "{brandId}/{type}/{filename}",
      "read": ["all"],
      "write": ["brand-manager", "content-creator"]
    },
    "outputs": {
      "source": "/files/outputs/",
      "pattern": "{brandId}/{outputType}/{filename}",
      "read": ["all"],
      "write": ["content-creator", "content-designer"]
    },
    "assets": {
      "source": "/files/assets/",
      "pattern": "{brandId}/{assetType}/{filename}",
      "read": ["all"],
      "write": ["brand-manager"]
    },
    "temp": {
      "source": "/files/temp/",
      "pattern": "{sessionId}/{filename}",
      "read": ["all"],
      "write": ["all"],
      "autoClean": true
    },
    "brandConfig": {
      "source": "/apps/{brandId}/_config/",
      "pattern": "{configFile}",
      "read": ["all"],
      "write": ["brand-manager"]
    },
    "brandDraft": {
      "source": "/apps/{brandId}/draft/",
      "pattern": "{sectionPath}.json",
      "read": ["all"],
      "write": ["content-creator"]
    },
    "brandKit": {
      "source": "/apps/{brandId}/brandkit/",
      "pattern": "{sectionPath}.json",
      "read": ["all"],
      "write": ["brand-manager"]
    },
    "exports": {
      "source": "/apps/{brandId}/exports/",
      "pattern": "{date}/{format}/{filename}",
      "read": ["all"],
      "write": ["content-creator"]
    }
  }
}
```

### Path Resolution API

```typescript
// lib/server/builders/path-resolver.ts

interface PathRequest {
  location: string;       // 'uploads', 'assets', 'brandConfig', etc.
  brandId?: string;
  params: Record<string, string>;  // Fill in pattern variables
}

// Get path for a file location
function getPath(request: PathRequest): string;
function getBasePath(location: string): string;
function resolvePattern(location: string, params: Record<string, string>): string;

// Ensure directory exists (create if not)
function ensureDir(path: string): Promise<void>;
function ensureDirForFile(filePath: string): Promise<void>;  // Gets parent dir

// Check if location exists in registry
function locationExists(location: string): boolean;
function getLocationConfig(location: string): LocationConfig;
```

### Usage

```typescript
// When creating files - Builders know where to put them
const uploadPath = Builders.getPath({
  location: 'uploads',
  brandId: 'mind-the-box',
  params: { type: 'images', filename: 'hero.jpg' }
});
// Returns: /files/uploads/mind-the-box/images/hero.jpg

// When reading files - Services ask Builders for the path
const configPath = Builders.getPath({
  location: 'brandConfig',
  brandId: 'mind-the-box',
  params: { configFile: 'brand.json' }
});
// Returns: /apps/mind-the-box/_config/brand.json

// Then use Reader/fs to actually read the file
const config = await fs.readFile(configPath, 'utf-8');
```

### Flow: Creating vs Reading

```
CREATING FILES:
  Service → Builders.getPath() → Writer.write(path)

READING FILES:
  Service → Builders.getPath() → Reader/fs.read(path)
```

Builders are the single source of truth for WHERE files live. They don't read/write - they just know the locations.

---

## Responsibility

| Does | Does NOT |
|------|----------|
| **Know where files are stored** (registry) | Write data (Writer does) |
| **Provide path resolution** for all services | Format/transform data (Parsers do) |
| Know correct folder structure | Validate data (Archivist does) |
| **Create directory trees** (mkdir directly) | Fetch external data (Fetchers do) |
| Build prompt templates | Read files (Reader/fs does) |

---

## FolderBuilder

Creates directory structures for brands and projects.

### API

```typescript
// lib/server/builders/folder-builder.ts

interface FolderStructure {
  path: string;
  children?: FolderStructure[];
  files?: Array<{ name: string; content: string | object }>;
}

interface BuildResult {
  success: boolean;
  paths: string[];
  error?: string;
}

function buildBrandStructure(brandPath: string, brandConfig: BrandConfig): Promise<BuildResult>;
function buildExportStructure(brandPath: string, exportId: string): Promise<BuildResult>;
function ensureStructure(structure: FolderStructure): Promise<BuildResult>;
function getBrandTemplate(): FolderStructure;
```

### Brand Structure Template

```typescript
const BRAND_STRUCTURE: FolderStructure = {
  path: '{brandPath}',
  children: [
    {
      path: '_config',
      files: [
        { name: 'brand.json', content: { id: '', name: '' } },
        { name: 'progress.json', content: { sections: {} } }
      ]
    },
    {
      path: '_meta',
      files: [
        { name: 'catalog.json', content: { entries: [] } }
      ]
    },
    { path: 'draft' },
    { path: 'brandkit' },
    {
      path: 'sources',
      children: [
        { path: 'typography' },
        { path: 'colours' },
        { path: 'logos' },
        { path: 'images' }
      ]
    },
    { path: 'exports' }
  ]
};
```

### Usage

```typescript
// Create new brand structure
const result = await FolderBuilder.buildBrandStructure(
  '/brands/acme',
  { id: 'acme', name: 'Acme Corp', domain: 'tech' }
);

if (result.success) {
  console.log(`Created ${result.paths.length} directories`);
}
```

---

## PromptBuilder

Assembles prompts for AI generation.

**Note:** This exists in `prompt-builder.ts` from Phase 4.

### API

```typescript
// lib/server/builders/prompt-builder.ts

interface PromptBuildOptions {
  sectionId: string;
  brandPath: string;
  includeContext?: boolean;
  contextSections?: string[];
  mode?: 'generate' | 'refine' | 'expand';
}

interface BuiltPrompt {
  systemPrompt: string;
  userPrompt: string;
  schema?: JSONSchema;
  estimatedTokens: number;
}

function buildSectionPrompt(options: PromptBuildOptions): Promise<BuiltPrompt>;
function buildSystemPrompt(sectionId: string, brandConfig: BrandConfig): string;
function buildUserPrompt(sectionId: string, context: ContextData): string;
function buildRefinePrompt(sectionId: string, currentDraft: unknown, feedback: string): BuiltPrompt;
```

### Current Implementation

Already built in Phase 4.1:
- `buildPrompt()`
- `buildSystemPrompt()`
- `buildUserPrompt()`
- `estimateTokens()`

May need wrapper for consistent interface.

---

## ConfigBuilder

Creates and updates configuration files.

### API

```typescript
// lib/server/builders/config-builder.ts

interface ConfigBuildOptions {
  merge?: boolean;        // Merge with existing
  validate?: boolean;     // Validate against schema
}

interface ConfigBuildResult {
  success: boolean;
  path: string;
  config?: object;
  error?: string;
}

function buildBrandConfig(brandPath: string, data: Partial<BrandConfig>): Promise<ConfigBuildResult>;
function buildProgressConfig(brandPath: string): Promise<ConfigBuildResult>;
function updateConfig<T>(configPath: string, updates: Partial<T>, options?: ConfigBuildOptions): Promise<ConfigBuildResult>;
function mergeConfig<T>(existing: T, updates: Partial<T>): T;
```

### Usage

```typescript
// Create initial brand config
await ConfigBuilder.buildBrandConfig('/brands/acme', {
  id: 'acme',
  name: 'Acme Corporation',
  domain: 'tech',
  website: 'https://acme.com'
});

// Update existing config
await ConfigBuilder.updateConfig('/brands/acme/_config/brand.json', {
  domain: 'saas'
}, { merge: true });
```

---

## ExportBuilder

Assembles export packages.

**Note:** Related to Phase 8 export system.

### API

```typescript
// lib/server/builders/export-builder.ts

interface ExportBuildOptions {
  formats: ExportFormat[];
  sections?: string[];     // Specific sections, or all
  includeHistory?: boolean;
}

interface ExportBundle {
  id: string;
  path: string;
  formats: ExportFormat[];
  files: Array<{ format: ExportFormat; path: string; size: number }>;
  createdAt: string;
}

function buildExportBundle(brandPath: string, options: ExportBuildOptions): Promise<ExportBundle>;
function buildExportManifest(bundle: ExportBundle): object;
```

### Usage

```typescript
// Build multi-format export
const bundle = await ExportBuilder.buildExportBundle('/brands/acme', {
  formats: ['json', 'markdown', 'pdf'],
  includeHistory: true
});

// bundle.path = '/brands/acme/exports/2026-01-16/'
// bundle.files = [
//   { format: 'json', path: '...brandkit.json', size: 12345 },
//   { format: 'markdown', path: '...brandkit.md', size: 8765 },
//   { format: 'pdf', path: '...brandkit.pdf', size: 45678 }
// ]
```

---

## Common Pattern

All builders follow the same pattern:

```typescript
interface BuildResult {
  success: boolean;
  path?: string;       // What was created
  paths?: string[];    // Multiple items created
  error?: string;
}

// Async function - may use Writer internally
async function build(options: Options): Promise<BuildResult>;
```

- **Input:** Configuration/data
- **Output:** Created structure + paths
- **Side effects:** Creates via Writer
- **State:** None (stateless)

---

## Builder vs Writer

| Builder | Writer |
|---------|--------|
| Knows structure | Knows how to write |
| Assembles data | Writes bytes to disk |
| Decides what to create | Doesn't decide anything |
| Uses Writer | Used by Builder |

```typescript
// Builder uses Writer internally
async function buildBrandStructure(brandPath: string, config: BrandConfig) {
  // Builder knows structure
  const dirs = [
    `${brandPath}/_config`,
    `${brandPath}/_meta`,
    `${brandPath}/draft`,
    `${brandPath}/brandkit`
  ];

  // Writer does the writing
  for (const dir of dirs) {
    await Writer.ensureDir(dir);
  }

  await Writer.writeJSON(`${brandPath}/_config/brand.json`, config);
}
```

---

## Implementation Notes

### File Locations

```
lib/server/builders/
  folder-builder.ts
  prompt-builder.ts   (may wrap existing)
  config-builder.ts
  export-builder.ts   (may wrap existing export-manager.ts)
  index.ts            (re-exports all)
```

### Dependencies

| Builder | Dependencies |
|---------|--------------|
| FolderBuilder | Writer |
| PromptBuilder | (none - existing code) |
| ConfigBuilder | Writer, JSON schema |
| ExportBuilder | Writer, exporters |

### Size Estimate

- FolderBuilder: ~150 lines (new)
- PromptBuilder: ~50 lines wrapper (existing: 400+ lines)
- ConfigBuilder: ~120 lines (new)
- ExportBuilder: ~100 lines wrapper (existing: export-manager)

---

## Build Phases

### B1: FolderBuilder
- Brand structure template
- `buildBrandStructure()`
- `ensureStructure()`

### B2: ConfigBuilder
- `buildBrandConfig()`
- `updateConfig()`
- Merge logic

### B3: Builder Wrappers
- PromptBuilder wrapper (around existing)
- ExportBuilder wrapper (around existing)

---

## Testing

| Test | Description |
|------|-------------|
| Build brand structure | Creates all directories |
| Build with existing | Doesn't overwrite |
| Build config | Creates valid JSON |
| Update config | Merges correctly |
| Build prompt | Assembles correctly |
| Build export bundle | Creates all formats |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [ARCHIVIST-PLAN.md](./ARCHIVIST-PLAN.md) - Uses Builder for paths during storage
- [WRITER-PLAN.md](./WRITER-PLAN.md) - Used by builders for disk writes

---

*Builders are the constructors - they know how things should be structured.*
