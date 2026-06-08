# Visual Consistency Checker App - Build Plan

> Palette extraction and visual consistency analysis tool for BrandKit Workflow integration.
> **Status:** Not Started | **Priority:** MEDIUM
> **Location:** `C:\Users\Business\VisualConsistencyChecker\`

---

## Overview

Visual Consistency Checker is a standalone utility app that extracts colour palettes from images (logos, photos, screenshots) and checks visual consistency across brand assets. It helps ensure brand colours are used consistently and can bootstrap a palette from existing assets.

### Purpose
- Extract dominant colours from uploaded images (logos, photos, website screenshots)
- Analyse colour consistency across multiple brand assets
- Detect mismatches between logo colours and defined palette
- Suggest palette from uploaded images for new brands
- Generate colour harmony analysis from extracted colours

### Use Cases

| Use Case | Description |
|----------|-------------|
| **Palette Bootstrap** | No existing CSS - extract palette from logo/images to seed ColourToken |
| **Consistency Check** | Compare logo colours against defined palette, flag mismatches |
| **Asset Audit** | Scan all brand images, report colour usage patterns |
| **Rebrand Validation** | After palette change, check which assets need updating |

---

## Integration with BrandKit Workflow

### Communication Pattern

```
┌─────────────────────────┐                    ┌─────────────────────────┐
│   BrandKit Workflow     │                    │  VisualConsistencyChecker│
│                         │                    │                         │
│  1. launchConsistency-  │                    │                         │
│     Checker() writes    ├───────────────────►│  2. App reads input     │
│     input JSON          │                    │     - image paths       │
│                         │                    │     - defined palette   │
│                         │                    │     - check mode        │
│                         │                    │                         │
│                         │                    │  3. App analyses images │
│                         │                    │     extracts colours    │
│                         │                    │                         │
│  5. handleComplete()    │◄───────────────────┤  4. App writes output   │
│     reads output JSON   │                    │     - extracted palette │
│     flags mismatches    │                    │     - consistency report│
└─────────────────────────┘                    └─────────────────────────┘
```

### File Locations

| File | Path | Written By |
|------|------|------------|
| Input | `{brand}/context/visual/_consistency-input.json` | BrandKit Workflow |
| Output | `{brand}/context/visual/_consistency-output.json` | VisualConsistencyChecker |

---

## Input/Output Specification

### Input JSON (`_consistency-input.json`)

```typescript
interface ConsistencyCheckerInput {
  /** Brand name for context */
  brandName: string;
  /** Mode: extract palette or check consistency */
  mode: 'extract' | 'check' | 'audit';
  /** Images to analyse */
  images: ImageToAnalyse[];
  /** Defined palette to check against (for 'check' mode) */
  definedPalette?: DefinedColour[];
  /** Tolerance for colour matching (0-100, default 10) */
  colourTolerance?: number;
  /** Minimum prominence to include colour (0-1, default 0.05) */
  minProminence?: number;
}

interface ImageToAnalyse {
  path: string;              // Relative or absolute path
  type: ImageType;           // 'logo' | 'photo' | 'screenshot' | 'graphic'
  name?: string;             // Friendly name for reports
}

interface DefinedColour {
  name: string;              // "Primary", "Sage Green"
  hex: string;               // "#8FA68A"
  role?: ColourRole;         // 'primary' | 'secondary' | 'accent' | etc.
}

type ImageType = 'logo' | 'photo' | 'screenshot' | 'graphic' | 'icon';
```

**Example Input (Extract Mode):**
```json
{
  "brandName": "Wellness Studio",
  "mode": "extract",
  "images": [
    { "path": "assets/logos/logo-main.svg", "type": "logo", "name": "Main Logo" },
    { "path": "context/images/hero.jpg", "type": "photo", "name": "Hero Image" }
  ],
  "minProminence": 0.05
}
```

**Example Input (Check Mode):**
```json
{
  "brandName": "Wellness Studio",
  "mode": "check",
  "images": [
    { "path": "assets/logos/logo-main.svg", "type": "logo" },
    { "path": "assets/logos/logo-dark.svg", "type": "logo" }
  ],
  "definedPalette": [
    { "name": "Sage", "hex": "#8FA68A", "role": "primary" },
    { "name": "Warm Sand", "hex": "#D4A574", "role": "accent" }
  ],
  "colourTolerance": 15
}
```

### Output JSON (`_consistency-output.json`)

```typescript
interface ConsistencyCheckerOutput {
  success: boolean;
  /** Extracted colours from all images */
  extractedColours?: ExtractedColour[];
  /** Consistency report (for check/audit modes) */
  consistencyReport?: ConsistencyReport;
  /** Suggested palette based on extraction */
  suggestedPalette?: SuggestedPalette;
  error?: string;
}

interface ExtractedColour {
  hex: string;               // "#8FA68A"
  rgb: { r: number; g: number; b: number };
  hsl: { h: number; s: number; l: number };
  prominence: number;        // 0-1, how dominant in image
  sources: ColourSource[];   // Which images contain this colour
  suggestedName?: string;    // "Sage Green" (from Colour Pizza API)
  suggestedRole?: ColourRole; // Inferred role based on usage
}

interface ColourSource {
  imagePath: string;
  imageType: ImageType;
  prominence: number;        // Prominence in this specific image
  pixelCount?: number;       // Approximate pixel count
}

interface ConsistencyReport {
  overallScore: number;      // 0-100, consistency percentage
  status: 'consistent' | 'minor_issues' | 'major_issues';
  totalImages: number;
  analysedImages: number;
  issues: ConsistencyIssue[];
  summary: string;
}

interface ConsistencyIssue {
  severity: 'warning' | 'error';
  type: IssueType;
  imagePath: string;
  description: string;
  foundColour: string;       // Hex of problematic colour
  expectedColour?: string;   // Nearest defined colour
  distance: number;          // Colour distance (deltaE)
  suggestion: string;
}

type IssueType =
  | 'off_brand_colour'       // Colour not in palette
  | 'similar_but_wrong'      // Close to palette colour but not exact
  | 'missing_primary'        // Primary colour not used
  | 'inconsistent_shade';    // Using different shades across assets

interface SuggestedPalette {
  colours: SuggestedColourEntry[];
  confidence: number;        // 0-1, how confident in suggestions
  notes: string[];           // Explanation of suggestions
}

interface SuggestedColourEntry {
  hex: string;
  suggestedName: string;
  suggestedRole: ColourRole;
  prominence: number;
  reasoning: string;         // Why this role was suggested
}
```

**Example Output (Extract Mode):**
```json
{
  "success": true,
  "extractedColours": [
    {
      "hex": "#8FA68A",
      "rgb": { "r": 143, "g": 166, "b": 138 },
      "hsl": { "h": 109, "s": 14, "l": 60 },
      "prominence": 0.45,
      "sources": [
        { "imagePath": "assets/logos/logo-main.svg", "imageType": "logo", "prominence": 0.8 }
      ],
      "suggestedName": "Sage Green",
      "suggestedRole": "primary"
    },
    {
      "hex": "#D4A574",
      "rgb": { "r": 212, "g": 165, "b": 116 },
      "hsl": { "h": 31, "s": 50, "l": 64 },
      "prominence": 0.25,
      "sources": [
        { "imagePath": "context/images/hero.jpg", "imageType": "photo", "prominence": 0.3 }
      ],
      "suggestedName": "Warm Sand",
      "suggestedRole": "accent"
    }
  ],
  "suggestedPalette": {
    "colours": [
      {
        "hex": "#8FA68A",
        "suggestedName": "Sage Green",
        "suggestedRole": "primary",
        "prominence": 0.45,
        "reasoning": "Most prominent colour in logo (80% coverage)"
      },
      {
        "hex": "#D4A574",
        "suggestedName": "Warm Sand",
        "suggestedRole": "accent",
        "prominence": 0.25,
        "reasoning": "Secondary colour appearing in hero imagery"
      }
    ],
    "confidence": 0.85,
    "notes": [
      "Primary colour clearly dominant in logo",
      "Consider adding a neutral colour for text/backgrounds"
    ]
  }
}
```

**Example Output (Check Mode):**
```json
{
  "success": true,
  "consistencyReport": {
    "overallScore": 78,
    "status": "minor_issues",
    "totalImages": 2,
    "analysedImages": 2,
    "issues": [
      {
        "severity": "warning",
        "type": "similar_but_wrong",
        "imagePath": "assets/logos/logo-dark.svg",
        "description": "Logo uses #8BAA85 instead of defined primary #8FA68A",
        "foundColour": "#8BAA85",
        "expectedColour": "#8FA68A",
        "distance": 4.2,
        "suggestion": "Update logo-dark.svg to use exact primary colour #8FA68A"
      }
    ],
    "summary": "1 minor colour inconsistency found. Logo dark variant uses slightly different shade of primary colour."
  }
}
```

---

## Feature Requirements

### Core Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Image colour extraction | HIGH | Extract dominant colours from PNG, JPG, SVG |
| Colour clustering | HIGH | Group similar colours, identify dominant palette |
| Palette suggestion | HIGH | Suggest roles (primary, secondary, accent) |
| Consistency checking | HIGH | Compare against defined palette |
| Colour distance calculation | HIGH | Calculate deltaE for colour comparison |
| Issue reporting | HIGH | Generate actionable consistency report |
| JSON export | HIGH | Output results for BrandKit integration |

### Advanced Features

| Feature | Priority | Description |
|---------|----------|-------------|
| SVG parsing | MEDIUM | Extract colours from SVG paths and fills |
| Colour naming | MEDIUM | Use Colour Pizza API for colour names |
| Batch analysis | MEDIUM | Analyse entire assets folder at once |
| Historical comparison | LOW | Compare against previous extractions |
| Colour harmony analysis | LOW | Identify harmony type in extracted palette |

---

## Technical Implementation

### Colour Extraction Algorithm

```typescript
// 1. Load image and get pixel data
// 2. Quantize colours (reduce to manageable palette)
// 3. Cluster similar colours
// 4. Calculate prominence for each cluster
// 5. Filter by minimum prominence
// 6. Sort by prominence

interface ColourExtractionOptions {
  maxColours: number;        // Max colours to extract (default: 10)
  minProminence: number;     // Min prominence threshold (default: 0.05)
  quantizationQuality: number; // 1-10, higher = slower but more accurate
}
```

### Colour Distance (deltaE)

Use CIEDE2000 formula for perceptually accurate colour comparison:

```typescript
function calculateDeltaE(colour1: string, colour2: string): number {
  // Convert hex to LAB colour space
  // Apply CIEDE2000 formula
  // Return distance (0 = identical, >10 = clearly different)
}

// Thresholds:
// 0-1: Not perceptible by human eyes
// 1-2: Perceptible through close observation
// 2-10: Perceptible at a glance
// 11-49: Colours are more similar than different
// 50-100: Colours are more different than similar
```

### SVG Colour Extraction

```typescript
function extractSVGColours(svgContent: string): ExtractedColour[] {
  // Parse SVG XML
  // Extract fill, stroke, stop-color attributes
  // Handle gradients (extract stop colours)
  // Ignore 'none', 'transparent', 'inherit'
  // Calculate prominence based on element area
}
```

---

## UI Design

### Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│  Visual Consistency Checker                           [Analyse] [Save]│
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  Mode: [● Extract Palette] [○ Check Consistency] [○ Full Audit]      │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │  Images to Analyse                                    [+ Add]  │   │
│  │                                                                 │   │
│  │  [🖼] logo-main.svg      Logo       ████ #8FA68A (80%)         │   │
│  │  [🖼] logo-dark.svg      Logo       ████ #8BAA85 (75%) ⚠️     │   │
│  │  [🖼] hero.jpg           Photo      ████ #D4A574 (30%)         │   │
│  │                                                                 │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────────┐  ┌───────────────────────────────────┐  │
│  │  Extracted Palette      │  │  Consistency Score: 78/100        │  │
│  │                         │  │  Status: Minor Issues             │  │
│  │  ████ #8FA68A Primary   │  │                                    │  │
│  │  ████ #D4A574 Accent    │  │  Issues Found:                     │  │
│  │  ████ #FFFFFF Neutral   │  │  ⚠️ logo-dark.svg uses #8BAA85    │  │
│  │                         │  │     instead of #8FA68A             │  │
│  │  [Send to ColourToken]  │  │                                    │  │
│  └─────────────────────────┘  └───────────────────────────────────┘  │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Technical Stack

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Framework | Electron or Tauri | Desktop app with file access |
| UI | React or Svelte | Component-based |
| Colour extraction | node-vibrant or colorthief | Proven algorithms |
| Colour manipulation | culori | OKLCH + deltaE support |
| SVG parsing | svg-parser or cheerio | Extract SVG colours |
| Image processing | Sharp | Fast image loading |
| Build | Vite | Fast dev experience |

---

## APIs Required

| API | Purpose | Key Required |
|-----|---------|--------------|
| Colour Pizza | Colour names | Optional (free) |

---

## Build Tasks

### Phase 1: Core (MVP)

- [ ] Initialize project with Electron/Tauri + Vite
- [ ] Create image loading component (drag & drop)
- [ ] Implement colour extraction from raster images (PNG, JPG)
- [ ] Implement colour clustering algorithm
- [ ] Calculate colour prominence
- [ ] Create extracted palette display
- [ ] Implement JSON input/output handling
- [ ] Test integration with BrandKit Workflow

### Phase 2: Consistency Checking

- [ ] Implement deltaE colour distance calculation
- [ ] Create palette comparison logic
- [ ] Generate consistency report
- [ ] Create issue display UI
- [ ] Add severity levels (warning, error)
- [ ] Add suggestions for fixing issues

### Phase 3: SVG Support

- [ ] Implement SVG colour extraction
- [ ] Handle gradients and patterns
- [ ] Calculate area-based prominence for SVG elements
- [ ] Support nested SVG groups

### Phase 4: Enhanced Features

- [ ] Add Colour Pizza API for naming
- [ ] Add batch folder analysis
- [ ] Add harmony type detection
- [ ] Add "Send to ColourToken" integration
- [ ] Add historical comparison

---

## Integration with ColourToken

This app can bootstrap ColourToken with an extracted palette:

```typescript
// In BrandKit Workflow
const extraction = await handleConsistencyCheckerComplete(brandPath);

if (extraction.suggestedPalette) {
  // Launch ColourToken with suggested colours pre-populated
  await launchColourToken(brandPath, {
    mode: 'edit',
    suggestedColours: extraction.suggestedPalette.colours.map(c => ({
      hex: c.hex,
      source: 'extracted',
      prominence: c.suggestedRole
    }))
  });
}
```

---

## Testing

### Integration Test

```bash
# 1. Create test input file
echo '{
  "brandName": "Test",
  "mode": "extract",
  "images": [{"path": "logo.png", "type": "logo"}]
}' > test-input.json

# 2. Launch app with input
visualconsistencychecker --input test-input.json --output test-output.json

# 3. User triggers analysis

# 4. Verify output
cat test-output.json  # Should have extractedColours
```

### BrandKit Workflow Integration Test

```typescript
// In BrandKit Workflow
const result = await launchConsistencyChecker(brandPath, {
  mode: 'extract',
  images: [{ path: 'assets/logos/logo-main.svg', type: 'logo' }]
});

const output = await handleConsistencyCheckerComplete(brandPath);
console.log(output.extractedColours);     // Extracted palette
console.log(output.suggestedPalette);     // Role suggestions
```

---

## Notes

- This app complements ColourToken - use this to bootstrap palette, ColourToken to refine
- deltaE tolerance of 10-15 is recommended for brand consistency checks
- SVG colours should be weighted by element area, not count
- Consider caching extraction results for large image sets
- Integration with ColourToken should be seamless - one click to transfer palette

---

*Last updated: 2026-01-15*
