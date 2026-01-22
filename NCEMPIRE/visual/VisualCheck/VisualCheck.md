# VisualCheck Module

> **Purpose:** Validate visual elements (images, graphics, colors)
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

VisualCheck validates visual elements in rendered content - images, graphics, color usage, and visual consistency. Catches missing images, incorrect sizing, and visual mismatches.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### What VisualCheck Validates

| Check | Auto-Fix | Notes |
|-------|----------|-------|
| Images present | No | Flag missing |
| Image sizing | Some | Resize to fit |
| Image quality | No | Flag low resolution |
| Alt text present | No | Accessibility |
| Color consistency | No | Brand colors used |
| Visual hierarchy | No | Flag cluttered areas |
| Image relevance | No | Match to keywords |

### Distinct From LayoutCheck

- **LayoutCheck**: Structure, formatting, text flow
- **VisualCheck**: Images, colors, visual elements

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100
  issues: [
    {
      type: string,           // "missing_image", "sizing", "quality"
      severity: "critical" | "major" | "minor",
      element: string,        // Image reference
      description: string,
      autoFixable: boolean
    }
  ],
  imageReport: {
    total: number,
    present: number,
    missing: number,
    qualityIssues: number
  }
}
```

### Image Validation

| Check | Threshold |
|-------|-----------|
| Minimum resolution | 72 DPI (web), 300 DPI (print) |
| Maximum file size | 5MB |
| Supported formats | PNG, JPG, SVG, WebP |
| Alt text required | Yes (accessibility) |

### Dependencies

| Depends On | For |
|------------|-----|
| Rendered output | Visual analysis |
| Image assets | Validation |
| Brand colors | Color consistency |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Visual validation |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
