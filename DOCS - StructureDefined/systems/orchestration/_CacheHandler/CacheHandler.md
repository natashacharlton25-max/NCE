# CacheHandler Module

> **Purpose:** Manage caching of AI responses and computed data
> **Type:** Data Management Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

CacheHandler stores and retrieves cached data to reduce AI calls and improve performance. Manages cache.db with configurable TTLs per data type.

---

## Notes

*Integration notes, cross-system dependencies, and future considerations:*

### Dev Team Gap Analysis (2026-01-20)
- **Cache key generation**: Consistent keys from input params (brandId + themeId + templateId + hash)
- **TTL management**: Per-data-type TTLs (themes: 48h, outlines: 24h, AI responses: 1h)
- **Invalidation strategies**:
  - Time-based expiry
  - Event-based (library update → invalidate related cache)
  - Manual flush
- **Memory vs disk**: Hot data in memory, cold data in cache.db
- **Section-level caching**: Cache individual sections for partial regeneration
- **Cache warming**: Pre-populate common queries on startup

---

*Spec version: 0.0.0*
*Created: 2026-01-19*
*Status: Placeholder*
