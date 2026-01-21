# ColourToken App - Build Plan

> Interactive colour palette builder for BrandKit Workflow integration.
> **Status:** Not Started | **Priority:** HIGH
> **Location:** `C:\Users\Business\ColourToken\`

---

## Overview

ColourToken is a standalone utility app that provides an interactive UI for building brand colour palettes. It integrates with BrandKit Workflow via file-based communication (JSON input/output).

### Purpose
- Create colour palettes from scratch when no CSS file exists
- Edit existing palettes extracted from CSS
- Generate shade scales (50-950) using OKLCH colour space
- Apply colour theory harmonies (complementary, analogous, etc.)
- Assign semantic roles (primary, secondary, accent, etc.)

---

## Integration with BrandKit Workflow

### Communication Pattern

```
┌─────────────────────────┐                    ┌─────────────────────────┐
│   BrandKit Workflow     │                    │    ColourToken App      │
│                         │                    │                         │
│  1. launchColourToken() │                    │                         │
│     writes input JSON   ├───────────────────►│  2. App reads input     │
│                         │                    │     - brand name        │
│                         │                    │     - domain            │
│                         │                    │     - existing colours  │
│                         │                    │     - suggestions       │
│                         │                    │                         │
│                         │                    │  3. User creates/edits  │
│                         │                    │     palette             │
│                         │                    │                         │
│  5. handleComplete()    │◄───────────────────┤  4. App writes output   │
│     reads output JSON   │                    │     - palette           │
│     converts to section │                    │     - semantic colours  │
└─────────────────────────┘                    └─────────────────────────┘
```

### File Locations

| File | Path | Written By |
|------|------|------------|
| Input | `{brand}/context/visual/_colourtoken-input.json` | BrandKit Workflow |
| Output | `{brand}/context/visual/_colourtoken-output.json` | ColourToken App |

### Integration Module

The integration layer is already built in Phase 7C:
- **File:** `BrandKit Workflow/lib/server/colourtoken-integration.ts`
- **Exports:** `launchColourToken`, `handleColourTokenComplete`, `isColourTokenComplete`
- **Utilities:** `generateCSSVariables`, `generateTailwindConfig`

---

## Input/Output Specification

### Input JSON (`_colourtoken-input.json`)

```typescript
interface ColourTokenInput {
  /** Brand name for context */
  brandName: string;
  /** Brand domain for color psychology suggestions */
  domain?: string;
  /** Mode: create new or edit existing */
  mode: 'create' | 'edit';
  /** Existing colors to pre-populate (edit mode) */
  existingColours?: ParsedColor[];
  /** Suggested colors from brand analysis */
  suggestedColours?: SuggestedColour[];
}

interface SuggestedColour {
  hex: string;
  source: 'website' | 'logo' | 'uploaded_css';
  prominence: 'primary' | 'secondary' | 'accent';
}
```

**Example Input:**
```json
{
  "brandName": "Wellness Studio",
  "domain": "therapeutic",
  "mode": "create",
  "suggestedColours": [
    { "hex": "#8FA68A", "source": "logo", "prominence": "primary" },
    { "hex": "#D4A574", "source": "website", "prominence": "accent" }
  ]
}
```

### Output JSON (`_colourtoken-output.json`)

```typescript
interface ColourTokenOutput {
  success: boolean;
  palette?: ColourPalette;
  semantic?: SemanticColours;
  error?: string;
}

interface ColourPalette {
  colours: BrandColour[];
  createdAt: string;
  version?: string;
}

interface BrandColour {
  name: string;              // "Primary", "Ocean Blue"
  role: ColorRole;           // 'primary' | 'secondary' | 'accent' | etc.
  shades: Record<string, string>;  // { "50": "#f8faf8", "500": "#8FA68A", ... }
  defaultShade: string;      // "500"
  usage?: string;            // "Main brand colour for buttons and headers"
}

interface SemanticColours {
  background: string;
  surface: string;
  textPrimary: string;
  textSecondary: string;
  border: string;
  link?: string;
  linkHover?: string;
}
```

**Example Output:**
```json
{
  "success": true,
  "palette": {
    "colours": [
      {
        "name": "Sage",
        "role": "primary",
        "shades": {
          "50": "#f8faf8",
          "100": "#e8f0e6",
          "200": "#d1e1cc",
          "300": "#b3cead",
          "400": "#9fba97",
          "500": "#8fa68a",
          "600": "#739070",
          "700": "#5a7358",
          "800": "#425642",
          "900": "#2d3b2d",
          "950": "#1a231a"
        },
        "defaultShade": "500",
        "usage": "Primary brand colour for buttons, headers, and key elements"
      }
    ],
    "createdAt": "2026-01-15T10:30:00.000Z",
    "version": "1.0.0"
  },
  "semantic": {
    "background": "#FFFFFF",
    "surface": "#f8faf8",
    "textPrimary": "#1a231a",
    "textSecondary": "#5a7358",
    "border": "#d1e1cc"
  }
}
```

---

## Existing Code to Leverage

### ColorTokenGenerator Project

**Location:** `C:\Users\Business\ColorTokenGenerator\`

| File | Purpose | Reuse |
|------|---------|-------|
| `ThemeTokenGen/simple-theme-gen.js` | OKLCH colour scale generation | **HIGH** - Core algorithm |
| `ThemeTokenGen/color-theory-comparison.js` | Harmony algorithms | **HIGH** - Palette suggestions |
| `ThemeTokenGen/preview-colors.js` | Colour preview rendering | **MEDIUM** - UI reference |
| `ThemeSwitcher.js` | Theme switching with a11y | **LOW** - Reference only |

### Key Features from simple-theme-gen.js

```javascript
// OKLCH colour scale generation (50-950)
// Input: base colour + position (which shade is the "500")
// Output: Full scale with perceptually uniform steps

// Colour theory harmonies:
// - Complementary (opposite on wheel)
// - Analogous (adjacent colours)
// - Triadic (3 evenly spaced)
// - Split-complementary (complement + adjacents)
// - Tetradic (4 evenly spaced)
```

---

## Feature Requirements

### Core Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Colour picker | HIGH | Select base colour via picker or hex input |
| Shade generator | HIGH | Generate 50-950 scale using OKLCH |
| Role assignment | HIGH | Assign primary/secondary/accent/neutral |
| Harmony suggestions | HIGH | Suggest complementary colours |
| Semantic mapping | HIGH | Assign background/text/border colours |
| Preview panel | HIGH | Live preview of palette in context |
| Export to JSON | HIGH | Write output file for BrandKit |

### Advanced Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Colour names | MEDIUM | Use Colour Pizza API for names |
| Accessibility check | MEDIUM | WCAG contrast ratio display |
| Domain suggestions | MEDIUM | Suggest colours for domain (therapeutic = greens/blues) |
| Import from image | LOW | Extract palette from uploaded logo |
| History/undo | LOW | Track changes for undo/redo |

---

## UI Design

### Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│  ColourToken                                           [Save] [Cancel]│
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  ┌─────────────────────────┐  ┌───────────────────────────────────┐  │
│  │                         │  │           Preview                  │  │
│  │    Colour Picker        │  │                                    │  │
│  │                         │  │  ┌─────────────────────────────┐   │  │
│  │    [#8FA68A    ]        │  │  │  Header Text                │   │  │
│  │                         │  │  │  Body text sample           │   │  │
│  │    ○ Primary            │  │  │  [Primary Button]           │   │  │
│  │    ○ Secondary          │  │  │  [Secondary]                │   │  │
│  │    ○ Accent             │  │  └─────────────────────────────┘   │  │
│  │    ○ Neutral            │  │                                    │  │
│  └─────────────────────────┘  └───────────────────────────────────┘  │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │                        Shade Scale                              │   │
│  │  50   100  200  300  400  500  600  700  800  900  950         │   │
│  │  [  ] [  ] [  ] [  ] [  ] [●●] [  ] [  ] [  ] [  ] [  ]        │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │  Harmonies                                                      │   │
│  │  [Complementary] [Analogous] [Triadic] [Split] [Tetradic]      │   │
│  │                                                                  │   │
│  │  Suggested: [#D4A574] [#7A8FA6] [#A68F8A]                       │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Technical Stack

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Framework | Electron or Tauri | Desktop app with file access |
| UI | React or Svelte | Component-based, fast |
| Colour manipulation | culori or chroma.js | OKLCH support |
| Colour picker | react-colorful or custom | Lightweight |
| Build | Vite | Fast dev experience |

---

## Build Tasks

### Phase 1: Core (MVP)

- [ ] Initialize project with Electron/Tauri + Vite
- [ ] Create main window with file argument parsing
- [ ] Implement JSON input reading from command line arg or env
- [ ] Port OKLCH shade generation from `simple-theme-gen.js`
- [ ] Create colour picker component
- [ ] Create shade scale display component
- [ ] Create role assignment UI
- [ ] Implement JSON output writing
- [ ] Test integration with BrandKit Workflow

### Phase 2: Enhanced Features

- [ ] Add harmony suggestions from `color-theory-comparison.js`
- [ ] Add live preview panel
- [ ] Add semantic colour mapping UI
- [ ] Add accessibility contrast display
- [ ] Add domain-based colour suggestions

### Phase 3: Polish

- [ ] Add Colour Pizza API integration for names
- [ ] Add undo/redo history
- [ ] Add keyboard shortcuts
- [ ] Add loading/saving states
- [ ] Error handling and validation

---

## APIs Required

| API | Purpose | Key Required |
|-----|---------|--------------|
| Colour Pizza | Colour names, suggestions | Optional (free tier) |

---

## Testing

### Integration Test

```bash
# 1. Create test input file
echo '{"brandName":"Test","mode":"create"}' > test-input.json

# 2. Launch ColourToken with input
colourtoken --input test-input.json --output test-output.json

# 3. User creates palette and saves

# 4. Verify output
cat test-output.json  # Should have palette structure
```

### BrandKit Workflow Integration Test

```typescript
// In BrandKit Workflow
const result = await launchColourToken(brandPath, { mode: 'create' });
// User completes in ColourToken app
const output = await handleColourTokenComplete(brandPath);
const section = convertToColoursSection(output);
// Verify section has expected structure
```

---

## Future Enhancements

| Enhancement | Complexity | Description |
|-------------|------------|-------------|
| **Design Token Export** | Medium | Export palette as CSS variables, Tailwind config, and Style Dictionary JSON |
| **PDF Brand Book Export** | High | Generate downloadable brand guidelines PDF with colour palette, usage rules, and accessibility notes |
| **Color Blindness Simulation** | Medium | Preview palette through deuteranopia, protanopia, tritanopia filters |
| **Dark Mode Auto-Generation** | Medium | Suggest dark mode palette from light mode colours using colour theory |
| **WCAG Report Generation** | Low | Exportable accessibility compliance report with all contrast ratios |
| **Palette from Image** | Medium | Integration with Visual Consistency Checker to import extracted palette |

> **Note:** PDF Brand Book and accessibility features should be built into ColourToken since it's the primary palette management tool.

---

## Design Token Export (Future Enhancement)

### Overview

Export colour palettes in multiple developer-friendly formats for direct integration into web projects, design systems, and style dictionaries.

### Export Formats

| Format | File | Use Case |
|--------|------|----------|
| CSS Variables | `tokens.css` | Direct inclusion in web projects |
| Tailwind Config | `tailwind.colors.js` | Tailwind CSS customization |
| Style Dictionary | `tokens.json` | Cross-platform design tokens |
| SCSS Variables | `_colors.scss` | SASS/SCSS projects |

### Example Outputs

**CSS Variables (`tokens.css`):**
```css
:root {
  /* Primary - Sage */
  --color-primary-50: #f8faf8;
  --color-primary-100: #e8f0e6;
  --color-primary-500: #8fa68a;
  --color-primary-900: #2d3b2d;

  /* Semantic */
  --color-background: #ffffff;
  --color-surface: #f8faf8;
  --color-text-primary: #1a231a;
  --color-text-secondary: #5a7358;
}
```

**Tailwind Config (`tailwind.colors.js`):**
```javascript
module.exports = {
  colors: {
    primary: {
      50: '#f8faf8',
      100: '#e8f0e6',
      500: '#8fa68a',
      900: '#2d3b2d',
      DEFAULT: '#8fa68a'
    },
    background: '#ffffff',
    surface: '#f8faf8'
  }
};
```

**Style Dictionary (`tokens.json`):**
```json
{
  "color": {
    "primary": {
      "50": { "value": "#f8faf8" },
      "500": { "value": "#8fa68a" },
      "900": { "value": "#2d3b2d" }
    },
    "semantic": {
      "background": { "value": "{color.primary.50}" },
      "text": { "value": "{color.primary.900}" }
    }
  }
}
```

### Implementation Notes

- Integrates with existing palette output
- Export button in ColourToken UI triggers format selection
- Files saved to brand `context/visual/` folder
- CSS parser in BrandKit Workflow can import these tokens (Phase 7A.4)

---

## Notes

- The integration layer in BrandKit Workflow is already complete (Phase 7C.1)
- ColourToken should read input file path from command line argument
- Output file path also from command line or same directory as input
- App can be launched by BrandKit Workflow via CLI spawn
- Consider using OKLCH for all colour manipulation (perceptually uniform)
- The `simple-theme-gen.js` already has the core shade generation algorithm

---

*Last updated: 2026-01-15*
