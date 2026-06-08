# Phase 4: Section Generation - Implementation Plan

> Detailed implementation plan for Phase 4 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [PHASE-3-PLAN.md](./PHASE-3-PLAN.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)

---

## Overview

Phase 4 implements the section generation system - the core workflow for creating brand sections through AI extraction, manual input, or library matching.

| Component | Purpose |
|-----------|---------|
| **4.1 AI Prompt Builder** | Assemble prompts for AI extraction/generation |
| **4.2 Section Save/Load Utilities** | Handle draft ↔ brandkit data movement |
| **4.3 Core Sections Handler** | Special handling for core/identity, core/purpose, core/contact |
| **4.4 Standard Sections Handler** | General section generation with dependencies |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| Brand folder with context | Phase 1 + 2 | Required |
| Domain detection | Phase 3 | Required - determines libraries |
| sources.json manifest | Phase 2 | Required - lists available context |
| progress.json | Phase 1 | Required - tracks section status |

---

## Section Generation Order

From `BrandKit AIGen/_config/generation-order.json`:

### Tier 1: Foundational (No dependencies)
| Order | Section | depends_on |
|-------|---------|------------|
| 1 | `core/identity` | `[]` |
| 2 | `core/purpose` | `["core/identity"]` |
| 3 | `legal/core` | `["core/identity"]` |
| 4 | `core/contact` | `["core/identity"]` |
| 5 | `audience/core` | `["core/identity", "core/purpose"]` |
| 6 | `values/brand` | `["core/purpose"]` |
| 7 | `methodology/philosophy` | `["core/purpose", "values/brand"]` |
| 8 | `legal/entity` | `["legal/core"]` |
| 9 | `audience/professional` | `["audience/core"]` |
| 10 | `audience/supporter` | `["audience/core"]` |

### Tier 2: Voice & Values
| Order | Section | depends_on |
|-------|---------|------------|
| 11 | `voice/personality` | `["core/identity", "values/brand"]` |
| 12 | `voice/tone` | `["voice/personality"]` |
| 13 | `voice/characteristics` | `["voice/personality"]` |
| 14 | `voice/vocabulary` | `["voice/personality", "audience/core"]` |
| 15 | `voice/guidelines` | `["voice/personality", "voice/tone", "voice/characteristics", "voice/vocabulary"]` |
| 16 | `values/commercial` | `["values/brand"]` |
| 17 | `values/client-outcomes` | `["audience/core", "methodology/philosophy"]` |
| 18 | `methodology/core-methodology` | `["methodology/philosophy"]` |
| 19 | `methodology/frameworks` | `["methodology/core-methodology"]` |
| 20 | `communication/principles` | `["voice/guidelines", "values/brand"]` |

### Tier 3-5: Content, Visual, Operations
(30 more sections - see `generation-order.json` for full list)

### Important: Visual Sections Use Different Pathway

**Note:** Visual sections (`visual/*`) are listed in `generation-order.json` for dependency tracking, but they are NOT processed by Phase 4's AI generation pipeline.

Visual sections are handled by **Phase 7: Visual Pathway** which uses:
- CSS token parsing (not AI extraction)
- External builder apps (ColourToken, TypographyBuilder, LogoMaker, ImageBrowser)
- File uploads and processing

When the Section Router (Phase 4.2) encounters a `visual/*` section, it should route to Phase 7 handlers instead of the AI generation pipeline.

---

## Section Status Tracking

**File:** `_meta/progress.json`

```json
{
  "brand_status": "in_progress",
  "core_complete": false,
  "sections": {
    "core/identity": {
      "status": "complete",
      "updated_at": "2026-01-13T10:00:00Z",
      "method": "ai_extraction"
    },
    "core/purpose": {
      "status": "in_progress",
      "updated_at": "2026-01-13T10:15:00Z",
      "method": "manual"
    },
    "voice/tone": {
      "status": "not_started",
      "updated_at": null,
      "method": null
    },
    "voice/vocabulary": {
      "status": "blocked",
      "blocked_by": ["voice/tone"],
      "updated_at": null
    }
  }
}
```

**Status Values:**
- `not_started` - No work done
- `in_progress` - Data in `draft/{section}/`
- `complete` - Data in `brandkit/{section}/`
- `blocked` - Dependencies not met

**Method Values:**
- `ai_extraction` - AI filled from context
- `ai_generation` - AI generated from brand name + domain
- `manual` - User filled manually
- `library` - Selected from library
- `mixed` - Combination of methods

---

## 4.1 AI Prompt Builder

### Objective
Assemble complete prompts for AI to extract or generate section data, combining context, schema, instructions, and examples.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\prompt-builder.ts`

### Core Functions

```typescript
interface PromptOptions {
  brandPath: string;           // Path to brand folder
  section: string;             // Section to generate (e.g., "core/identity")
  mode: 'extract' | 'generate' | 'complete';  // Generation mode
  fieldsFocus?: string[];      // Specific fields to focus on (for partial)
  userContext?: string;        // Additional user-provided context
}

interface PromptResult {
  systemPrompt: string;        // Role and rules
  userPrompt: string;          // Context and instructions
  outputSchema: object;        // Expected JSON structure
  tokens: {
    estimated: number;
    system: number;
    user: number;
  };
}

// Main prompt building function
export async function buildPrompt(options: PromptOptions): Promise<PromptResult>

// Load section config from BrandKit AIGen
export async function loadSectionConfig(section: string): Promise<SectionConfig>

// Load section schema from BrandKit
export async function loadSectionSchema(section: string): Promise<JSONSchema>

// Load section instructions from BrandKit AIGen
export async function loadSectionInstructions(section: string): Promise<Instructions>

// Load examples from BrandKit
export async function loadSectionExamples(section: string): Promise<Example[]>

// Gather context from brand folder
export async function gatherContext(brandPath: string, config: SectionConfig): Promise<ContextData>

// Load completed sections for context
export async function loadCompletedSections(brandPath: string, sections: string[]): Promise<SectionData[]>
```

### Prompt Assembly

```typescript
export async function buildPrompt(options: PromptOptions): Promise<PromptResult> {
  const { brandPath, section, mode, fieldsFocus, userContext } = options;

  // 1. Load section configuration
  const sectionConfig = await loadSectionConfig(section);
  const schema = await loadSectionSchema(section);
  const instructions = await loadSectionInstructions(section);
  const examples = await loadSectionExamples(section);

  // 2. Load brand metadata
  const brandConfig = await loadBrandConfig(brandPath);

  // 3. Gather context based on mode
  let contextData: ContextData;

  if (mode === 'extract' || mode === 'complete') {
    // Load external context (website, documents)
    contextData = await gatherContext(brandPath, sectionConfig);
  }

  // 4. Load other completed sections for context
  const otherSections = sectionConfig.context_sources?.other_sections || [];
  const completedSections = await loadCompletedSections(brandPath, otherSections);

  // 5. Build system prompt
  const systemPrompt = buildSystemPrompt({
    role: sectionConfig.prompt.role,
    rules: sectionConfig.prompt.rules,
    domain: brandConfig.domain
  });

  // 6. Build user prompt
  const userPrompt = buildUserPrompt({
    mode,
    brandName: brandConfig.name,
    domain: brandConfig.domain,
    section,
    fieldsFocus,
    instructions: sectionConfig.prompt.instructions,
    contextData,
    completedSections,
    userContext,
    examples
  });

  // 7. Build output schema
  const outputSchema = buildOutputSchema(schema, fieldsFocus);

  // 8. Estimate tokens
  const tokens = estimateTokens(systemPrompt, userPrompt);

  return { systemPrompt, userPrompt, outputSchema, tokens };
}
```

### System Prompt Template

```typescript
function buildSystemPrompt(options: SystemPromptOptions): string {
  return `You are a ${options.role} helping to define brand identity.

DOMAIN: ${options.domain}

RULES:
${options.rules.map(rule => `- ${rule}`).join('\n')}

OUTPUT FORMAT:
- Return ONLY valid JSON matching the provided schema
- Mark any inferred information with [INFERRED]
- Use null for fields you cannot determine
- Use empty arrays [] for list fields with no items
`;
}
```

### User Prompt Template

```typescript
function buildUserPrompt(options: UserPromptOptions): string {
  let prompt = `## Task
${options.mode === 'extract' ? 'Extract' : 'Generate'} the ${options.section} section for "${options.brandName}".

`;

  // Add instructions
  prompt += `## Instructions
${options.instructions.map(i => `- ${i}`).join('\n')}

`;

  // Add context if available
  if (options.contextData?.content) {
    prompt += `## Brand Context
${options.contextData.content}

`;
  }

  // Add completed sections as context
  if (options.completedSections.length > 0) {
    prompt += `## Completed Sections (Use for Context)
${options.completedSections.map(s => formatSectionForContext(s)).join('\n\n')}

`;
  }

  // Add user context if provided
  if (options.userContext) {
    prompt += `## Additional Notes from User
${options.userContext}

`;
  }

  // Add examples
  if (options.examples.length > 0) {
    prompt += `## Example Output
${JSON.stringify(options.examples[0].data, null, 2)}

`;
  }

  // Add focus fields if partial
  if (options.fieldsFocus?.length > 0) {
    prompt += `## Focus Fields
Only fill these fields: ${options.fieldsFocus.join(', ')}
Leave other fields as null.

`;
  }

  return prompt;
}
```

### Context Gathering

```typescript
interface ContextData {
  content: string;
  sources: string[];
  wordCount: number;
}

async function gatherContext(brandPath: string, config: SectionConfig): Promise<ContextData> {
  const sources: string[] = [];
  let content = '';

  // 1. Load sources manifest
  const sourcesPath = path.join(brandPath, 'context', 'sources.json');
  const manifest = JSON.parse(await fs.readFile(sourcesPath, 'utf-8'));

  // 2. Load website content (if exists and relevant)
  if (config.context_sources?.website) {
    const websitePath = path.join(brandPath, 'context', 'website', 'fetched-content.md');
    if (await fileExists(websitePath)) {
      const websiteContent = await fs.readFile(websitePath, 'utf-8');
      content += `### Website Content\n${websiteContent}\n\n`;
      sources.push('website');
    }
  }

  // 3. Load document content
  for (const source of manifest.sources.filter(s => s.type === 'document' && s.processed)) {
    const textPath = source.metadata?.extracted_text || source.file.replace(/\.[^.]+$/, '.txt');
    const fullPath = path.join(brandPath, 'context', textPath);
    if (await fileExists(fullPath)) {
      const docContent = await fs.readFile(fullPath, 'utf-8');
      content += `### Document: ${source.filename}\n${docContent}\n\n`;
      sources.push(source.filename);
    }
  }

  return {
    content,
    sources,
    wordCount: content.split(/\s+/).length
  };
}
```

### Tasks

- [x] Create `lib/server/prompt-builder.ts`
- [x] Create `lib/server/types/prompt.ts` with interfaces
- [x] Implement section config loading from BrandKit AIGen
- [x] Implement schema loading from BrandKit `_system/`
- [x] Implement instructions loading from BrandKit AIGen
- [x] Implement examples loading from BrandKit `_system/`
- [x] Implement context gathering from brand folder
- [x] Implement completed sections loading
- [x] Implement system prompt building
- [x] Implement user prompt building
- [x] Implement token estimation
- [x] Test with sample section prompts

**4.1 Build Notes:**
- Completed: 2026-01-14
- 50 sections discovered from BrandKit AIGen
- Token estimation: ~1180 tokens for core/identity extraction
- Supports extract/generate/complete modes

### Dependencies
- BrandKit AIGen `sections/` folder
- BrandKit `_system/` folder
- Brand's `context/` folder
- Phase 2 parsers (for extracted text)

---

## 4.2 Section Save/Load Utilities

### Objective
Handle all section data movement between draft and brandkit folders, maintaining proper state tracking.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\section-utils.ts`

### Core Functions

```typescript
interface SectionData {
  section: string;             // e.g., "core/identity"
  data: object;                // Section JSON data
  metadata: {
    method: GenerationMethod;
    created_at: string;
    updated_at: string;
    library_links?: LibraryLink[];
  };
}

type GenerationMethod = 'ai_extraction' | 'ai_generation' | 'manual' | 'library' | 'mixed';

// Save section data to draft
export async function saveToDraft(brandPath: string, sectionData: SectionData): Promise<void>

// Load section data from draft
export async function loadFromDraft(brandPath: string, section: string): Promise<SectionData | null>

// Move section from draft to brandkit (approval)
export async function approveSection(brandPath: string, section: string): Promise<void>

// Copy section from brandkit to draft (for editing)
export async function editSection(brandPath: string, section: string): Promise<void>

// Load section from brandkit
export async function loadFromBrandkit(brandPath: string, section: string): Promise<SectionData | null>

// Check if section exists in draft
export async function draftExists(brandPath: string, section: string): Promise<boolean>

// Check if section exists in brandkit
export async function brandkitExists(brandPath: string, section: string): Promise<boolean>

// Update section status in progress.json
export async function updateSectionStatus(
  brandPath: string,
  section: string,
  status: SectionStatus,
  method?: GenerationMethod
): Promise<void>

// Delete draft section (after approval or cancel)
export async function deleteDraft(brandPath: string, section: string): Promise<void>
```

### Save to Draft

```typescript
export async function saveToDraft(brandPath: string, sectionData: SectionData): Promise<void> {
  const { section, data, metadata } = sectionData;

  // 1. Create draft folder path (e.g., draft/core/identity/)
  const sectionPath = section.replace('/', path.sep);
  const draftPath = path.join(brandPath, 'draft', sectionPath);

  // 2. Ensure folder exists
  await fs.mkdir(draftPath, { recursive: true });

  // 3. Save section data
  const dataPath = path.join(draftPath, 'data.json');
  await fs.writeFile(dataPath, JSON.stringify(data, null, 2));

  // 4. Save metadata
  const metaPath = path.join(draftPath, 'metadata.json');
  await fs.writeFile(metaPath, JSON.stringify({
    ...metadata,
    updated_at: new Date().toISOString()
  }, null, 2));

  // 5. Update progress.json
  await updateSectionStatus(brandPath, section, 'in_progress', metadata.method);
}
```

### Approve Section (Draft → Brandkit)

```typescript
export async function approveSection(brandPath: string, section: string): Promise<void> {
  const sectionPath = section.replace('/', path.sep);
  const draftPath = path.join(brandPath, 'draft', sectionPath);
  const brandkitPath = path.join(brandPath, 'brandkit', sectionPath);

  // 1. Check draft exists
  if (!await fileExists(draftPath)) {
    throw new Error(`No draft found for section: ${section}`);
  }

  // 2. Load draft data
  const draftData = await loadFromDraft(brandPath, section);
  if (!draftData) {
    throw new Error(`Failed to load draft for section: ${section}`);
  }

  // 3. Create brandkit folder
  await fs.mkdir(brandkitPath, { recursive: true });

  // 4. Copy data to brandkit
  await fs.copyFile(
    path.join(draftPath, 'data.json'),
    path.join(brandkitPath, 'data.json')
  );

  // 5. Update metadata with approval timestamp
  const metadata = {
    ...draftData.metadata,
    approved_at: new Date().toISOString()
  };
  await fs.writeFile(
    path.join(brandkitPath, 'metadata.json'),
    JSON.stringify(metadata, null, 2)
  );

  // 6. Update progress.json
  await updateSectionStatus(brandPath, section, 'complete', draftData.metadata.method);

  // 7. Delete draft (optional - or keep for history)
  await deleteDraft(brandPath, section);

  // 8. Check if core complete
  await checkCoreComplete(brandPath);
}
```

### Edit Section (Brandkit → Draft)

```typescript
export async function editSection(brandPath: string, section: string): Promise<void> {
  const sectionPath = section.replace('/', path.sep);
  const brandkitPath = path.join(brandPath, 'brandkit', sectionPath);
  const draftPath = path.join(brandPath, 'draft', sectionPath);

  // 1. Check brandkit exists
  if (!await fileExists(brandkitPath)) {
    throw new Error(`Section not found in brandkit: ${section}`);
  }

  // 2. Check if draft already exists (don't overwrite)
  if (await fileExists(draftPath)) {
    console.log(`Draft already exists for ${section}, using existing`);
    return;
  }

  // 3. Create draft folder
  await fs.mkdir(draftPath, { recursive: true });

  // 4. Copy brandkit to draft
  await fs.copyFile(
    path.join(brandkitPath, 'data.json'),
    path.join(draftPath, 'data.json')
  );

  // 5. Copy and update metadata
  const metadata = JSON.parse(
    await fs.readFile(path.join(brandkitPath, 'metadata.json'), 'utf-8')
  );
  metadata.editing_started = new Date().toISOString();
  await fs.writeFile(
    path.join(draftPath, 'metadata.json'),
    JSON.stringify(metadata, null, 2)
  );

  // 6. Update progress.json
  await updateSectionStatus(brandPath, section, 'in_progress');
}
```

### Core Complete Check

```typescript
const CORE_SECTIONS = ['core/identity', 'core/purpose', 'core/contact'];

async function checkCoreComplete(brandPath: string): Promise<boolean> {
  const progressPath = path.join(brandPath, '_meta', 'progress.json');
  const progress = JSON.parse(await fs.readFile(progressPath, 'utf-8'));

  const allCoreComplete = CORE_SECTIONS.every(
    section => progress.sections[section]?.status === 'complete'
  );

  if (allCoreComplete && !progress.core_complete) {
    progress.core_complete = true;
    progress.core_completed_at = new Date().toISOString();
    await fs.writeFile(progressPath, JSON.stringify(progress, null, 2));

    // Also update brand.json
    const brandConfigPath = path.join(brandPath, '_config', 'brand.json');
    const brandConfig = JSON.parse(await fs.readFile(brandConfigPath, 'utf-8'));
    brandConfig.core_complete = true;
    brandConfig.status = 'complete';
    await fs.writeFile(brandConfigPath, JSON.stringify(brandConfig, null, 2));
  }

  return allCoreComplete;
}
```

### Tasks

- [x] Create `lib/server/section-utils.ts`
- [x] Implement `saveToDraft()`
- [x] Implement `loadFromDraft()`
- [x] Implement `approveSection()` (draft → brandkit)
- [x] Implement `editSection()` (brandkit → draft for editing)
- [x] Implement `loadFromBrandkit()`
- [x] Implement `updateSectionStatus()`
- [x] Implement `deleteDraft()`
- [x] Implement core complete checking
- [x] Test full save/load/approve/edit cycle

**4.2 Build Notes:**
- Completed: 2026-01-14
- Created `lib/server/section-utils.ts` with 12 exported functions
- Created `lib/server/types/section.ts` with type definitions
- Core sections: core/identity, core/purpose, core/contact
- Generation methods: ai_extraction, ai_generation, manual, library, mixed
- Full test passed: save → load → approve → edit → load cycle

---

## 4.3 Core Sections Handler

### Objective
Special handling for the three core sections that are mandatory and must be completed first.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\core-sections.ts`

### Core Sections

| Section | Purpose | Key Fields |
|---------|---------|------------|
| `core/identity` | Brand identification | brand_name, tagline, positioning_statement |
| `core/purpose` | Why brand exists | mission, vision, story, origin |
| `core/contact` | Contact information | email, phone, address, social_links |

### Core Functions

```typescript
interface CoreGenerationOptions {
  brandPath: string;
  section: 'core/identity' | 'core/purpose' | 'core/contact';
  mode: 'extract' | 'generate' | 'manual';
}

interface CoreResult {
  success: boolean;
  data: object;
  method: GenerationMethod;
  confidence: number;
  missingFields: string[];
  inferredFields: string[];
}

// Generate/extract core section
export async function generateCoreSection(options: CoreGenerationOptions): Promise<CoreResult>

// Check if all core sections are complete
export async function areCoreComplete(brandPath: string): Promise<boolean>

// Get core sections status summary
export async function getCoreStatus(brandPath: string): Promise<CoreStatusSummary>

// Validate core section data
export async function validateCoreSection(section: string, data: object): Promise<ValidationResult>

// Mark core as complete (triggers domain confirmation)
export async function markCoreComplete(brandPath: string): Promise<void>
```

### Core Section Generation Flow

```typescript
export async function generateCoreSection(options: CoreGenerationOptions): Promise<CoreResult> {
  const { brandPath, section, mode } = options;

  // 1. Load section configuration
  const config = await loadSectionConfig(section);
  const schema = await loadSectionSchema(section);

  // 2. Check for available context
  const hasContext = await hasAvailableContext(brandPath, config);

  // 3. Determine actual mode
  let actualMode = mode;
  if (mode === 'extract' && !hasContext) {
    actualMode = 'generate';  // Fall back to generation
  }

  // 4. Build prompt
  const prompt = await buildPrompt({
    brandPath,
    section,
    mode: actualMode
  });

  // 5. Call AI (placeholder - actual AI call handled separately)
  const aiResult = await callAI(prompt);

  // 6. Parse and validate result
  const parsed = JSON.parse(aiResult);
  const validation = await validateCoreSection(section, parsed);

  // 7. Identify missing and inferred fields
  const missingFields = findMissingFields(parsed, schema);
  const inferredFields = findInferredFields(parsed);

  // 8. Calculate confidence
  const confidence = calculateConfidence(parsed, schema, hasContext);

  return {
    success: validation.valid,
    data: parsed,
    method: actualMode === 'extract' ? 'ai_extraction' : 'ai_generation',
    confidence,
    missingFields,
    inferredFields
  };
}
```

### Core Validation

```typescript
interface ValidationResult {
  valid: boolean;
  errors: ValidationError[];
  warnings: ValidationWarning[];
}

async function validateCoreSection(section: string, data: object): Promise<ValidationResult> {
  const schema = await loadSectionSchema(section);
  const errors: ValidationError[] = [];
  const warnings: ValidationWarning[] = [];

  // Check required fields
  for (const field of schema.required || []) {
    if (!data[field] || data[field] === null) {
      errors.push({
        field,
        message: `Required field "${field}" is missing`
      });
    }
  }

  // Section-specific validation
  switch (section) {
    case 'core/identity':
      // Brand name must exist
      if (!data['brand_name']) {
        errors.push({ field: 'brand_name', message: 'Brand name is required' });
      }
      // Tagline should be under 10 words
      if (data['tagline'] && data['tagline'].split(' ').length > 10) {
        warnings.push({ field: 'tagline', message: 'Tagline should be under 10 words' });
      }
      break;

    case 'core/purpose':
      // Mission should exist
      if (!data['mission']) {
        errors.push({ field: 'mission', message: 'Mission statement is required' });
      }
      break;

    case 'core/contact':
      // At least one contact method
      const hasContact = data['email'] || data['phone'] || data['address'];
      if (!hasContact) {
        warnings.push({ field: 'contact', message: 'At least one contact method recommended' });
      }
      break;
  }

  return {
    valid: errors.length === 0,
    errors,
    warnings
  };
}
```

### Tasks

- [ ] Create `lib/server/core-sections.ts`
- [ ] Implement `generateCoreSection()`
- [ ] Implement `areCoreComplete()`
- [ ] Implement `getCoreStatus()`
- [ ] Implement `validateCoreSection()` with section-specific rules
- [ ] Implement `markCoreComplete()`
- [ ] Test core section generation flow
- [ ] Test core completion triggering domain confirmation

---

## 4.4 Standard Sections Handler

### Objective
Handle all non-core sections with proper dependency checking, multiple fill methods, and library matching integration.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\standard-sections.ts`

### Core Functions

```typescript
interface SectionGenerationOptions {
  brandPath: string;
  section: string;
  mode: 'extract' | 'generate' | 'manual' | 'library';
  fieldsFocus?: string[];      // For partial fills
  librarySelections?: LibrarySelection[];  // Pre-selected library entries
}

interface SectionResult {
  success: boolean;
  data: object;
  method: GenerationMethod;
  partialFields: PartialField[];  // Fields that need user input
  libraryMatches: LibraryMatch[]; // Suggested library matches
}

interface PartialField {
  field: string;
  status: 'filled' | 'missing' | 'inferred';
  value?: any;
  options: FillOption[];
}

type FillOption = 'manual' | 'library' | 'ai_complete';

// Check if section can be started (dependencies met)
export async function canStartSection(brandPath: string, section: string): Promise<DependencyResult>

// Generate/extract section
export async function generateSection(options: SectionGenerationOptions): Promise<SectionResult>

// Get next recommended sections
export async function getRecommendedSections(brandPath: string): Promise<SectionRecommendation[]>

// Get section's available fill methods
export async function getAvailableMethods(brandPath: string, section: string): Promise<FillMethod[]>
```

### Dependency Checking

```typescript
interface DependencyResult {
  canStart: boolean;
  missingDependencies: string[];
  completedDependencies: string[];
  recommendation: string | null;
}

export async function canStartSection(brandPath: string, section: string): Promise<DependencyResult> {
  // 1. Load section config
  const config = await loadSectionConfig(section);
  const dependencies = config.depends_on || [];

  // 2. Load progress
  const progress = await loadProgress(brandPath);

  // 3. Check each dependency
  const completed: string[] = [];
  const missing: string[] = [];

  for (const dep of dependencies) {
    if (progress.sections[dep]?.status === 'complete') {
      completed.push(dep);
    } else {
      missing.push(dep);
    }
  }

  // 4. Build result
  return {
    canStart: missing.length === 0,
    missingDependencies: missing,
    completedDependencies: completed,
    recommendation: missing.length > 0
      ? `Complete ${missing[0]} first`
      : null
  };
}
```

### Section Generation

```typescript
export async function generateSection(options: SectionGenerationOptions): Promise<SectionResult> {
  const { brandPath, section, mode, fieldsFocus, librarySelections } = options;

  // 1. Check dependencies
  const depCheck = await canStartSection(brandPath, section);
  if (!depCheck.canStart) {
    throw new Error(`Cannot start ${section}: missing ${depCheck.missingDependencies.join(', ')}`);
  }

  // 2. Load section configuration
  const config = await loadSectionConfig(section);
  const schema = await loadSectionSchema(section);

  // 3. Handle different modes
  let data: object;
  let method: GenerationMethod;

  switch (mode) {
    case 'extract':
    case 'generate':
      // Build prompt and call AI
      const prompt = await buildPrompt({ brandPath, section, mode, fieldsFocus });
      const aiResult = await callAI(prompt);
      data = JSON.parse(aiResult);
      method = mode === 'extract' ? 'ai_extraction' : 'ai_generation';
      break;

    case 'library':
      // Use library selections as base
      data = buildFromLibrarySelections(librarySelections, schema);
      method = 'library';
      break;

    case 'manual':
      // Return empty structure for manual fill
      data = buildEmptyStructure(schema);
      method = 'manual';
      break;
  }

  // 4. Identify partial fields
  const partialFields = analyzePartialFields(data, schema, config);

  // 5. Find library matches for filled fields
  const libraryMatches = await findLibraryMatches(brandPath, section, data);

  return {
    success: true,
    data,
    method,
    partialFields,
    libraryMatches
  };
}
```

### Get Recommended Sections

```typescript
interface SectionRecommendation {
  section: string;
  priority: 'recommended' | 'available' | 'blocked';
  reason: string;
  dependencies: {
    met: string[];
    missing: string[];
  };
}

export async function getRecommendedSections(brandPath: string): Promise<SectionRecommendation[]> {
  // 1. Load all section configs (from generation-order.json)
  const generationOrder = await loadGenerationOrder();
  const progress = await loadProgress(brandPath);

  const recommendations: SectionRecommendation[] = [];

  // 2. Go through all sections in order
  for (const tier of Object.values(generationOrder.tiers)) {
    for (const sectionInfo of tier.sections) {
      const section = sectionInfo.section;

      // Skip completed sections
      if (progress.sections[section]?.status === 'complete') continue;

      // Check dependencies
      const depCheck = await canStartSection(brandPath, section);

      if (depCheck.canStart) {
        // Find if this is the next logical step
        const isNext = recommendations.filter(r => r.priority === 'recommended').length === 0;

        recommendations.push({
          section,
          priority: isNext ? 'recommended' : 'available',
          reason: isNext ? 'Next in sequence' : 'Dependencies met',
          dependencies: {
            met: depCheck.completedDependencies,
            missing: []
          }
        });
      } else {
        recommendations.push({
          section,
          priority: 'blocked',
          reason: `Needs: ${depCheck.missingDependencies.join(', ')}`,
          dependencies: {
            met: depCheck.completedDependencies,
            missing: depCheck.missingDependencies
          }
        });
      }
    }
  }

  return recommendations;
}
```

### Tasks

- [ ] Create `lib/server/standard-sections.ts`
- [ ] Implement `canStartSection()` with dependency checking
- [ ] Implement `generateSection()` for all modes
- [ ] Implement `getRecommendedSections()`
- [ ] Implement `getAvailableMethods()`
- [ ] Implement partial field analysis
- [ ] Integrate with library matcher (Phase 5)
- [ ] Test with various section configurations

---

## Implementation Order

```
4.1 AI Prompt Builder
         ↓
4.2 Section Save/Load Utilities
         ↓
4.3 Core Sections Handler
         ↓
4.4 Standard Sections Handler
```

**Recommended sequence:**
1. Build 4.1 first (prompt building is foundation)
2. Build 4.2 (needed for saving results)
3. Build 4.3 (core sections are first user flow)
4. Build 4.4 (general sections, builds on 4.1-4.3)

---

## Verification Plan

### 4.1 AI Prompt Builder
- [x] Load section config from BrandKit AIGen
- [x] Load schema from BrandKit `_system/`
- [x] Gather context from brand folder
- [x] Build complete prompt with all components
- [x] Token estimation is reasonable

### 4.2 Section Save/Load Utilities
- [x] Save section to draft creates correct folder structure
- [x] Load from draft returns correct data
- [x] Approve section moves data to brandkit
- [x] Edit section copies brandkit to draft
- [x] Progress.json updates correctly
- [x] Core complete triggers when all 3 done

### 4.3 Core Sections Handler
- [x] Generate core/identity with context → extracts brand name
- [x] Generate core/purpose → creates mission/vision
- [x] Validate catches missing required fields
- [x] Core complete check works correctly

### 4.4 Standard Sections Handler
- [ ] Dependency check blocks sections correctly
- [ ] Generate with extraction mode works
- [ ] Generate with library selections works
- [ ] Recommended sections list is logical
- [ ] Partial fields identified correctly

---

## Files to Create (Summary)

| File | Purpose |
|------|---------|
| `lib/server/prompt-builder.ts` | AI prompt assembly |
| `lib/server/types/prompt.ts` | Prompt interfaces |
| `lib/server/section-utils.ts` | Section save/load/approve |
| `lib/server/core-sections.ts` | Core section handling |
| `lib/server/standard-sections.ts` | General section handling |
| `lib/server/types/section.ts` | Section interfaces |

---

## Integration Points

### With Phase 3 (Domain Detection)
- Domain affects library availability
- Domain passed to prompts for context

### With Phase 5 (Library System)
- Library matches suggested for fields
- User selections saved with section

### With AI System (TBD)
- Prompt builder outputs ready for AI
- AI responses parsed and validated
- AI call handling is separate concern

---

## Notes

- AI call execution is handled separately (TBD in SCRIPTS-MAP)
- Prompt builder creates prompts; doesn't call AI directly
- Section save/load is synchronous JSON file operations
- Core sections have stricter validation than standard
- Dependencies are hard blocks; context_sources are soft recommendations

---

## Build Notes (2026-01-14)

### AI Client (Added as 4.3 in BUILD-CHECKLIST)
Built multi-provider AI client supporting:
- Groq (llama-3.3-70b-versatile) - default, fastest
- OpenAI (gpt-4o)
- Google AI (gemini-2.5-flash)
- OpenRouter (various free models)

Files created:
- `lib/server/ai-client.ts` - callAI(), generateSection(), testAIConnection()
- `lib/server/types/ai-client.ts` - Provider configs, request/response types

### Core Sections Handler (4.3b)
Built complete handler for core sections:
- `lib/server/core-sections.ts` - Validation, generation, approval, status
- `lib/server/types/core-sections.ts` - Type definitions

Features:
- Required field validation (brand_name, mission_statement, website_url)
- [INFERRED] marker detection with confidence scoring
- Orchestrated generation of all 3 core sections
- Status tracking (getCoreStatus, getCoreSectionsNeeded)

Test scripts in `scripts/`:
- `test-ai.ts` - AI model comparison tool
- `test-core-sections.ts` - Core sections handler tests

### Section Router (4.4)
Built complete routing system for sections:
- `lib/server/section-router.ts` - Routing, dependencies, availability
- `lib/server/types/section-router.ts` - Type definitions

Features:
- Pathway routing: 35 AI, 10 Visual, 4 Legal sections
- Dependency checking with generation-order.json
- Available sections detection (ready vs blocked)
- Progress tracking by pathway and tier
- Dependency tree analysis (transitive dependencies)

Key functions:
- `getSectionPathway()` - Returns 'ai' | 'visual' | 'legal'
- `canGenerateSection()` - Check dependencies satisfied
- `getSectionsReadyForGeneration()` - Get available/blocked/complete sections
- `getNextRecommendedSections()` - Top N by generation order
- `getGenerationProgress()` - Progress by pathway and tier

Test script: `scripts/test-section-router.ts`

### Response Processor (4.5)
Built complete response processing pipeline:
- `lib/server/response-processor.ts` - Main processing module
- `lib/server/types/response-processor.ts` - Type definitions

Features:
- JSON parsing with 4 extraction methods (direct, markdown_block, object_extract, repair)
- JSON repair: close brackets, trailing commas, quote keys, single-to-double quotes
- Schema validation: required fields, type checking, nested object/array validation
- Inferred marker extraction: [INFERRED] and [INFERRED: reason] formats
- Partialness analysis: completeness %, missing required/optional tracking
- Full pipeline: `processResponse()` combines all steps with confidence scoring

Key functions:
- `parseJSON()` - Parse with error recovery
- `validateAgainstSchema()` - Validate against section schema
- `extractInferredFields()` - Extract and clean inferred markers
- `analyzePartialness()` - Analyze response completeness
- `processResponse()` - Full processing pipeline

Test script: `scripts/test-response-processor.ts`

### Enhancement Features (Post-4.5)
Added quality-of-life improvements to Phase 4:

**1. npm Test Scripts**
- Added to `package.json`: test:ai, test:core, test:router, test:response, test:all
- Also added: batch (batch-generate), typecheck (tsc --noEmit)

**2. Section-to-Model Mapping**
- Created `_config/section-models.json` - configures AI models by section/pathway
- Added `getModelForSection(sectionId)` to ai-client.ts
- Returns `{ provider, model, source }` with resolution: override → pathway → fallback

**3. Schema Validation at Save**
- Added validation to `saveToDraft()` - validates before writing
- Added validation to `approveSection()` - re-validates before approval
- Added `skipValidation` option and `validationErrors` return field
- Ensures brand data integrity throughout save/approve flow

**4. Batch Generation Script**
- Created `scripts/batch-generate.ts` - CLI for bulk section generation
- Uses section-router for dependency order and availability checking
- Supports filters: `--tier`, `--pathway`, `--sections`
- Supports modes: `--dry-run`, `--auto-approve`, `--continue-on-error`

**Documentation:**
See [DOC-INDEX.md](../DOC-INDEX.md) for usage examples and configuration details.
