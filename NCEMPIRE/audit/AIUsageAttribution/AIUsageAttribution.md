# AIUsageAttribution Module

> **Purpose:** Attribute AI usage to content and decisions
> **Type:** Audit Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

AIUsageAttribution tracks which AI models were used for which content and decisions. Provides transparency about AI involvement for compliance and disclosure requirements.

---

## Notes

### Role
- Track AI model usage per content piece
- Attribute AI involvement to decisions
- Support AI disclosure requirements

### Owns
- AI usage records
- Model attribution
- Usage reporting

### Attribution Data
- Content ID → Models used
- Decision ID → AI involvement level
- Token usage per content piece
- Cost attribution per brand/content

### Dependencies (Systems)
- **AICaller:** Provides call records.
- **AIVizCaller:** Provides image generation records.
- **ContentMetadataManager:** Links to content.

### Feeds Into
- **DecisionTimeline:** AI context for audit.
- **CostTracker:** AI cost attribution.
- **Compliance reporting:** AI Act disclosure.

---

*Spec version: 0.0.0*
*Created: 2026-01-21*
*Status: Placeholder*
