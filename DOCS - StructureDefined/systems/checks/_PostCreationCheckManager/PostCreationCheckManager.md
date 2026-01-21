# PostCreationCheckManager Module

> **Purpose:** Orchestrate post-creation quality checks, aggregate scores, decide actions
> **Type:** Quality Assurance Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

PostCreationCheckManager coordinates multiple quality checkers after content generation. It runs checks in parallel, aggregates scores, and decides whether to accept, flag, auto-fix, or regenerate content.

```
┌─────────────────────────────────────────────────────────────────┐
│                    POST-CREATION CHECK FLOW                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  TemplateEngine.renderSection()                                 │
│           │                                                      │
│           ↓                                                      │
│  PostCreationCheckManager.check(content, context, scope)        │
│           │                                                      │
│           ├──→ BrandCheck            ──→ brand score            │
│           ├──→ ValuesCheck           ──→ values score           │
│           ├──→ ContentAlignmentCheck ──→ content score          │
│           ├──→ TransformationCheck   ──→ transformation score   │
│           ├──→ SpellingCheck         ──→ spelling score         │
│           ├──→ GrammarCheck          ──→ grammar score          │
│           ├──→ LayoutCheck           ──→ layout score           │
│           └──→ VisualCheck           ──→ visual score           │
│           │        (all run in parallel)                        │
│           ↓                                                      │
│  Aggregate scores + decide action                               │
│           │                                                      │
│           ├─── All pass ────────→ Accept                        │
│           ├─── Minor issues ────→ Accept + warnings             │
│           ├─── Fixable issues ──→ Auto-fix + recheck            │
│           └─── Major issues ────→ Regenerate or flag            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Checker Registry

| Checker | Category | Auto-Fix | Notes |
|---------|----------|----------|-------|
| BrandCheck | Brand | No | Voice, terminology, identity |
| ValuesCheck | Brand | No | Values, principles, ethics |
| ContentAlignmentCheck | Content | No | Audience, theme, tone, structure |
| TransformationCheck | Content | No | Belief journey, promises |
| SpellingCheck | Language | Yes | Python-based spelling |
| GrammarCheck | Language | Some | Python-based grammar |
| LayoutCheck | Visual | Some | Structure, formatting, spacing |
| VisualCheck | Visual | Some | Images, colors, graphics |

### Validation Scopes

| Scope | Checkers Run | Use Case |
|-------|--------------|----------|
| `quick` | SpellingCheck, GrammarCheck | Drafts, rapid iteration |
| `language` | SpellingCheck, GrammarCheck | Language review only |
| `brand` | BrandCheck, ValuesCheck | Brand compliance only |
| `content` | ContentAlignmentCheck, TransformationCheck | Content quality only |
| `visual` | LayoutCheck, VisualCheck | Visual review only |
| `standard` | All except Visual | Normal generation |
| `full` | All checkers | Final output, premium tier |
| `custom` | Specified list | Specific needs |

### Category Weights

```javascript
const categoryWeights = {
  brand: 0.20,        // BrandCheck + ValuesCheck
  content: 0.35,      // ContentAlignmentCheck + TransformationCheck
  language: 0.25,     // SpellingCheck + GrammarCheck
  visual: 0.20        // LayoutCheck + VisualCheck
};
```

### Decision Matrix

| Scenario | Action |
|----------|--------|
| All scores ≥ 80 | Accept |
| Any score 60-79, none < 60 | Accept + warnings |
| Language < 80, auto-fixable | Auto-fix + recheck |
| Layout minor issues | Auto-fix + recheck |
| Brand/Content < 60 | Regenerate with feedback |
| Multiple scores < 60 | Flag for human review |
| Any critical failure | Block + flag |

> **See Also:** Detailed thresholds, weights, and critical failure rules in [_constants.md](../../reference/_constants.md#postcreationcheckmanager-thresholds)

### Functions (Planned)

| Function | Input | Output |
|----------|-------|--------|
| `check(content, context, scope)` | content, context, scope | CheckResult |
| `runCheckers(checkers, content, context)` | checkers[], content, context | CheckerResults[] |
| `aggregate(results, strategy)` | results[], strategy | AggregatedScore |
| `decide(aggregated, thresholds)` | score, thresholds | Decision |
| `autoFix(content, fixableIssues)` | content, issues | FixedContent |
| `recheck(content, failedCheckers)` | content, checkers | RecheckResult |

### Output Type

```javascript
{
  passed: boolean,
  overallScore: number,           // 0-100 weighted aggregate

  categoryScores: {
    brand: number,
    content: number,
    language: number,
    visual: number
  },

  checkerResults: {
    brandCheck: CheckerResult,
    valuesCheck: CheckerResult,
    contentAlignmentCheck: CheckerResult,
    transformationCheck: CheckerResult,
    spellingCheck: CheckerResult,
    grammarCheck: CheckerResult,
    layoutCheck: CheckerResult,
    visualCheck: CheckerResult
  },

  decision: {
    action: "accept" | "accept_with_warnings" | "auto_fix" | "regenerate" | "flag",
    reasons: string[],
    autoFixApplied?: boolean,
    fixedContent?: string
  },

  issues: Issue[],                // Aggregated from all checkers
  warnings: string[]
}
```

### Dependencies

| Depends On | For |
|------------|-----|
| BrandCheck | Brand voice validation |
| ValuesCheck | Values alignment |
| ContentAlignmentCheck | Content alignment scoring |
| TransformationCheck | Transformation validation |
| SpellingCheck | Spelling validation |
| GrammarCheck | Grammar validation |
| LayoutCheck | Layout validation |
| VisualCheck | Visual validation |

**Depended On By:**

| Module | Purpose |
|--------|---------|
| TemplateEngine | Post-render validation |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
