# TransformationCheck Module

> **Purpose:** Validate content delivers on transformation promises and belief journey
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

TransformationCheck validates that generated content properly facilitates the belief transformation journey defined in the theme. It checks that belief shifts are supported, promised outcomes are delivered, and brand perspective is enforced.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### What TransformationCheck Validates

| Check | Description |
|-------|-------------|
| Belief shifts facilitated | Content moves reader from old → new belief |
| Signature phrases present | Brand's signature phrases appear naturally |
| Rejected language absent | Brand's "whatImRejecting" language avoided |
| Promised outcomes addressed | Section.promisesOutcome[] delivered |
| Emotional journey followed | Content matches theme.emotionalJourney |

### Distinct from ContentAlignment

**ContentAlignment** (separate checker):
- General alignment: brand voice, audience language, theme truths, tone, structure
- Scores: 0-100 per category

**TransformationCheck** (this module):
- Transformation-specific: belief shifts, promises, brand perspective enforcement
- Binary + score: { passed: boolean, score: number, issues: string[] }

Both run in parallel via PostCreationCheckManager.

### Inputs

```javascript
{
  content: string,           // Generated content
  theme: Theme,              // For transformationFramework, beliefShifts
  outline: Outline,          // For section.promisesOutcome
  brand: Brand               // For signature phrases, rejected language
}
```

### Output

```javascript
{
  passed: boolean,
  score: number,             // 0-100
  checks: {
    beliefShifts: { passed, issues },
    signaturePhrases: { passed, found, missing },
    rejectedLanguage: { passed, violations },
    promisedOutcomes: { passed, delivered, missing },
    emotionalJourney: { passed, issues }
  },
  issues: string[],
  suggestions: string[]
}
```

### Thresholds

| Check | Pass Threshold | Notes |
|-------|----------------|-------|
| Belief shifts | ≥1 shift supported | Must facilitate at least one |
| Signature phrases | ≥50% present | At least half of signature phrases |
| Rejected language | 0 violations | Zero tolerance |
| Promised outcomes | 100% delivered | All promises must be kept |

### Dependencies

| Depends On | For |
|------------|-----|
| (none) | Stateless checker |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Transformation validation |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
