# {{Component Name}} â€” Configuration

## Overview

| Category | Count | Description |
|----------|-------|-------------|
| Core | {{n}} | Essential settings |
| Performance | {{n}} | Tuning parameters |
| Feature Flags | {{n}} | Enable/disable features |
| Limits | {{n}} | Boundaries and constraints |

---

## Configuration Structure

```typescript
interface {{ComponentName}}Config {
  // Core settings
  core: {
    enabled: boolean;
    version: string;
  };
  
  // Performance tuning
  performance: {
    cacheEnabled: boolean;
    cacheTTL: number;
    maxConcurrency: number;
  };
  
  // Feature flags
  features: {
    featureA: boolean;
    featureB: boolean;
  };
  
  // Limits
  limits: {
    maxItems: number;
    maxBatchSize: number;
    requestTimeout: number;
  };
}
```

---

## Configuration Options

### Core Settings

#### `core.enabled`

| Attribute | Value |
|-----------|-------|
| Type | boolean |
| Default | `true` |
| Required | No |
| Environment Variable | `{{PREFIX}}_ENABLED` |

**Description:** Enable or disable the component entirely.

**Values:**
- `true` â€” Component is active
- `false` â€” Component returns `NOT_ENABLED` error for all calls

---

#### `core.version`

| Attribute | Value |
|-----------|-------|
| Type | string |
| Default | `"1.0.0"` |
| Required | No |
| Environment Variable | N/A (read-only) |

**Description:** Current component version. Read-only.

---

### Performance Settings

#### `performance.cacheEnabled`

| Attribute | Value |
|-----------|-------|
| Type | boolean |
| Default | `true` |
| Required | No |
| Environment Variable | `{{PREFIX}}_CACHE_ENABLED` |

**Description:** Enable in-memory caching.

**Impact:**
- `true` â€” Faster reads, uses memory
- `false` â€” Always reads from storage

---

#### `performance.cacheTTL`

| Attribute | Value |
|-----------|-------|
| Type | number (seconds) |
| Default | `300` (5 minutes) |
| Required | No |
| Range | 0-3600 |
| Environment Variable | `{{PREFIX}}_CACHE_TTL` |

**Description:** Cache time-to-live in seconds.

**Recommendations:**
- Frequently changing data: 60-120
- Stable data: 300-900
- Static data: 1800-3600

---

#### `performance.maxConcurrency`

| Attribute | Value |
|-----------|-------|
| Type | number |
| Default | `10` |
| Required | No |
| Range | 1-100 |
| Environment Variable | `{{PREFIX}}_MAX_CONCURRENCY` |

**Description:** Maximum concurrent operations.

**Impact:**
- Higher = more throughput, more resource usage
- Lower = less throughput, more stable

---

### Feature Flags

#### `features.{{featureName}}`

| Attribute | Value |
|-----------|-------|
| Type | boolean |
| Default | `false` |
| Required | No |
| Environment Variable | `{{PREFIX}}_FEATURE_{{NAME}}` |

**Description:** Enable {{feature description}}.

**When to Enable:**
- {{condition when this should be enabled}}

**Dependencies:**
- Requires: {{other features or config}}

---

### Limits

#### `limits.maxItems`

| Attribute | Value |
|-----------|-------|
| Type | number |
| Default | `10000` |
| Required | No |
| Range | 100-1000000 |
| Environment Variable | `{{PREFIX}}_MAX_ITEMS` |

**Description:** Maximum items this component can manage.

**Impact:**
- Exceeding limit returns `LIMIT_EXCEEDED` error
- Memory usage scales with this value

---

#### `limits.maxBatchSize`

| Attribute | Value |
|-----------|-------|
| Type | number |
| Default | `100` |
| Required | No |
| Range | 1-1000 |
| Environment Variable | `{{PREFIX}}_MAX_BATCH_SIZE` |

**Description:** Maximum items per batch operation.

---

#### `limits.requestTimeout`

| Attribute | Value |
|-----------|-------|
| Type | number (milliseconds) |
| Default | `30000` (30 seconds) |
| Required | No |
| Range | 1000-300000 |
| Environment Variable | `{{PREFIX}}_REQUEST_TIMEOUT` |

**Description:** Maximum time for a single request.

---

## Default Configuration

```json
{
  "core": {
    "enabled": true,
    "version": "1.0.0"
  },
  "performance": {
    "cacheEnabled": true,
    "cacheTTL": 300,
    "maxConcurrency": 10
  },
  "features": {
    "featureA": false,
    "featureB": false
  },
  "limits": {
    "maxItems": 10000,
    "maxBatchSize": 100,
    "requestTimeout": 30000
  }
}
```

---

## Environment Variables

| Variable | Config Path | Type | Default |
|----------|-------------|------|---------|
| `{{PREFIX}}_ENABLED` | `core.enabled` | boolean | `true` |
| `{{PREFIX}}_CACHE_ENABLED` | `performance.cacheEnabled` | boolean | `true` |
| `{{PREFIX}}_CACHE_TTL` | `performance.cacheTTL` | number | `300` |
| `{{PREFIX}}_MAX_CONCURRENCY` | `performance.maxConcurrency` | number | `10` |
| `{{PREFIX}}_MAX_ITEMS` | `limits.maxItems` | number | `10000` |
| `{{PREFIX}}_MAX_BATCH_SIZE` | `limits.maxBatchSize` | number | `100` |
| `{{PREFIX}}_REQUEST_TIMEOUT` | `limits.requestTimeout` | number | `30000` |

**Priority:** Environment variables override config file values.

---

## Configuration Loading

```
1. Load default values
2. Load from config file (if exists)
3. Override with environment variables
4. Validate all values
5. Apply configuration
```

**Validation Errors:**
- Out of range values â†’ `CONFIG_INVALID_VALUE`
- Invalid types â†’ `CONFIG_INVALID_TYPE`
- Missing required â†’ `CONFIG_MISSING_REQUIRED`

---

## Runtime Configuration Changes

| Setting | Can Change at Runtime | Requires Restart |
|---------|----------------------|------------------|
| `core.enabled` | Yes | No |
| `performance.cacheEnabled` | Yes | No |
| `performance.cacheTTL` | Yes | No |
| `performance.maxConcurrency` | No | Yes |
| `limits.*` | No | Yes |

**Changing at Runtime:**
```typescript
component.updateConfig({
  performance: { cacheTTL: 600 }
});
```

---

## Configuration Profiles

### Development

```json
{
  "performance": {
    "cacheEnabled": false,
    "maxConcurrency": 2
  },
  "limits": {
    "maxItems": 1000
  }
}
```

### Production

```json
{
  "performance": {
    "cacheEnabled": true,
    "cacheTTL": 600,
    "maxConcurrency": 20
  },
  "limits": {
    "maxItems": 100000,
    "maxBatchSize": 500
  }
}
```

### Testing

```json
{
  "performance": {
    "cacheEnabled": false,
    "maxConcurrency": 1
  },
  "limits": {
    "maxItems": 100,
    "requestTimeout": 5000
  }
}
```

---

## Notes

- All durations are in milliseconds unless noted
- Environment variables use uppercase with underscores
- Config file path: `/config/{{component}}.json`
- See BEHAVIOUR.md for how config affects behaviour

---
Source: PRE-SPEC-NOTES.md section 8
Phase: 21 (Internal Component Specs)
---
