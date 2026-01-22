# LayoutCheck Module

> **Purpose:** Validate document layout and structure formatting
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

LayoutCheck validates the structural layout of rendered content. Catches issues like orphaned headers, page break problems, whitespace issues, and formatting inconsistencies.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### What LayoutCheck Validates

| Check | Auto-Fix | Notes |
|-------|----------|-------|
| Orphan/widow lines | Some | Page break adjustment |
| Header hierarchy | No | H1 → H2 → H3 order |
| Section spacing | Yes | Normalize gaps |
| List formatting | Yes | Consistent bullets/numbers |
| Paragraph length | No | Flag if too long/short |
| Whitespace | Yes | Remove excess |

### Distinct From VisualCheck

- **LayoutCheck**: Structure, formatting, text flow
- **VisualCheck**: Images, colors, visual elements

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100
  issues: [
    {
      type: string,           // "orphan", "whitespace", "hierarchy"
      severity: "critical" | "major" | "minor",
      location: string,
      description: string,
      autoFixable: boolean
    }
  ],
  autoFixed?: {
    changeCount: number,
    changes: string[]
  }
}
```

### Content Type Rules

| Content Type | Extra Checks |
|--------------|--------------|
| Workbook | Exercise boxes, reflection space |
| PDF | Page margins, headers/footers |
| Email | Line length, mobile width |

### Dependencies

| Depends On | For |
|------------|-----|
| Rendered output | Layout analysis |
| Content type config | Type-specific rules |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Layout validation |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
