# ColourMaker Module

> **Purpose:** Create colors from various inputs (names, moods, images, values)
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ColourMaker generates colors from abstract inputs - mood words, brand values, images, or descriptions. It's the creative color generator that translates concepts into specific colors.

```
"Professional, innovative, trustworthy" → ColourMaker.fromMood() → #1a73e8
```

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `fromMood(moods)` | Generate color from moods | moods | colors |
| `fromValues(values)` | Generate from brand values | values | colors |
| `fromImage(image)` | Extract dominant colors | image | colors |
| `fromDescription(description)` | Generate from text description | description | colors |
| `fromIndustry(industry)` | Typical industry colors | industry | colors |
| `fromName(colorName)` | Color from name | colorName | color |
| `random(constraints)` | Random color with constraints | constraints | color |

---

## Detailed Function Specs

### fromMood(moods)

Generates colors from mood descriptors.

```javascript
// Input
{
  moods: ["professional", "innovative", "calm"],
  count: 3
}

// Output
{
  colors: [
    {
      color: "#1a73e8",
      name: "Professional Blue",
      moods: ["professional", "calm"],
      confidence: 0.92
    },
    {
      color: "#7c4dff",
      name: "Innovation Purple",
      moods: ["innovative"],
      confidence: 0.88
    },
    {
      color: "#00bfa5",
      name: "Calm Teal",
      moods: ["calm", "innovative"],
      confidence: 0.85
    }
  ],
  moodMapping: {
    "professional": { tendency: "blue/navy", saturation: "medium" },
    "innovative": { tendency: "purple/cyan", saturation: "high" },
    "calm": { tendency: "blue/green", saturation: "low-medium" }
  }
}
```

### fromValues(values)

Generates colors from brand values.

```javascript
// Input
{
  values: ["trust", "growth", "innovation"],
  style: "corporate"  // corporate, startup, creative, luxury
}

// Output
{
  recommended: {
    primary: { color: "#1a73e8", value: "trust", confidence: 0.95 },
    secondary: { color: "#34a853", value: "growth", confidence: 0.92 },
    accent: { color: "#7c4dff", value: "innovation", confidence: 0.88 }
  },
  alternatives: {
    trust: ["#1565c0", "#0d47a1", "#2196f3"],
    growth: ["#2e7d32", "#43a047", "#00c853"],
    innovation: ["#6200ea", "#651fff", "#00bcd4"]
  }
}
```

### fromImage(image)

Extracts and generates colors from image.

```javascript
// Input
{
  image: "/assets/inspiration.jpg",
  count: 6,
  options: {
    vibrant: true,
    muted: true,
    dominant: true
  }
}

// Output
{
  extracted: {
    dominant: "#2d5a7b",
    vibrant: "#4fc3f7",
    muted: "#78909c",
    lightVibrant: "#81d4fa",
    darkVibrant: "#01579b",
    lightMuted: "#b0bec5"
  },
  palette: [
    { color: "#2d5a7b", percentage: 32, name: "Ocean Blue" },
    { color: "#f4e4bc", percentage: 28, name: "Sand" }
  ],
  suggested: {
    primary: "#2d5a7b",
    secondary: "#4fc3f7",
    accent: "#f4e4bc"
  }
}
```

### fromDescription(description)

Generates colors from text description.

```javascript
// Input
{
  description: "A warm sunset over mountains with golden light"
}

// Output
{
  colors: [
    { color: "#ff7043", name: "Sunset Orange", element: "sunset" },
    { color: "#ffd54f", name: "Golden Light", element: "golden light" },
    { color: "#5d4037", name: "Mountain Brown", element: "mountains" },
    { color: "#ffab91", name: "Warm Glow", element: "warm" }
  ],
  palette: {
    primary: "#ff7043",
    secondary: "#ffd54f",
    accent: "#5d4037",
    neutral: "#ffab91"
  }
}
```

### fromIndustry(industry)

Gets typical colors for an industry.

```javascript
// Input
{
  industry: "finance",
  style: "modern"  // traditional, modern, disruptive
}

// Output
{
  industry: "finance",
  typical: {
    traditional: ["#1a237e", "#0d47a1", "#004d40"],
    modern: ["#1a73e8", "#00bfa5", "#7c4dff"],
    disruptive: ["#00e676", "#651fff", "#ff1744"]
  },
  recommended: ["#1a73e8", "#00bfa5"],
  avoid: ["bright red (anxiety)", "neon colors (unprofessional)"],
  competitors: {
    "bank-a": "#003087",
    "bank-b": "#006633"
  }
}
```

---

## Mood-Color Mapping

```json
{
  "moods": {
    "professional": { "colors": ["blue", "navy", "gray"], "saturation": "medium" },
    "innovative": { "colors": ["purple", "cyan", "electric blue"], "saturation": "high" },
    "trustworthy": { "colors": ["blue", "green", "navy"], "saturation": "medium" },
    "energetic": { "colors": ["orange", "red", "yellow"], "saturation": "high" },
    "calm": { "colors": ["blue", "green", "soft purple"], "saturation": "low" },
    "luxurious": { "colors": ["gold", "purple", "black"], "saturation": "medium" },
    "natural": { "colors": ["green", "brown", "earth tones"], "saturation": "medium" },
    "playful": { "colors": ["bright primaries", "pink", "orange"], "saturation": "high" }
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "ColourMaker",
  "version": "1.0.0",
  "description": "Create colors from various inputs",
  "type": "service",

  "functions": [
    { "name": "fromMood", "input": ["moods"], "output": "colors" },
    { "name": "fromValues", "input": ["values"], "output": "colors" },
    { "name": "fromImage", "input": ["image"], "output": "colors" },
    { "name": "fromDescription", "input": ["description"], "output": "colors" },
    { "name": "fromIndustry", "input": ["industry"], "output": "colors" },
    { "name": "fromName", "input": ["colorName"], "output": "color" },
    { "name": "random", "input": ["constraints"], "output": "color" }
  ],

  "config": {
    "moodMappingSource": "/repos/mood-color-mappings/",
    "industryMappingSource": "/repos/industry-colors/"
  }
}
```

---

## Used By

| Module | How |
|--------|-----|
| ColourToken | Initial color generation |
| VisualBrand | Brand color creation |
| AIImageCreation | Color direction for images |

---

*Last updated: 2026-01-17*
