# ColourMix Module

> **Purpose:** Mix, blend, and create color harmonies from existing colors
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ColourMix handles color manipulation - blending colors together, creating harmonies, adjusting properties (lightness, saturation), and deriving new colors from existing ones. It's the color mixing palette.

```
Base colors → ColourMix.blend() → New colors
Color → ColourMix.harmonize() → Harmonious palette
```

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `blend(color1, color2, ratio)` | Blend two colors | color1, color2, ratio | blendedColor |
| `harmonize(baseColor, type)` | Create color harmony | baseColor, type | harmony |
| `adjust(color, adjustments)` | Adjust color properties | color, adjustments | adjustedColor |
| `tint(color, amount)` | Add white (lighten) | color, amount | tintedColor |
| `shade(color, amount)` | Add black (darken) | color, amount | shadedColor |
| `tone(color, amount)` | Add gray (desaturate) | color, amount | tonedColor |
| `complement(color)` | Get complementary color | color | complementColor |
| `gradient(color1, color2, steps)` | Generate gradient steps | color1, color2, steps | gradientColors |

---

## Detailed Function Specs

### blend(color1, color2, ratio)

Blends two colors together.

```javascript
// Input
{
  color1: "#1a73e8",
  color2: "#34a853",
  ratio: 0.5  // 50/50 blend
}

// Output
{
  blended: "#278dbd",
  inputs: ["#1a73e8", "#34a853"],
  ratio: 0.5,
  mode: "rgb"  // Can also be hsl, lab
}
```

### harmonize(baseColor, type)

Creates harmonious color palette from base.

```javascript
// Input
{
  baseColor: "#1a73e8",
  type: "triadic"  // complementary, analogous, triadic, tetradic, split-complementary
}

// Output
{
  base: "#1a73e8",
  harmonyType: "triadic",
  colors: [
    { color: "#1a73e8", role: "base", angle: 0 },
    { color: "#e81a73", role: "triadic-1", angle: 120 },
    { color: "#73e81a", role: "triadic-2", angle: 240 }
  ],
  wheel: "/visuals/triadic-wheel.svg"
}
```

### adjust(color, adjustments)

Adjusts color properties.

```javascript
// Input
{
  color: "#1a73e8",
  adjustments: {
    lightness: +10,      // Increase lightness by 10%
    saturation: -5,      // Decrease saturation by 5%
    hue: +15             // Shift hue by 15 degrees
  }
}

// Output
{
  original: "#1a73e8",
  adjusted: "#4d8fec",
  changes: {
    lightness: { from: 50, to: 60 },
    saturation: { from: 82, to: 77 },
    hue: { from: 217, to: 232 }
  }
}
```

### gradient(color1, color2, steps)

Generates gradient color steps.

```javascript
// Input
{
  color1: "#1a73e8",
  color2: "#34a853",
  steps: 5
}

// Output
{
  gradient: [
    { step: 0, color: "#1a73e8", percent: 0 },
    { step: 1, color: "#247cb8", percent: 25 },
    { step: 2, color: "#2d8588", percent: 50 },
    { step: 3, color: "#318f6b", percent: 75 },
    { step: 4, color: "#34a853", percent: 100 }
  ],
  css: "linear-gradient(to right, #1a73e8, #247cb8, #2d8588, #318f6b, #34a853)"
}
```

---

## Color Harmony Types

| Type | Description | Angles |
|------|-------------|--------|
| Complementary | Opposite colors | 180° |
| Analogous | Adjacent colors | ±30° |
| Triadic | Three evenly spaced | 120° |
| Tetradic | Four evenly spaced | 90° |
| Split-complementary | Base + split complement | 150°, 210° |

---

## Config

### module.config.json

```json
{
  "module": "ColourMix",
  "version": "1.0.0",
  "description": "Mix and blend colors",
  "type": "service",

  "functions": [
    { "name": "blend", "input": ["color1", "color2", "ratio"], "output": "blendedColor" },
    { "name": "harmonize", "input": ["baseColor", "type"], "output": "harmony" },
    { "name": "adjust", "input": ["color", "adjustments"], "output": "adjustedColor" },
    { "name": "tint", "input": ["color", "amount"], "output": "tintedColor" },
    { "name": "shade", "input": ["color", "amount"], "output": "shadedColor" },
    { "name": "tone", "input": ["color", "amount"], "output": "tonedColor" },
    { "name": "complement", "input": ["color"], "output": "complementColor" },
    { "name": "gradient", "input": ["color1", "color2", "steps"], "output": "gradientColors" }
  ],

  "config": {
    "defaultBlendMode": "rgb",
    "defaultGradientSteps": 5
  }
}
```

---

## Used By

| Module | How |
|--------|-----|
| ColourToken | Palette generation |
| ColourMaker | Color creation |

---

*Last updated: 2026-01-17*
