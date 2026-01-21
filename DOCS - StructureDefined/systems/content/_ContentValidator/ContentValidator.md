# ContentValidator Module

> **Purpose:** Validate generated content against transformation framework
> **Type:** Quality Assurance Module
> **Status:** SUPERSEDED by TransformationCheck
> **Version:** 0.0.0

**Note:** This module has been superseded by [TransformationCheck](../_TransformationCheck/TransformationCheck.md) which runs as part of [PostCreationCheckManager](../_PostCreationCheckManager/PostCreationCheckManager.md). Keeping this file for reference during transition.

---

## Overview

ContentValidator checks that AI-generated content properly reflects the transformation framework, brand perspective, and promised outcomes. Runs post-generation to ensure quality and alignment.

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Dev Team Gap Analysis (2026-01-20)
- **Transformation framework validation**:
  - Check beliefShifts are facilitated in content
  - Verify primaryOutcome is addressed
  - Ensure audienceTransformation promises are delivered
- **Brand perspective enforcement**:
  - Check signature phrases appear (from natashaPerspective.signaturePhrases)
  - Verify "whatImRejecting" language is avoided
  - Confirm brand voice alignment
- **Promised outcomes validation**:
  - Verify section.promisesOutcome[] are delivered
  - Check theyGain/theyExperience/theyWalkAwayWith alignment
- **Quality scoring**:
  - Coherence score (0-1)
  - Brand alignment score (0-1)
  - Audience fit score (0-1)
  - Template compliance (pass/fail)

### Validation Workflow
1. Receive: filled content + theme + outline
2. Extract: transformation framework from theme
3. Check: signature phrases present
4. Check: rejected language absent
5. Check: belief shifts facilitated
6. Score: overall quality
7. Return: { valid: boolean, score: number, issues: string[] }

### Integration Points
- Called by: TemplateEngine (post-fill validation)
- Reads: Theme.transformationFramework
- Reads: Outline.sections[].promisesOutcome
- Reports to: QualityTracker (if exists)

### Relationship to ContentAlignment

**ContentAlignment** (separate module) handles general alignment grading:
- Brand voice/values alignment
- Audience language/level match
- Theme truth presence
- Tone consistency
- Framework element coverage
- Structure compliance

**ContentValidator** handles transformation-specific validation:
- Belief shifts facilitated
- Brand perspective enforcement (signature phrases, rejected language)
- Promised outcomes delivered
- Transformation promises kept

**Decision needed:** Merge into one module or keep separate with ContentValidator calling ContentAlignment for general scoring.

### Thresholds (from _constants.md)
| Metric | Threshold | Action if Below |
|--------|-----------|-----------------|
| Coherence | 0.7 | Flag for review |
| Brand alignment | 0.8 | Regenerate with more context |
| Audience fit | 0.75 | Adjust language patterns |

---

*Spec version: 0.0.0*
*Created: 2026-01-20*
*Status: Placeholder*
