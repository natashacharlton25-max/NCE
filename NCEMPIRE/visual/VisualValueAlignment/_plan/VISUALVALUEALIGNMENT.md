# VisualValueAlignment Module

> **Purpose:** Ensure visual elements align with brand values and messaging
> **Type:** Service Module
> **Status:** Planning

---

## Overview

VisualValueAlignment checks that visual choices (colors, imagery, typography, design) actually communicate the intended brand values. It bridges the gap between abstract values ("innovation", "trust") and concrete visual expression.

```
Visual asset + Brand values → VisualValueAlignment.check() → Alignment score + Recommendations
```

**What it validates:**
- Do colors communicate intended emotions?
- Does imagery reflect brand personality?
- Does typography match brand voice?
- Is there visual-verbal consistency?

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `checkAlignment(visual, values)` | Check visual-value alignment | visual, values | alignmentReport |
| `analyzeColorMeaning(colors, values)` | Analyze color psychology alignment | colors, values | colorAnalysis |
| `analyzeImageryMeaning(imagery, values)` | Analyze imagery alignment | imagery, values | imageryAnalysis |
| `analyzeTypographyMeaning(typography, values)` | Analyze typography alignment | typography, values | typographyAnalysis |
| `suggestImprovements(analysis)` | Suggest value-aligned alternatives | analysis | suggestions |
| `scoreOverall(analyses)` | Calculate overall alignment | analyses | overallScore |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `VisualValueAlignment.fullAudit` | Complete visual-value audit | analyzeColorMeaning → analyzeImageryMeaning → analyzeTypographyMeaning → scoreOverall |
| `VisualValueAlignment.quickCheck` | Quick alignment check | checkAlignment |

---

## Detailed Function Specs

### checkAlignment(visual, values)

Checks overall visual-value alignment.

```javascript
// Input
{
  visual: {
    colors: {
      primary: "#1a73e8",
      secondary: "#34a853",
      accent: "#ff5722"
    },
    typography: {
      heading: "Montserrat",
      body: "Open Sans"
    },
    imagery: {
      style: "corporate photography",
      samples: ["/assets/hero.jpg", "/assets/team.jpg"]
    }
  },
  values: {
    primary: ["innovation", "trust", "professionalism"],
    personality: ["modern", "approachable", "reliable"],
    avoid: ["cold", "aggressive", "outdated"]
  }
}

// Process
1. Analyze each visual element against values
2. Check for contradictions
3. Score alignment
4. Generate recommendations

// Output
{
  aligned: true,
  overallScore: 0.82,
  analysis: {
    colors: {
      score: 0.88,
      aligned: ["trust (blue)", "growth (green)"],
      concerns: [
        {
          element: "accent #ff5722",
          issue: "Orange can convey energy but may conflict with 'professionalism'",
          severity: "low",
          suggestion: "Consider a purple accent for innovation"
        }
      ]
    },
    typography: {
      score: 0.92,
      aligned: ["modern (Montserrat)", "approachable (Open Sans)"],
      concerns: []
    },
    imagery: {
      score: 0.75,
      aligned: ["professional"],
      concerns: [
        {
          element: "team.jpg",
          issue: "Stock-like imagery may undermine authenticity",
          severity: "medium",
          suggestion: "Use more candid, authentic photography"
        }
      ]
    }
  },
  recommendations: [
    "Consider replacing orange accent with purple for better innovation alignment",
    "Update team photography to feel more authentic and less staged"
  ]
}
```

### analyzeColorMeaning(colors, values)

Analyzes how colors communicate values.

```javascript
// Input
{
  colors: {
    primary: "#1a73e8",
    secondary: "#34a853",
    accent: "#7c4dff"
  },
  values: ["innovation", "trust", "growth"]
}

// Process
1. Map colors to psychological associations
2. Compare with target values
3. Identify alignments and gaps

// Output
{
  analysis: {
    primary: {
      color: "#1a73e8",
      name: "Blue",
      associations: ["trust", "stability", "professionalism", "technology"],
      valueAlignment: {
        "trust": { aligned: true, strength: 0.95 },
        "innovation": { aligned: true, strength: 0.7 },
        "growth": { aligned: false, strength: 0.3 }
      },
      overallFit: 0.88
    },
    secondary: {
      color: "#34a853",
      name: "Green",
      associations: ["growth", "nature", "health", "prosperity"],
      valueAlignment: {
        "trust": { aligned: true, strength: 0.6 },
        "innovation": { aligned: true, strength: 0.5 },
        "growth": { aligned: true, strength: 0.95 }
      },
      overallFit: 0.92
    },
    accent: {
      color: "#7c4dff",
      name: "Purple",
      associations: ["creativity", "innovation", "luxury", "wisdom"],
      valueAlignment: {
        "trust": { aligned: true, strength: 0.5 },
        "innovation": { aligned: true, strength: 0.9 },
        "growth": { aligned: false, strength: 0.4 }
      },
      overallFit: 0.78
    }
  },
  palette: {
    overallScore: 0.86,
    valuesCovered: ["trust", "innovation", "growth"],
    valuesWeak: [],
    suggestions: []
  }
}
```

### analyzeImageryMeaning(imagery, values)

Analyzes how imagery communicates values.

```javascript
// Input
{
  imagery: {
    samples: ["/assets/hero.jpg", "/assets/product.jpg"],
    style: "modern tech photography"
  },
  values: ["innovation", "trust", "approachability"]
}

// Process
1. Analyze image characteristics
2. Extract implied meanings
3. Compare with target values

// Output
{
  analysis: {
    samples: [
      {
        image: "/assets/hero.jpg",
        characteristics: {
          lighting: "bright, natural",
          composition: "clean, minimal",
          subjects: ["technology", "modern office"],
          mood: "optimistic, professional"
        },
        valueAlignment: {
          "innovation": { aligned: true, strength: 0.8, reason: "Modern tech setting" },
          "trust": { aligned: true, strength: 0.7, reason: "Clean, professional environment" },
          "approachability": { aligned: true, strength: 0.6, reason: "Bright, welcoming lighting" }
        },
        overallFit: 0.78
      }
    ],
    style: {
      current: "modern tech photography",
      alignedValues: ["innovation", "professionalism"],
      suggestions: [
        "Add more human elements for approachability",
        "Include collaborative scenes for trust"
      ]
    }
  },
  overallScore: 0.75
}
```

### analyzeTypographyMeaning(typography, values)

Analyzes how typography communicates values.

```javascript
// Input
{
  typography: {
    heading: "Montserrat",
    body: "Open Sans"
  },
  values: ["innovation", "trust", "approachability"]
}

// Output
{
  analysis: {
    heading: {
      font: "Montserrat",
      category: "geometric sans-serif",
      characteristics: {
        personality: ["modern", "clean", "confident"],
        formality: "medium-high",
        warmth: "neutral-warm"
      },
      valueAlignment: {
        "innovation": { aligned: true, strength: 0.85, reason: "Geometric, modern forms" },
        "trust": { aligned: true, strength: 0.8, reason: "Stable, confident letterforms" },
        "approachability": { aligned: true, strength: 0.7, reason: "Rounded, friendly shapes" }
      },
      overallFit: 0.85
    },
    body: {
      font: "Open Sans",
      category: "humanist sans-serif",
      characteristics: {
        personality: ["friendly", "clear", "open"],
        formality: "medium",
        warmth: "warm"
      },
      valueAlignment: {
        "innovation": { aligned: true, strength: 0.6, reason: "Clean, modern" },
        "trust": { aligned: true, strength: 0.75, reason: "High legibility builds trust" },
        "approachability": { aligned: true, strength: 0.9, reason: "Humanist, friendly design" }
      },
      overallFit: 0.88
    }
  },
  pairing: {
    harmony: 0.9,
    reason: "Complementary geometric/humanist pairing"
  },
  overallScore: 0.87
}
```

---

## Value-Visual Mapping

```json
{
  "valueAssociations": {
    "innovation": {
      "colors": ["purple", "electric blue", "cyan"],
      "typography": ["geometric sans", "modern serif"],
      "imagery": ["technology", "abstract", "future-focused"],
      "avoid": ["traditional", "ornate", "dated"]
    },
    "trust": {
      "colors": ["blue", "green", "navy"],
      "typography": ["stable serif", "clean sans"],
      "imagery": ["people", "handshakes", "consistency"],
      "avoid": ["chaotic", "aggressive red", "unstable compositions"]
    },
    "approachability": {
      "colors": ["warm tones", "soft blues", "greens"],
      "typography": ["rounded sans", "humanist"],
      "imagery": ["smiling people", "natural settings", "warmth"],
      "avoid": ["cold", "stark", "intimidating"]
    }
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "VisualValueAlignment",
  "version": "1.0.0",
  "description": "Ensure visual-value alignment",
  "type": "service",

  "functions": [
    {
      "name": "checkAlignment",
      "description": "Check visual-value alignment",
      "input": ["visual", "values"],
      "output": "alignmentReport"
    },
    {
      "name": "analyzeColorMeaning",
      "description": "Analyze color psychology alignment",
      "input": ["colors", "values"],
      "output": "colorAnalysis"
    },
    {
      "name": "analyzeImageryMeaning",
      "description": "Analyze imagery alignment",
      "input": ["imagery", "values"],
      "output": "imageryAnalysis"
    },
    {
      "name": "analyzeTypographyMeaning",
      "description": "Analyze typography alignment",
      "input": ["typography", "values"],
      "output": "typographyAnalysis"
    },
    {
      "name": "suggestImprovements",
      "description": "Suggest value-aligned alternatives",
      "input": ["analysis"],
      "output": "suggestions"
    },
    {
      "name": "scoreOverall",
      "description": "Calculate overall alignment",
      "input": ["analyses"],
      "output": "overallScore"
    }
  ],

  "steps": [
    {
      "name": "fullAudit",
      "description": "Complete visual-value audit",
      "sequence": ["analyzeColorMeaning", "analyzeImageryMeaning", "analyzeTypographyMeaning", "scoreOverall"]
    },
    {
      "name": "quickCheck",
      "description": "Quick alignment check",
      "sequence": ["checkAlignment"]
    }
  ],

  "config": {
    "minimumAlignmentScore": 0.7,
    "valueMappingSource": "/repos/value-mappings/"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Loading value mappings |
| ColourToken | Color analysis |

---

## Used By

| Module | How |
|--------|-----|
| VisualBrand | Validating visual systems |
| ContentAlignmentManager | Visual-value checking |
| BrandKit | Brand validation |

---

*Last updated: 2026-01-17*
