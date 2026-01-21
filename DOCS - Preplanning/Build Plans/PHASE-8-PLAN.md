# Phase 8: Export - Implementation Plan

> **Status:** ✅ Complete | **Completed:** 2026-01-16
> Detailed implementation plan for Phase 8 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)

---

## Overview

Phase 8 implements the export system - generating various output formats from completed brand data. Export is a key deliverable that allows users to:
- Share brand guidelines with clients/teams
- Use brand data in other tools and AI assistants
- Archive completed brand work

| Component | Purpose |
|-----------|---------|
| **8.1 Export Router** | Determine available exports and route to appropriate generator |
| **8.2 JSON Export** | Full structured data export for developer/API use |
| **8.3 Markdown Export** | Human-readable brand guide document |
| **8.4 PDF Export** | Formatted brand guidelines document for sharing |
| **8.5 Prompt Pack Export** | Optimised prompts for AI assistants |
| **8.6 Export Manager** | Track exports, versioning, and output file management |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| Completed brandkit sections | Phase 4 | Required - data to export |
| Section save/load utilities | Phase 4 | Required - read completed sections |
| Brand status utilities | Phase 1.5 | Required - check completion status |
| Global config | Phase 1.1 | Required - export paths |

---

## Export Triggers

| Trigger | When | Behaviour |
|---------|------|-----------|
| **Manual Export** | User clicks export button | Export selected formats |
| **Completion Export** | All sections marked complete | Prompt user to export |
| **Partial Export** | User requests before completion | Export available sections with warnings |
| **Re-export** | After editing existing section | Update existing exports |

---

## Export Formats

| Format | Use Case | Contents |
|--------|----------|----------|
| **JSON** | Developer/API use | Raw brandkit JSON structure |
| **Markdown** | Documentation | Human-readable brand guide |
| **PDF** | Client sharing | Formatted brand guidelines document |
| **Prompt Pack** | AI tools | Optimised prompts for AI assistants |

---

## Export Completion Criteria

### Minimum Requirements by Format

| Format | Minimum Required | Why |
|--------|------------------|-----|
| **JSON** | Any 1 complete section | JSON is flexible, any data is valid |
| **Markdown** | `core/identity` | Need at least brand name for document |
| **PDF** | `core/identity` + `core/purpose` | PDF needs meaningful content |
| **Prompt Pack** | `core/identity` + `voice/tone` | AI needs identity and tone context |

### What Happens With Incomplete Brands

| Scenario | Behaviour |
|----------|-----------|
| No complete sections | Export button disabled |
| Some sections complete | Export available, shows what's included |
| All core complete, others missing | Full export available with gaps noted |
| Drafts exist | Can include with `includeDrafts: true` (labelled as DRAFT) |

### Draft Export Behaviour

When `includeDrafts: true` is set:
- Draft sections ARE included in export
- JSON: Draft sections have `"_status": "draft"` flag
- Markdown: Draft sections have "DRAFT" watermark header
- PDF: Draft sections shown in different colour/style
- Prompt Pack: Draft content marked with `[DRAFT]` prefix

Default: `includeDrafts: false` - only complete sections exported.

---

## 8.1 Export Router

### Objective
Determine what can be exported, route requests to appropriate generators, and orchestrate multi-format exports.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\export-router.ts`

### Core Interfaces

```typescript
type ExportFormat = 'json' | 'markdown' | 'pdf' | 'prompt_pack';

interface ExportRequest {
  brandPath: string;
  formats: ExportFormat[];
  options: ExportOptions;
}

interface ExportOptions {
  includeAllCompleted: boolean;    // Include all completed sections
  includeDrafts: boolean;          // Include draft sections (marked as draft)
  includeLibraryRefs: boolean;     // Include library reference data
  sectionsToInclude?: string[];    // Specific sections (if not all)
  outputPath?: string;             // Override default exports folder
}

interface ExportAvailability {
  canExport: boolean;
  availableFormats: ExportFormat[];
  completedSections: string[];
  draftSections: string[];
  missingSections: string[];
  warnings: string[];
}

interface ExportResult {
  success: boolean;
  format: ExportFormat;
  outputPath: string;
  fileSize: number;
  sectionsIncluded: string[];
  warnings: string[];
  errors: string[];
}

interface MultiExportResult {
  overall_success: boolean;
  results: ExportResult[];
  exportedAt: string;
}
```

### Core Functions

```typescript
// Check what can be exported for a brand
export async function checkExportAvailability(
  brandPath: string
): Promise<ExportAvailability>

// Route and execute export request
export async function executeExport(
  request: ExportRequest
): Promise<MultiExportResult>

// Get export history for a brand
export async function getExportHistory(
  brandPath: string
): Promise<ExportRecord[]>

// Clean up old exports
export async function cleanupExports(
  brandPath: string,
  keepLatest: number
): Promise<void>
```

### Export Availability Logic

```typescript
export async function checkExportAvailability(
  brandPath: string
): Promise<ExportAvailability> {
  const progress = await getBrandStatus(brandPath);
  const warnings: string[] = [];

  // Categorise sections
  const completedSections: string[] = [];
  const draftSections: string[] = [];
  const missingSections: string[] = [];

  for (const [section, status] of Object.entries(progress.sections)) {
    if (status.status === 'complete') {
      completedSections.push(section);
    } else if (status.status === 'in_progress') {
      draftSections.push(section);
    } else {
      missingSections.push(section);
    }
  }

  // Check minimum requirements
  const coreComplete = completedSections.includes('core/identity') &&
                       completedSections.includes('core/purpose');

  if (!coreComplete) {
    warnings.push('Core sections (identity, purpose) must be complete to export');
  }

  // All formats available if core complete
  const availableFormats: ExportFormat[] = coreComplete
    ? ['json', 'markdown', 'pdf', 'prompt_pack']
    : [];

  return {
    canExport: coreComplete,
    availableFormats,
    completedSections,
    draftSections,
    missingSections,
    warnings
  };
}
```

### Multi-Format Export

```typescript
export async function executeExport(
  request: ExportRequest
): Promise<MultiExportResult> {
  const { brandPath, formats, options } = request;
  const results: ExportResult[] = [];

  // 1. Load all section data
  const sectionData = await loadSectionsForExport(brandPath, options);

  // 2. Generate each requested format
  for (const format of formats) {
    try {
      let result: ExportResult;

      switch (format) {
        case 'json':
          result = await generateJSONExport(brandPath, sectionData, options);
          break;
        case 'markdown':
          result = await generateMarkdownExport(brandPath, sectionData, options);
          break;
        case 'pdf':
          result = await generatePDFExport(brandPath, sectionData, options);
          break;
        case 'prompt_pack':
          result = await generatePromptPackExport(brandPath, sectionData, options);
          break;
      }

      results.push(result);
    } catch (error) {
      results.push({
        success: false,
        format,
        outputPath: '',
        fileSize: 0,
        sectionsIncluded: [],
        warnings: [],
        errors: [error.message]
      });
    }
  }

  // 3. Update export history
  await updateExportHistory(brandPath, results);

  return {
    overall_success: results.every(r => r.success),
    results,
    exportedAt: new Date().toISOString()
  };
}
```

### Tasks

- [x] Create `lib/server/export-router.ts`
- [x] Create `lib/server/types/export.ts`
- [x] Implement export availability checking
- [x] Implement multi-format export orchestration
- [x] Create export history tracking
- [x] Add export cleanup for old files

### Dependencies
- Phase 1.5 Brand Status Utilities
- Phase 4 Section Save/Load

---

## 8.2 JSON Export

### Objective
Export complete brandkit data as structured JSON for developer/API use.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\exporters\json-exporter.ts`

### Output Structure

```json
{
  "meta": {
    "brand_name": "Brand Name",
    "brand_slug": "brand-name",
    "domain": "therapeutic",
    "exported_at": "2026-01-13T10:30:00Z",
    "export_version": "1.0",
    "sections_included": ["core/identity", "core/purpose", "voice/tone", "..."],
    "sections_draft": [],
    "sections_missing": ["visual/photography", "operations/calendar"]
  },
  "brandkit": {
    "core": {
      "identity": {
        "brand_name": "Brand Name",
        "tagline": "Your tagline here",
        "positioning_statement": "For [audience]..."
      },
      "purpose": {
        "mission": "...",
        "vision": "...",
        "values": ["..."]
      },
      "contact": {
        "email": "...",
        "phone": "..."
      }
    },
    "voice": {
      "personality": { "..." },
      "tone": { "..." },
      "vocabulary": { "..." }
    },
    "audience": { "..." },
    "values": { "..." },
    "visual": { "..." },
    "legal": { "..." }
  },
  "libraries": {
    "linked_entries": [
      {
        "library": "universal/tone",
        "entry_id": "warm",
        "field": "voice/tone.primary_tone"
      }
    ]
  }
}
```

### Core Functions

```typescript
interface JSONExportOptions extends ExportOptions {
  prettyPrint?: boolean;           // Format with indentation (default: true)
  includeLibraries?: boolean;      // Include library reference data
  excludeInternalFields?: boolean; // Remove _meta, _draft fields
}

// Generate JSON export
export async function generateJSONExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: JSONExportOptions
): Promise<ExportResult>

// Build export meta section
export function buildExportMeta(
  brandPath: string,
  sections: string[],
  drafts: string[],
  missing: string[]
): ExportMeta

// Clean section data for export (remove internal fields)
export function cleanSectionData(data: object): object

// Merge all sections into brandkit structure
export function mergeToExportStructure(
  sections: SectionDataMap
): BrandkitExport
```

### Implementation

```typescript
export async function generateJSONExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: JSONExportOptions
): Promise<ExportResult> {
  const { prettyPrint = true, includeLibraries = false, excludeInternalFields = true } = options;

  // 1. Build meta section
  const brandConfig = await loadBrandConfig(brandPath);
  const availability = await checkExportAvailability(brandPath);

  const meta: ExportMeta = {
    brand_name: brandConfig.name,
    brand_slug: brandConfig.id,
    domain: brandConfig.domain,
    exported_at: new Date().toISOString(),
    export_version: '1.0',
    sections_included: availability.completedSections,
    sections_draft: options.includeDrafts ? availability.draftSections : [],
    sections_missing: availability.missingSections
  };

  // 2. Clean and merge section data
  const cleanedSections: SectionDataMap = {};
  for (const [section, data] of Object.entries(sectionData)) {
    cleanedSections[section] = excludeInternalFields
      ? cleanSectionData(data)
      : data;
  }

  const brandkit = mergeToExportStructure(cleanedSections);

  // 3. Build export object
  const exportData: any = { meta, brandkit };

  // 4. Include libraries if requested
  if (includeLibraries) {
    exportData.libraries = await loadLibraryLinks(brandPath);
  }

  // 5. Write to file
  const outputPath = path.join(brandPath, 'exports', `${brandConfig.id}-brandkit.json`);
  const content = prettyPrint
    ? JSON.stringify(exportData, null, 2)
    : JSON.stringify(exportData);

  await fs.mkdir(path.dirname(outputPath), { recursive: true });
  await fs.writeFile(outputPath, content, 'utf-8');

  const stats = await fs.stat(outputPath);

  return {
    success: true,
    format: 'json',
    outputPath,
    fileSize: stats.size,
    sectionsIncluded: availability.completedSections,
    warnings: [],
    errors: []
  };
}
```

### Tasks

- [x] Create `lib/server/exporters/json-exporter.ts`
- [x] Implement meta section builder
- [x] Implement section data cleaner
- [x] Implement section merger (flat sections → nested structure)
- [x] Add library reference inclusion
- [x] Add `parseJSONExport()` for re-import
- [x] Add `getExportSummary()` for export overview

### Dependencies
- Phase 4 Section Save/Load

---

## 8.3 Markdown Export

### Objective
Generate human-readable brand guide document in Markdown format.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\exporters\markdown-exporter.ts`

### Output Structure

```markdown
# Brand Name - Brand Guidelines

> Generated on 13 January 2026

---

## Core Identity

### Brand Name
**Brand Name:** Brand Name
**Tagline:** Your tagline here

### Positioning
For [audience], Brand Name is the [category] that [benefit] because [reason].

---

## Voice & Tone

### Primary Tone
**Warm** - Friendly, approachable, caring

### Characteristics
- Use "we" not "I"
- Active voice preferred
- Conversational but professional

### Do's
- Use inclusive language
- Keep sentences concise
- Lead with benefits

### Don'ts
- Avoid jargon
- Don't use "synergy" or "leverage"
- Never condescend

---

## Audience

### Primary Audience
[Audience description]

---

## Visual Guidelines

### Colours
| Role | Name | Hex | Usage |
|------|------|-----|-------|
| Primary | Sage Green | #8FA68A | Main brand colour, CTAs |
| Secondary | Ocean Blue | #6B8E9F | Supporting elements |

### Typography
| Role | Font | Weights |
|------|------|---------|
| Heading | Playfair Display | 400, 700 |
| Body | Inter | 400, 500, 600 |

---

## Legal

### Copyright
© 2026 Brand Name. All rights reserved.

### Disclaimer
[Disclaimer text]

---

*Generated by BrandKit Workflow*
```

### Core Functions

```typescript
interface MarkdownExportOptions extends ExportOptions {
  includeTableOfContents?: boolean;
  includeVisualAssets?: boolean;     // Embed images as base64 or references
  sectionOrder?: string[];           // Custom section ordering
}

// Generate Markdown export
export async function generateMarkdownExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: MarkdownExportOptions
): Promise<ExportResult>

// Generate markdown for a specific section
export function generateSectionMarkdown(
  section: string,
  data: object
): string

// Generate table of contents
export function generateTableOfContents(
  sections: string[]
): string

// Format value for markdown (handle arrays, objects, etc.)
export function formatValueForMarkdown(
  value: any,
  depth: number
): string
```

### Section Templates

```typescript
const SECTION_TEMPLATES: Record<string, (data: object) => string> = {
  'core/identity': (data) => `
## Core Identity

### Brand Name
**Brand Name:** ${data.brand_name}
${data.tagline ? `**Tagline:** ${data.tagline}` : ''}

### Positioning
${data.positioning_statement || '*Not defined*'}
`,

  'voice/tone': (data) => `
## Voice & Tone

### Primary Tone
**${data.primary_tone?.name || 'Not defined'}** - ${data.primary_tone?.description || ''}

### Characteristics
${formatList(data.characteristics)}

### Do's
${formatList(data.usage_do)}

### Don'ts
${formatList(data.usage_dont)}
`,

  'visual/colours': (data) => `
## Colour Palette

| Role | Name | Hex | Usage |
|------|------|-----|-------|
${data.palettes?.map(p =>
  p.colours.map(c => `| ${c.role || ''} | ${c.name} | ${c.hex} | ${c.use || ''} |`).join('\n')
).join('\n')}

### Accessibility
${formatList(data.accessibility?.passing_combinations)}
`,

  'legal/disclaimers': (data) => `
## Legal

### Copyright
${data.copyright || '*Not defined*'}

### Disclaimer
${data.disclaimers?.general || '*Not defined*'}
`
};
```

### Implementation

```typescript
export async function generateMarkdownExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: MarkdownExportOptions
): Promise<ExportResult> {
  const { includeTableOfContents = true, sectionOrder } = options;

  const brandConfig = await loadBrandConfig(brandPath);
  const sections = sectionOrder || Object.keys(sectionData);

  let markdown = `# ${brandConfig.name} - Brand Guidelines\n\n`;
  markdown += `> Generated on ${formatDate(new Date())}\n\n`;
  markdown += `---\n\n`;

  // Generate table of contents
  if (includeTableOfContents) {
    markdown += generateTableOfContents(sections);
    markdown += `\n---\n\n`;
  }

  // Generate each section
  for (const section of sections) {
    const data = sectionData[section];
    if (!data) continue;

    const template = SECTION_TEMPLATES[section];
    if (template) {
      markdown += template(data);
    } else {
      // Fallback: auto-generate from data
      markdown += generateGenericSectionMarkdown(section, data);
    }
    markdown += `\n---\n\n`;
  }

  markdown += `*Generated by BrandKit Workflow*\n`;

  // Write to file
  const outputPath = path.join(brandPath, 'exports', `${brandConfig.id}-guide.md`);
  await fs.mkdir(path.dirname(outputPath), { recursive: true });
  await fs.writeFile(outputPath, markdown, 'utf-8');

  const stats = await fs.stat(outputPath);

  return {
    success: true,
    format: 'markdown',
    outputPath,
    fileSize: stats.size,
    sectionsIncluded: sections,
    warnings: [],
    errors: []
  };
}
```

### Tasks

- [x] Create `lib/server/exporters/markdown-exporter.ts`
- [x] Create section templates for all major sections (core/identity, core/purpose, voice/tone, visual/colours, visual/typography, values/core)
- [x] Implement table of contents generator
- [x] Implement generic section formatter (fallback)
- [x] Handle arrays, objects, nested data in markdown
- [x] Format lists, tables, and complex structures

### Dependencies
- Phase 4 Section Save/Load

---

## 8.4 PDF Export

### Objective
Generate formatted PDF brand guidelines document suitable for client sharing.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\exporters\pdf-exporter.ts`

### Approach Options

| Option | Pros | Cons |
|--------|------|------|
| **Markdown → PDF** | Simple, uses existing markdown | Limited styling |
| **HTML → PDF (Puppeteer)** | Full styling control | Heavier dependency |
| **PDF library (pdf-lib)** | No browser dependency | More code for layouts |

**Recommended:** Markdown → HTML → PDF using Puppeteer (or similar headless browser)

### Core Interfaces

```typescript
interface PDFExportOptions extends ExportOptions {
  template?: 'modern' | 'classic' | 'minimal';
  pageSize?: 'A4' | 'Letter';
  includeColourSwatches?: boolean;
  includeCoverPage?: boolean;
  brandColours?: boolean;           // Use brand colours in PDF design
}

interface PDFTemplate {
  css: string;
  coverPage: (brand: BrandConfig) => string;
  headerFooter: (brand: BrandConfig) => { header: string; footer: string };
}
```

### Core Functions

```typescript
// Generate PDF export
export async function generatePDFExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: PDFExportOptions
): Promise<ExportResult>

// Convert markdown to styled HTML
export function markdownToStyledHTML(
  markdown: string,
  template: PDFTemplate,
  brandConfig: BrandConfig
): string

// Generate PDF from HTML
export async function htmlToPDF(
  html: string,
  outputPath: string,
  options: PDFOptions
): Promise<void>

// Get PDF template
export function getPDFTemplate(templateName: string): PDFTemplate
```

### PDF Templates

```typescript
const PDF_TEMPLATES: Record<string, PDFTemplate> = {
  modern: {
    css: `
      body {
        font-family: 'Inter', sans-serif;
        line-height: 1.6;
        color: #1a1a1a;
      }
      h1 { font-size: 2.5rem; font-weight: 700; }
      h2 { font-size: 1.75rem; border-bottom: 2px solid var(--primary-color); }
      .colour-swatch {
        display: inline-block;
        width: 60px;
        height: 60px;
        border-radius: 8px;
      }
      table { width: 100%; border-collapse: collapse; }
      th, td { padding: 12px; text-align: left; border-bottom: 1px solid #eee; }
    `,
    coverPage: (brand) => `
      <div class="cover-page">
        <h1>${brand.name}</h1>
        <p class="subtitle">Brand Guidelines</p>
        <p class="date">Generated ${formatDate(new Date())}</p>
      </div>
    `,
    headerFooter: (brand) => ({
      header: `<div class="header">${brand.name} - Brand Guidelines</div>`,
      footer: `<div class="footer">Page <span class="pageNumber"></span> | Confidential</div>`
    })
  },

  classic: {
    css: `
      body {
        font-family: 'Georgia', serif;
        line-height: 1.8;
        color: #333;
      }
      h1 { font-size: 2rem; text-transform: uppercase; letter-spacing: 2px; }
      h2 { font-size: 1.5rem; border-bottom: 1px solid #999; }
    `,
    coverPage: (brand) => `
      <div class="cover-page classic">
        <h1>${brand.name}</h1>
        <hr />
        <p>Brand Guidelines</p>
      </div>
    `,
    headerFooter: (brand) => ({
      header: '',
      footer: `<div class="footer classic">${brand.name} | Page <span class="pageNumber"></span></div>`
    })
  },

  minimal: {
    css: `
      body {
        font-family: 'Helvetica', sans-serif;
        line-height: 1.5;
        color: #000;
      }
      h1, h2 { font-weight: 400; }
    `,
    coverPage: (brand) => `<h1>${brand.name}</h1>`,
    headerFooter: () => ({ header: '', footer: '' })
  }
};
```

### Implementation

```typescript
export async function generatePDFExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: PDFExportOptions
): Promise<ExportResult> {
  const {
    template = 'modern',
    pageSize = 'A4',
    includeColourSwatches = true,
    includeCoverPage = true
  } = options;

  // 1. Generate markdown first
  const markdownResult = await generateMarkdownExport(brandPath, sectionData, {
    ...options,
    includeTableOfContents: true
  });

  const markdown = await fs.readFile(markdownResult.outputPath, 'utf-8');

  // 2. Convert to HTML
  const brandConfig = await loadBrandConfig(brandPath);
  const pdfTemplate = getPDFTemplate(template);
  const html = markdownToStyledHTML(markdown, pdfTemplate, brandConfig);

  // 3. Add cover page if requested
  let fullHTML = html;
  if (includeCoverPage) {
    fullHTML = pdfTemplate.coverPage(brandConfig) + fullHTML;
  }

  // 4. Generate PDF
  const outputPath = path.join(brandPath, 'exports', `${brandConfig.id}-guidelines.pdf`);
  await htmlToPDF(fullHTML, outputPath, {
    pageSize,
    ...pdfTemplate.headerFooter(brandConfig)
  });

  const stats = await fs.stat(outputPath);

  return {
    success: true,
    format: 'pdf',
    outputPath,
    fileSize: stats.size,
    sectionsIncluded: Object.keys(sectionData),
    warnings: [],
    errors: []
  };
}

async function htmlToPDF(
  html: string,
  outputPath: string,
  options: PDFOptions
): Promise<void> {
  // Using Puppeteer (or alternative like Playwright)
  const puppeteer = await import('puppeteer');
  const browser = await puppeteer.launch({ headless: true });
  const page = await browser.newPage();

  await page.setContent(html, { waitUntil: 'networkidle0' });

  await page.pdf({
    path: outputPath,
    format: options.pageSize,
    printBackground: true,
    displayHeaderFooter: !!(options.header || options.footer),
    headerTemplate: options.header || '',
    footerTemplate: options.footer || '',
    margin: { top: '60px', bottom: '60px', left: '40px', right: '40px' }
  });

  await browser.close();
}
```

### Tasks

- [x] Create `lib/server/exporters/pdf-exporter.ts`
- [x] Create PDF templates (modern, classic, minimal)
- [x] Implement HTML generation with brand styling
- [x] Implement optional puppeteer PDF conversion (graceful fallback to HTML)
- [x] Add brand colour CSS variables
- [x] Add cover page generation
- [x] Add section styling and headers

### Dependencies
- 8.3 Markdown Exporter
- npm: puppeteer (optional - falls back to HTML export if unavailable)

---

## 8.5 Prompt Pack Export

### Objective
Generate optimised prompts for AI assistants (ChatGPT, Claude, etc.) that capture brand voice and guidelines.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\exporters\prompt-exporter.ts`

### Output Structure

```
exports/{brand}-prompts/
├── system-prompt.md          # Full system prompt for AI assistants
├── voice-guide.md            # Voice & tone reference
├── quick-reference.md        # Key points summary (condensed)
└── writing-checklist.md      # Checklist for content review
```

### Prompt Templates

**system-prompt.md:**
```markdown
You are writing content for {Brand Name}. Follow these brand guidelines strictly.

## Brand Identity
- **Name:** {Brand Name}
- **Tagline:** {Tagline}
- **Domain:** {Domain}

## Voice & Tone
- **Primary Tone:** {Tone} - {Description}
- **Formality Level:** {X}/10
- **Always use:** "we" not "I", active voice

## Key Characteristics
{Characteristics list}

## Do's
{Do list}

## Don'ts
{Don't list}

## Vocabulary
### Preferred Terms
{Preferred terms}

### Terms to Avoid
{Avoided terms}

## Audience
{Audience description}

## Content Guidelines
{Additional guidelines}

---
Remember: You are representing {Brand Name}. Maintain consistency with these guidelines in all responses.
```

**quick-reference.md:**
```markdown
# {Brand Name} Quick Reference

**Tone:** {Primary tone}, {Secondary tone}
**Voice:** {Key voice characteristics in 1-2 lines}
**Avoid:** {Top 3-5 things to avoid}
**CTAs:** {CTA style guidance}
**Sign-off:** {How to end communications}
```

### Core Functions

```typescript
interface PromptPackOptions extends ExportOptions {
  includeSystemPrompt?: boolean;
  includeVoiceGuide?: boolean;
  includeQuickReference?: boolean;
  includeChecklist?: boolean;
  maxLength?: number;              // Max characters for prompts (for token limits)
  targetAI?: 'chatgpt' | 'claude' | 'generic';
}

// Generate prompt pack export
export async function generatePromptPackExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: PromptPackOptions
): Promise<ExportResult>

// Generate system prompt
export function generateSystemPrompt(
  brandConfig: BrandConfig,
  sectionData: SectionDataMap,
  targetAI: string
): string

// Generate voice guide
export function generateVoiceGuide(
  sectionData: SectionDataMap
): string

// Generate quick reference
export function generateQuickReference(
  brandConfig: BrandConfig,
  sectionData: SectionDataMap
): string

// Generate writing checklist
export function generateWritingChecklist(
  sectionData: SectionDataMap
): string

// Condense content to fit token limits
export function condenseForTokenLimit(
  content: string,
  maxLength: number
): string
```

### Implementation

```typescript
export async function generatePromptPackExport(
  brandPath: string,
  sectionData: SectionDataMap,
  options: PromptPackOptions
): Promise<ExportResult> {
  const {
    includeSystemPrompt = true,
    includeVoiceGuide = true,
    includeQuickReference = true,
    includeChecklist = true,
    targetAI = 'generic'
  } = options;

  const brandConfig = await loadBrandConfig(brandPath);
  const outputDir = path.join(brandPath, 'exports', `${brandConfig.id}-prompts`);
  await fs.mkdir(outputDir, { recursive: true });

  const files: string[] = [];

  // 1. System prompt
  if (includeSystemPrompt) {
    const systemPrompt = generateSystemPrompt(brandConfig, sectionData, targetAI);
    await fs.writeFile(path.join(outputDir, 'system-prompt.md'), systemPrompt);
    files.push('system-prompt.md');
  }

  // 2. Voice guide
  if (includeVoiceGuide) {
    const voiceGuide = generateVoiceGuide(sectionData);
    await fs.writeFile(path.join(outputDir, 'voice-guide.md'), voiceGuide);
    files.push('voice-guide.md');
  }

  // 3. Quick reference
  if (includeQuickReference) {
    const quickRef = generateQuickReference(brandConfig, sectionData);
    await fs.writeFile(path.join(outputDir, 'quick-reference.md'), quickRef);
    files.push('quick-reference.md');
  }

  // 4. Writing checklist
  if (includeChecklist) {
    const checklist = generateWritingChecklist(sectionData);
    await fs.writeFile(path.join(outputDir, 'writing-checklist.md'), checklist);
    files.push('writing-checklist.md');
  }

  return {
    success: true,
    format: 'prompt_pack',
    outputPath: outputDir,
    fileSize: await calculateDirectorySize(outputDir),
    sectionsIncluded: Object.keys(sectionData),
    warnings: [],
    errors: []
  };
}

function generateSystemPrompt(
  brandConfig: BrandConfig,
  sectionData: SectionDataMap,
  targetAI: string
): string {
  const identity = sectionData['core/identity'] || {};
  const voice = sectionData['voice/personality'] || {};
  const tone = sectionData['voice/tone'] || {};
  const vocabulary = sectionData['voice/vocabulary'] || {};
  const audience = sectionData['audience/core'] || {};

  let prompt = `You are writing content for ${brandConfig.name}. Follow these brand guidelines strictly.\n\n`;

  // Brand identity
  prompt += `## Brand Identity\n`;
  prompt += `- **Name:** ${identity.brand_name || brandConfig.name}\n`;
  if (identity.tagline) prompt += `- **Tagline:** ${identity.tagline}\n`;
  prompt += `- **Domain:** ${brandConfig.domain}\n\n`;

  // Voice & tone
  prompt += `## Voice & Tone\n`;
  if (tone.primary_tone) {
    prompt += `- **Primary Tone:** ${tone.primary_tone.name} - ${tone.primary_tone.description}\n`;
  }
  if (tone.formality_level) {
    prompt += `- **Formality Level:** ${tone.formality_level}/10\n`;
  }
  prompt += `\n`;

  // Characteristics
  if (voice.characteristics?.length) {
    prompt += `## Key Characteristics\n`;
    voice.characteristics.forEach(c => prompt += `- ${c}\n`);
    prompt += `\n`;
  }

  // Do's and Don'ts
  if (tone.usage_do?.length) {
    prompt += `## Do's\n`;
    tone.usage_do.forEach(d => prompt += `- ${d}\n`);
    prompt += `\n`;
  }

  if (tone.usage_dont?.length) {
    prompt += `## Don'ts\n`;
    tone.usage_dont.forEach(d => prompt += `- ${d}\n`);
    prompt += `\n`;
  }

  // Vocabulary
  if (vocabulary.preferred_terms?.length || vocabulary.terms_to_avoid?.length) {
    prompt += `## Vocabulary\n`;
    if (vocabulary.preferred_terms?.length) {
      prompt += `### Preferred Terms\n`;
      vocabulary.preferred_terms.forEach(t => {
        prompt += `- Use "${t.term}" instead of "${t.instead_of}"\n`;
      });
    }
    if (vocabulary.terms_to_avoid?.length) {
      prompt += `### Terms to Avoid\n`;
      vocabulary.terms_to_avoid.forEach(t => prompt += `- ${t}\n`);
    }
    prompt += `\n`;
  }

  // Footer
  prompt += `---\n`;
  prompt += `Remember: You are representing ${brandConfig.name}. Maintain consistency with these guidelines in all responses.\n`;

  return prompt;
}
```

### Tasks

- [x] Create `lib/server/exporters/prompt-pack-exporter.ts`
- [x] Implement system prompt generator
- [x] Implement voice guide generator
- [x] Implement quick reference generator
- [x] Implement writing checklist generator
- [x] Implement example prompts generator (content type-specific)
- [x] Add condensed summaries for quick reference

### Dependencies
- Phase 4 Section Save/Load
- Completed voice and tone sections (recommended)

---

## 8.6 Export Manager

### Objective
Track export history, manage export files, and provide export status information.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\export-manager.ts`

### Export History Storage

**File:** `BrandData/{brand}/exports/_history.json`

```json
{
  "exports": [
    {
      "id": "exp_001",
      "date": "2026-01-13T10:30:00Z",
      "formats": ["json", "markdown", "prompt_pack"],
      "files": [
        {"format": "json", "path": "brand-name-brandkit.json", "size": 45678},
        {"format": "markdown", "path": "brand-name-guide.md", "size": 12345},
        {"format": "prompt_pack", "path": "brand-name-prompts/", "size": 5678}
      ],
      "sections_included": ["core/identity", "core/purpose", "voice/tone"],
      "trigger": "manual",
      "success": true
    }
  ],
  "latest": "exp_001",
  "total_exports": 1
}
```

### Core Functions

```typescript
interface ExportRecord {
  id: string;
  date: string;
  formats: ExportFormat[];
  files: ExportFile[];
  sections_included: string[];
  trigger: 'manual' | 'completion' | 'scheduled';
  success: boolean;
  errors?: string[];
}

interface ExportFile {
  format: ExportFormat;
  path: string;
  size: number;
}

// Get export history for brand
export async function getExportHistory(
  brandPath: string
): Promise<ExportRecord[]>

// Add export to history
export async function addExportRecord(
  brandPath: string,
  record: ExportRecord
): Promise<void>

// Get latest export
export async function getLatestExport(
  brandPath: string
): Promise<ExportRecord | null>

// Clean up old exports (keep latest N)
export async function cleanupOldExports(
  brandPath: string,
  keepCount: number
): Promise<number>

// Delete specific export
export async function deleteExport(
  brandPath: string,
  exportId: string
): Promise<void>

// Get total export size for brand
export async function getExportSize(
  brandPath: string
): Promise<number>
```

### Tasks

- [x] Create `lib/server/export-manager.ts`
- [x] Implement ExportManager class with singleton instance
- [x] Implement export history storage
- [x] Implement cleanup functionality (`cleanup()`, `cleanupOldExports()`)
- [x] Implement export deletion (`deleteExport()`, `deleteAllExports()`)
- [x] Add export size tracking (`getSize()`)
- [x] Add export summary (`getSummary()`)
- [x] Add batch export for multiple brands (`batchExport()`)
- [x] Add re-export operations (`reexport()`, `reexportFormat()`)
- [x] Create `lib/server/exporters/index.ts` barrel exports

### Dependencies
- None (utility module)

---

## Implementation Order

```
8.1 Export Router
         ↓
    ┌────┴────┬────────────┐
    ↓         ↓            ↓
8.2 JSON   8.3 Markdown   8.5 Prompt Pack
Exporter   Exporter       Exporter
              ↓
           8.4 PDF
           Exporter
              ↓
         8.6 Export
         Manager
```

**Sequence:**
1. Build 8.1 first (routes all exports)
2. Build 8.2 (JSON is simplest, foundational)
3. Build 8.3 (Markdown used by PDF)
4. Build 8.5 (Prompt Pack can be parallel)
5. Build 8.4 (PDF depends on Markdown)
6. Build 8.6 last (manages all exports)

---

## Verification Plan

### 8.1 Export Router
- [x] Correctly identifies completed vs draft vs missing sections
- [x] Returns available formats based on completion status
- [x] Orchestrates multi-format export
- [x] Updates export history after successful export

### 8.2 JSON Export
- [x] Exports all completed sections
- [x] Correctly structures nested brandkit object
- [x] Excludes internal fields (_meta, _draft)
- [x] Includes library references when requested
- [x] Valid JSON output (parseable)

### 8.3 Markdown Export
- [x] Generates readable markdown for all section types
- [x] Table of contents links work
- [x] Tables render correctly
- [x] Arrays and objects formatted properly

### 8.4 PDF Export
- [x] HTML generates without errors
- [x] Styling applied correctly with brand colours
- [x] Cover page included when requested
- [x] Section headers and formatting appropriate
- [x] Optional PDF conversion with puppeteer

### 8.5 Prompt Pack Export
- [x] System prompt includes all voice/tone data
- [x] Quick reference is concise
- [x] Writing checklist is actionable
- [x] All components exported (system prompt, voice guide, quick reference, checklist, examples)

### 8.6 Export Manager
- [x] History tracked correctly
- [x] Cleanup removes old exports
- [x] Export size calculated correctly
- [x] Batch export works for multiple brands

---

## Files to Create (Summary)

| File | Purpose |
|------|---------|
| `lib/server/export-router.ts` | Route and orchestrate exports |
| `lib/server/exporters/json-exporter.ts` | JSON export generation |
| `lib/server/exporters/markdown-exporter.ts` | Markdown export generation |
| `lib/server/exporters/pdf-exporter.ts` | PDF export generation |
| `lib/server/exporters/prompt-exporter.ts` | Prompt pack generation |
| `lib/server/export-manager.ts` | Export history and management |
| `lib/server/types/export.ts` | Export type definitions |

---

## Notes

- Export is handled by separate script (Workflow App is READ-only for brandkit data)
- PDF generation uses HTML with optional Puppeteer (graceful fallback)
- Prompt pack is optimised for AI assistant consumption
- Consider token limits when generating prompts for AI tools
- Export history helps track what's been shared with clients
- Cleanup policy prevents exports folder from growing indefinitely

---

## Build Notes (2026-01-16)

### Implementation Summary

**All 6 sub-phases completed:**
1. **8.1 Export Router** - Routes exports, checks availability, orchestrates multi-format
2. **8.2 JSON Exporter** - Full brand data with meta, sections, library refs
3. **8.3 Markdown Exporter** - Human-readable guide with TOC and section templates
4. **8.4 PDF Exporter** - HTML generation with brand styling, optional puppeteer
5. **8.5 Prompt Pack Exporter** - AI-ready prompts with system prompt, voice guide, etc.
6. **8.6 Export Manager** - High-level API with singleton, batch export, cleanup

### Key Decisions

| Decision | Rationale |
|----------|-----------|
| HTML-first PDF approach | Puppeteer optional, graceful fallback to HTML export |
| Singleton ExportManager | Consistent configuration, easy to use |
| Section-specific templates | Better formatting for known section types |
| Prompt Pack as folder | Multiple files (system-prompt.md, voice-guide.md, etc.) |

### Files Created

| File | Lines | Purpose |
|------|-------|---------|
| `lib/server/types/export.ts` | ~150 | All export type definitions |
| `lib/server/export-router.ts` | ~350 | Export routing and orchestration |
| `lib/server/exporters/json-exporter.ts` | ~300 | JSON export generation |
| `lib/server/exporters/markdown-exporter.ts` | ~500 | Markdown export generation |
| `lib/server/exporters/pdf-exporter.ts` | ~400 | PDF/HTML export generation |
| `lib/server/exporters/prompt-pack-exporter.ts` | ~550 | AI prompt pack generation |
| `lib/server/export-manager.ts` | ~580 | High-level export management |
| `lib/server/exporters/index.ts` | ~30 | Barrel exports |

### Error Fixes During Build

| Error | Fix Applied |
|-------|-------------|
| BrandConfig import not found | Changed to `import type { BrandConfig } from './types/brand'` |
| Error type 'unknown' | Added `error instanceof Error ? error.message : String(error)` |
| brandConfig possibly null | Added null checks with throw errors |
| Puppeteer module resolution | Changed to `require('puppeteer')` with try/catch |
| Interface export errors | Added `export` keyword to interface declarations |

### Usage Examples

```typescript
import { exportManager, quickExportAll, getExportSummary } from '$lib/server';

// Quick export all formats
const result = await quickExportAll('/path/to/brand');

// Export specific formats
const result = await exportManager.export(brandPath, ['json', 'markdown'], {
  includeDrafts: false,
  includeLibraryRefs: true
});

// Export single format
await exportManager.exportJSON(brandPath, { prettyPrint: true });
await exportManager.exportMarkdown(brandPath, { includeTableOfContents: true });
await exportManager.exportPDF(brandPath, { template: 'modern' });
await exportManager.exportPromptPack(brandPath, { targetAI: 'claude' });

// Get export summary
const summary = await getExportSummary(brandPath);

// Re-export last configuration
await exportManager.reexport(brandPath);

// Batch export multiple brands
const batchResult = await exportManager.batchExport({
  brandPaths: ['/brand1', '/brand2'],
  formats: ['json', 'markdown']
});

// Cleanup old exports
await exportManager.cleanup(brandPath);
```

---

## Post-Build Enhancements (2026-01-16)

Following Phase 8 completion, additional improvements were made:

### Puppeteer Availability Check (IMPLEMENTED)

Added runtime detection of puppeteer availability:

```typescript
// lib/server/export-router.ts
export function isPuppeteerAvailable(): boolean {
  if (puppeteerAvailableCache !== null) {
    return puppeteerAvailableCache;
  }
  try {
    require.resolve('puppeteer');
    puppeteerAvailableCache = true;
    return true;
  } catch {
    puppeteerAvailableCache = false;
    return false;
  }
}

export function clearPuppeteerCache(): void {
  puppeteerAvailableCache = null;
}
```

**Behaviour:**
- `checkExportAvailability()` only includes 'pdf' format if puppeteer is installed
- If not installed, adds warning: "PDF export requires puppeteer to be installed"
- Result cached for performance, clearable for testing

### Test Script Created

Created comprehensive test script: `scripts/test-export.ts`

| Test Group | Tests | Coverage |
|------------|-------|----------|
| 8.1 Export Router | 12 | Availability, formats, requirements, loading |
| 8.2 JSON Export | 7 | Generation, parsing, summary |
| 8.3 Markdown Export | 6 | Generation, TOC, content |
| 8.4 PDF Export | 7 | HTML generation, styling, fallback |
| 8.5 Prompt Pack | 8 | JSON structure, text files |
| 8.6 Export Manager | 8 | Class, methods, operations |
| History & Convenience | 6 | History tracking, helper functions |
| Multi-Format | 4 | Multiple format export |
| **Total** | **58** | **100% pass rate** |

**Run tests:**
```bash
npx tsx scripts/test-export.ts
```

### Index.ts Exports Updated

All Phase 8 exports added to `lib/server/index.ts`:

- **Types:** ExportFormat, ExportOptions, ExportResult, etc.
- **Constants:** DEFAULT_EXPORT_OPTIONS, FORMAT_REQUIREMENTS, SECTION_EXPORT_ORDER
- **Router:** checkExportAvailability, executeExport, isPuppeteerAvailable, etc.
- **Manager:** ExportManager, exportManager, quickExportAll, quickExport
- **Exporters:** generateJSONExport, generateMarkdownExport, generatePDFExport, generatePromptPackExport

### Test Brand Config Fixed

Updated `test-brand/_config/brand.json` with required `id` field:
```json
{
  "id": "test-brand",
  "name": "Test Brand",
  "display_name": "Test Brand",
  ...
}
```

---

### Next Steps

Phase 9: Integrations
- Canva API integration
- Stock photo APIs (Unsplash, Pexels)
- Google Fonts API
- Integration manager

