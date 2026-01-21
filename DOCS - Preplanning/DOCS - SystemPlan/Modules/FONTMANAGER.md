# FontManager Module

> **Purpose:** Manage font assets, licensing, loading, and distribution
> **Type:** Service Module
> **Status:** Planning

---

## Overview

FontManager handles the technical side of fonts - managing font files, tracking licenses, loading fonts for use, and distributing font assets. It works with Typography (which selects fonts) by managing the actual font assets.

```
Typography selects "Montserrat" → FontManager provides the font files
```

**Manages:**
- Font file storage
- License tracking
- Web font loading
- Font subsetting
- Format conversion
- Font distribution

---

## Roles & Rules

### FontManager DOES:
- Manage font file storage
- Track font licenses
- Generate web font loading code
- Create font subsets
- Convert font formats
- Install new fonts

### FontManager does NOT:
- Select fonts for brands (Typography does that)
- Create typography systems (Typography does that)
- Purchase font licenses (human/admin only)
- Design with fonts (VisualBrand does that)

### Boundaries:
- Cannot select fonts (only provides assets)
- Cannot approve unlicensed usage
- Cannot modify font files (only subset/convert)
- Cannot bypass license restrictions

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `getFont(fontId)` | Get font files and info | fontId | fontAssets |
| `loadWebFont(fontId, options)` | Generate web font loading code | fontId, options | loadingCode |
| `checkLicense(fontId, usage)` | Check license for usage | fontId, usage | licenseStatus |
| `subset(fontId, characters)` | Create font subset | fontId, characters | subsetFont |
| `convert(fontId, format)` | Convert font format | fontId, format | convertedFont |
| `install(fontSource, fontData)` | Install new font | fontSource, fontData | installed |
| `listFonts(filter)` | List available fonts | filter | fonts |

---

## Detailed Function Specs

### getFont(fontId)

Gets font files and metadata.

```javascript
// Input
{
  fontId: "montserrat"
}

// Output
{
  id: "montserrat",
  name: "Montserrat",
  family: "Montserrat",
  category: "sans-serif",
  source: "google-fonts",
  license: {
    type: "OFL",
    commercial: true,
    modification: true,
    attribution: false
  },
  weights: [100, 200, 300, 400, 500, 600, 700, 800, 900],
  styles: ["normal", "italic"],
  files: {
    woff2: {
      400: "/fonts/montserrat/montserrat-400.woff2",
      700: "/fonts/montserrat/montserrat-700.woff2"
    },
    woff: { ... },
    ttf: { ... }
  },
  metrics: {
    unitsPerEm: 1000,
    ascender: 968,
    descender: -251,
    xHeight: 486
  }
}
```

### loadWebFont(fontId, options)

Generates web font loading code.

```javascript
// Input
{
  fontId: "montserrat",
  options: {
    weights: [400, 500, 700],
    styles: ["normal"],
    display: "swap",
    preload: true
  }
}

// Output
{
  css: `
@font-face {
  font-family: 'Montserrat';
  font-style: normal;
  font-weight: 400;
  font-display: swap;
  src: url('/fonts/montserrat-400.woff2') format('woff2');
}
@font-face {
  font-family: 'Montserrat';
  font-style: normal;
  font-weight: 500;
  font-display: swap;
  src: url('/fonts/montserrat-500.woff2') format('woff2');
}
/* ... */`,
  preloadLinks: [
    '<link rel="preload" href="/fonts/montserrat-400.woff2" as="font" type="font/woff2" crossorigin>',
    '<link rel="preload" href="/fonts/montserrat-700.woff2" as="font" type="font/woff2" crossorigin>'
  ],
  googleFontsUrl: "https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;700&display=swap"
}
```

### subset(fontId, characters)

Creates a subset font with only needed characters.

```javascript
// Input
{
  fontId: "montserrat",
  characters: "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",
  weights: [400, 700]
}

// Output
{
  original: {
    size: 245000,
    glyphs: 1509
  },
  subset: {
    size: 32000,
    glyphs: 62,
    savings: "87%"
  },
  files: {
    woff2: {
      400: "/fonts/montserrat-400-subset.woff2",
      700: "/fonts/montserrat-700-subset.woff2"
    }
  }
}
```

### checkLicense(fontId, usage)

Checks if font license allows usage.

```javascript
// Input
{
  fontId: "montserrat",
  usage: {
    type: "commercial",
    medium: ["web", "print", "app"],
    distribution: "client-delivery"
  }
}

// Output
{
  allowed: true,
  license: "OFL",
  details: {
    commercial: { allowed: true },
    web: { allowed: true },
    print: { allowed: true },
    app: { allowed: true, embedding: "allowed" },
    modification: { allowed: true },
    redistribution: { allowed: true, condition: "same license" }
  },
  attribution: {
    required: false,
    text: null
  }
}
```

---

## Font Sources

| Source | Type | License Types |
|--------|------|---------------|
| Google Fonts | Free | OFL, Apache 2.0 |
| Adobe Fonts | Subscription | Adobe License |
| Custom/Purchased | Varies | Per-license |
| System | Built-in | Platform |

---

## Font Storage Structure

```
/fonts/
  /montserrat/
    montserrat-400.woff2
    montserrat-400.woff
    montserrat-400.ttf
    montserrat-700.woff2
    montserrat-700.woff
    montserrat-700.ttf
    license.txt
    metadata.json
```

---

## Config

### module.config.json

```json
{
  "module": "FontManager",
  "version": "1.0.0",
  "description": "Manage font assets and licensing",
  "type": "service",

  "functions": [
    { "name": "getFont", "input": ["fontId"], "output": "fontAssets" },
    { "name": "loadWebFont", "input": ["fontId", "options"], "output": "loadingCode" },
    { "name": "checkLicense", "input": ["fontId", "usage"], "output": "licenseStatus" },
    { "name": "subset", "input": ["fontId", "characters"], "output": "subsetFont" },
    { "name": "convert", "input": ["fontId", "format"], "output": "convertedFont" },
    { "name": "install", "input": ["fontSource", "fontData"], "output": "installed" },
    { "name": "listFonts", "input": ["filter"], "output": "fonts" }
  ],

  "config": {
    "fontsPath": "/fonts/",
    "defaultFormats": ["woff2", "woff"],
    "defaultDisplay": "swap"
  }
}
```

---

## Used By

| Module | How |
|--------|-----|
| Typography | Font file access |
| PDFMaker | Embedding fonts |
| VisualBrand | Font distribution |

---

*Last updated: 2026-01-17*
