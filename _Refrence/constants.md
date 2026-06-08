# System Constants Reference

> **Purpose:** Central reference for system-wide constants
> **Type:** Reference Document
> **Status:** Draft
> **Version:** 0.1.0

---

## Overview

Defines system-wide constants including token limits, TTLs, status enums, and quality thresholds. Single source of truth for magic numbers.

---

## Token Limits

| Model | Max Input | Max Output | Notes |
|-------|-----------|------------|-------|
| Claude 3.5 Sonnet | 200K | 8K | Primary model |
| Claude 3 Opus | 200K | 4K | Complex generation |
| GPT-4 Turbo | 128K | 4K | Fallback |

### Token Budgets by Context

| Context Type | Budget | Notes |
|--------------|--------|-------|
| Brand context | 2000 | Voice, values, name |
| Audience context | 1500 | Psychographics, language |
| Theme context | 3000 | Full theme with outcomes |
| Framework context | 1500 | Stages, prompts, structure |
| Template context | 1000 | Structure and placeholders |
| Section context | 500 | Per-section allocation |
| Paragraph context | 500 | Structure guidance |

### PromptCondenser Thresholds

| Threshold | Value | Notes |
|-----------|-------|-------|
| warnMarginPercent | 15% | Warn if margin < 15% of available |
| minRetainPercent | 20% | Never condense below 20% of original |
| summarizeMinTokens | 100 | Only summarize content > 100 tokens |
| truncateMinTokens | 50 | Only truncate content > 50 tokens |

---

## Cache TTLs

| Data Type | TTL | Notes |
|-----------|-----|-------|
| Theme generation | 48 hours | Themes rarely change |
| Outline generation | 24 hours | Outlines tied to theme |
| AI responses (generic) | 1 hour | Short-lived |
| Library queries | 15 minutes | Hot data |
| Reference lookups | 1 hour | Stable data |

---

## Status Enums

### Entry Status
```
active    - Currently available for use
archived  - Soft deleted, preserved for history
draft     - In staging, not yet promoted
deprecated - Still works but shouldn't be used for new content
```

### Reference Status (`ref_status`)
```
valid     - All references intact
stale     - Referenced entity was archived
broken    - Referenced entity deleted (shouldn't happen)
pending   - References not yet validated
```

### Generation Quality Tiers
```
Tier 1 - Full context: Brand + audience + theme + outcomes
Tier 2 - Partial context: Brand + theme (audience inferred)
Tier 3 - Minimal context: Brand only
Tier 4 - Legacy: No transformation context
```

---

## Quality Thresholds

| Metric | Threshold | Action if Below |
|--------|-----------|-----------------|
| Coherence score | 0.7 | Flag for review |
| Brand alignment | 0.8 | Regenerate with more context |
| Audience fit | 0.75 | Adjust language patterns |
| Template compliance | 1.0 | Must be 100% or fail |

### ContentAlignment Thresholds

| Category | Min Score | Notes |
|----------|-----------|-------|
| Overall | 70 | Minimum to pass |
| Brand | 75 | Voice/values alignment |
| Audience | 70 | Language/level match |
| Theme | 75 | Truth/message presence |
| Tone | 65 | Tone consistency |
| Framework | 70 | Stage purpose met |
| Structure | 80 | Section type compliance |

### ContentAlignment Category Weights

| Category | Weight | Notes |
|----------|--------|-------|
| Brand | 0.20 | 20% of total score |
| Audience | 0.15 | 15% of total score |
| Theme | 0.20 | 20% of total score |
| Tone | 0.10 | 10% of total score |
| Framework | 0.15 | 15% of total score |
| Structure | 0.20 | 20% of total score |

### ContentAlignment Grade Boundaries

| Grade | Min Score | Quality Tier |
|-------|-----------|--------------|
| A | 90 | premium |
| B | 80 | standard |
| C | 70 | acceptable |
| D | 60 | revision_needed |
| F | 0 | reject |

---

## PostCreationCheckManager Thresholds

### Checker Pass Thresholds

| Checker | Min Score | Critical Threshold | Notes |
|---------|-----------|-------------------|-------|
| BrandCheck | 75 | 50 | Below critical = reject |
| ValuesCheck | 70 | 45 | Ethics violations = immediate reject |
| ContentAlignmentCheck | 70 | 50 | Audience/theme alignment |
| TransformationCheck | 70 | 50 | Belief journey delivery |
| SpellingCheck | 95 | 85 | High bar for language |
| GrammarCheck | 90 | 80 | High bar for language |
| LayoutCheck | 80 | 60 | Structure compliance |
| VisualCheck | 75 | 50 | Image/color validation |

### Checker Weights (for aggregate score)

| Checker | Weight | Notes |
|---------|--------|-------|
| BrandCheck | 0.15 | Brand voice critical |
| ValuesCheck | 0.10 | Ethics baseline |
| ContentAlignmentCheck | 0.20 | Core alignment |
| TransformationCheck | 0.15 | Outcome delivery |
| SpellingCheck | 0.10 | Language quality |
| GrammarCheck | 0.10 | Language quality |
| LayoutCheck | 0.10 | Structure |
| VisualCheck | 0.10 | Visual elements |

### Validation Scopes

| Scope | Checkers Included | Use Case |
|-------|-------------------|----------|
| quick | Spelling, Grammar | Draft review |
| language | Spelling, Grammar, Layout | Language-only pass |
| brand | Brand, Values | Brand compliance check |
| content | ContentAlignment, Transformation | Content quality |
| visual | Layout, Visual | Visual review |
| standard | Brand, ContentAlignment, Spelling, Grammar | Normal validation |
| full | All 8 checkers | Final quality gate |

### Aggregate Decision Thresholds

| Aggregate Score | Decision | Action |
|-----------------|----------|--------|
| 90+ | pass | Accept content |
| 70-89 | pass_with_warnings | Accept, log issues |
| 50-69 | review | Flag for human review |
| < 50 | fail | Reject, regenerate |

### Critical Failure Rules

| Condition | Action |
|-----------|--------|
| Any checker below critical threshold | Immediate fail |
| ValuesCheck finds ethics violation | Immediate reject |
| SpellingCheck < 85 | Flag for manual review |
| 3+ checkers below pass threshold | Aggregate fail |

---

## Rate Limits

| Provider | Requests/min | Tokens/min | Notes |
|----------|--------------|------------|-------|
| Anthropic | 60 | 100K | Default tier |
| OpenAI | 60 | 90K | Default tier |

---

## Field Naming Convention

| Context | Convention | Example |
|---------|------------|---------|
| JSON fields | camelCase | `targetOutcomes`, `beliefShifts` |
| SQL columns | snake_case | `target_outcomes`, `belief_shifts` |
| IDs | kebab-case with prefix | `theme_confident-clarity` |

---

## File Size Limits

> Intentionally different between read and write operations.

| Module | Warn At | Reject At | Bypassable | Rationale |
|--------|---------|-----------|------------|-----------|
| Writer | 5MB | 50MB | Yes | Writes should be smaller; large writes suggest misuse |
| FileManager | 10MB | 100MB | Yes | Reads may need larger files for migrations/imports |

**Note:** These limits are intentionally asymmetric. Generated content should rarely exceed 5MB, while reading legacy data or performing migrations may require larger file access.

---

## Notes

*Additional constants to document as system develops:*

### Dev Team Gap Analysis (2026-01-20)
- Add retry limits per error type
- Document backoff parameters (base, max, jitter)
- Define batch size limits for bulk operations
- Document pagination defaults

---

*Created: 2026-01-20*
*Status: Draft*
