# ColourToken Module

> **Purpose:** Generate, manage, and tokenize color palettes for brands
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

ColourToken handles everything color-related for brands - generating palettes, creating design tokens, ensuring accessibility, managing color variations. It creates systematic color systems that work across all brand touchpoints.

```
Brand values/mood → ColourToken.generate() → Full color system with tokens
```

**Capabilities:**
- Palette generation from brand values
- Color harmony creation
- Accessibility checking (WCAG)
- Design token generation
- Color variation systems
- Cross-platform color formats

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `generatePalette(brief)` | Generate palette from brief | brief | palette |
| `createTokens(palette)` | Create design tokens | palette | tokens |
| `checkAccessibility(colors)` | Check WCAG accessibility | colors | accessibilityReport |
| `generateVariations(baseColor, type)` | Generate color variations | baseColor, type | variations |
| `harmonize(colors)` | Create harmonious palette | colors | harmonizedPalette |
| `convertFormats(color, formats)` | Convert to multiple formats | color, formats | converted |
| `extractFromImage(image)` | Extract palette from image | image | extractedPalette |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `ColourToken.createBrandPalette` | Full brand palette creation | generatePalette → checkAccessibility → createTokens |
| `ColourToken.systemFromPrimary` | Build system from primary color | generateVariations → harmonize → createTokens |

---

## Detailed Function Specs

### generatePalette(brief)

Generates a color palette from brand brief.

```javascript
// Input
{
  brief: {
    brandValues: ["innovation", "trust", "growth"],
    industry: "technology",
    mood: ["professional", "modern", "approachable"],
    preferences: {
      warmth: "cool",
      saturation: "medium",
      avoid: ["red", "orange"]
    },
    existingColors: {
      mustInclude: ["#1a73e8"]  // Existing brand blue
    }
  }
}

// Process
1. Analyze values and mood
2. Map to color psychology
3. Generate primary, secondary, accent
4. Create neutral scale
5. Validate accessibility

// Output
{
  palette: {
    primary: {
      base: "#1a73e8",
      name: "Trust Blue",
      meaning: "Reliability, technology, trust"
    },
    secondary: {
      base: "#34a853",
      name: "Growth Green",
      meaning: "Growth, success, innovation"
    },
    accent: {
      base: "#7c4dff",
      name: "Innovation Purple",
      meaning: "Creativity, forward-thinking"
    },
    neutrals: {
      900: "#1a1a2e",
      800: "#2d2d44",
      700: "#4a4a5c",
      600: "#6b6b7b",
      500: "#8c8c9a",
      400: "#adadb8",
      300: "#cecdd6",
      200: "#e8e8ec",
      100: "#f5f5f7",
      50: "#fafafa"
    },
    semantic: {
      success: "#34a853",
      warning: "#f9ab00",
      error: "#ea4335",
      info: "#1a73e8"
    }
  },
  rationale: {
    primary: "Blue conveys trust and technology, aligned with brand values",
    secondary: "Green represents growth and success",
    accent: "Purple adds innovation and creativity"
  }
}
```

### createTokens(palette)

Creates design tokens from palette.

```javascript
// Input
{
  palette: {
    primary: { base: "#1a73e8" },
    secondary: { base: "#34a853" },
    neutrals: { ... }
  },
  format: "all"  // css, scss, json, tailwind, figma
}

// Output
{
  tokens: {
    css: `
:root {
  --color-primary-500: #1a73e8;
  --color-primary-400: #4285f4;
  --color-primary-600: #1557b0;
  --color-secondary-500: #34a853;
  --color-neutral-900: #1a1a2e;
  --color-neutral-100: #f5f5f7;
  /* ... */
}`,
    scss: `
$color-primary-500: #1a73e8;
$color-primary-400: #4285f4;
/* ... */`,
    json: {
      "color": {
        "primary": {
          "500": { "value": "#1a73e8" },
          "400": { "value": "#4285f4" }
        }
      }
    },
    tailwind: {
      colors: {
        primary: {
          500: "#1a73e8",
          400: "#4285f4"
        }
      }
    }
  },
  files: {
    css: "/tokens/colors.css",
    scss: "/tokens/_colors.scss",
    json: "/tokens/colors.json"
  }
}
```

### checkAccessibility(colors)

Checks color combinations for WCAG compliance.

```javascript
// Input
{
  colors: {
    background: "#ffffff",
    text: "#1a1a2e",
    primary: "#1a73e8",
    primaryText: "#ffffff"
  }
}

// Output
{
  combinations: [
    {
      foreground: "#1a1a2e",
      background: "#ffffff",
      contrastRatio: 15.3,
      wcag: {
        AA: { normal: true, large: true },
        AAA: { normal: true, large: true }
      },
      status: "pass"
    },
    {
      foreground: "#ffffff",
      background: "#1a73e8",
      contrastRatio: 4.6,
      wcag: {
        AA: { normal: true, large: true },
        AAA: { normal: false, large: true }
      },
      status: "pass-AA"
    }
  ],
  summary: {
    totalCombinations: 4,
    passAAA: 2,
    passAA: 4,
    fail: 0
  },
  recommendations: []
}
```

### generateVariations(baseColor, type)

Generates color variations (tints, shades, tones).

```javascript
// Input
{
  baseColor: "#1a73e8",
  type: "full"  // tints, shades, tones, full
}

// Output
{
  baseColor: "#1a73e8",
  variations: {
    50: "#e8f1fd",
    100: "#c5dbfa",
    200: "#9fc4f7",
    300: "#79adf4",
    400: "#5396f1",
    500: "#1a73e8",  // base
    600: "#1557b0",
    700: "#104189",
    800: "#0b2c62",
    900: "#06173b"
  },
  tints: ["#e8f1fd", "#c5dbfa", "#9fc4f7"],
  shades: ["#1557b0", "#104189", "#0b2c62"],
  complementary: "#e87a1a",
  analogous: ["#1ae8d4", "#1a3de8"],
  triadic: ["#e81a73", "#73e81a"]
}
```

### extractFromImage(image)

Extracts color palette from an image.

```javascript
// Input
{
  image: "/assets/inspiration.jpg",
  options: {
    count: 6,
    quality: "high"
  }
}

// Output
{
  extracted: [
    { color: "#2d5a7b", percentage: 32, name: "Ocean Blue" },
    { color: "#f4e4bc", percentage: 28, name: "Sand" },
    { color: "#8b4513", percentage: 18, name: "Saddle Brown" },
    { color: "#87ceeb", percentage: 12, name: "Sky Blue" },
    { color: "#228b22", percentage: 7, name: "Forest Green" },
    { color: "#ffffff", percentage: 3, name: "White" }
  ],
  suggested: {
    primary: "#2d5a7b",
    secondary: "#228b22",
    accent: "#f4e4bc",
    neutral: "#8b4513"
  }
}
```

---

## Color Token Structure

```json
{
  "id": "acme-colors",
  "brandId": "acme",
  "version": "1.0",
  "palette": {
    "primary": {
      "50": "#e8f1fd",
      "100": "#c5dbfa",
      "200": "#9fc4f7",
      "300": "#79adf4",
      "400": "#5396f1",
      "500": "#1a73e8",
      "600": "#1557b0",
      "700": "#104189",
      "800": "#0b2c62",
      "900": "#06173b"
    },
    "secondary": { ... },
    "accent": { ... },
    "neutral": { ... },
    "semantic": {
      "success": "#34a853",
      "warning": "#f9ab00",
      "error": "#ea4335",
      "info": "#1a73e8"
    }
  },
  "usage": {
    "backgrounds": ["neutral.50", "neutral.100", "primary.50"],
    "text": ["neutral.900", "neutral.800"],
    "buttons": {
      "primary": { "bg": "primary.500", "text": "white" },
      "secondary": { "bg": "secondary.500", "text": "white" }
    }
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "ColourToken",
  "version": "1.0.0",
  "description": "Generate and manage color palettes",
  "type": "pipeline",

  "functions": [
    {
      "name": "generatePalette",
      "description": "Generate palette from brief",
      "input": ["brief"],
      "output": "palette"
    },
    {
      "name": "createTokens",
      "description": "Create design tokens",
      "input": ["palette"],
      "output": "tokens"
    },
    {
      "name": "checkAccessibility",
      "description": "Check WCAG accessibility",
      "input": ["colors"],
      "output": "accessibilityReport"
    },
    {
      "name": "generateVariations",
      "description": "Generate color variations",
      "input": ["baseColor", "type"],
      "output": "variations"
    },
    {
      "name": "harmonize",
      "description": "Create harmonious palette",
      "input": ["colors"],
      "output": "harmonizedPalette"
    },
    {
      "name": "convertFormats",
      "description": "Convert to multiple formats",
      "input": ["color", "formats"],
      "output": "converted"
    },
    {
      "name": "extractFromImage",
      "description": "Extract palette from image",
      "input": ["image"],
      "output": "extractedPalette"
    }
  ],

  "steps": [
    {
      "name": "createBrandPalette",
      "description": "Full brand palette creation",
      "sequence": ["generatePalette", "checkAccessibility", "createTokens"]
    },
    {
      "name": "systemFromPrimary",
      "description": "Build system from primary color",
      "sequence": ["generateVariations", "harmonize", "createTokens"]
    }
  ],

  "config": {
    "defaultVariationCount": 10,
    "wcagLevel": "AA",
    "tokenFormats": ["css", "scss", "json", "tailwind"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Storing palettes and tokens |
| Archivist | Saving to repos |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Color palette generation |
| VisualBrand | Brand visual system |
| SVGDrawer | Applying colors |
| ContentAlignmentManager | Visual compliance checking |

---

*Last updated: 2026-01-17*
