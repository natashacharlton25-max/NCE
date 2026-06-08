# Phase 7: Visual Pathway - Implementation Plan

> Detailed implementation plan for Phase 7 of BrandKit Workflow build.
> **Status:** Complete (199 tests) | **Updated:** 2026-01-15
> Reference: [DOC-INDEX.md](../DOC-INDEX.md) | [BUILD-CHECKLIST.md](../Build%20Lists%20Detailed/BUILD-CHECKLIST.md)

---

## Overview

Phase 7 implements the Visual Pathway - a separate processing flow for visual sections that differs from standard AI-based section generation. Visual sections require:
- Parsing technical files (CSS tokens, typography files)
- Interactive builder apps for creating from scratch
- Asset management (logo files, photography)
- Different input sources than text-based sections

**Key Discovery:** Significant existing code can be reused from previous projects:
- `ColorTokenGenerator` - OKLCH color scale generation, theme switching
- `Empire v3.1` - CSS token parser, brand file system utilities
- `Empire v4.1` - Design token system, brand schema, 1,512 icons

### Phase 7 Sub-Phases

| Sub-Phase | Focus | Effort | Status |
|-----------|-------|--------|--------|
| **7A** | CSS Token Parser (port existing code) | LOW | ✅ Complete (73 tests) |
| **7B** | Visual Router + Handlers | MEDIUM | ✅ Complete (61 tests) |
| **7C** | Utility App Integrations | MEDIUM | ✅ Complete (65 tests) |

### Components

| Component | Purpose | Status |
|-----------|---------|--------|
| **7A.1 CSS Token Parser** | Port colorTokenParser.js to TypeScript | ✅ Done |
| **7A.2 Color Family Grouping** | Group Primary-100, Primary-500 etc. | ✅ Done |
| **7A.3 Accessibility Utils** | WCAG contrast ratios, compliance checks | ✅ Done |
| **7B.1 Visual Section Router** | Route visual sections to appropriate handler | ✅ Done |
| **7B.2 Logo Handler** | Process logo uploads and track variants | ✅ Done |
| **7B.3 Photography Handler** | Handle photography style and guidelines | ✅ Done |
| **7B.4 Visual Guidelines Generator** | AI generation for usage guidelines (do/don't) | ✅ Done |
| **7C.1 ColourToken Integration** | Interface with ColourToken App | ✅ Done |
| **7C.2 TypographyBuilder Integration** | Interface with TypographyBuilder App | ✅ Done |
| **7C.3 LogoMaker Integration** | Interface with LogoMaker App | ✅ Done |
| **7C.4 ImageBrowser Integration** | Interface with ImageBrowser App | ✅ Done |

---

## Existing Code to Reuse

### 1. ColorTokenGenerator (`C:\Users\Business\ColorTokenGenerator\`)

| File | Purpose | Reuse Value |
|------|---------|-------------|
| `ThemeTokenGen/simple-theme-gen.js` | OKLCH color scale generator (50-950) | HIGH - Port for palette generation |
| `ThemeSwitcher.js` | Dynamic theme switching with a11y | MEDIUM - Reference for theme system |
| `generate-core-tokens.js` | Token extraction from CSS files | HIGH - Pattern for CSS parsing |

**Key Features to Port:**
- OKLCH perceptually uniform color scales (50-950)
- Color theory harmonies (complementary, analogous, triadic, split-complementary, tetradic)
- Smart chroma adjustment for readability
- Base position mapping (specify which shade is the "500")

### 2. Empire v3.1 (`C:\Users\Business\Empire v3.1\`)

| File | Purpose | Reuse Value |
|------|---------|-------------|
| `src/utils/colorTokenParser.js` | CSS color/font token extraction | **CRITICAL** - Direct port to TypeScript |
| `src/utils/brandFileSystem.js` | Brand file I/O utilities | MEDIUM - Reference pattern |

**colorTokenParser.js Key Functions:**
```javascript
parseCSSColorTokens(css)     // Extract color variables
parseCSSFontTokens(css)      // Extract font families
extractBrandPalette(css)     // Smart extraction with grouping
groupColorsByFamily(colors)  // Group Primary-100, Primary-500, etc.
inferColorRole(varName)      // primary, secondary, accent, neutral
convertToHex(value)          // rgb, hsl, oklch → hex
```

### 3. Empire v4.1 (`C:\Users\Business\Empire v4.1\`)

| Folder | Purpose | Reuse Value |
|--------|---------|-------------|
| `shared/styles/tokens/` | Comprehensive design token system | MEDIUM - Token structure reference |
| `shared/data/brand-schema.js` | 16-part brand profile schema | LOW - Schema reference |
| `shared/Icons/` | 1,512 Phosphor SVG icons | MEDIUM - For visual/icons section |

### 4. External APIs Available

| API | Purpose | Phase 7 Use |
|-----|---------|-------------|
| **Colour Pizza API** | Get colour names, palette suggestions | ColourToken app |
| **Background Remover API** | Auto-remove backgrounds from images | LogoMaker - transparent variants |
| **Google Fonts API** | Font metadata and pairing suggestions | TypographyBuilder app |
| **Unsplash/Pexels API** | Stock photo search | ImageBrowser app |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| Brand folder created | Phase 1.3 | Required - visual context storage |
| Section save/load utilities | Phase 4 | Required - draft/brandkit workflow |
| Library matcher | Phase 5 | Required - accessibility library |
| Global config with paths | Phase 1.1 | Required - utility app locations |

### External Utility Apps Required

Phase 7 depends on 4 external utility apps that must be built separately (or in parallel):

| Utility App | Location | Purpose | Integration |
|-------------|----------|---------|-------------|
| **ColourToken App** | `C:\Users\Business\ColourToken\` | Interactive colour palette builder | File-based (input/output JSON) |
| **TypographyBuilder App** | `C:\Users\Business\TypographyBuilder\` | Font selection with Google Fonts | File-based (input/output JSON) |
| **LogoMaker App** | `C:\Users\Business\LogoMaker\` | Generate logo variants | File-based (input/output JSON) |
| **ImageBrowser App** | `C:\Users\Business\ImageBrowser\` | Search Unsplash/Pexels, manage images | File-based (input/output JSON) |

**Note:** These apps can be built in parallel with Phase 7 implementation. Phase 7 provides the integration layer; apps provide the functionality. See section 7.3/7.4/7.5/7.6 for integration specifications.

---

## Visual Sections Overview

| Section | Order | Depends On | Input Sources |
|---------|-------|------------|---------------|
| `visual/logos` | 37 | core/identity | File upload, LogoMaker App |
| `visual/colours` | 38 | core/identity, voice/personality | CSS upload, ColourToken App |
| `visual/typography` | 39 | voice/personality | CSS upload, TypographyBuilder App |
| `visual/photography` | 40 | voice/personality, audience/core | Image upload, ImageBrowser App |
| `visual/graphics` | 41 | visual/colours | Config UI |
| `visual/icons` | 42 | visual/graphics | Config UI (icon library selection) |
| `visual/charts` | 43 | visual/colours, visual/typography | Config UI |
| `visual/templates` | 44 | visual/colours, visual/typography, visual/logos | Config UI |
| `visual/emails` | 45 | visual/templates, emails/types | Config UI |
| `visual/custom-categories` | 46 | visual/graphics | Config UI |

### Visual Sections vs Standard Sections

| Aspect | Standard Sections | Visual Sections |
|--------|-------------------|-----------------|
| Primary input | Text content (website, docs) | Technical files (CSS, images) |
| Processing | AI extraction/generation | File parsing + builder apps |
| Content type | Subjective (tone, values) | Objective (hex codes, font names) |
| Manual entry | Natural (text fields) | Tedious (colour pickers, file paths) |

---

## 7.1 Visual Section Router

### Objective
Route visual section requests to the appropriate handler based on:
- What input sources are available (CSS files vs none)
- User preference (upload vs create from scratch)
- Section type (colours vs logos vs photography)

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\visual-router.ts`

### Core Interfaces

```typescript
type VisualSection =
  | 'visual/logos'
  | 'visual/colours'
  | 'visual/typography'
  | 'visual/photography'
  | 'visual/graphics'
  | 'visual/icons'
  | 'visual/charts'
  | 'visual/templates'
  | 'visual/emails'
  | 'visual/custom-categories';

interface VisualInputSource {
  type: 'css_file' | 'uploaded_asset' | 'builder_app' | 'config_ui';
  path?: string;           // Path to uploaded file if applicable
  appId?: string;          // Builder app identifier
}

interface VisualRouteDecision {
  section: VisualSection;
  handler: 'css_parser' | 'builder_app' | 'asset_upload' | 'config_ui' | 'ai_guidelines';
  availableSources: VisualInputSource[];
  recommendedSource: VisualInputSource;
  dependencies_met: boolean;
  missing_dependencies: string[];
}

interface VisualProcessingOptions {
  brandPath: string;
  section: VisualSection;
  forceMethod?: 'upload' | 'builder' | 'manual';
}
```

### Core Functions

```typescript
// Determine how to handle a visual section
export async function routeVisualSection(
  options: VisualProcessingOptions
): Promise<VisualRouteDecision>

// Check what input sources are available for a section
export async function checkAvailableSources(
  brandPath: string,
  section: VisualSection
): Promise<VisualInputSource[]>

// Check if dependencies are met for visual section
export async function checkVisualDependencies(
  brandPath: string,
  section: VisualSection
): Promise<{ met: boolean; missing: string[] }>

// Get handler for section type
export function getVisualHandler(section: VisualSection): string
```

### Routing Logic

```typescript
const VISUAL_SECTION_HANDLERS: Record<VisualSection, string[]> = {
  'visual/logos': ['asset_upload', 'builder_app'],      // LogoMaker
  'visual/colours': ['css_parser', 'builder_app'],      // ColourToken
  'visual/typography': ['css_parser', 'builder_app'],   // TypographyBuilder
  'visual/photography': ['asset_upload', 'builder_app'], // ImageBrowser
  'visual/graphics': ['config_ui', 'ai_guidelines'],
  'visual/icons': ['config_ui'],
  'visual/charts': ['config_ui'],
  'visual/templates': ['config_ui'],
  'visual/emails': ['config_ui'],
  'visual/custom-categories': ['config_ui']
};

export async function routeVisualSection(
  options: VisualProcessingOptions
): Promise<VisualRouteDecision> {
  const { brandPath, section, forceMethod } = options;

  // 1. Check dependencies
  const deps = await checkVisualDependencies(brandPath, section);

  // 2. Check available sources
  const sources = await checkAvailableSources(brandPath, section);

  // 3. Determine best handler
  const handlers = VISUAL_SECTION_HANDLERS[section];
  let handler = handlers[0];
  let recommendedSource = sources[0];

  // If CSS file exists for colours/typography, prefer parser
  if (section === 'visual/colours' || section === 'visual/typography') {
    const cssSource = sources.find(s => s.type === 'css_file');
    if (cssSource) {
      handler = 'css_parser';
      recommendedSource = cssSource;
    }
  }

  // If user forces a method
  if (forceMethod === 'upload') {
    handler = section.includes('logo') || section.includes('photo')
      ? 'asset_upload'
      : 'css_parser';
  } else if (forceMethod === 'builder') {
    handler = 'builder_app';
  }

  return {
    section,
    handler,
    availableSources: sources,
    recommendedSource,
    dependencies_met: deps.met,
    missing_dependencies: deps.missing
  };
}
```

### Tasks

- [ ] Create `lib/server/visual-router.ts`
- [ ] Define visual section handler mappings
- [ ] Implement source detection (CSS files in context/visual/)
- [ ] Implement dependency checking
- [ ] Create routing logic with method override support

### Dependencies
- Phase 4 section dependency utilities

---

## 7.2 CSS Token Parser

### Objective
Parse CSS files to extract colour and typography tokens, converting them to structured brand data.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\css-token-parser.ts`

### Reference Implementation
Existing code at: `Empire v3.1/src/utils/colorTokenParser.js` (to be ported to TypeScript)

### Core Interfaces

```typescript
interface ParsedColour {
  name: string;              // "Primary 500"
  hex: string;               // "#8FA68A"
  original: string;          // Original CSS value (could be rgb, hsl)
  variable: string;          // "--color-Primary-500"
  role: ColourRole | null;   // Inferred role
  family?: string;           // "Primary" (grouped colours)
  shade?: string;            // "500"
}

type ColourRole = 'primary' | 'secondary' | 'accent' | 'neutral' | 'success' | 'warning' | 'error' | 'info';

interface ParsedFont {
  name: string;              // "Heading"
  family: string;            // "Playfair Display"
  source: 'google' | 'system' | 'custom';
  role: FontRole | null;     // Inferred role
  cssVariable?: string;      // "--font-heading"
  weights?: number[];        // [400, 700]
  fallback?: string;         // "serif"
}

type FontRole = 'heading' | 'body' | 'accent' | 'monospace';

interface ColourFamily {
  name: string;              // "Primary"
  role: ColourRole | null;
  shades: Record<string, string>;  // { "100": "#E8F0E6", "500": "#8FA68A" }
  default: string;           // "500" (the main shade)
}

interface CSSParseResult {
  success: boolean;
  colours: ParsedColour[];
  colourFamilies: ColourFamily[];
  fonts: ParsedFont[];
  errors: string[];
  warnings: string[];
}
```

### Core Functions

```typescript
// Main parsing function
export function parseCSSTokens(css: string): CSSParseResult

// Extract colour variables from CSS
export function parseCSSColorTokens(css: string): ParsedColour[]

// Extract font declarations from CSS
export function parseCSSFontTokens(css: string): ParsedFont[]

// Group colours by family (Primary-100, Primary-500, etc.)
export function groupColorsByFamily(colours: ParsedColour[]): ColourFamily[]

// Infer colour role from variable name
export function inferColorRole(varName: string): ColourRole | null

// Infer font role from variable name
export function inferFontRole(varName: string): FontRole | null

// Convert any colour format to hex
export function normalizeColourToHex(value: string): string | null

// Extract Google Fonts imports
export function extractGoogleFontsImports(css: string): ParsedFont[]
```

### Colour Parsing Logic

```typescript
// Regex patterns for CSS colour extraction
const CSS_VAR_PATTERN = /--([a-zA-Z0-9-_]+)\s*:\s*([^;]+);/g;
const HEX_PATTERN = /^#([0-9a-fA-F]{3}|[0-9a-fA-F]{6})$/;
const RGB_PATTERN = /^rgba?\((\d+),\s*(\d+),\s*(\d+)/;
const HSL_PATTERN = /^hsla?\((\d+),\s*(\d+)%,\s*(\d+)%/;

export function parseCSSColorTokens(css: string): ParsedColour[] {
  const colours: ParsedColour[] = [];
  let match;

  while ((match = CSS_VAR_PATTERN.exec(css)) !== null) {
    const [, varName, value] = match;

    // Skip non-colour variables
    if (!isColourValue(value)) continue;

    const hex = normalizeColourToHex(value.trim());
    if (!hex) continue;

    // Parse family and shade from variable name
    const { family, shade } = parseColourVariableName(varName);
    const role = inferColorRole(varName);

    colours.push({
      name: formatColourName(varName),
      hex,
      original: value.trim(),
      variable: `--${varName}`,
      role,
      family,
      shade
    });
  }

  return colours;
}

function inferColorRole(varName: string): ColourRole | null {
  const lower = varName.toLowerCase();

  if (lower.includes('primary')) return 'primary';
  if (lower.includes('secondary')) return 'secondary';
  if (lower.includes('accent')) return 'accent';
  if (lower.includes('neutral') || lower.includes('gray') || lower.includes('grey')) return 'neutral';
  if (lower.includes('success') || lower.includes('green')) return 'success';
  if (lower.includes('warning') || lower.includes('yellow') || lower.includes('orange')) return 'warning';
  if (lower.includes('error') || lower.includes('danger') || lower.includes('red')) return 'error';
  if (lower.includes('info') || lower.includes('blue')) return 'info';

  return null;
}

function parseColourVariableName(varName: string): { family?: string; shade?: string } {
  // Pattern: color-Primary-500, Primary-500, color-primary-light
  const patterns = [
    /^color[_-]?([A-Za-z]+)[_-]?(\d+)$/i,
    /^([A-Za-z]+)[_-]?(\d+)$/,
    /^color[_-]?([A-Za-z]+)[_-]?(light|dark|lighter|darker)$/i
  ];

  for (const pattern of patterns) {
    const match = varName.match(pattern);
    if (match) {
      return { family: match[1], shade: match[2] };
    }
  }

  return {};
}
```

### Font Parsing Logic

```typescript
export function parseCSSFontTokens(css: string): ParsedFont[] {
  const fonts: ParsedFont[] = [];

  // Extract from CSS variables
  const varFonts = extractFontVariables(css);
  fonts.push(...varFonts);

  // Extract from Google Fonts imports
  const importFonts = extractGoogleFontsImports(css);
  fonts.push(...importFonts);

  // Deduplicate by family
  return deduplicateFonts(fonts);
}

function extractFontVariables(css: string): ParsedFont[] {
  const fonts: ParsedFont[] = [];
  const FONT_VAR_PATTERN = /--font-([a-zA-Z0-9-_]+)\s*:\s*['"]?([^;'"]+)['"]?;/g;

  let match;
  while ((match = FONT_VAR_PATTERN.exec(css)) !== null) {
    const [, varName, family] = match;
    const role = inferFontRole(varName);

    fonts.push({
      name: formatFontName(varName),
      family: family.split(',')[0].trim(),
      source: detectFontSource(family),
      role,
      cssVariable: `--font-${varName}`,
      fallback: extractFallback(family)
    });
  }

  return fonts;
}

function extractGoogleFontsImports(css: string): ParsedFont[] {
  const fonts: ParsedFont[] = [];
  const IMPORT_PATTERN = /@import\s+url\(['"]?https:\/\/fonts\.googleapis\.com\/css2?\?family=([^'")\s]+)/g;

  let match;
  while ((match = IMPORT_PATTERN.exec(css)) !== null) {
    const fontParam = match[1];
    // Parse: "Playfair+Display:wght@400;700"
    const parsed = parseGoogleFontParam(fontParam);
    if (parsed) {
      fonts.push({
        ...parsed,
        source: 'google'
      });
    }
  }

  return fonts;
}

function inferFontRole(varName: string): FontRole | null {
  const lower = varName.toLowerCase();

  if (lower.includes('heading') || lower.includes('display') || lower.includes('title')) return 'heading';
  if (lower.includes('body') || lower.includes('text') || lower.includes('paragraph')) return 'body';
  if (lower.includes('accent') || lower.includes('special')) return 'accent';
  if (lower.includes('mono') || lower.includes('code')) return 'monospace';

  return null;
}
```

### Tasks

- [ ] Create `lib/server/css-token-parser.ts`
- [ ] Port colour parsing from existing colorTokenParser.js
- [ ] Implement colour format normalization (hex, rgb, hsl → hex)
- [ ] Implement colour family grouping
- [ ] Implement font variable extraction
- [ ] Implement Google Fonts @import parsing
- [ ] Add role inference for colours and fonts
- [ ] Add comprehensive error handling
- [ ] Test with various CSS token formats (Tailwind, Figma, custom)

### Dependencies
- None (standalone utility)

---

## 7.3 ColourToken Integration

### Objective
Interface with the ColourToken App (separate utility) for interactive palette building when no CSS file is available.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\colourtoken-integration.ts`

### ColourToken App Location
`C:\Users\Business\ColourToken\` (to be created separately)

### Core Interfaces

```typescript
interface ColourTokenInput {
  brandPath: string;
  existingColours?: ParsedColour[];  // Pre-populate if partial data exists
  mode: 'create' | 'edit';
}

interface ColourTokenOutput {
  success: boolean;
  palette: ColourPalette;
  semantic?: SemanticColours;
}

interface ColourPalette {
  colours: BrandColour[];
}

interface BrandColour {
  name: string;
  role: ColourRole;
  shades: Record<string, string>;
  default: string;
  usage?: string;
}

interface SemanticColours {
  background: string;
  surface: string;
  text_primary: string;
  text_secondary: string;
  border: string;
}
```

### Core Functions

```typescript
// Launch ColourToken App and wait for result
export async function launchColourToken(
  input: ColourTokenInput
): Promise<ColourTokenOutput>

// Convert ColourToken output to visual/colours section format
export function convertToColoursSection(
  output: ColourTokenOutput
): VisualColoursData

// Save ColourToken output to brand
export async function saveColourTokenResult(
  brandPath: string,
  output: ColourTokenOutput
): Promise<void>
```

### Integration Flow

```typescript
export async function launchColourToken(
  input: ColourTokenInput
): Promise<ColourTokenOutput> {
  const { brandPath, existingColours, mode } = input;

  // 1. Prepare input file for ColourToken App
  const inputFile = path.join(brandPath, 'context', 'visual', '_colourtoken-input.json');
  await fs.writeFile(inputFile, JSON.stringify({
    mode,
    existing: existingColours || [],
    brand_name: await getBrandName(brandPath)
  }, null, 2));

  // 2. Launch ColourToken App (implementation depends on app architecture)
  // Option A: CLI call
  // Option B: IPC/message passing
  // Option C: File-based communication

  const colourTokenPath = await getGlobalConfig('paths.colour_token');

  // For now, assume file-based communication
  const outputFile = path.join(brandPath, 'context', 'visual', '_colourtoken-output.json');

  // Signal that we're waiting for ColourToken
  return {
    success: true,
    message: 'ColourToken App launched. Complete palette selection to continue.',
    outputFile
  };
}

// Called when ColourToken App completes
export async function handleColourTokenComplete(
  brandPath: string
): Promise<ColourTokenOutput> {
  const outputFile = path.join(brandPath, 'context', 'visual', '_colourtoken-output.json');

  if (!await fs.exists(outputFile)) {
    throw new Error('ColourToken output not found');
  }

  const output = JSON.parse(await fs.readFile(outputFile, 'utf-8'));

  // Validate output structure
  validateColourTokenOutput(output);

  return output;
}
```

### Tasks

- [ ] Create `lib/server/colourtoken-integration.ts`
- [ ] Define communication protocol with ColourToken App
- [ ] Implement input file generation
- [ ] Implement output file handling
- [ ] Add conversion to visual/colours section format
- [ ] Handle app launch (CLI or IPC)

### Dependencies
- ColourToken App (separate project - to be built)
- Phase 1.1 Global Config (for app path)

---

## 7.4 TypographyBuilder Integration

### Objective
Interface with the TypographyBuilder App (separate utility) for interactive font selection when no typography file is available.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\typographybuilder-integration.ts`

### TypographyBuilder App Location
`C:\Users\Business\TypographyBuilder\` (to be created separately)

### Core Interfaces

```typescript
interface TypographyBuilderInput {
  brandPath: string;
  existingFonts?: ParsedFont[];  // Pre-populate if partial data exists
  mode: 'create' | 'edit';
}

interface TypographyBuilderOutput {
  success: boolean;
  fonts: TypographyFont[];
  scale?: TypeScale;
}

interface TypographyFont {
  role: FontRole;
  family: string;
  source: 'google' | 'system' | 'custom';
  weights: number[];
  fallback: string;
}

interface TypeScale {
  h1: string;
  h2: string;
  h3: string;
  h4: string;
  body: string;
  small: string;
  caption: string;
}
```

### Core Functions

```typescript
// Launch TypographyBuilder App and wait for result
export async function launchTypographyBuilder(
  input: TypographyBuilderInput
): Promise<TypographyBuilderOutput>

// Convert TypographyBuilder output to visual/typography section format
export function convertToTypographySection(
  output: TypographyBuilderOutput
): VisualTypographyData

// Save TypographyBuilder output to brand
export async function saveTypographyBuilderResult(
  brandPath: string,
  output: TypographyBuilderOutput
): Promise<void>

// Get Google Fonts pairing suggestions
export async function getFontPairingSuggestions(
  headingFont: string
): Promise<string[]>
```

### TypographyBuilder Features (App Specification)

| Feature | Description |
|---------|-------------|
| **Google Fonts Search** | Search and browse Google Fonts library |
| **Font Pairing Suggestions** | Suggest complementary fonts for heading/body pairs |
| **Live Preview** | Preview fonts with sample text |
| **Weight Selection** | Choose which weights to include |
| **Role Assignment** | Assign fonts to heading, body, accent, mono roles |
| **Fallback Detection** | Auto-assign appropriate fallback (serif, sans-serif, monospace) |

### Tasks

- [ ] Create `lib/server/typographybuilder-integration.ts`
- [ ] Define communication protocol with TypographyBuilder App
- [ ] Implement input/output file handling
- [ ] Add conversion to visual/typography section format
- [ ] Implement font pairing suggestions (can use API or local data)

### Dependencies
- TypographyBuilder App (separate project - to be built)
- Phase 1.1 Global Config (for app path)
- Google Fonts API key (Phase 1.4 API Keys Manager)

---

## 7.5 Logo Handler

### Objective
Process logo file uploads, track variants, and integrate with LogoMaker App for variant generation.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\logo-handler.ts`

### LogoMaker App Location
`C:\Users\Business\LogoMaker\` (to be created separately)

### Core Interfaces

```typescript
interface LogoVariant {
  type: 'main' | 'transparent' | 'dark' | 'text' | 'favicon' | 'icon';
  file: string;           // Relative path from brand folder
  format: string;         // png, svg, ico
  dimensions?: string;    // "800x400"
  status: 'uploaded' | 'generated' | 'missing';
}

interface LogoStatus {
  variants: LogoVariant[];
  required_complete: boolean;  // At least main logo
  recommended_complete: boolean;  // Main + transparent + favicon
  all_complete: boolean;
}

interface LogoUploadResult {
  success: boolean;
  variant: LogoVariant;
  savedPath: string;
}

interface LogoGenerationRequest {
  brandPath: string;
  sourceVariant: 'main';        // Generate from main logo
  targetVariants: string[];     // ['transparent', 'dark', 'favicon']
}
```

### Required Logo Variants

| Variant | Description | Required |
|---------|-------------|----------|
| `main` | Primary logo (full colour) | Yes |
| `transparent` | Logo with transparent background | Recommended |
| `dark` | Logo for dark backgrounds | Recommended |
| `text` | Text-only version / wordmark | Optional |
| `favicon` | Square icon for browser tabs (16x16 to 512x512) | Recommended |
| `icon` | App icon / square logo | Optional |

### Core Functions

```typescript
// Upload a logo variant
export async function uploadLogo(
  brandPath: string,
  file: Buffer,
  filename: string,
  variant: string
): Promise<LogoUploadResult>

// Get status of all logo variants
export async function getLogoStatus(brandPath: string): Promise<LogoStatus>

// Request LogoMaker to generate missing variants
export async function requestLogoGeneration(
  request: LogoGenerationRequest
): Promise<void>

// Handle LogoMaker completion
export async function handleLogoMakerComplete(
  brandPath: string
): Promise<LogoVariant[]>

// Get logo section data for saving
export async function getLogoSectionData(
  brandPath: string
): Promise<VisualLogosData>
```

### Logo Storage Structure

```
BrandData/{brand}/
└── context/
    └── visual/
        └── logos/
            ├── logo-main.png
            ├── logo-transparent.png
            ├── logo-dark.png
            ├── logo-text.png
            ├── favicon.ico
            └── icon-512.png
```

### Logo Section Output

```typescript
interface VisualLogosData {
  summary: string;
  usage_do: string[];
  usage_dont: string[];
  primary: {
    file: string;
    format: string;
    dimensions: string;
  };
  variants: Record<string, string | null>;
  minimum_size: {
    print: string;
    digital: string;
  };
  clear_space: {
    description: string;
    measurement: string;
  };
  folder_locations: {
    local: string;
    canva?: string;
    google_drive?: string;
  };
}
```

### Tasks

- [ ] Create `lib/server/logo-handler.ts`
- [ ] Implement logo upload with variant detection
- [ ] Implement logo status tracking
- [ ] Create LogoMaker integration protocol
- [ ] Implement variant generation request
- [ ] Add image format validation (png, svg, ico)
- [ ] Add dimension extraction from uploaded images

### Dependencies
- LogoMaker App (separate project - to be built)
- Phase 1.1 Global Config

---

## 7.6 Photography/Graphics Handler

### Objective
Handle photography style definition and guidelines, integrate with ImageBrowser App for stock photo search.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\photography-handler.ts`

### ImageBrowser App Location
`C:\Users\Business\ImageBrowser\` (to be created separately)

### Core Interfaces

```typescript
interface PhotographyStyle {
  style: string;                 // "candid", "editorial", "lifestyle"
  mood: string[];                // ["warm", "natural", "authentic"]
  subjects: string[];            // ["people", "nature", "interiors"]
  colour_treatment: string;      // "natural", "muted", "vibrant"
}

interface PhotographyGuidelines {
  summary: string;
  usage_do: string[];
  usage_dont: string[];
  diversity_requirements: string[];
  images_to_avoid: string[];
  sourcing: {
    type: 'original' | 'stock' | 'mixed';
    providers?: string[];        // ["Unsplash", "Pexels"]
  };
  editing_guidelines: {
    filters: string;
    adjustments: string;
    retouching: string;
  };
  composition_rules: {
    framing: string;
    focal_points: string;
    negative_space: string;
  };
}

interface ImageBrowserResult {
  selected_images: SelectedImage[];
  style_analysis?: PhotographyStyle;
}

interface SelectedImage {
  id: string;
  source: 'unsplash' | 'pexels';
  url: string;
  thumbnail: string;
  photographer: string;
  license: string;
}
```

### Core Functions

```typescript
// Launch ImageBrowser App for stock photo selection
export async function launchImageBrowser(
  brandPath: string,
  purpose: 'hero' | 'blog' | 'social' | 'general'
): Promise<ImageBrowserResult>

// Analyze uploaded images for style patterns
export async function analyzePhotographyStyle(
  brandPath: string
): Promise<PhotographyStyle>

// Generate photography guidelines from style
export async function generatePhotographyGuidelines(
  brandPath: string,
  style: PhotographyStyle
): Promise<PhotographyGuidelines>

// Save selected stock images to brand
export async function saveSelectedImages(
  brandPath: string,
  images: SelectedImage[]
): Promise<void>
```

### Photography Analysis

```typescript
// Use AI to analyze uploaded brand images for style patterns
export async function analyzePhotographyStyle(
  brandPath: string
): Promise<PhotographyStyle> {
  // 1. Find uploaded images
  const imagesPath = path.join(brandPath, 'context', 'images');
  const images = await findImages(imagesPath);

  if (images.length === 0) {
    return {
      style: 'not_defined',
      mood: [],
      subjects: [],
      colour_treatment: 'natural'
    };
  }

  // 2. Analyze images (would use AI vision in production)
  // For now, return defaults based on domain
  const domain = await getBrandDomain(brandPath);

  return getDefaultStyleForDomain(domain);
}

function getDefaultStyleForDomain(domain: string): PhotographyStyle {
  const defaults: Record<string, PhotographyStyle> = {
    therapeutic: {
      style: 'lifestyle',
      mood: ['calm', 'peaceful', 'authentic'],
      subjects: ['people', 'nature', 'quiet spaces'],
      colour_treatment: 'warm'
    },
    ecommerce: {
      style: 'product',
      mood: ['clean', 'professional', 'aspirational'],
      subjects: ['products', 'lifestyle', 'people using products'],
      colour_treatment: 'vibrant'
    },
    'professional-services': {
      style: 'editorial',
      mood: ['professional', 'trustworthy', 'modern'],
      subjects: ['people', 'offices', 'teamwork'],
      colour_treatment: 'natural'
    }
  };

  return defaults[domain] || defaults['professional-services'];
}
```

### Tasks

- [ ] Create `lib/server/photography-handler.ts`
- [ ] Implement ImageBrowser integration
- [ ] Implement photography style analysis
- [ ] Generate default styles based on domain
- [ ] Create stock image save/management
- [ ] Add diversity requirement generation

### Dependencies
- ImageBrowser App (separate project - to be built)
- Phase 3 Domain Detection
- Unsplash/Pexels API keys (Phase 1.4)

---

## 7.7 Visual Guidelines Generator

### Objective
Use AI to generate usage guidelines (do/don't) for visual sections after the technical data is captured.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\visual-guidelines-generator.ts`

### Core Functions

```typescript
// Generate guidelines for colours section
export async function generateColourGuidelines(
  brandPath: string,
  colourData: ColourPalette
): Promise<ColourGuidelines>

// Generate guidelines for typography section
export async function generateTypographyGuidelines(
  brandPath: string,
  typographyData: TypographyFont[]
): Promise<TypographyGuidelines>

// Generate guidelines for logos section
export async function generateLogoGuidelines(
  brandPath: string,
  logoData: LogoVariant[]
): Promise<LogoGuidelines>

// Generate guidelines for photography section
export async function generatePhotographyGuidelines(
  brandPath: string,
  styleData: PhotographyStyle
): Promise<PhotographyGuidelines>
```

### Guidelines Generation

```typescript
interface GuidelinesGenerationContext {
  brandName: string;
  domain: string;
  voicePersonality?: object;   // From completed voice/personality section
  audienceCore?: object;       // From completed audience/core section
}

export async function generateColourGuidelines(
  brandPath: string,
  colourData: ColourPalette
): Promise<ColourGuidelines> {
  // 1. Load context
  const context = await loadGuidelinesContext(brandPath);

  // 2. Load section config for prompt
  const sectionConfig = await loadSectionConfig('visual/colours');

  // 3. Build prompt
  const prompt = buildColourGuidelinesPrompt(colourData, context, sectionConfig);

  // 4. Generate with AI (or use templates)
  const guidelines = await generateWithAI(prompt);

  return {
    summary: guidelines.summary,
    usage_do: guidelines.usage_do,
    usage_dont: guidelines.usage_dont,
    accessibility: generateAccessibilityGuidelines(colourData),
    colour_psychology: generateColourPsychology(colourData, context.domain)
  };
}

function generateAccessibilityGuidelines(colourData: ColourPalette): AccessibilityGuidelines {
  // Calculate contrast ratios
  const combinations = [];

  for (const colour of colourData.colours) {
    const mainHex = colour.shades[colour.default];

    // Check against white and dark backgrounds
    const contrastOnWhite = calculateContrastRatio(mainHex, '#FFFFFF');
    const contrastOnDark = calculateContrastRatio(mainHex, '#1A1A1A');

    combinations.push({
      colour: colour.name,
      on_white: {
        ratio: contrastOnWhite,
        wcag_aa: contrastOnWhite >= 4.5,
        wcag_aaa: contrastOnWhite >= 7
      },
      on_dark: {
        ratio: contrastOnDark,
        wcag_aa: contrastOnDark >= 4.5,
        wcag_aaa: contrastOnDark >= 7
      }
    });
  }

  return {
    passing_combinations: combinations.filter(c => c.on_white.wcag_aa || c.on_dark.wcag_aa),
    avoid_combinations: combinations.filter(c => !c.on_white.wcag_aa && !c.on_dark.wcag_aa)
  };
}
```

### Tasks

- [ ] Create `lib/server/visual-guidelines-generator.ts`
- [ ] Implement colour guidelines generation
- [ ] Implement typography guidelines generation
- [ ] Implement logo guidelines generation
- [ ] Implement photography guidelines generation
- [ ] Add accessibility contrast ratio calculations
- [ ] Add colour psychology generation based on domain
- [ ] Integrate with Phase 4 AI prompt builder

### Dependencies
- Phase 4 AI Prompt Builder
- Phase 4 Section Save utilities
- Phase 5 Library Matcher (for accessibility library)

---

## Implementation Order (Revised)

### New Structure: 3 Sub-Phases

```
Phase 7A: CSS Parser (Port existing code)
├── 7A.1 Port colorTokenParser.js to TypeScript
├── 7A.2 Add font parsing from Google Fonts @imports
└── 7A.3 Add color family grouping

Phase 7B: Visual Router + Handlers
├── 7B.1 Visual section router
├── 7B.2 Logo handler
├── 7B.3 Photography handler
└── 7B.4 Visual guidelines generator

Phase 7C: Utility App Integrations
├── 7C.1 ColourToken integration (build from ColorTokenGenerator)
├── 7C.2 TypographyBuilder integration
├── 7C.3 LogoMaker integration
└── 7C.4 ImageBrowser integration
```

### Files to Create

| File | Phase | Source |
|------|-------|--------|
| `lib/server/css-token-parser.ts` | 7A | Port from Empire v3.1 |
| `lib/server/types/visual.ts` | 7A | New |
| `lib/server/visual-router.ts` | 7B | New |
| `lib/server/logo-handler.ts` | 7B | New |
| `lib/server/photography-handler.ts` | 7B | New |
| `lib/server/visual-guidelines-generator.ts` | 7B | New |
| `lib/server/colourtoken-integration.ts` | 7C | New |
| `lib/server/typographybuilder-integration.ts` | 7C | New |
| `scripts/test-css-parser.ts` | 7A | New |
| `scripts/test-visual.ts` | 7B | New |

### Source Files to Port

| Original File | New Location | Action |
|---------------|--------------|--------|
| `Empire v3.1/src/utils/colorTokenParser.js` | `css-token-parser.ts` | Port to TypeScript |
| `ColorTokenGenerator/ThemeTokenGen/simple-theme-gen.js` | ColourToken App | Reference for color theory |
| `Empire v4.1/shared/Icons/` | Copy to shared location | Asset copy |

**Sequence:**
1. **7A First** - Port CSS parser (existing code, low effort)
2. **7B Second** - Router + handlers (new code, medium effort)
3. **7C Third** - Utility app integrations (depends on apps being built)

---

## Utility Apps Specification

These apps are built SEPARATELY from BrandKit Workflow:

| App | Location | Purpose | Key Features |
|-----|----------|---------|--------------|
| **ColourToken** | `C:\Users\Business\ColourToken\` | Interactive palette builder | Colour picker, palette generator, role assignment |
| **TypographyBuilder** | `C:\Users\Business\TypographyBuilder\` | Font selection | Google Fonts search, pairing suggestions, preview |
| **LogoMaker** | `C:\Users\Business\LogoMaker\` | Logo variant generation | Transparent background, dark variant, favicon |
| **ImageBrowser** | `C:\Users\Business\ImageBrowser\` | Stock photo search | Unsplash/Pexels integration, image selection |

All apps follow the same communication pattern:
1. BrandKit Workflow writes input file to `context/visual/_app-input.json`
2. App launches (CLI or opens window)
3. User completes task in app
4. App writes output to `context/visual/_app-output.json`
5. BrandKit Workflow reads output and continues

---

## Verification Plan

### 7.1 Visual Section Router
- [ ] Routes `visual/colours` to CSS parser when CSS file exists
- [ ] Routes `visual/colours` to ColourToken when no CSS file
- [ ] Routes `visual/logos` to asset upload handler
- [ ] Correctly identifies missing dependencies

### 7.2 CSS Token Parser
- [ ] Parses hex colours correctly
- [ ] Parses rgb/rgba colours correctly
- [ ] Groups colour families (Primary-100, Primary-500)
- [ ] Infers colour roles from variable names
- [ ] Extracts Google Fonts @imports
- [ ] Handles malformed CSS gracefully

### 7.3 ColourToken Integration
- [ ] Creates input file for ColourToken App
- [ ] Reads output file when app completes
- [ ] Converts output to visual/colours section format

### 7.4 TypographyBuilder Integration
- [ ] Creates input file for TypographyBuilder App
- [ ] Reads output file when app completes
- [ ] Converts output to visual/typography section format

### 7.5 Logo Handler
- [ ] Accepts logo upload with correct variant type
- [ ] Saves to correct location in context/visual/logos/
- [ ] Tracks which variants are missing
- [ ] Integrates with LogoMaker for generation

### 7.6 Photography Handler
- [ ] Launches ImageBrowser for stock photo selection
- [ ] Saves selected images to brand folder
- [ ] Generates style analysis based on domain

### 7.7 Visual Guidelines Generator
- [ ] Generates usage_do/usage_dont for colours
- [ ] Calculates accessibility contrast ratios
- [ ] Generates colour psychology based on domain
- [ ] Generates typography guidelines

---

## Files to Create (Summary)

| File | Purpose |
|------|---------|
| `lib/server/visual-router.ts` | Route visual sections to handlers |
| `lib/server/css-token-parser.ts` | Parse CSS files for tokens |
| `lib/server/colourtoken-integration.ts` | ColourToken App interface |
| `lib/server/typographybuilder-integration.ts` | TypographyBuilder App interface |
| `lib/server/logo-handler.ts` | Logo upload and variant management |
| `lib/server/photography-handler.ts` | Photography style and ImageBrowser |
| `lib/server/visual-guidelines-generator.ts` | AI guidelines for visual sections |
| `lib/server/types/visual.ts` | Visual type definitions |

---

## Notes

- Visual sections are processed SEPARATELY from standard sections
- CSS Token Parser is ported from existing `Empire v3.1/src/utils/colorTokenParser.js`
- Utility apps (ColourToken, TypographyBuilder, LogoMaker, ImageBrowser) are SEPARATE projects
- Communication with utility apps is file-based (input/output JSON files)
- Visual guidelines are generated AFTER technical data is captured
- Accessibility guidelines auto-calculated from colour contrast ratios
- LogoMaker and ImageBrowser require external API keys (managed by APIKeyManager)

---

## Key Decisions

1. **Port existing code** - Don't rebuild what exists in ColorTokenGenerator and Empire v3.1
2. **ColourToken from ColorTokenGenerator** - Existing app is 80% complete
3. **File-based communication** - Simple, debuggable, no IPC complexity
4. **Accessibility built-in** - Contrast ratios calculated automatically
5. **Domain defaults** - Use Phase 3 domain to set photography style defaults
6. **Colour Pizza API** - Use for colour naming and palette suggestions
7. **Background Remover API** - Use for auto-generating transparent logo variants

---

## Source Files Reference

### Files to Port

| Source | Destination | Notes |
|--------|-------------|-------|
| `Empire v3.1/src/utils/colorTokenParser.js` | `css-token-parser.ts` | Main CSS parsing logic |

### Files to Reference

| File | Use For |
|------|---------|
| `ColorTokenGenerator/ThemeTokenGen/simple-theme-gen.js` | OKLCH scales, color theory |
| `ColorTokenGenerator/ThemeTokenGen/color-theory-comparison.js` | Harmony algorithms |
| `Empire v3.1/src/utils/brandFileSystem.js` | File I/O patterns |
| `Empire v4.1/shared/styles/tokens/` | Token structure reference |

### Assets to Copy

| Source | Destination | Notes |
|--------|-------------|-------|
| `Empire v4.1/shared/Icons/` | Phosphor icons library | 1,512 SVG icons |

---

*Phase 7 Complete (7A + 7B + 7C) - 199 tests total*
*Updated: 2026-01-15*

---

## Phase 7B Build Notes

### Files Created
- `lib/server/visual-router.ts` - Visual section router (7B.1)
- `lib/server/logo-handler.ts` - Logo upload and variant tracking (7B.2)
- `lib/server/photography-handler.ts` - Photography style and ImageBrowser (7B.3)
- `lib/server/visual-guidelines-generator.ts` - Guidelines with accessibility (7B.4)
- `scripts/test-visual.ts` - 61 tests

### Key Features
- **Visual Router**: Routes 10 visual sections to appropriate handlers (css_parser, builder_app, asset_upload, config_ui)
- **Logo Handler**: Tracks 7 logo variants (main, transparent, dark, light, favicon, icon, wordmark) with required/recommended status
- **Photography Handler**: Domain-based photography style defaults (therapeutic, ecommerce, professional, creative, technology, hospitality)
- **Guidelines Generator**: Auto-generates usage do's/don'ts with WCAG accessibility analysis and color psychology notes

### Test Coverage
- Visual Router: 12 tests (constants, utilities, routing, builder app integration)
- Logo Handler: 12 tests (constants, paths, status)
- Photography Handler: 10 tests (constants, styles, status)
- Guidelines Generator: 24 tests (color, typography, logo, photography guidelines)
- Accessibility: 3 tests (contrast calculations)

---

## Phase 7C Build Notes

### Files Created
- `lib/server/colourtoken-integration.ts` - ColourToken app integration (7C.1)
- `lib/server/typographybuilder-integration.ts` - TypographyBuilder app integration (7C.2)
- `lib/server/logomaker-integration.ts` - LogoMaker app integration (7C.3)
- `lib/server/imagebrowser-integration.ts` - ImageBrowser app integration (7C.4)
- `scripts/test-integrations.ts` - 65 tests for all Phase 7C integrations

### Key Features
- **File-Based Communication**: All utility apps use input/output JSON files for communication
  - Input: `context/visual/_appname-input.json`
  - Output: `context/visual/_appname-output.json`
- **ColourToken Integration**: CSS variable generation, Tailwind config output, palette editing, semantic colour support
- **TypographyBuilder Integration**: Google Fonts import URL generation, type scale configuration, font metadata
- **LogoMaker Integration**: 7 logo variants (main, transparent, dark, light, favicon, icon, wordmark), preview HTML generation
- **ImageBrowser Integration**: Stock photo selection with licensing, attribution HTML/JSON, diversity tracking

### Test Coverage (65 tests)
- ColourToken Integration: 13 tests (types, launch, output handling, CSS generation, Tailwind config)
- TypographyBuilder Integration: 15 tests (types, launch, output handling, Google Fonts URL, section conversion)
- LogoMaker Integration: 14 tests (types, launch, output handling, variants, preview HTML)
- ImageBrowser Integration: 18 tests (types, launch, output handling, attribution, photography section)
- Cross-Integration: 5 tests (file patterns, cleanup, multiple apps)

### Integration Pattern

```
┌─────────────────────┐     ┌─────────────────────┐
│  BrandKit Workflow  │     │   Utility App       │
│                     │     │   (ColourToken,     │
│  1. Write input.json├────►│    LogoMaker, etc)  │
│                     │     │                     │
│  4. Read output.json│◄────┤  2. User completes  │
│  5. Convert to      │     │     task in app     │
│     section format  │     │  3. Write output    │
└─────────────────────┘     └─────────────────────┘
```

### Total Phase 7 Tests: 230
- 7A: 104 tests (CSS Token Parser - includes 7A.4 enhancements)
- 7B: 61 tests (Visual Router + Handlers)
- 7C: 65 tests (Utility App Integrations)

---

## Phase 7A.4: CSS Parser Enhancements (Complete ✓)

> **Status:** Complete | **Completed:** 2026-01-15
> **Objective:** Extend CSS token parser to handle additional input formats

### New Parsers to Add

| Parser | Format | Description |
|--------|--------|-------------|
| `parseSCSSVariables()` | SCSS/SASS | Parse `$variable: value;` declarations |
| `parseCSSInJS()` | CSS-in-JS | Parse styled-components, emotion themes |
| `parseDesignTokens()` | Design Tokens | Parse Style Dictionary JSON format |
| `parseAnyFormat()` | Auto-detect | Wrapper that routes to correct parser |

### Files to Modify

| File | Changes |
|------|---------|
| `lib/server/types/visual.ts` | Add `DesignToken` interface, extend `ColorFormat` |
| `lib/server/css-token-parser.ts` | Add 4 new parser functions |
| `lib/server/index.ts` | Export new functions |
| `scripts/test-css-parser.ts` | Add ~31 tests for new formats |

### SCSS Parser

```typescript
// Pattern: $variable-name: value;
const SCSS_VAR_PATTERN = /\$([a-zA-Z_][\w-]*)\s*:\s*([^;!]+)(?:\s*!default)?;/g;
```

**Handles:**
- Basic variables: `$primary-color: #8FA68A;`
- With !default: `$font-family: 'Roboto' !default;`
- RGB/RGBA values: `$secondary: rgba(143, 166, 138, 0.8);`

### CSS-in-JS Parser

```typescript
// Patterns for theme objects, exports, template literals
const patterns = [
  /(?:colors?|palette|theme)\s*[=:]\s*\{([^}]+)\}/gi,
  /export\s+(?:const|let)\s+(\w+)\s*=\s*['"]?(#[0-9a-fA-F]{3,8})['"]?/gi
];
```

**Handles:**
- Theme objects: `const colors = { primary: '#8FA68A' }`
- Exports: `export const PRIMARY = '#8FA68A';`
- Emotion props: `css={{ backgroundColor: '#8FA68A' }}`

### Design Token Parser

```typescript
// Style Dictionary / DTCG format
{
  "colors": {
    "primary": {
      "500": { "value": "#8FA68A" }  // or "$value" for DTCG
    }
  }
}
```

**Handles:**
- Style Dictionary JSON format
- DTCG spec (`$value` instead of `value`)
- Nested token hierarchies
- Token references (warns, doesn't resolve)

### Auto-Detection Logic

```typescript
export function parseAnyFormat(content: string, filename?: string): CSSParseResult {
  // 1. Check file extension (.scss, .json)
  // 2. Check content patterns ($var:, export const, "value")
  // 3. Default to existing CSS parser
}
```

### Test Plan

| Group | Tests | Description |
|-------|-------|-------------|
| SCSS Variables | 8 | Basic, defaults, references, fonts |
| CSS-in-JS | 10 | Objects, exports, templates, nested |
| Design Tokens | 8 | Style Dictionary, DTCG, nested |
| Auto-Detection | 5 | Extension, content sniffing |
| **Total New** | **31** | |

**Target:** 73 existing + 31 new = **104 tests**

### Implementation Notes

- Keep backward compatibility - existing `parseCSSTokens()` unchanged
- All new parsers output same `ParsedColor[]`/`ParsedFont[]` types
- Don't resolve references (SCSS `$other-var`, token `{ref}`) - just warn
- SCSS map syntax out of scope for MVP
- CSS-in-JS only extracts static values, not computed

### Build Notes (2026-01-15)

**Files Modified:**
- `lib/server/types/visual.ts` - Added `DesignToken`, `DesignTokenType`, `DesignTokenSource`, `DesignTokenParseResult` types; extended `ColorFormat` and `FontSource` unions
- `lib/server/css-token-parser.ts` - Added 4 parser functions (~400 lines)
- `lib/server/index.ts` - Added exports for new types and functions
- `scripts/test-css-parser.ts` - Added 31 tests

**Test Results:** 104 tests passing (73 existing + 31 new)

**Functions Implemented:**
| Function | Description |
|----------|-------------|
| `parseSCSSVariables()` | Parses SCSS `$variable: value;` declarations |
| `parseCSSInJS()` | Parses theme objects, exports, styled-components |
| `parseDesignTokens()` | Parses Style Dictionary/DTCG JSON format |
| `parseAnyFormat()` | Auto-detects format and routes to correct parser |
| `detectFormat()` | Returns detected format without parsing |

