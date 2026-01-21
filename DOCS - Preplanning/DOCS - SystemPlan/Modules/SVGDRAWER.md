# SVGDrawer Module

> **Purpose:** Generate and manipulate SVG graphics programmatically
> **Type:** Service Module
> **Status:** Planning

---

## Overview

SVGDrawer creates and manipulates SVG graphics. It can generate simple logos, icons, patterns, and decorative elements. For complex designs, it prepares SVG templates that can be customized with brand colors and text.

```
Parameters → SVGDrawer.draw() → SVG code → File or inline
```

**Capabilities:**
- Generate geometric shapes
- Create simple logos/icons
- Apply color schemes
- Generate patterns/backgrounds
- Text to SVG paths
- SVG optimization

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `draw(spec)` | Draw SVG from specification | spec | svgCode |
| `fromTemplate(templateId, data)` | Generate from template | templateId, data | svgCode |
| `applyColors(svg, palette)` | Apply color palette to SVG | svg, palette | coloredSvg |
| `addText(svg, text, options)` | Add text to SVG | svg, text, options | svgWithText |
| `optimize(svg)` | Optimize SVG for web | svg | optimizedSvg |
| `toPng(svg, size)` | Convert SVG to PNG | svg, size | pngBuffer |
| `combine(svgs, layout)` | Combine multiple SVGs | svgs, layout | combinedSvg |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `SVGDrawer.generateLogo` | Generate simple logo | fromTemplate → applyColors → addText → optimize |
| `SVGDrawer.generateIcon` | Generate icon | draw → applyColors → optimize |

---

## Detailed Function Specs

### draw(spec)

Draws SVG from a specification object.

```javascript
// Input
{
  spec: {
    width: 200,
    height: 200,
    elements: [
      {
        type: "circle",
        cx: 100, cy: 100, r: 80,
        fill: "#1a73e8",
        stroke: "#0d47a1",
        strokeWidth: 2
      },
      {
        type: "rect",
        x: 60, y: 60, width: 80, height: 80,
        fill: "#ffffff",
        rx: 10  // rounded corners
      },
      {
        type: "path",
        d: "M 80 90 L 100 110 L 130 80",
        stroke: "#1a73e8",
        strokeWidth: 4,
        fill: "none"
      }
    ]
  }
}

// Output
{
  svg: '<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg">...</svg>',
  elements: 3,
  size: { width: 200, height: 200 }
}
```

### fromTemplate(templateId, data)

Generates SVG from a template.

```javascript
// Input
{
  templateId: "monogram-circle",
  data: {
    letters: "AC",
    primaryColor: "#1a73e8",
    secondaryColor: "#ffffff",
    font: "Montserrat"
  }
}

// Process
1. Load template
2. Replace variables
3. Apply styles
4. Return SVG

// Output
{
  svg: '<svg>...<text>AC</text>...</svg>',
  template: "monogram-circle"
}
```

### applyColors(svg, palette)

Applies a color palette to SVG.

```javascript
// Input
{
  svg: '<svg>...<rect fill="{{primary}}"/>...</svg>',
  palette: {
    primary: "#1a73e8",
    secondary: "#34a853",
    accent: "#ea4335",
    neutral: "#5f6368"
  }
}

// Output
{
  svg: '<svg>...<rect fill="#1a73e8"/>...</svg>',
  colorsApplied: ["primary"]
}
```

### optimize(svg)

Optimizes SVG for web use (using SVGO).

```javascript
// Input
{
  svg: '<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="..." width="200px" height="200px">...'
}

// Process
1. Remove unnecessary attributes
2. Minify paths
3. Remove metadata
4. Optimize numbers

// Output
{
  svg: '<svg width="200" height="200">...',  // Optimized
  reduction: {
    before: 2450,
    after: 1820,
    percent: 25.7
  }
}
```

---

## SVG Templates

Stored in `/repos/svg-templates/`:

```json
{
  "id": "monogram-circle",
  "name": "Circle Monogram",
  "type": "logo",
  "variables": ["letters", "primaryColor", "secondaryColor", "font"],
  "svg": "<svg viewBox=\"0 0 100 100\"><circle cx=\"50\" cy=\"50\" r=\"48\" fill=\"{{primaryColor}}\"/><text x=\"50\" y=\"58\" text-anchor=\"middle\" fill=\"{{secondaryColor}}\" font-family=\"{{font}}\" font-size=\"32\">{{letters}}</text></svg>",
  "defaults": {
    "primaryColor": "#000000",
    "secondaryColor": "#ffffff",
    "font": "Arial"
  }
}
```

---

## Element Types

| Type | Properties |
|------|------------|
| `rect` | x, y, width, height, rx, ry, fill, stroke |
| `circle` | cx, cy, r, fill, stroke |
| `ellipse` | cx, cy, rx, ry, fill, stroke |
| `line` | x1, y1, x2, y2, stroke, strokeWidth |
| `path` | d, fill, stroke, strokeWidth |
| `polygon` | points, fill, stroke |
| `text` | x, y, content, font, fontSize, fill |
| `group` | transform, elements[] |

---

## Config

### module.config.json

```json
{
  "module": "SVGDrawer",
  "version": "1.0.0",
  "description": "Generate and manipulate SVG graphics",
  "type": "service",

  "functions": [
    {
      "name": "draw",
      "description": "Draw SVG from specification",
      "input": ["spec"],
      "output": "svgCode"
    },
    {
      "name": "fromTemplate",
      "description": "Generate from template",
      "input": ["templateId", "data"],
      "output": "svgCode"
    },
    {
      "name": "applyColors",
      "description": "Apply color palette to SVG",
      "input": ["svg", "palette"],
      "output": "coloredSvg"
    },
    {
      "name": "addText",
      "description": "Add text to SVG",
      "input": ["svg", "text", "options"],
      "output": "svgWithText"
    },
    {
      "name": "optimize",
      "description": "Optimize SVG for web",
      "input": ["svg"],
      "output": "optimizedSvg"
    },
    {
      "name": "toPng",
      "description": "Convert SVG to PNG",
      "input": ["svg", "size"],
      "output": "pngBuffer"
    },
    {
      "name": "combine",
      "description": "Combine multiple SVGs",
      "input": ["svgs", "layout"],
      "output": "combinedSvg"
    }
  ],

  "steps": [
    {
      "name": "generateLogo",
      "description": "Generate simple logo",
      "sequence": ["fromTemplate", "applyColors", "addText", "optimize"]
    },
    {
      "name": "generateIcon",
      "description": "Generate icon",
      "sequence": ["draw", "applyColors", "optimize"]
    }
  ],

  "config": {
    "templatesRepo": "svg-templates",
    "defaultOptimize": true,
    "pngDpi": 300
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Templates | Loading SVG templates |
| FileManager | Loading palettes |

---

## Used By

| Module | How |
|--------|-----|
| LogoMaker | Simple logo generation |
| BrandKit | Icon/pattern generation |
| Export | SVG asset export |

---

*Last updated: 2026-01-17*
