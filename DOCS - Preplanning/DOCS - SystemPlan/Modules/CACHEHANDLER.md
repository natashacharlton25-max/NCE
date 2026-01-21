# CacheHandler Module

> **Purpose:** System-wide caching to reduce redundant operations
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

CacheHandler provides **intelligent caching** across the system. It caches frequently accessed data, API responses, and computed results to reduce latency and costs.

```
Request → CacheHandler.get() → Cache hit? Return cached : Fetch fresh → Cache → Return
```

**CacheHandler reduces:**
- Redundant file reads
- Duplicate API calls
- Repeated computations
- Database queries

---

## Roles & Rules

### CacheHandler DOES:
- Store and retrieve cached data
- Manage cache expiration (TTL)
- Invalidate stale data
- Track cache hit/miss rates
- Provide cache statistics
- Support multiple cache tiers

### CacheHandler does NOT:
- Fetch data itself (modules do that)
- Make business decisions about caching
- Persist permanent data (repos do that)
- Handle authentication (AuthHandler does that)

### Boundaries:
- Caching only - no data modification
- Respects TTL strictly
- Cannot cache sensitive data (API keys, etc.)
- Memory-based with optional persistence

---

## Cache Types

| Type | TTL | Use Case | Example |
|------|-----|----------|---------|
| `hot` | 60s | Frequently accessed | Brand data during batch |
| `warm` | 5m | Moderately accessed | Template content |
| `cold` | 1h | Rarely changes | Config files |
| `session` | Session | Session-scoped | User preferences |
| `persistent` | Days | Expensive to compute | AI response dedup |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `get(key)` | Get cached value | key | value or null |
| `set(key, value, options)` | Set cache value | key, value, options | stored |
| `delete(key)` | Delete cached value | key | deleted |
| `has(key)` | Check if key exists | key | exists |
| `invalidate(pattern)` | Invalidate by pattern | pattern | count |
| `getStats()` | Get cache statistics | - | stats |
| `clear(type)` | Clear cache by type | type | cleared |

---

## Detailed Function Specs

### get(key)

Get cached value.

```javascript
// Input
{
  key: "brand:acme:voice"
}

// Output - Cache hit
{
  hit: true,
  value: {
    tone: "professional",
    style: "conversational"
  },
  cachedAt: "2026-01-17T14:25:00Z",
  expiresAt: "2026-01-17T14:30:00Z",
  remaining: 45000  // ms until expiry
}

// Output - Cache miss
{
  hit: false,
  value: null,
  reason: "not_found"  // or "expired"
}
```

### set(key, value, options)

Set cache value.

```javascript
// Input
{
  key: "brand:acme:voice",
  value: {
    tone: "professional",
    style: "conversational"
  },
  options: {
    type: "warm",       // Cache tier
    ttl: 300000,        // 5 minutes (overrides type default)
    tags: ["brand", "acme"]  // For bulk invalidation
  }
}

// Output
{
  stored: true,
  key: "brand:acme:voice",
  expiresAt: "2026-01-17T14:35:00Z"
}
```

### invalidate(pattern)

Invalidate by pattern or tags.

```javascript
// By key pattern
{
  pattern: "brand:acme:*"
}

// Output
{
  invalidated: true,
  count: 5,
  keys: [
    "brand:acme:voice",
    "brand:acme:values",
    "brand:acme:mission",
    "brand:acme:typography",
    "brand:acme:colors"
  ]
}

// By tag
{
  tags: ["acme"]
}

// Output
{
  invalidated: true,
  count: 5,
  byTag: { "acme": 5 }
}
```

### getStats()

Get cache statistics.

```javascript
// Output
{
  stats: {
    totalEntries: 150,
    memoryUsed: "2.5MB",
    hitRate: 0.85,
    hits: 850,
    misses: 150
  },
  byType: {
    hot: { entries: 20, hitRate: 0.95 },
    warm: { entries: 80, hitRate: 0.82 },
    cold: { entries: 50, hitRate: 0.70 }
  },
  topKeys: [
    { key: "brand:acme:voice", hits: 45 },
    { key: "template:blog-post", hits: 38 }
  ]
}
```

---

## Cache Key Conventions

```
{entity}:{id}:{property}

Examples:
- brand:acme:voice
- brand:acme:typography
- template:blog-post:content
- model:gpt-4-turbo:pricing
- provider:openai:status
```

---

## Automatic Caching Integration

Modules can use CacheHandler transparently:

```javascript
// FileManager with caching
async function loadBrandVoice(brandId) {
  const cacheKey = `brand:${brandId}:voice`;

  // Check cache
  const cached = await CacheHandler.get(cacheKey);
  if (cached.hit) return cached.value;

  // Load from file
  const voice = await FileManager.read(`/repos/brands/${brandId}/voice.json`);

  // Cache for next time
  await CacheHandler.set(cacheKey, voice, { type: "warm", tags: ["brand", brandId] });

  return voice;
}
```

---

## Cache Invalidation Triggers

| Event | Invalidation |
|-------|--------------|
| Brand updated | `brand:{brandId}:*` |
| Template changed | `template:{templateId}:*` |
| Config reloaded | `config:*` |
| Model pricing update | `model:*:pricing` |

---

## Config

### module.config.json

```json
{
  "module": "CacheHandler",
  "version": "1.0.0",
  "description": "System-wide caching",
  "type": "system-service",

  "functions": [
    {
      "name": "get",
      "description": "Get cached value",
      "input": ["key"],
      "output": "value"
    },
    {
      "name": "set",
      "description": "Set cache value",
      "input": ["key", "value", "options"],
      "output": "stored"
    },
    {
      "name": "delete",
      "description": "Delete cached value",
      "input": ["key"],
      "output": "deleted"
    },
    {
      "name": "has",
      "description": "Check if key exists",
      "input": ["key"],
      "output": "exists"
    },
    {
      "name": "invalidate",
      "description": "Invalidate by pattern",
      "input": ["pattern"],
      "output": "count"
    },
    {
      "name": "getStats",
      "description": "Get cache statistics",
      "input": [],
      "output": "stats"
    },
    {
      "name": "clear",
      "description": "Clear cache by type",
      "input": ["type"],
      "output": "cleared"
    }
  ],

  "config": {
    "maxMemory": "50MB",
    "defaultTTL": 300000,
    "types": {
      "hot": { "ttl": 60000 },
      "warm": { "ttl": 300000 },
      "cold": { "ttl": 3600000 },
      "session": { "ttl": 86400000 }
    },
    "persistEnabled": false
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Logger | Logging cache events |

---

## Used By

| Component | How |
|-----------|-----|
| FileManager | Cache file reads |
| PromptBuilder | Cache templates |
| Librarian | Cache search results |
| All modules | Reduce redundant operations |

---

*Last updated: 2026-01-17*
