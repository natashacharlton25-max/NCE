# PostCreationQualityDecider Module

> **Purpose:** Decision engine for quality checks
> **Type:** Checks Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

PostCreationQualityDecider takes outputs from PostCreationCheckManager and decides what to do: pass, retry, manual review, or salvage.

---

## Notes

### Role
- Evaluate check results against quality policies
- Determine retry vs salvage vs manual review
- Trigger regeneration for failed sections
- Track decision history

### Owns
- Quality decisions (pass/retry/manual/salvage)
- Policy evaluation
- Retry triggering

### Why Critical
- PostCreationCheckManager runs checks but doesn't decide outcomes
- Blocks automated content publishing workflow

### Depends On
- PostCreationCheckManager (consumes check results)
- PolicyEngine (defines quality thresholds)
- TemplateEngine (regenerates failed sections)
- FailureHandler (manages retry failures)

---

*Spec version: 0.0.0*
*Created: 2026-01-21*
*Status: Placeholder*
