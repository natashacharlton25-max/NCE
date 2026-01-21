# LogoMaker Module

> **Purpose:** Generate and manage logo concepts and variations
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

LogoMaker handles logo creation - from initial concepts to final variations. It can generate simple logos (monograms, wordmarks, icons) using SVG and AI, manage logo variations (color, size, format), and ensure logo consistency.

```
Brand brief → LogoMaker.generate() → Logo concepts → Variations → Final package
```

**Capabilities:**
- Logo concept generation
- Monogram/wordmark creation
- Icon generation
- Variation management (color, size)
- Format export (SVG, PNG, PDF)
- Usage guidelines generation

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `generateConcepts(brief)` | Generate logo concepts | brief | concepts |
| `createMonogram(letters, style)` | Create monogram logo | letters, style | monogram |
| `createWordmark(text, style)` | Create wordmark logo | text, style | wordmark |
| `createIcon(brief, style)` | Create icon/symbol | brief, style | icon |
| `generateVariations(logo)` | Generate logo variations | logo | variations |
| `exportFormats(logo, formats)` | Export to multiple formats | logo, formats | exported |
| `createGuidelines(logo)` | Create usage guidelines | logo | guidelines |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `LogoMaker.createBrandLogo` | Full logo creation flow | generateConcepts → generateVariations → exportFormats |
| `LogoMaker.createLogoPackage` | Complete logo package | generateVariations → exportFormats → createGuidelines |

---

## Detailed Function Specs

### generateConcepts(brief)

Generates multiple logo concepts from brief.

```javascript
// Input
{
  brief: {
    brandName: "Acme Corp",
    industry: "technology",
    values: ["innovation", "trust", "growth"],
    style: ["modern", "minimal", "professional"],
    colors: {
      primary: "#1a73e8",
      secondary: "#34a853"
    },
    preferences: {
      types: ["monogram", "wordmark", "combination"],
      avoid: ["generic tech symbols", "globes"]
    }
  }
}

// Process
1. Analyze brand values and style
2. Generate multiple concept types
3. Apply brand colors
4. Return concepts for review

// Output
{
  concepts: [
    {
      id: "concept_001",
      type: "monogram",
      name: "AC Hexagon",
      description: "Letters AC in a hexagonal frame representing structure and innovation",
      svg: "<svg>...</svg>",
      preview: "/previews/concept_001.png",
      colors: { primary: "#1a73e8", secondary: "#ffffff" }
    },
    {
      id: "concept_002",
      type: "wordmark",
      name: "Acme Modern",
      description: "Clean wordmark with custom letterforms",
      svg: "<svg>...</svg>",
      preview: "/previews/concept_002.png",
      font: "Custom geometric sans"
    },
    {
      id: "concept_003",
      type: "combination",
      name: "Acme Symbol",
      description: "Abstract A symbol with wordmark",
      svg: "<svg>...</svg>",
      preview: "/previews/concept_003.png"
    }
  ],
  briefAlignment: {
    valuesRepresented: ["innovation", "trust"],
    styleMatch: 0.85
  }
}
```

### createMonogram(letters, style)

Creates a monogram logo.

```javascript
// Input
{
  letters: "AC",
  style: {
    shape: "circle",  // circle, square, hexagon, none
    arrangement: "stacked",  // stacked, side-by-side, interlock
    font: "geometric-sans",
    weight: "bold",
    colors: {
      letters: "#ffffff",
      background: "#1a73e8"
    }
  }
}

// Process
1. Apply typography
2. Arrange letters
3. Add shape container
4. Generate SVG

// Output
{
  monogram: {
    svg: '<svg viewBox="0 0 100 100">...</svg>',
    preview: "/preview/monogram_ac.png",
    dimensions: { width: 100, height: 100 },
    style: { ... }
  }
}
```

### generateVariations(logo)

Generates all necessary logo variations.

```javascript
// Input
{
  logo: {
    id: "logo_acme_001",
    svg: "<svg>...</svg>",
    colors: {
      primary: "#1a73e8",
      secondary: "#34a853"
    }
  }
}

// Output
{
  variations: {
    color: {
      full: { svg: "...", name: "Full Color" },
      reversed: { svg: "...", name: "Reversed (white on dark)" },
      black: { svg: "...", name: "Black" },
      white: { svg: "...", name: "White" },
      grayscale: { svg: "...", name: "Grayscale" }
    },
    layout: {
      horizontal: { svg: "...", name: "Horizontal" },
      vertical: { svg: "...", name: "Stacked/Vertical" },
      icon: { svg: "...", name: "Icon Only" },
      wordmark: { svg: "...", name: "Wordmark Only" }
    },
    size: {
      favicon: { size: "32x32", svg: "..." },
      small: { size: "64x64", svg: "..." },
      medium: { size: "256x256", svg: "..." },
      large: { size: "1024x1024", svg: "..." }
    }
  },
  totalVariations: 15
}
```

### exportFormats(logo, formats)

Exports logo to multiple formats.

```javascript
// Input
{
  logo: {
    id: "logo_acme_001",
    svg: "<svg>...</svg>",
    variations: { ... }
  },
  formats: ["svg", "png", "pdf", "eps"],
  sizes: [64, 128, 256, 512, 1024],
  outputPath: "/brands/acme/logos/"
}

// Output
{
  exported: {
    svg: [
      "/brands/acme/logos/acme-logo-full-color.svg",
      "/brands/acme/logos/acme-logo-reversed.svg",
      "/brands/acme/logos/acme-logo-black.svg"
    ],
    png: [
      "/brands/acme/logos/png/acme-logo-64.png",
      "/brands/acme/logos/png/acme-logo-128.png",
      "/brands/acme/logos/png/acme-logo-256.png",
      "/brands/acme/logos/png/acme-logo-512.png",
      "/brands/acme/logos/png/acme-logo-1024.png"
    ],
    pdf: ["/brands/acme/logos/acme-logo-vector.pdf"],
    eps: ["/brands/acme/logos/acme-logo-vector.eps"]
  },
  totalFiles: 15,
  packageZip: "/brands/acme/logos/acme-logo-package.zip"
}
```

### createGuidelines(logo)

Creates logo usage guidelines.

```javascript
// Input
{
  logo: {
    id: "logo_acme_001",
    brandName: "Acme Corp",
    variations: { ... },
    colors: { ... }
  }
}

// Output
{
  guidelines: {
    clearSpace: {
      description: "Maintain minimum clear space equal to the height of the 'A' in Acme",
      diagram: "<svg>...</svg>",
      minimum: "10% of logo width"
    },
    minimumSize: {
      print: "25mm width",
      digital: "80px width",
      favicon: "16x16px (icon only)"
    },
    colorUsage: {
      primary: "Use full color on white or light backgrounds",
      reversed: "Use reversed (white) on dark or brand color backgrounds",
      black: "Use for single-color applications"
    },
    donts: [
      "Don't stretch or distort",
      "Don't change the colors",
      "Don't add effects (shadows, gradients)",
      "Don't place on busy backgrounds",
      "Don't rotate the logo"
    ],
    examples: {
      correct: ["/guidelines/correct-1.png", "/guidelines/correct-2.png"],
      incorrect: ["/guidelines/incorrect-1.png", "/guidelines/incorrect-2.png"]
    }
  },
  output: "/brands/acme/guidelines/logo-guidelines.pdf"
}
```

---

## Logo Types

| Type | Description | Best For |
|------|-------------|----------|
| Monogram | Initials/letters | Corporate, premium |
| Wordmark | Styled brand name | Name recognition |
| Icon | Symbol/mark | App icons, favicons |
| Combination | Icon + wordmark | Versatile use |
| Emblem | Text inside symbol | Traditional, badges |

---

## Config

### module.config.json

```json
{
  "module": "LogoMaker",
  "version": "1.0.0",
  "description": "Generate and manage logos",
  "type": "pipeline",

  "functions": [
    {
      "name": "generateConcepts",
      "description": "Generate logo concepts",
      "input": ["brief"],
      "output": "concepts"
    },
    {
      "name": "createMonogram",
      "description": "Create monogram logo",
      "input": ["letters", "style"],
      "output": "monogram"
    },
    {
      "name": "createWordmark",
      "description": "Create wordmark logo",
      "input": ["text", "style"],
      "output": "wordmark"
    },
    {
      "name": "createIcon",
      "description": "Create icon/symbol",
      "input": ["brief", "style"],
      "output": "icon"
    },
    {
      "name": "generateVariations",
      "description": "Generate logo variations",
      "input": ["logo"],
      "output": "variations"
    },
    {
      "name": "exportFormats",
      "description": "Export to multiple formats",
      "input": ["logo", "formats"],
      "output": "exported"
    },
    {
      "name": "createGuidelines",
      "description": "Create usage guidelines",
      "input": ["logo"],
      "output": "guidelines"
    }
  ],

  "steps": [
    {
      "name": "createBrandLogo",
      "description": "Full logo creation flow",
      "sequence": ["generateConcepts", "generateVariations", "exportFormats"]
    },
    {
      "name": "createLogoPackage",
      "description": "Complete logo package",
      "sequence": ["generateVariations", "exportFormats", "createGuidelines"]
    }
  ],

  "config": {
    "defaultFormats": ["svg", "png"],
    "defaultSizes": [64, 128, 256, 512, 1024],
    "conceptCount": 3
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| SVGDrawer | SVG generation |
| AIImageCreation | AI-assisted concepts |
| PDFMaker | Guidelines PDF |
| ColourToken | Color variations |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Logo creation for brands |
| VisualBrand | Brand visual system |

---

*Last updated: 2026-01-17*
