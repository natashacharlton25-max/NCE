# SpellGrammarCheck Module

> **Purpose:** Check spelling and grammar, auto-fix where possible
> **Type:** Quality Assurance Checker
> **Status:** SUPERSEDED by SpellingCheck + GrammarCheck
> **Version:** 0.0.0

**Note:** This module has been split into separate checkers for granular control:
- [SpellingCheck](../_SpellingCheck/SpellingCheck.md) - Spelling validation only
- [GrammarCheck](../_GrammarCheck/GrammarCheck.md) - Grammar validation only

Both run as part of [PostCreationCheckManager](../_PostCreationCheckManager/PostCreationCheckManager.md).

---

## Overview

SpellGrammarCheck validates spelling and grammar in generated content. Uses Python-based tools (LanguageTool, pyspellchecker, or similar) for detection and can auto-fix common issues.

~~Runs as part of PostCreationCheckManager (in parallel with other checkers).~~

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Capabilities

| Capability | Auto-Fix | Notes |
|------------|----------|-------|
| Spelling errors | Yes | Dictionary-based correction |
| Grammar issues | Some | Rule-based fixes only |
| Punctuation | Yes | Common patterns |
| Capitalization | Yes | Sentence starts, proper nouns |
| Word repetition | No | Flag only |
| Passive voice | No | Flag only (style choice) |

### Tech Stack Options

| Tool | Pros | Cons |
|------|------|------|
| LanguageTool | Comprehensive, multi-language | Java dependency |
| pyspellchecker | Pure Python, fast | Spelling only |
| textblob | Easy API | Limited grammar |
| Grammarly API | Best quality | Paid, external |

### Output

```javascript
{
  passed: boolean,
  score: number,              // 0-100 (100 = no issues)
  issueCount: number,
  issues: [
    {
      type: "spelling" | "grammar" | "punctuation",
      text: string,           // The problematic text
      suggestion: string,     // Suggested fix
      position: { start, end },
      autoFixable: boolean
    }
  ],
  autoFixed?: {
    content: string,          // Content with auto-fixes applied
    fixCount: number
  }
}
```

### Thresholds

| Metric | Threshold | Notes |
|--------|-----------|-------|
| Score to pass | ≥85 | Allows minor issues |
| Auto-fix trigger | Score < 85 | Apply fixes if below |
| Recheck after fix | Required | Verify fixes worked |

### Brand Dictionary

Support brand-specific terms that shouldn't be flagged:
- Brand name variations
- Product names
- Industry jargon
- Proprietary terms

### Dependencies

| Depends On | For |
|------------|-----|
| Python runtime | Spell/grammar tools |
| Brand dictionary | Custom terms |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| PostCreationCheckManager | Language quality |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
