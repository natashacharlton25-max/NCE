# TypographyBuilder App - Build Plan

> Interactive font selection and type scale builder for BrandKit Workflow integration.
> **Status:** Not Started | **Priority:** HIGH
> **Location:** `C:\Users\Business\TypographyBuilder\`

---

## Overview

TypographyBuilder is a standalone utility app that provides an interactive UI for selecting brand fonts and defining type scales. It integrates with Google Fonts API for font discovery and previewing.

### Purpose
- Search and browse Google Fonts library
- Select fonts for heading, body, accent, and monospace roles
- Get font pairing suggestions
- Preview fonts with sample text
- Define type scale (h1-h6, body, caption sizes)
- Configure font weights and line heights

---

## Integration with BrandKit Workflow

### Communication Pattern

```
┌─────────────────────────┐                    ┌─────────────────────────┐
│   BrandKit Workflow     │                    │  TypographyBuilder App  │
│                         │                    │                         │
│  1. launchTypography-   │                    │                         │
│     Builder() writes    ├───────────────────►│  2. App reads input     │
│     input JSON          │                    │     - brand name        │
│                         │                    │     - domain            │
│                         │                    │     - existing fonts    │
│                         │                    │     - personality       │
│                         │                    │                         │
│                         │                    │  3. User selects fonts  │
│                         │                    │     and scale           │
│                         │                    │                         │
│  5. handleComplete()    │◄───────────────────┤  4. App writes output   │
│     reads output JSON   │                    │     - fonts             │
│     generates imports   │                    │     - type scale        │
└─────────────────────────┘                    └─────────────────────────┘
```

### File Locations

| File | Path | Written By |
|------|------|------------|
| Input | `{brand}/context/visual/_typographybuilder-input.json` | BrandKit Workflow |
| Output | `{brand}/context/visual/_typographybuilder-output.json` | TypographyBuilder App |

### Integration Module

The integration layer is already built in Phase 7C:
- **File:** `BrandKit Workflow/lib/server/typographybuilder-integration.ts`
- **Exports:** `launchTypographyBuilder`, `handleTypographyBuilderComplete`
- **Utilities:** `generateGoogleFontsImport`, `generatePreloadLinks`, `generateTypeScaleCSS`

---

## Input/Output Specification

### Input JSON (`_typographybuilder-input.json`)

```typescript
interface TypographyBuilderInput {
  /** Brand name for context */
  brandName: string;
  /** Brand domain for font suggestions */
  domain?: string;
  /** Mode: create new or edit existing */
  mode: 'create' | 'edit';
  /** Existing fonts to pre-populate (edit mode) */
  existingFonts?: ParsedFont[];
  /** Brand personality traits for font matching */
  personality?: string[];
}
```

**Example Input:**
```json
{
  "brandName": "Wellness Studio",
  "domain": "therapeutic",
  "mode": "create",
  "personality": ["calm", "professional", "nurturing"]
}
```

### Output JSON (`_typographybuilder-output.json`)

```typescript
interface TypographyBuilderOutput {
  success: boolean;
  fonts?: TypographyFont[];
  scale?: TypeScale;
  error?: string;
}

interface TypographyFont {
  role: FontRole;           // 'heading' | 'body' | 'accent' | 'monospace'
  family: string;           // "Playfair Display"
  source: FontSource;       // 'google' | 'system' | 'custom'
  weights: number[];        // [400, 700]
  fallback: string;         // "serif"
  googleFontsUrl?: string;  // Full Google Fonts URL
  usage?: string;           // "Headlines and section titles"
}

interface TypeScale {
  h1: TypeScaleEntry;
  h2: TypeScaleEntry;
  h3: TypeScaleEntry;
  h4: TypeScaleEntry;
  h5?: TypeScaleEntry;
  h6?: TypeScaleEntry;
  body: TypeScaleEntry;
  bodyLarge?: TypeScaleEntry;
  small: TypeScaleEntry;
  caption: TypeScaleEntry;
}

interface TypeScaleEntry {
  fontSize: string;         // "2.5rem"
  lineHeight: string;       // "1.2"
  fontWeight: number;       // 700
  letterSpacing?: string;   // "-0.02em"
  fontRole: FontRole;       // Which font to use
}
```

**Example Output:**
```json
{
  "success": true,
  "fonts": [
    {
      "role": "heading",
      "family": "Playfair Display",
      "source": "google",
      "weights": [400, 700],
      "fallback": "serif",
      "googleFontsUrl": "https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&display=swap",
      "usage": "Headlines, section titles, and featured quotes"
    },
    {
      "role": "body",
      "family": "Source Sans 3",
      "source": "google",
      "weights": [400, 600],
      "fallback": "sans-serif",
      "googleFontsUrl": "https://fonts.googleapis.com/css2?family=Source+Sans+3:wght@400;600&display=swap",
      "usage": "Body text, UI elements, and navigation"
    }
  ],
  "scale": {
    "h1": { "fontSize": "3rem", "lineHeight": "1.1", "fontWeight": 700, "fontRole": "heading" },
    "h2": { "fontSize": "2.25rem", "lineHeight": "1.2", "fontWeight": 700, "fontRole": "heading" },
    "h3": { "fontSize": "1.75rem", "lineHeight": "1.25", "fontWeight": 600, "fontRole": "heading" },
    "h4": { "fontSize": "1.25rem", "lineHeight": "1.3", "fontWeight": 600, "fontRole": "heading" },
    "body": { "fontSize": "1rem", "lineHeight": "1.6", "fontWeight": 400, "fontRole": "body" },
    "small": { "fontSize": "0.875rem", "lineHeight": "1.5", "fontWeight": 400, "fontRole": "body" },
    "caption": { "fontSize": "0.75rem", "lineHeight": "1.4", "fontWeight": 400, "fontRole": "body" }
  }
}
```

---

## Feature Requirements

### Core Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Google Fonts search | HIGH | Search 1500+ fonts by name |
| Font preview | HIGH | Live preview with sample text |
| Role assignment | HIGH | Assign heading/body/accent/mono |
| Weight selection | HIGH | Choose which weights to include |
| Fallback detection | HIGH | Auto-assign serif/sans-serif/mono |
| Type scale editor | HIGH | Set sizes for h1-h6, body, caption |
| Export to JSON | HIGH | Write output file for BrandKit |

### Advanced Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Font pairing suggestions | MEDIUM | Suggest compatible pairs |
| Category filter | MEDIUM | Filter by serif/sans/display/etc |
| Personality matching | MEDIUM | Suggest fonts based on brand traits |
| Variable fonts | MEDIUM | Support variable font weights |
| Font preview context | LOW | Preview in card/header/paragraph |
| Recently used | LOW | Track frequently selected fonts |

---

## UI Design

### Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│  TypographyBuilder                                     [Save] [Cancel]│
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │  Search Fonts: [                                          🔍 ] │   │
│  │  Filter: [All ▼] [Serif ▼] [Sans ▼] [Display ▼] [Mono ▼]      │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌─────────────────────┐  ┌───────────────────────────────────────┐  │
│  │   Font Results      │  │           Font Selection              │  │
│  │                     │  │                                        │  │
│  │  ▸ Playfair Display │  │  HEADING: Playfair Display            │  │
│  │  ▸ Source Sans 3    │  │  Weights: [✓400] [✓700]               │  │
│  │  ▸ Inter            │  │  Fallback: serif                       │  │
│  │  ▸ Lora             │  │                                        │  │
│  │  ▸ Merriweather     │  │  BODY: Source Sans 3                   │  │
│  │  ▸ Roboto           │  │  Weights: [✓400] [✓600]               │  │
│  │  ...                │  │  Fallback: sans-serif                  │  │
│  └─────────────────────┘  └───────────────────────────────────────┘  │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │                         Preview                                 │   │
│  │  ─────────────────────────────────────────────────────────     │   │
│  │  This is a Heading (Playfair Display)                          │   │
│  │                                                                  │   │
│  │  This is body text set in Source Sans 3. The quick brown       │   │
│  │  fox jumps over the lazy dog. Typography is the art and        │   │
│  │  technique of arranging type.                                   │   │
│  │  ─────────────────────────────────────────────────────────     │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │  Type Scale                                                     │   │
│  │  H1: [3rem   ▼] LH: [1.1 ▼] Weight: [700▼]                     │   │
│  │  H2: [2.25rem▼] LH: [1.2 ▼] Weight: [700▼]                     │   │
│  │  H3: [1.75rem▼] LH: [1.25▼] Weight: [600▼]                     │   │
│  │  Body: [1rem ▼] LH: [1.6 ▼] Weight: [400▼]                     │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Technical Stack

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Framework | Electron or Tauri | Desktop app with file access |
| UI | React or Svelte | Component-based |
| Font loading | Web Font Loader | Async font loading |
| API | Google Fonts API | Font metadata and URLs |
| Build | Vite | Fast dev experience |

---

## APIs Required

| API | Purpose | Key Required |
|-----|---------|--------------|
| Google Fonts API | Font metadata, categories, variants | Yes (free) |

### Google Fonts API Usage

```typescript
// Get all fonts
GET https://www.googleapis.com/webfonts/v1/webfonts?key=API_KEY&sort=popularity

// Response includes:
// - family: "Roboto"
// - variants: ["300", "400", "700", "italic"]
// - subsets: ["latin", "latin-ext"]
// - category: "sans-serif"
```

---

## Font Pairing Suggestions

### Pre-configured Pairings

| Heading | Body | Style |
|---------|------|-------|
| Playfair Display | Source Sans 3 | Classic/Elegant |
| Montserrat | Open Sans | Modern/Clean |
| Lora | Roboto | Traditional/Readable |
| Poppins | Inter | Contemporary |
| Merriweather | Lato | Warm/Approachable |
| Oswald | Source Serif Pro | Bold/Editorial |

### Domain-Based Suggestions

| Domain | Heading Style | Body Style |
|--------|--------------|------------|
| Therapeutic | Soft serif, humanist | Clean sans-serif |
| Professional | Strong sans-serif | Neutral sans-serif |
| Creative | Display/decorative | Readable sans |
| Ecommerce | Modern sans | Clean sans |
| Education | Traditional serif | Readable body |

---

## Build Tasks

### Phase 1: Core (MVP)

- [ ] Initialize project with Electron/Tauri + Vite
- [ ] Set up Google Fonts API integration
- [ ] Create font search component
- [ ] Create font list/results component
- [ ] Create font preview component
- [ ] Create role assignment UI (heading/body/accent/mono)
- [ ] Create weight selector component
- [ ] Implement JSON input/output
- [ ] Test integration with BrandKit Workflow

### Phase 2: Type Scale

- [ ] Create type scale editor component
- [ ] Add preset scale options (major third, perfect fourth, etc.)
- [ ] Add live preview of scale
- [ ] Add responsive scale option

### Phase 3: Enhanced Features

- [ ] Add font pairing suggestions
- [ ] Add category filters (serif, sans, display, mono)
- [ ] Add personality-based suggestions
- [ ] Add recently used fonts
- [ ] Add variable font support

---

## Popular Fonts Reference

### By Role (Pre-configured in Integration)

```typescript
const POPULAR_FONTS = {
  heading: [
    'Playfair Display', 'Montserrat', 'Oswald', 'Lora',
    'Merriweather', 'Poppins', 'Raleway', 'Roboto Slab'
  ],
  body: [
    'Source Sans 3', 'Open Sans', 'Roboto', 'Lato',
    'Inter', 'Nunito', 'Work Sans', 'IBM Plex Sans'
  ],
  accent: [
    'Dancing Script', 'Pacifico', 'Caveat', 'Satisfy',
    'Great Vibes', 'Sacramento', 'Allura', 'Alex Brush'
  ],
  monospace: [
    'Fira Code', 'JetBrains Mono', 'Source Code Pro',
    'IBM Plex Mono', 'Roboto Mono', 'Space Mono'
  ]
};
```

---

## Testing

### Integration Test

```bash
# 1. Create test input file
echo '{"brandName":"Test","mode":"create","domain":"professional"}' > test-input.json

# 2. Launch TypographyBuilder with input
typographybuilder --input test-input.json --output test-output.json

# 3. User selects fonts and saves

# 4. Verify output
cat test-output.json  # Should have fonts and scale
```

### BrandKit Workflow Integration Test

```typescript
// In BrandKit Workflow
const result = await launchTypographyBuilder(brandPath, { mode: 'create' });
// User completes in TypographyBuilder app
const output = await handleTypographyBuilderComplete(brandPath);
const importUrl = generateGoogleFontsImport(output.fonts);
const section = convertToTypographySection(output);
```

---

## Notes

- The integration layer is already complete (Phase 7C.2)
- Google Fonts API key should be stored in APIKeyManager
- Font loading can be slow - implement lazy loading and caching
- Consider offline mode with cached popular fonts metadata
- Variable fonts reduce file size but have browser support considerations

---

*Last updated: 2026-01-15*
