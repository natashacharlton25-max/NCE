# Typography Module

> **Purpose:** Select, manage, and apply typography for brands
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

Typography handles font selection, pairing, and typographic systems for brands. It selects appropriate fonts based on brand values, creates type scales, manages font assets, and ensures typographic consistency.

```
Brand brief → Typography.select() → Font pairings → Type system → Tokens
```

**Capabilities:**
- Font selection based on brand
- Font pairing recommendations
- Type scale generation
- Typography tokens
- Font licensing management
- Web font optimization

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `selectFonts(brief)` | Select fonts for brand | brief | fontSelection |
| `pairFonts(primary, options)` | Find complementary fonts | primary, options | pairings |
| `createTypeScale(baseSize, ratio)` | Generate type scale | baseSize, ratio | typeScale |
| `createTokens(typography)` | Create typography tokens | typography | tokens |
| `checkLicense(fontId)` | Check font license | fontId | licenseInfo |
| `optimizeWebFonts(fonts)` | Optimize for web | fonts | optimized |
| `generateSpecimen(typography)` | Generate type specimen | typography | specimen |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Typography.createBrandTypography` | Full typography selection | selectFonts → pairFonts → createTypeScale → createTokens |
| `Typography.generateTypeSystem` | Generate complete type system | createTypeScale → createTokens → generateSpecimen |

---

## Detailed Function Specs

### selectFonts(brief)

Selects fonts based on brand brief.

```javascript
// Input
{
  brief: {
    brandValues: ["innovation", "trust", "professionalism"],
    industry: "technology",
    mood: ["modern", "clean", "confident"],
    usage: ["web", "print", "app"],
    preferences: {
      style: "sans-serif",
      weight: "versatile",
      avoid: ["script", "decorative"]
    }
  }
}

// Process
1. Match values to font characteristics
2. Filter by usage requirements
3. Check availability/licensing
4. Score and rank options

// Output
{
  recommendations: [
    {
      role: "heading",
      font: {
        name: "Montserrat",
        category: "sans-serif",
        weights: [300, 400, 500, 600, 700],
        source: "google-fonts",
        license: "OFL"
      },
      score: 0.92,
      reason: "Modern geometric sans, conveys innovation and professionalism"
    },
    {
      role: "body",
      font: {
        name: "Open Sans",
        category: "sans-serif",
        weights: [300, 400, 600, 700],
        source: "google-fonts",
        license: "OFL"
      },
      score: 0.89,
      reason: "Highly legible, friendly yet professional"
    },
    {
      role: "accent",
      font: {
        name: "Roboto Mono",
        category: "monospace",
        weights: [400, 500, 700],
        source: "google-fonts",
        license: "OFL"
      },
      score: 0.85,
      reason: "Technical accent for code/data elements"
    }
  ],
  pairing: {
    heading: "Montserrat",
    body: "Open Sans",
    accent: "Roboto Mono"
  }
}
```

### pairFonts(primary, options)

Finds complementary fonts for a primary choice.

```javascript
// Input
{
  primary: {
    name: "Montserrat",
    role: "heading"
  },
  options: {
    roles: ["body", "accent"],
    style: "complement",  // complement, contrast, match
    sources: ["google-fonts"]
  }
}

// Output
{
  primary: "Montserrat",
  pairings: [
    {
      role: "body",
      recommendations: [
        { font: "Open Sans", score: 0.94, reason: "Excellent readability, similar proportions" },
        { font: "Source Sans Pro", score: 0.91, reason: "Clean, professional companion" },
        { font: "Lato", score: 0.88, reason: "Warm, approachable body text" }
      ]
    },
    {
      role: "accent",
      recommendations: [
        { font: "Roboto Mono", score: 0.89, reason: "Technical contrast" },
        { font: "Fira Code", score: 0.85, reason: "Modern code font" }
      ]
    }
  ]
}
```

### createTypeScale(baseSize, ratio)

Generates a harmonious type scale.

```javascript
// Input
{
  baseSize: 16,  // px
  ratio: 1.25,   // Major third
  levels: 10
}

// Output
{
  scale: {
    "3xs": { size: 10, lineHeight: 1.5, name: "Tiny" },
    "2xs": { size: 11, lineHeight: 1.5, name: "Caption" },
    "xs": { size: 13, lineHeight: 1.5, name: "Small" },
    "sm": { size: 14, lineHeight: 1.5, name: "Body Small" },
    "base": { size: 16, lineHeight: 1.6, name: "Body" },
    "lg": { size: 20, lineHeight: 1.4, name: "Lead" },
    "xl": { size: 25, lineHeight: 1.3, name: "H4" },
    "2xl": { size: 31, lineHeight: 1.2, name: "H3" },
    "3xl": { size: 39, lineHeight: 1.2, name: "H2" },
    "4xl": { size: 49, lineHeight: 1.1, name: "H1" },
    "5xl": { size: 61, lineHeight: 1.1, name: "Display" }
  },
  ratio: 1.25,
  ratioName: "Major Third",
  baseSize: 16
}
```

### createTokens(typography)

Creates typography design tokens.

```javascript
// Input
{
  typography: {
    fonts: {
      heading: "Montserrat",
      body: "Open Sans",
      mono: "Roboto Mono"
    },
    scale: { ... }
  }
}

// Output
{
  tokens: {
    css: `
:root {
  /* Font Families */
  --font-heading: 'Montserrat', sans-serif;
  --font-body: 'Open Sans', sans-serif;
  --font-mono: 'Roboto Mono', monospace;

  /* Font Sizes */
  --text-xs: 0.8125rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.25rem;
  --text-xl: 1.5625rem;
  --text-2xl: 1.9375rem;
  --text-3xl: 2.4375rem;
  --text-4xl: 3.0625rem;

  /* Line Heights */
  --leading-tight: 1.2;
  --leading-snug: 1.3;
  --leading-normal: 1.5;
  --leading-relaxed: 1.6;
}`,
    scss: "$font-heading: 'Montserrat', sans-serif;\n...",
    json: { ... },
    tailwind: {
      fontFamily: {
        heading: ['Montserrat', 'sans-serif'],
        body: ['Open Sans', 'sans-serif']
      },
      fontSize: { ... }
    }
  }
}
```

---

## Font Sources

| Source | Type | License |
|--------|------|---------|
| Google Fonts | Free | Various (mostly OFL) |
| Adobe Fonts | Subscription | Adobe license |
| Custom | Purchased | Varies |
| System | Built-in | None needed |

---

## Type Scale Ratios

| Name | Ratio | Use Case |
|------|-------|----------|
| Minor Second | 1.067 | Subtle hierarchy |
| Major Second | 1.125 | Body-focused |
| Minor Third | 1.2 | General purpose |
| Major Third | 1.25 | Balanced |
| Perfect Fourth | 1.333 | Strong hierarchy |
| Augmented Fourth | 1.414 | Bold statements |
| Perfect Fifth | 1.5 | Dramatic |

---

## Config

### module.config.json

```json
{
  "module": "Typography",
  "version": "1.0.0",
  "description": "Select and manage brand typography",
  "type": "pipeline",

  "functions": [
    {
      "name": "selectFonts",
      "description": "Select fonts for brand",
      "input": ["brief"],
      "output": "fontSelection"
    },
    {
      "name": "pairFonts",
      "description": "Find complementary fonts",
      "input": ["primary", "options"],
      "output": "pairings"
    },
    {
      "name": "createTypeScale",
      "description": "Generate type scale",
      "input": ["baseSize", "ratio"],
      "output": "typeScale"
    },
    {
      "name": "createTokens",
      "description": "Create typography tokens",
      "input": ["typography"],
      "output": "tokens"
    },
    {
      "name": "checkLicense",
      "description": "Check font license",
      "input": ["fontId"],
      "output": "licenseInfo"
    },
    {
      "name": "optimizeWebFonts",
      "description": "Optimize for web",
      "input": ["fonts"],
      "output": "optimized"
    },
    {
      "name": "generateSpecimen",
      "description": "Generate type specimen",
      "input": ["typography"],
      "output": "specimen"
    }
  ],

  "steps": [
    {
      "name": "createBrandTypography",
      "description": "Full typography selection",
      "sequence": ["selectFonts", "pairFonts", "createTypeScale", "createTokens"]
    },
    {
      "name": "generateTypeSystem",
      "description": "Generate complete type system",
      "sequence": ["createTypeScale", "createTokens", "generateSpecimen"]
    }
  ],

  "config": {
    "defaultBaseSize": 16,
    "defaultRatio": 1.25,
    "defaultSource": "google-fonts"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Font asset storage |
| Librarian | Font database search |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Typography selection |
| VisualBrand | Brand type system |
| PDFMaker | Document typography |

---

*Last updated: 2026-01-17*
