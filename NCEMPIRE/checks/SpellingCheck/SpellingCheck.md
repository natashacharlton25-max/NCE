# SpellingCheck Module

> **Purpose:** Detect and auto-fix spelling errors
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

SpellingCheck detects spelling errors in generated content. Uses Python-based spell checking (pyspellchecker or similar). Can auto-fix common misspellings.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### Capabilities

| Capability | Auto-Fix | Notes |
|------------|----------|-------|
| Misspellings | Yes | Dictionary-based |
| Typos | Yes | Common patterns |
| Capitalization | Yes | Proper nouns, sentence starts |
| Brand terms | No | Custom dictionary, flag only |

### Tech Stack

- Python runtime required
- pyspellchecker or hunspell
- Brand-specific dictionary support

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100 (100 = no errors)
  errorCount: number,
  errors: [
    {
      word: string,
      suggestions: string[],
      position: { start, end },
      autoFixable: boolean
    }
  ],
  autoFixed?: {
    content: string,
    fixCount: number
  }
}
```

### Brand Dictionary

Custom terms that shouldn't be flagged:
- Brand name variations
- Product names
- Industry jargon

### Dependencies

| Depends On | For |
|------------|-----|
| Python runtime | Spell checking |
| Brand dictionary | Custom terms |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Spelling validation |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
