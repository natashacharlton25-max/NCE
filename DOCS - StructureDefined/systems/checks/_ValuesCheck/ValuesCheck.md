# ValuesCheck Module

> **Purpose:** Validate content reflects brand values and principles
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

ValuesCheck validates that generated content embodies and reflects the brand's core values and principles. Separate from brand voice/terminology (BrandCheck) - focuses on deeper value alignment.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### What ValuesCheck Validates

| Check | Description |
|-------|-------------|
| Values reflected | Brand values implicit in content |
| Principles upheld | Core principles not contradicted |
| Ethical alignment | Content meets ethical standards |
| Mission support | Content advances brand mission |

### Distinct From BrandCheck

- **BrandCheck**: Voice, terminology, identity (surface)
- **ValuesCheck**: Values, principles, ethics (deeper)

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100
  checks: {
    valuesReflected: { passed, valuesFound, valuesMissing },
    principlesUpheld: { passed, conflicts },
    ethicalAlignment: { passed, concerns },
    missionSupport: { passed, evidence }
  }
}
```

### Dependencies

| Depends On | For |
|------------|-----|
| Brand data | Values, principles, mission |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Values alignment scoring |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
