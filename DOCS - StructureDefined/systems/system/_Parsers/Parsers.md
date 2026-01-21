# Parsers Module

> **Purpose:** Parse AI responses and extract structured content
> **Type:** Content Quality Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

Parsers extracts structured fields from AI responses. Called by TemplateEngine after AICaller returns content.

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

- Called by TemplateEngine.fillSections()

### Dev Team Gap Analysis (2026-01-20)
- **Schema validation**: Use Zod schemas to validate AI output structure
- **Field extraction**: Extract title, body, cta from AI responses
- **Normalization**: Trim whitespace, fix common formatting issues
- **Error recovery**: Handle partial/malformed responses gracefully
- **Partial parse handling**: Return what parsed + flag incomplete sections
- **Type safety**: Output typed objects matching library schemas

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Status: Placeholder*
