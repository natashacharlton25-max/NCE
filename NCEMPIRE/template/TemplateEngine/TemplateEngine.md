# TemplateEngine Module

> **Purpose:** Orchestrate content generation: read template → AI fills sections → hand to renderer
> **Type:** Template System Module
> **Status:** Approved
> **Version:** 1.1.0

---

## Overview

TemplateEngine is the orchestration layer for content generation. It reads a template (an ordered list of section types), coordinates with AI services to fill each section, and hands the assembled content to a Renderer for final output.

```
Template → TemplateEngine → Filled Sections → Renderer → Final Output
```

**Kitchen Analogy:** TemplateEngine is the head chef coordinating a meal. The recipe (template) says what courses to prepare, the chef directs each station (AI, Image systems) to prepare their part, then hands everything to plating (Renderer) for final presentation.

---

## Roles & Rules

### TemplateEngine DOES:
- Read templates from SQLite database (`/data/templates.db`)
- Load section specs from `/repos/section-specs/`
- Coordinate section filling through PromptBuilder and AICaller
- Assemble filled sections in template order
- Select and invoke appropriate Renderer
- Pass context (brand, audience, topic) through the pipeline
- Handle section variants (e.g., image sizes for different platforms)

### TemplateEngine does NOT:
- Build prompts directly (PromptBuilder does that)
- Call AI directly (AICaller does that)
- Parse AI responses (Parsers does that)
- Render final output (Renderers do that)
- Modify templates (TemplateFactory does that)
- Validate brand/audience data (those modules validate themselves)
- Generate outlines (OutlineGenerator does that)

### Boundaries:
- Read-only access to own template database (writes via TemplateFactory)
- Cannot bypass section specs
- Must use registered Renderers only

---

## Functions (Public)

| Function | Input | Output | Description |
|----------|-------|--------|-------------|
| `initialize()` | - | initResult | Validate renderer availability, load section spec index |
| `run(templateId, context)` | templateId, context | renderedOutput | Main entry: template → fill → render |
| `fillSections(sections, context, options?)` | sections[], context, options | filledSections[] | Fill sections without rendering (for preview) |
| `handToRenderer(renderer, data)` | renderer, data | outputPath | Pass filled sections to specific renderer |

---

## Options Type Definitions

### RunContext

```javascript
{
  brandId: string,              // required - which brand context to use
  audienceId?: string,          // optional - target audience
  themeId?: string,             // optional - theme from ThemeGenerator
  outlineId?: string,           // optional - pre-generated outline from OutlineGenerator
  contentType: string,          // required - "workbook", "blog-post", "email", etc.
  topic: string,                // required - what the content is about
  variant?: string,             // optional - e.g., "instagram-post" for sizing
  renderer: string,             // required - which renderer to use
  renderOptions?: object,       // optional - renderer-specific options

  // Optional overrides
  overrides?: {
    [sectionIndex: number]: {   // Override specific sections
      content?: object,         // Pre-filled content (skip AI)
      hints?: string            // Additional hints for AI
    }
  }
}
```

### FilledSection

```javascript
{
  type: string,                 // Section type (e.g., "title", "body", "cta")
  index: number,                // Position in template
  spec: object,                 // Section spec that was used
  content: object,              // AI-generated content matching spec fields
  metadata: {
    promptTokens: number,
    completionTokens: number,
    model: string,
    cached: boolean             // Whether content came from cache
  }
}
```

### RenderOutput

```javascript
{
  success: boolean,
  runId: string,                // Unique run identifier for tracing (e.g., "tmpl-20260119-9f3a")
  renderer: string,             // Which renderer was used
  outputPath?: string,          // For file-based outputs
  outputId?: string,            // For cloud-based outputs (Canva designId, Google docId)
  previewUrl?: string,          // Direct link to view output (if available)
  format: string,               // Output format (pdf, docx, png, etc.)
  sections: number,             // Number of sections rendered
  metadata: {
    totalTokens: number,
    totalCost: number,
    renderTime: number          // ms
  }
}
```

**Note:** For cloud-based renderers (Canva, Google), `previewUrl` must be a **view-only link** to prevent accidental edits before user is ready. Edit access is granted separately via the platform's native sharing.

### FillSectionsOptions

```javascript
{
  allowPartial?: boolean,       // default: false - if true, return partial results on error
  continueOnError?: boolean,    // default: false - if true, try remaining sections after failure
  forceRefresh?: boolean        // default: false - if true, bypass cache
}
```

**Note:** `allowPartial` and `continueOnError` are for **debugging/preview only**. `run()` always uses `allowPartial: false` to ensure complete documents.

### Template

```javascript
{
  id: string,                    // e.g., "template_entrepreneur-workbook"
  name: string,                  // Human-readable name
  description?: string,          // What this template is for
  contentType: string,           // "workbook", "blog-post", "email", etc.
  sections: string[],            // Ordered array of section types ["title", "body", "exercise"]

  // Metadata
  createdAt: string,             // ISO timestamp
  updatedAt: string,             // ISO timestamp
  createdBy?: string,            // User/system that created

  // Status
  status: "active" | "draft" | "archived",
  version: string                // Template version (e.g., "1.0.0")
}
```

**Note:** Template CRUD operations (create, update, delete, list, search) are handled by **TemplateFactory**. TemplateEngine only reads templates via `run()` for execution.

**Version Archiving:** When a template's structure changes (sections added/removed/reordered), TemplateFactory should archive the previous version. This enables OutlineGenerator outlines to reference their original template version via `templateSnapshot.version`, allowing FailureHandler to load archived templates when handling `OUTLINE_TEMPLATE_DRIFT_DETECTED` errors.

---

## Detailed Function Specs

### initialize()

Called on system startup to initialize template database, validate renderer availability, and build section spec index.

```javascript
// Input
(none)

// Process
1. Check template database exists at config.database.path
2. If not exists: create database file via DatabaseHandler
3. Create tables if not exist (templates, templates_fts)
4. Validate schema version, run migrations if needed
5. Scan /repos/section-specs/ for available files
6. Validate each file is readable JSON (header check, not full parse)
7. Build section spec index (type → filepath mapping)
8. Check each registered renderer is available
9. Return readiness report

// Note: Full spec parsing happens on first use (lazy load with memory cache)
// This ensures fast startup while validating file existence

// Output (success)
{
  success: true,
  database: "/data/templates.db",
  templateCount: 25,
  schemaVersion: "1.1.0",
  sectionSpecs: 15,             // Number of section specs indexed
  renderers: {
    available: ["PDFRenderer", "MarkdownRenderer", "HTMLRenderer"],
    unavailable: ["CanvaRenderer"]  // e.g., missing API key
  }
}

// Output (error)
{
  success: false,
  error: {
    code: "DATABASE_INIT_FAILED",
    message: "Could not create database at /data/templates.db",
    cause: "Permission denied"
  }
}
```

**Rationale:** SQLite provides fast queries, full-text search, and better organization than flat files. Pre-loading all specs is slow and wastes memory. Hybrid approach validates existence on startup, loads content on demand.

---

### run(templateId, context)

Main entry point. Loads template, fills all sections, hands to renderer.

```javascript
// Input
{
  templateId: "template_entrepreneur-workbook",
  context: {
    brandId: "brand_acme",
    audienceId: "audience_entrepreneurs",
    themeId: "theme_productivity",
    outlineId: "outline_mtb_workbook_time-mgmt",  // optional - pre-generated outline
    contentType: "workbook",
    topic: "Time Management for Founders",
    variant: null,
    renderer: "PDFRenderer",
    renderOptions: {
      pageSize: "letter",
      margins: "normal"
    }
  }
}

// Process
1. Generate runId (e.g., "tmpl-20260119-9f3a")
2. Load template from database: SELECT * FROM templates WHERE id = ?
3. Validate template exists
4. Check renderer availability (fail-fast before AI calls)
5. Load brand context from /repos/brands/{brandId}.json
6. Load audience context (if provided) from /repos/audiences/{audienceId}.json
7. Load theme context (if provided) via ThemeGenerator.get(themeId)
8. Load outline (if provided) via OutlineGenerator.get(outlineId)
9. Call fillSections(template.sections, context, outline)
10. Call handToRenderer(context.renderer, filledSections)
11. Return render result with runId

// Output (success)
{
  success: true,
  runId: "tmpl-20260119-9f3a",
  renderer: "PDFRenderer",
  outputPath: "/exports/entrepreneur-workbook-time-management.pdf",
  format: "pdf",
  sections: 5,
  metadata: {
    totalTokens: 2450,
    totalCost: 0.0245,
    renderTime: 1523
  }
}

// Output (error - template not found)
{
  success: false,
  error: {
    code: "TEMPLATE_NOT_FOUND",
    message: "Template 'template_entrepreneur-workbook' not found",
    templateId: "template_entrepreneur-workbook"
  }
}
```

---

### fillSections(sections, context)

Process each section through the AI pipeline. Can be called directly for preview without rendering.

```javascript
// Input
{
  sections: ["title", "body", "body", "exercise", "closing"],
  context: {
    brandId: "brand_acme",
    audienceId: "audience_entrepreneurs",
    themeId: "theme_productivity",
    outlineId: "outline_mtb_workbook_time-mgmt",
    contentType: "workbook",
    topic: "Time Management for Founders",
    variant: null,
    overrides: {
      0: { hints: "Make the title punchy and action-oriented" }
    }
  },
  outline: { /* loaded outline object if outlineId was provided */ }
}

// Process
For each section in order:
1. Load section spec from /repos/section-specs/{sectionType}.json
2. Check for override content (skip AI if provided)
3. Get section guidance from outline (if provided)
4. Build prompt via PromptBuilder.build({
     sectionSpec: spec,
     brand: brandContext,
     audience: audienceContext,
     theme: themeContext,
     outline: outline?.sections[index],  // Pre-generated guidance for this section
     contentType: context.contentType,
     topic: context.topic,
     variant: context.variant,
     hints: override?.hints
   })
5. Call AICaller.generate(prompt)
6. Parse response via Parsers.parseSection(response, spec)
7. Validate parsed content matches spec fields
8. Store filled section

// Output (success)
{
  success: true,
  sections: [
    {
      type: "title",
      index: 0,
      spec: { /* section spec */ },
      content: {
        title: "Master Your Minutes",
        subtitle: "A Founder's Guide to Reclaiming Your Time"
      },
      metadata: {
        promptTokens: 245,
        completionTokens: 28,
        model: "claude-3-haiku",
        cached: false
      }
    },
    // ... more sections
  ],
  totalTokens: 2450,
  totalCost: 0.0245
}

// Output (error - section spec not found)
{
  success: false,
  error: {
    code: "SECTION_SPEC_NOT_FOUND",
    message: "Section spec 'exercise' not found",
    sectionType: "exercise",
    sectionIndex: 3
  }
}
```

---

### handToRenderer(renderer, data)

Pass filled sections to a specific renderer for output generation.

```javascript
// Input
{
  renderer: "PDFRenderer",
  data: {
    sections: [ /* filled sections array */ ],
    context: {
      brandId: "brand_acme",
      topic: "Time Management for Founders"
    },
    options: {
      pageSize: "letter",
      margins: "normal"
    }
  }
}

// Process
1. Validate renderer is registered and available
2. Get renderer instance
3. Call renderer.render(data.sections, data.options)
4. Return renderer result

// Output (success)
{
  success: true,
  renderer: "PDFRenderer",
  outputPath: "/exports/entrepreneur-workbook-time-management.pdf",
  format: "pdf"
}

// Output (error - renderer unavailable)
{
  success: false,
  error: {
    code: "RENDERER_UNAVAILABLE",
    message: "Renderer 'CanvaRenderer' is not available",
    renderer: "CanvaRenderer",
    reason: "API key not configured"
  }
}
```

---

## Configuration

```json
{
  "module": "TemplateEngine",
  "version": "1.1.0",
  "description": "Orchestrate content generation from templates",
  "type": "template-system",

  "config": {
    "database": {
      "path": "/data/templates.db",
      "schemaVersion": "1.1.0"
    },

    "repos": {
      "sectionSpecs": "/repos/section-specs/",
      "brands": "/repos/brands/",
      "audiences": "/repos/audiences/"
    },

    "renderers": {
      "registered": [
        "CanvaRenderer",
        "GoogleDocsRenderer",
        "GoogleSheetsRenderer",
        "GoogleSlidesRenderer",
        "ImageRenderer",
        "PDFRenderer",
        "DOCXRenderer",
        "MarkdownRenderer",
        "HTMLRenderer"
      ],
      "default": "PDFRenderer"
    },

    "ai": {
      "defaultModel": "claude-3-haiku",
      "maxRetries": 2,
      "cacheEnabled": true,
      "cacheTTL": {
        "default": 3600000,           // 1 hour default
        "allowSectionOverride": true  // Section specs can define their own cacheTTL
      }
    },

    "validation": {
      "strictFieldMatching": true,
      "allowExtraFields": false,
      "requireAllRequired": true
    },

    "parallelSections": {
      "enabled": true,
      "maxConcurrent": 3
    }
  }
}
```

### Config Explained

| Setting | Value | Purpose |
|---------|-------|---------|
| `database.path` | /data/templates.db | SQLite database for templates |
| `database.schemaVersion` | 1.1.0 | Current schema version |
| `repos.sectionSpecs` | /repos/section-specs/ | Where section specs live |
| `renderers.registered` | [...] | Available renderers |
| `renderers.default` | PDFRenderer | Default if none specified |
| `ai.defaultModel` | claude-3-haiku | Default model for section filling |
| `ai.cacheEnabled` | true | Cache identical section requests |
| `ai.cacheTTL.default` | 1 hour | Default cache duration |
| `ai.cacheTTL.allowSectionOverride` | true | Section specs can define their own TTL |
| `validation.strictFieldMatching` | true | AI output must match spec fields exactly |
| `parallelSections.enabled` | true | Fill independent sections in parallel |
| `parallelSections.maxConcurrent` | 3 | Max parallel AI calls |

---

## SQLite Schema

```sql
-- Main templates table
CREATE TABLE templates (
  id TEXT PRIMARY KEY,                    -- e.g., "template_entrepreneur-workbook"
  name TEXT NOT NULL,                     -- Human-readable name
  description TEXT,                       -- What this template is for
  content_type TEXT NOT NULL,             -- "workbook", "blog-post", "email", etc.
  sections TEXT NOT NULL,                 -- JSON array of section types

  -- Metadata
  created_at TEXT DEFAULT (datetime('now')),
  updated_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,                        -- User/system that created this

  -- Status
  status TEXT DEFAULT 'active',           -- "active", "archived", "draft"
  version TEXT DEFAULT '1.0.0'            -- Template version
);

-- Full-text search index for template discovery
CREATE VIRTUAL TABLE templates_fts USING fts5(
  id,
  name,
  description,
  content_type,
  content='templates',
  content_rowid='rowid'
);

-- Trigger to keep FTS in sync on INSERT
CREATE TRIGGER templates_ai AFTER INSERT ON templates BEGIN
  INSERT INTO templates_fts(rowid, id, name, description, content_type)
  VALUES (new.rowid, new.id, new.name, new.description, new.content_type);
END;

-- Trigger to keep FTS in sync on UPDATE
CREATE TRIGGER templates_au AFTER UPDATE ON templates BEGIN
  DELETE FROM templates_fts WHERE rowid = old.rowid;
  INSERT INTO templates_fts(rowid, id, name, description, content_type)
  VALUES (new.rowid, new.id, new.name, new.description, new.content_type);
END;

-- Trigger to keep FTS in sync on DELETE
CREATE TRIGGER templates_ad AFTER DELETE ON templates BEGIN
  DELETE FROM templates_fts WHERE rowid = old.rowid;
END;

-- Index for common queries
CREATE INDEX idx_templates_content_type ON templates(content_type);
CREATE INDEX idx_templates_status ON templates(status);
```

### Template Record Example

```javascript
{
  id: "template_entrepreneur-workbook",
  name: "Entrepreneur Workbook",
  description: "Multi-section workbook for business planning and exercises",
  content_type: "workbook",
  sections: ["title", "body", "body", "exercise", "closing"],
  created_at: "2026-01-19T10:30:00Z",
  updated_at: "2026-01-19T10:30:00Z",
  created_by: "admin",
  status: "active",
  version: "1.0.0"
}
```

### Schema Migrations

When schema changes:
1. **Backup first**: DatabaseHandler creates `templates.db.bak` before any migration
2. DatabaseHandler manages migrations via version tracking
3. Migrations are forward-only (no downgrades)
4. Migrations run automatically on `initialize()` if needed
5. Migration scripts stored in `/migrations/templates/`
6. On failure: restore from backup, log error, return DATABASE_MIGRATION_FAILED

---

## Error Handling

| Error Code | Cause | Recovery |
|------------|-------|----------|
| `TEMPLATE_NOT_FOUND` | Template ID doesn't exist in database | Check templateId spelling |
| `SECTION_SPEC_NOT_FOUND` | Section type has no spec | Add spec to /repos/section-specs/ |
| `BRAND_NOT_FOUND` | Brand ID doesn't exist | Check brandId |
| `AUDIENCE_NOT_FOUND` | Audience ID doesn't exist | Check audienceId or omit |
| `THEME_NOT_FOUND` | ThemeGenerator.get() failed | Check themeId or generate new theme |
| `OUTLINE_NOT_FOUND` | OutlineGenerator.get() failed | Check outlineId or omit |
| `RENDERER_UNAVAILABLE` | Renderer not available (missing config/API key) | Configure renderer or use different one |
| `RENDERER_NOT_REGISTERED` | Renderer not in registered list | Add to config or use registered renderer |
| `AI_GENERATION_FAILED` | AICaller failed to generate content | Check AICaller logs, retry |
| `PARSE_FAILED` | Parsers couldn't extract fields from AI response | Check section spec, retry with clearer hints |
| `FIELD_VALIDATION_FAILED` | AI output doesn't match spec requirements | Retry or adjust spec constraints |
| `DATABASE_INIT_FAILED` | Could not create/initialize database | Check permissions, disk space |
| `DATABASE_MIGRATION_FAILED` | Schema migration failed | Check migration scripts, rollback |
| `MODULE_NOT_READY` | initialize() not called | Call initialize() on startup |

---

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Parallel section filling | Yes (configurable) | Independent sections can fill concurrently for speed |
| AI response caching | Yes | Same section + context = same output, save costs |
| Strict field validation | Yes | AI output must match spec, ensures renderer compatibility |
| Override support | Yes | Allow pre-filled sections for hybrid workflows |
| Renderer selection | Per-request | Different outputs may need different renderers |

---

## Operational Guarantees

### 1. Section Order Preservation

Sections are always rendered in template-defined order, regardless of parallel filling:

```javascript
// Template
["title", "body", "body", "exercise", "closing"]

// fillSections may process in parallel but returns in order:
[
  { index: 0, type: "title", ... },
  { index: 1, type: "body", ... },
  { index: 2, type: "body", ... },
  { index: 3, type: "exercise", ... },
  { index: 4, type: "closing", ... }
]
```

**Rationale:** Document structure must match template intent.

### 2. Fail-Fast on Missing Specs

If any section spec is missing, fail immediately before calling AI:

```javascript
// Validation order
1. Load template
2. For each section type in template:
   - Check section spec exists
   - If missing → fail with SECTION_SPEC_NOT_FOUND
3. Only after all specs validated → start filling
```

**Rationale:** Don't waste AI calls if we can't complete the document.

### 3. Override Priority

Overrides take precedence in this order:
1. Pre-filled content (skip AI entirely)
2. Override hints (add to prompt)
3. Default section spec

```javascript
// Override handling
if (override?.content) {
  // Skip AI, use provided content directly
  return { type, index, content: override.content, metadata: { cached: true } };
}

if (override?.hints) {
  // Add hints to prompt
  prompt = PromptBuilder.build({ ...baseParams, hints: override.hints });
}
```

### 4. Partial Success Not Allowed (Production Mode)

Either all sections fill successfully, or the entire operation fails:

```javascript
// fillSections default behavior (allowPartial: false)
- If any section fails → return error (no partial results)
- If all succeed → return all filled sections

// Debug mode (allowPartial: true)
- If section fails → record error, continue with remaining
- Return partial results with status per section
- run() NEVER uses debug mode
```

**Rationale:** A document missing sections is worse than no document. Debug mode exists only for development/preview.

### 5. Renderer Availability Check

Validate renderer availability before filling sections:

```javascript
// run() validation order
1. Load template
2. Check renderer availability
3. If unavailable → fail immediately (don't fill sections)
4. Fill sections
5. Render
```

**Rationale:** Don't waste AI calls if we can't render the result.

### 6. Context Immutability

Context passed to sections is read-only. Sections cannot modify context for subsequent sections:

```javascript
// Each section receives a copy of context
// No shared mutable state between section fills
// Ensures deterministic, parallelizable execution
```

### 7. Cache Key Composition

Cache keys include all inputs that affect output:

```javascript
cacheKey = hash({
  sectionType: "title",
  sectionSpec: specHash,
  brandId: "brand_acme",
  audienceId: "audience_entrepreneurs",
  themeId: "theme_productivity",
  contentType: "workbook",
  topic: "Time Management for Founders",
  variant: null,
  hints: null,
  model: "claude-3-haiku"
});
```

**Rationale:** Same inputs = same output. Different inputs = different cache entry. Including `contentType` ensures a "title" section for a "workbook" doesn't share cache with a "title" for an "email".

### 8. Image Field Routing

Image fields in section specs are **not** filled by AICaller. They route to the image subsystem:

```javascript
// When processing a section with image fields:
1. Detect field type === "image"
2. Get allowed sources from spec (brand-library, unsplash, ai-generated)
3. Get size from spec.sizes[variant] or spec.fields.image.size
4. Route to appropriate module:
   - "ai-generated" → AIImageCreation (uses /repos/image-prompts/)
   - "unsplash" → ImageBrowser
   - "brand-library" → ImageCollections

// TemplateEngine coordinates, does not generate images
```

**Flow:**
```
Section spec says: "image": { "type": "image", "source": ["ai-generated", "unsplash"] }

TemplateEngine:
1. Asks AICaller for image description/keywords (text field)
2. Passes description + size to image subsystem
3. Receives image path/URL back
4. Includes in filled section content
```

**Rationale:** Image generation has its own prompts, templates, and sizing logic. TemplateEngine orchestrates but doesn't duplicate that complexity.

---

## Constraints & Assumptions

### Section Independence (v1)

In v1, all sections are treated as independent and can be filled in parallel. Sections cannot reference content from other sections.

**Future consideration:** Some use cases may need section dependencies (e.g., "closing" summarizes "body" sections). This would require:
- Dependency declaration in template
- Sequential filling for dependent sections
- Passing prior section content to PromptBuilder

### Section Spec Contract

Section specs must define:
- `id`: Unique identifier
- `name`: Human-readable name
- `fields`: Object with field definitions

Field definitions must include:
- `type`: Field type (text, image, url, array, number, input-area)
- `required`: Boolean

### Renderer Contract

All renderers must implement:
- `render(sections[], options)` → output path/id
- `isAvailable()` → boolean

### AI Response Format

AICaller must return responses that Parsers can extract into spec-defined fields. PromptBuilder ensures AI receives correct output format instructions.

---

## Used By

| Module | Function | Purpose |
|--------|----------|---------|
| **Orchestrator** | run() | Main content generation workflow |
| **ContentManager** | fillSections() | Preview content before rendering |
| **Builders** | run() | Document/email/social generation |

---

## Dependencies

| Depends On | For |
|------------|-----|
| DatabaseHandler | Direct access to templates.db (own database) |
| FileManager | Read section specs, brand/audience data (external repos) |
| ThemeGenerator | Load theme context via get() |
| OutlineGenerator | Load outline context via get() |
| PromptBuilder | Build prompts for each section |
| AICaller | Generate text content for each section |
| Parsers | Extract fields from AI responses |
| CacheHandler | Cache section fill results |
| AIImageCreation | Generate AI images for image fields |
| ImageBrowser | Fetch images from Unsplash |
| ImageCollections | Fetch images from brand library |
| Renderers | Produce final output |

**Data Access Pattern:**
- **Own database**: Direct via DatabaseHandler (TemplateEngine reads templates.db)
- **Other repos**: Via FileManager (brands, audiences, section-specs)
- **Discovery**: Librarian (cross-system search, list, recommend)
- **Template writes**: TemplateFactory (create, update, delete)

---

## Build Notes

*Notes for implementation and future updates:*

### Version Compatibility (Dev Team Gap - 2026-01-20)
| Dependency | Min Version | Notes |
|------------|-------------|-------|
| templates.library | 1.0.0+ | Template structure for rendering |
| outlines.library | 1.0.0+ | Outline with sections for content |
| section-specs | 1.0.0+ | Section type definitions |
| brands.library | 1.0.0+ | Brand context via FileManager |
| audiences.library | 1.0.0+ | Audience context via FileManager |

### Cross-Library Integrity
- Calls OutlineGenerator.incrementUsage() after successful render
- Calls ThemeGenerator.incrementUsage() after successful render

### Social Templates Integration (Dev Team Gap - 2026-01-20)
- **Platform-specific rendering**: Social templates (from social-templates.library) require platform-aware rendering
- Character limit enforcement per platform (Twitter: 280, LinkedIn: 3000, Instagram: 2200)
- Image aspect ratio requirements per platform
- Hashtag and mention handling
- Consider: SocialMediaRenderer module or platform variants in handToRenderer()

### ContentValidator Integration (Dev Team Gap - 2026-01-20)
- Call ContentValidator.validate() after section filling
- Pass: filled content + theme + outline
- Handle: validation failures (regenerate or flag for review)

### Paragraph Rendering Integration (Layer 4 - 2026-01-20)

TemplateEngine renders paragraphs using Layer 4 paragraph structures:

```
┌─────────────────────────────────────────────────────────────────┐
│                  PARAGRAPH RENDERING FLOW                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  For each section in outline:                                   │
│                                                                  │
│  1. Load paragraph structure from section.paragraphStructure    │
│     (e.g., "pearl" → PEARL structure definition)                │
│                                                                  │
│  2. Load cognitive level variant                                │
│     (e.g., Level 3 → 20 word max sentences, no jargon)          │
│                                                                  │
│  3. For each paragraph in section.paragraphBreakdown:           │
│     a. Load component guidance for this structure               │
│     b. Generate each component:                                 │
│        - Point: "Here's what happens..."                        │
│        - Example: "You're in a meeting and..."                  │
│        - Application: "Your nervous system..."                  │
│        - Reflection: "Notice: this response..."                 │
│        - Link: "Now we'll explore..."                           │
│     c. Apply brand voice to each component                      │
│     d. Assemble into paragraph                                  │
│     e. Validate all components present                          │
│                                                                  │
│  4. Assemble paragraphs into section                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Key Structures:**

| Structure | Use For | Components |
|-----------|---------|------------|
| **PEARL** | Mechanism, definition, explanation | Point → Example → Application → Reflection → Link |
| **SAFE** | Reflection, reframe, opening | State → Anchor → Frame → Extend |
| **CER** | Evidence-backed claims | Claim → Evidence → Reasoning |

**Implementation Notes:**
- Load paragraph structures via Librarian
- Apply cognitive level variants to component guidance
- Build prompts component-by-component for coherent paragraph structure
- Validate each component is present in generated output

**Dependencies:**
- Paragraph Structures Library (read): Structure definitions + cognitive variants

---

*Spec version: 1.1.1*
*Created: 2026-01-19*
*Updated: 2026-01-19 - Added SQLite storage, outlineId support, Template type definition*
*Status: Approved*
