# BrandCheck Module

> **Purpose:** Validate content aligns with brand voice, terminology, and identity
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

BrandCheck validates that generated content aligns with brand voice, uses correct terminology, and maintains brand identity. Focuses specifically on brand-level alignment.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### What BrandCheck Validates

| Check | Description |
|-------|-------------|
| Voice core present | Core voice elements appear in language |
| Terminology correct | Uses brand-specific terms correctly |
| Avoid list honored | Doesn't use prohibited terms/approaches |
| Signature phrases | Brand phrases used naturally |
| Philosophy consistent | Content doesn't contradict brand philosophy |

### Distinct From Other Checkers

- **ValuesCheck**: Brand values alignment (separate)
- **ContentAlignmentCheck**: Audience, theme, structure (separate)
- **TransformationCheck**: Belief journey, promises (separate)

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100
  checks: {
    voiceCore: { passed, evidence },
    terminology: { passed, issues },
    avoidList: { passed, violations },
    signaturePhrases: { passed, found, missing },
    philosophy: { passed, conflicts }
  }
}
```

### Dependencies

| Depends On | For |
|------------|-----|
| Brand data | Voice, terminology, avoid list |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Brand alignment scoring |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
