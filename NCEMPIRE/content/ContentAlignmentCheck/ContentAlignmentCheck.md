# ContentAlignmentCheck Module

> **Purpose:** Validate content aligns with audience, theme, tone, and structure
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

ContentAlignmentCheck validates that generated content aligns with audience expectations, theme truths, tone requirements, and structural specifications. Focuses on content-level alignment (not brand-level).

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### What ContentAlignmentCheck Validates

| Check | Description |
|-------|-------------|
| Audience language | Language matches audience cognitive level |
| Audience pain points | References audience challenges |
| Theme truths present | Primary/secondary truths appear |
| Theme concept coherent | Stays on-topic with theme |
| Tone consistent | Matches merged tone guidance |
| Structure followed | Section type requirements met |
| Framework elements | Required elements present |

### Distinct From Other Checkers

- **BrandCheck**: Brand voice, terminology (separate)
- **ValuesCheck**: Brand values, principles (separate)
- **TransformationCheck**: Belief journey, promises (separate)

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100
  categoryScores: {
    audience: { score, checks },
    theme: { score, checks },
    tone: { score, checks },
    structure: { score, checks },
    framework: { score, checks }
  }
}
```

### Dependencies

| Depends On | For |
|------------|-----|
| Audience data | Language level, pain points |
| Theme data | Truths, concept, tone |
| Section spec | Structure requirements |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Content alignment scoring |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
