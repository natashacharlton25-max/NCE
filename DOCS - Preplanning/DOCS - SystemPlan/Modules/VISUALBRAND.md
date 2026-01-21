# VisualBrand Module

> **Purpose:** Manage complete visual brand identity systems
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

VisualBrand orchestrates the complete visual identity system - bringing together colors, typography, logos, imagery, and visual guidelines into a cohesive brand visual system. It's the master controller for visual brand consistency.

```
Brand brief → VisualBrand.create() → Complete visual system → Guidelines + Assets
```

**Components managed:**
- Color system (via ColourToken)
- Typography (via Typography)
- Logo (via LogoMaker)
- Imagery style (via AIImageCreation)
- Visual guidelines
- Design tokens

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `createVisualSystem(brief)` | Create complete visual system | brief | visualSystem |
| `assembleGuidelines(brandId)` | Assemble visual guidelines | brandId | guidelines |
| `validateConsistency(assets)` | Check visual consistency | assets | consistencyReport |
| `exportDesignTokens(brandId)` | Export all design tokens | brandId | tokens |
| `createMoodBoard(brief)` | Generate mood board | brief | moodBoard |
| `updateElement(brandId, element, value)` | Update visual element | brandId, element, value | updated |
| `compareVersions(brandId, v1, v2)` | Compare visual versions | brandId, v1, v2 | comparison |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `VisualBrand.createFromScratch` | Create new visual brand | createMoodBoard → createVisualSystem → assembleGuidelines |
| `VisualBrand.refreshBrand` | Refresh existing brand | validateConsistency → updateElement → assembleGuidelines |

---

## Detailed Function Specs

### createVisualSystem(brief)

Creates complete visual brand system.

```javascript
// Input
{
  brief: {
    brandName: "Acme Corp",
    brandValues: ["innovation", "trust", "growth"],
    industry: "technology",
    audience: {
      primary: "enterprise decision makers",
      secondary: "developers"
    },
    mood: ["professional", "modern", "approachable"],
    competitors: ["CompetitorA", "CompetitorB"],
    differentiation: "Premium but accessible technology"
  }
}

// Process
1. Generate mood board
2. Create color palette (ColourToken)
3. Select typography (Typography)
4. Generate logo concepts (LogoMaker)
5. Define imagery style
6. Create design tokens
7. Assemble guidelines

// Output
{
  visualSystem: {
    id: "vs_acme_001",
    brandId: "acme",
    version: "1.0",

    colors: {
      palette: {
        primary: { base: "#1a73e8", name: "Trust Blue" },
        secondary: { base: "#34a853", name: "Growth Green" },
        accent: { base: "#7c4dff", name: "Innovation Purple" },
        neutrals: { ... }
      },
      tokens: "/tokens/colors.json"
    },

    typography: {
      fonts: {
        heading: "Montserrat",
        body: "Open Sans",
        mono: "Roboto Mono"
      },
      scale: { ... },
      tokens: "/tokens/typography.json"
    },

    logo: {
      primary: "/logos/acme-primary.svg",
      variations: "/logos/variations/",
      guidelines: "/guidelines/logo-usage.pdf"
    },

    imagery: {
      style: "modern professional photography",
      characteristics: [
        "Clean, bright backgrounds",
        "Natural lighting",
        "Technology focus without clichés",
        "Diverse, professional people"
      ],
      avoid: ["stock photo clichés", "outdated tech"]
    },

    patterns: {
      primary: "/patterns/wave-pattern.svg",
      usage: "Backgrounds, dividers"
    }
  },
  created: "2026-01-17"
}
```

### assembleGuidelines(brandId)

Assembles comprehensive visual guidelines document.

```javascript
// Input
{
  brandId: "acme"
}

// Process
1. Gather all visual elements
2. Create guidelines for each
3. Add usage examples
4. Generate PDF

// Output
{
  guidelines: {
    sections: [
      {
        name: "Brand Overview",
        content: "Introduction to Acme visual identity..."
      },
      {
        name: "Logo",
        content: "Logo usage, clear space, minimum sizes...",
        assets: ["/logos/guidelines-examples.png"]
      },
      {
        name: "Color Palette",
        content: "Primary, secondary, and neutral colors...",
        swatches: [...],
        usage: "When to use each color"
      },
      {
        name: "Typography",
        content: "Font families, hierarchy, usage...",
        specimens: ["/typography/specimen.png"]
      },
      {
        name: "Imagery",
        content: "Photography style, illustration guidelines...",
        examples: ["/imagery/style-examples.png"]
      },
      {
        name: "Applications",
        content: "Business cards, letterheads, social media...",
        templates: [...]
      }
    ],
    exports: {
      pdf: "/guidelines/acme-brand-guidelines.pdf",
      web: "/guidelines/index.html"
    }
  }
}
```

### validateConsistency(assets)

Validates visual consistency across assets.

```javascript
// Input
{
  assets: [
    { type: "social-post", path: "/content/social/post-1.png" },
    { type: "document", path: "/content/docs/brochure.pdf" },
    { type: "website", url: "https://acme.com" }
  ],
  brandId: "acme"
}

// Process
1. Load brand visual system
2. Analyze each asset
3. Check color usage
4. Check typography
5. Check logo usage
6. Score consistency

// Output
{
  consistency: {
    overall: 0.87,
    byAsset: [
      {
        asset: "social-post",
        score: 0.95,
        issues: []
      },
      {
        asset: "document",
        score: 0.78,
        issues: [
          { type: "typography", issue: "Wrong heading font", severity: "medium" },
          { type: "color", issue: "Off-brand accent color #ff5722", severity: "low" }
        ]
      }
    ]
  },
  recommendations: [
    "Update document template with correct heading font",
    "Replace off-brand accent with approved alternative"
  ]
}
```

### createMoodBoard(brief)

Generates visual mood board for brand direction.

```javascript
// Input
{
  brief: {
    values: ["innovation", "trust"],
    mood: ["professional", "modern"],
    industry: "technology"
  }
}

// Output
{
  moodBoard: {
    id: "mood_acme_001",
    images: [
      { url: "/mood/tech-office.jpg", category: "environment" },
      { url: "/mood/clean-ui.jpg", category: "digital" },
      { url: "/mood/geometric-pattern.jpg", category: "pattern" }
    ],
    colorExtracted: ["#1a73e8", "#ffffff", "#333333"],
    typography: ["geometric sans-serif", "clean, modern"],
    keywords: ["minimal", "professional", "trustworthy", "innovative"],
    direction: "Clean, modern aesthetic with blue as trust anchor"
  }
}
```

---

## Visual System Structure

```json
{
  "id": "vs_acme_001",
  "brandId": "acme",
  "version": "1.0",
  "created": "2026-01-17",
  "updated": "2026-01-17",

  "elements": {
    "colors": "ref:colourtoken/acme-colors",
    "typography": "ref:typography/acme-type",
    "logo": "ref:logo/acme-logo-001",
    "imagery": { ... },
    "patterns": { ... },
    "icons": { ... }
  },

  "tokens": {
    "colors": "/tokens/colors.json",
    "typography": "/tokens/typography.json",
    "spacing": "/tokens/spacing.json"
  },

  "guidelines": "/guidelines/acme-brand-guidelines.pdf",

  "templates": {
    "businessCard": "/templates/business-card.ai",
    "letterhead": "/templates/letterhead.ai",
    "socialMedia": "/templates/social-media/",
    "presentation": "/templates/presentation.pptx"
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "VisualBrand",
  "version": "1.0.0",
  "description": "Manage complete visual brand systems",
  "type": "pipeline",

  "functions": [
    {
      "name": "createVisualSystem",
      "description": "Create complete visual system",
      "input": ["brief"],
      "output": "visualSystem"
    },
    {
      "name": "assembleGuidelines",
      "description": "Assemble visual guidelines",
      "input": ["brandId"],
      "output": "guidelines"
    },
    {
      "name": "validateConsistency",
      "description": "Check visual consistency",
      "input": ["assets"],
      "output": "consistencyReport"
    },
    {
      "name": "exportDesignTokens",
      "description": "Export all design tokens",
      "input": ["brandId"],
      "output": "tokens"
    },
    {
      "name": "createMoodBoard",
      "description": "Generate mood board",
      "input": ["brief"],
      "output": "moodBoard"
    },
    {
      "name": "updateElement",
      "description": "Update visual element",
      "input": ["brandId", "element", "value"],
      "output": "updated"
    },
    {
      "name": "compareVersions",
      "description": "Compare visual versions",
      "input": ["brandId", "v1", "v2"],
      "output": "comparison"
    }
  ],

  "steps": [
    {
      "name": "createFromScratch",
      "description": "Create new visual brand",
      "sequence": ["createMoodBoard", "createVisualSystem", "assembleGuidelines"]
    },
    {
      "name": "refreshBrand",
      "description": "Refresh existing brand",
      "sequence": ["validateConsistency", "updateElement", "assembleGuidelines"]
    }
  ],

  "config": {
    "guidelinesFormat": "pdf",
    "tokenFormats": ["css", "scss", "json", "tailwind"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| ColourToken | Color palette generation |
| Typography | Font selection/system |
| LogoMaker | Logo creation |
| ImageCollections | Mood board images |
| PDFMaker | Guidelines PDF |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Complete brand creation |
| ContentAlignmentManager | Visual consistency checking |

---

*Last updated: 2026-01-17*
