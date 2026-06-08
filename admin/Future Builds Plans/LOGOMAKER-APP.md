# LogoMaker App - Build Plan

> Logo creation and variant generation tool for BrandKit Workflow integration.
> **Status:** Not Started | **Priority:** MEDIUM
> **Location:** `C:\Users\Business\LogoMaker\`

---

## Overview

LogoMaker is a standalone utility app for creating simple logos and generating logo variants. It can work in two modes:
1. **Create Mode** - Generate new text-based logos (wordmarks, lettermarks)
2. **Variants Mode** - Generate variants from an existing logo (transparent, dark, favicon)

### Purpose
- Create simple text-based logos (wordmarks)
- Generate logo variants with different backgrounds
- Create favicons and app icons from logos
- Auto-generate transparent backgrounds
- Export in multiple formats (SVG, PNG)

### Scope Limitations
LogoMaker is NOT a full graphic design tool. It focuses on:
- Text-based logos (wordmarks, lettermarks)
- Simple combinations (text + basic shapes)
- Automated variant generation

For complex logos with custom illustrations, users should use professional design tools and upload the results.

---

## Integration with BrandKit Workflow

### Communication Pattern

```
┌─────────────────────────┐                    ┌─────────────────────────┐
│   BrandKit Workflow     │                    │     LogoMaker App       │
│                         │                    │                         │
│  1. launchLogoMaker()   │                    │                         │
│     writes input JSON   ├───────────────────►│  2. App reads input     │
│                         │                    │     - brand name        │
│                         │                    │     - colours           │
│                         │                    │     - typography        │
│                         │                    │     - mode              │
│                         │                    │                         │
│                         │                    │  3. User creates/edits  │
│                         │                    │     logo                │
│                         │                    │                         │
│  5. handleComplete()    │◄───────────────────┤  4. App writes output   │
│     reads output JSON   │                    │     - logo files        │
│     + logo files        │                    │     - metadata          │
└─────────────────────────┘                    └─────────────────────────┘
```

### File Locations

| File | Path | Written By |
|------|------|------------|
| Input | `{brand}/context/visual/_logomaker-input.json` | BrandKit Workflow |
| Output | `{brand}/context/visual/_logomaker-output.json` | LogoMaker App |
| Logo files | `{brand}/assets/logos/logo-{variant}.{ext}` | LogoMaker App |

### Integration Module

The integration layer is already built in Phase 7C:
- **File:** `BrandKit Workflow/lib/server/logomaker-integration.ts`
- **Exports:** `launchLogoMaker`, `handleLogoMakerComplete`, `isLogoMakerComplete`
- **Utilities:** `getExistingVariants`, `getMissingVariants`, `generateLogoPreviewHTML`

---

## Input/Output Specification

### Input JSON (`_logomaker-input.json`)

```typescript
interface LogoMakerInput {
  /** Brand name for logo text */
  brandName: string;
  /** Brand tagline/slogan */
  tagline?: string;
  /** Brand domain for style suggestions */
  domain?: string;
  /** Brand personality traits */
  personality?: string[];
  /** Colour palette to use */
  colours?: LogoColourInput;
  /** Typography preferences */
  typography?: LogoTypographyInput;
  /** Mode: create new or generate variants */
  mode: 'create' | 'variants';
  /** Existing logo path (for variants mode) */
  existingLogoPath?: string;
  /** Which variants to generate */
  requestedVariants?: LogoVariantType[];
}

interface LogoColourInput {
  primary: string;      // "#8FA68A"
  secondary?: string;   // "#D4A574"
  accent?: string;      // "#4A6741"
}

interface LogoTypographyInput {
  headingFont?: string;  // "Playfair Display"
  style?: 'serif' | 'sans-serif' | 'script' | 'display' | 'monospace';
}
```

**Example Input (Create Mode):**
```json
{
  "brandName": "Wellness Studio",
  "tagline": "Find Your Balance",
  "domain": "therapeutic",
  "mode": "create",
  "colours": {
    "primary": "#8FA68A",
    "secondary": "#D4A574"
  },
  "typography": {
    "headingFont": "Playfair Display",
    "style": "serif"
  }
}
```

**Example Input (Variants Mode):**
```json
{
  "brandName": "Wellness Studio",
  "mode": "variants",
  "existingLogoPath": "assets/logos/logo-main.svg",
  "requestedVariants": ["transparent", "dark", "favicon", "icon"]
}
```

### Output JSON (`_logomaker-output.json`)

```typescript
interface LogoMakerOutput {
  success: boolean;
  logos?: CreatedLogo[];
  metadata?: LogoMetadata;
  error?: string;
}

interface CreatedLogo {
  variant: LogoVariantType;  // 'main' | 'transparent' | 'dark' | etc.
  path: string;              // "assets/logos/logo-main.svg"
  format: 'svg' | 'png' | 'jpg' | 'webp';
  width: number;
  height: number;
  fileSize: number;
  background: 'transparent' | 'white' | 'dark' | 'colour';
}

interface LogoMetadata {
  style: LogoStyle;          // 'wordmark' | 'lettermark' | etc.
  colours: string[];         // Hex values used
  fonts?: string[];          // Fonts used
  iconDescription?: string;  // Description of icon/symbol
  designRationale?: string;  // Why this design was chosen
  createdAt: string;
  version?: string;
}
```

**Example Output:**
```json
{
  "success": true,
  "logos": [
    {
      "variant": "main",
      "path": "assets/logos/logo-main.svg",
      "format": "svg",
      "width": 400,
      "height": 100,
      "fileSize": 2048,
      "background": "white"
    },
    {
      "variant": "transparent",
      "path": "assets/logos/logo-transparent.png",
      "format": "png",
      "width": 400,
      "height": 100,
      "fileSize": 8192,
      "background": "transparent"
    },
    {
      "variant": "favicon",
      "path": "assets/logos/favicon.png",
      "format": "png",
      "width": 32,
      "height": 32,
      "fileSize": 1024,
      "background": "transparent"
    }
  ],
  "metadata": {
    "style": "wordmark",
    "colours": ["#8FA68A", "#FFFFFF"],
    "fonts": ["Playfair Display"],
    "designRationale": "Elegant serif wordmark reflecting wellness and balance",
    "createdAt": "2026-01-15T10:30:00.000Z"
  }
}
```

---

## Logo Variants

### Required/Recommended Variants

| Variant | Dimensions | Required | Use Case |
|---------|------------|----------|----------|
| `main` | 400×100 | **Required** | Primary logo on light backgrounds |
| `transparent` | 400×100 | Recommended | Overlays, watermarks |
| `dark` | 400×100 | Recommended | Dark backgrounds |
| `light` | 400×100 | Optional | Coloured backgrounds |
| `favicon` | 32×32 | Recommended | Browser tabs |
| `icon` | 512×512 | Optional | App icons, social profiles |
| `wordmark` | 300×60 | Optional | Text-only version |

### Logo Styles

| Style | Description | Complexity |
|-------|-------------|------------|
| `wordmark` | Brand name in stylized typography | LOW |
| `lettermark` | Initials or abbreviation | LOW |
| `brandmark` | Symbol/icon only | HIGH (out of scope) |
| `combination` | Icon + text | MEDIUM |
| `emblem` | Text inside shape/badge | MEDIUM |
| `abstract` | Abstract shapes | HIGH (out of scope) |
| `mascot` | Character-based | HIGH (out of scope) |

---

## Feature Requirements

### Core Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Wordmark creator | HIGH | Generate text-based logos |
| Font selection | HIGH | Choose from available fonts |
| Colour application | HIGH | Apply brand colours |
| Variant generator | HIGH | Generate required variants |
| SVG export | HIGH | Vector format export |
| PNG export | HIGH | Raster format export |
| Background remover | HIGH | Create transparent versions |

### Advanced Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Favicon generator | MEDIUM | Auto-resize for favicons |
| Icon generator | MEDIUM | Square icon variants |
| Simple shapes | MEDIUM | Basic geometric shapes |
| Layout options | MEDIUM | Horizontal, stacked, icon-only |
| Lettermark mode | LOW | Create initial-based logos |

---

## UI Design

### Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│  LogoMaker                                             [Save] [Cancel]│
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  Mode: [● Create New] [○ Generate Variants]                          │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │                         Canvas                                  │   │
│  │  ─────────────────────────────────────────────────────────     │   │
│  │                                                                  │   │
│  │                   Wellness Studio                                │   │
│  │                   Find Your Balance                              │   │
│  │                                                                  │   │
│  │  ─────────────────────────────────────────────────────────     │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────┐  ┌───────────────────────────────────────┐  │
│  │  Style              │  │  Settings                              │  │
│  │                     │  │                                        │  │
│  │  ○ Wordmark         │  │  Font: [Playfair Display ▼]           │  │
│  │  ○ Lettermark       │  │  Size: [48px ▼]                       │  │
│  │  ○ With Tagline     │  │  Weight: [400 ▼]                      │  │
│  │                     │  │                                        │  │
│  │  Layout:            │  │  Primary: [■ #8FA68A]                 │  │
│  │  ○ Horizontal       │  │  Background: [■ #FFFFFF]              │  │
│  │  ○ Stacked          │  │                                        │  │
│  └─────────────────────┘  └───────────────────────────────────────┘  │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │  Generate Variants                                              │   │
│  │  [✓] Main  [✓] Transparent  [✓] Dark  [✓] Favicon  [ ] Icon   │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Technical Stack

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Framework | Electron or Tauri | Desktop app with file access |
| Canvas | Fabric.js or Konva | Canvas manipulation |
| SVG | svg.js or paper.js | Vector graphics |
| Image processing | Sharp | PNG generation, resizing |
| Background removal | remove.bg API or local | Transparent variants |
| Build | Vite | Fast dev experience |

---

## APIs Required

| API | Purpose | Key Required |
|-----|---------|--------------|
| remove.bg | Background removal for transparent variants | Yes (paid) |
| Google Fonts | Font loading | Yes (free) |

**Alternative:** Local background removal using ML model (e.g., U2Net) to avoid API costs.

---

## Build Tasks

### Phase 1: Core (MVP)

- [ ] Initialize project with Electron/Tauri + Vite
- [ ] Create canvas component with text rendering
- [ ] Implement font loading (Google Fonts + system)
- [ ] Create colour picker integration
- [ ] Implement SVG export
- [ ] Implement PNG export with Sharp
- [ ] Create JSON input/output handling
- [ ] Test integration with BrandKit Workflow

### Phase 2: Variant Generation

- [ ] Implement background colour changes
- [ ] Implement favicon generation (resize to 32×32)
- [ ] Implement icon generation (resize to 512×512)
- [ ] Add background removal (remove.bg API or local)
- [ ] Generate all variants in batch

### Phase 3: Enhanced Features

- [ ] Add lettermark mode
- [ ] Add simple shape options
- [ ] Add stacked layout option
- [ ] Add tagline support
- [ ] Add preview modes (on dark/light backgrounds)

---

## File Output Structure

```
{brand}/
└── assets/
    └── logos/
        ├── logo-main.svg          # Primary vector logo
        ├── logo-main.png          # Primary raster logo
        ├── logo-transparent.png   # Transparent background
        ├── logo-dark.svg          # For dark backgrounds
        ├── logo-dark.png
        ├── logo-light.svg         # For coloured backgrounds
        ├── favicon.png            # 32×32 favicon
        ├── favicon.ico            # ICO format
        └── icon-512.png           # App icon
```

---

## Testing

### Integration Test

```bash
# 1. Create test input file
echo '{"brandName":"Test Brand","mode":"create","colours":{"primary":"#8FA68A"}}' > test-input.json

# 2. Launch LogoMaker with input
logomaker --input test-input.json --output test-output.json --assets ./assets

# 3. User creates logo and saves

# 4. Verify output
cat test-output.json  # Should have logos array
ls ./assets/logos/    # Should have logo files
```

### BrandKit Workflow Integration Test

```typescript
// In BrandKit Workflow
const result = await launchLogoMaker(brandPath, { mode: 'create' });
// User completes in LogoMaker app
const output = await handleLogoMakerComplete(brandPath);
const section = convertToLogosSection(output);
const previewHTML = generateLogoPreviewHTML(output.logos);
```

---

## Future Enhancements

| Enhancement | Complexity | Description |
|-------------|------------|-------------|
| **Canva API Integration** | High | Generate social media variants (LinkedIn banner, Twitter header, Instagram) via Canva API with size library |
| **Brand Asset Versioning** | Medium | Track changes to logo files over time, enable rollback to previous versions |
| **Social Media Size Library** | Low | Pre-defined sizes for all major platforms (Facebook cover, YouTube thumbnail, etc.) |
| **Batch Variant Generation** | Low | Generate all variants at once with progress tracking |

> **Note:** Social media variant generation will integrate with a separate Canva API app that manages image generation with the size library.

---

## Notes

- The integration layer is already complete (Phase 7C.3)
- LogoMaker should save files directly to the brand's assets/logos folder
- File paths in output JSON should be relative to brand folder
- Consider offline-first with local background removal
- SVG preferred for main logo (scalable), PNG for variants
- Favicon should also generate .ico format for legacy browser support

---

*Last updated: 2026-01-15*
