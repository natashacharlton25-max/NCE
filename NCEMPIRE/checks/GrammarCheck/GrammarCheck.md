# GrammarCheck Module

> **Purpose:** Detect and flag grammar issues
> **Type:** Quality Assurance Checker
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

GrammarCheck detects grammar issues in generated content. Uses Python-based grammar checking (LanguageTool, textblob, or similar). Some issues can be auto-fixed.

Runs as part of PostCreationCheckManager (in parallel with other checkers).

---

## Notes

### Capabilities

| Capability | Auto-Fix | Notes |
|------------|----------|-------|
| Subject-verb agreement | Some | Rule-based |
| Tense consistency | No | Flag only |
| Punctuation | Yes | Common patterns |
| Run-on sentences | No | Flag only |
| Fragment sentences | No | Flag only |
| Word repetition | No | Flag only |
| Passive voice | No | Flag only (style choice) |

### Tech Stack

- Python runtime required
- LanguageTool (comprehensive) or textblob (lighter)
- May need Java for LanguageTool

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100 (100 = no issues)
  issueCount: number,
  issues: [
    {
      type: string,           // "agreement", "tense", "punctuation", etc.
      text: string,
      suggestion: string,
      position: { start, end },
      autoFixable: boolean,
      severity: "error" | "warning" | "style"
    }
  ],
  autoFixed?: {
    content: string,
    fixCount: number
  }
}
```

### Style vs Error

| Type | Action |
|------|--------|
| Error | Must fix |
| Warning | Should fix |
| Style | Optional (passive voice, etc.) |

### Dependencies

| Depends On | For |
|------------|-----|
| Python runtime | Grammar checking |
| LanguageTool/textblob | Analysis |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Grammar validation |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
