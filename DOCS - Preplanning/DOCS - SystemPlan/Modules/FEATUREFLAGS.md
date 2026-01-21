# FeatureFlags Module

> **Purpose:** Feature toggle management for gradual rollouts
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

FeatureFlags provides **feature toggle management** for controlled rollouts. It enables features gradually, supports A/B testing, and allows quick rollback of problematic features.

```
Check feature → FeatureFlags.isEnabled() → true/false → Code path selected
```

**FeatureFlags enables:**
- Gradual feature rollouts
- A/B testing
- Quick feature disable
- Per-brand feature control

---

## Roles & Rules

### FeatureFlags DOES:
- Store feature flag states
- Check if features are enabled
- Support percentage rollouts
- Enable per-brand overrides
- Track feature usage
- Support flag dependencies

### FeatureFlags does NOT:
- Implement features (modules do that)
- Make business decisions
- Persist user preferences (separate concern)
- Handle authentication (AuthHandler does that)

### Boundaries:
- Boolean decisions only
- Cannot execute code
- Real-time updates supported
- All checks logged

---

## Flag Types

| Type | Description | Example |
|------|-------------|---------|
| `boolean` | Simple on/off | New UI enabled |
| `percentage` | Gradual rollout | 50% see new feature |
| `allowlist` | Specific brands | Beta brands only |
| `experiment` | A/B testing | Show variant A or B |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `isEnabled(flag, context)` | Check if flag enabled | flag, context | enabled |
| `getFlags()` | Get all flags | - | flags |
| `setFlag(flag, config)` | Set flag config | flag, config | updated |
| `getVariant(flag, context)` | Get experiment variant | flag, context | variant |
| `getFlagUsage(flag)` | Get flag usage stats | flag | stats |

---

## Detailed Function Specs

### isEnabled(flag, context)

Check if a feature flag is enabled.

```javascript
// Input
{
  flag: "new-prompt-builder",
  context: {
    brandId: "acme",
    userId: "user_123"
  }
}

// Output
{
  enabled: true,
  flag: "new-prompt-builder",
  reason: "percentage_rollout",  // "global" | "percentage" | "allowlist" | "override"
  rolloutPercent: 50
}
```

### setFlag(flag, config)

Configure a feature flag.

```javascript
// Input - Boolean
{
  flag: "new-prompt-builder",
  config: {
    type: "boolean",
    enabled: true,
    description: "New prompt builder implementation"
  }
}

// Input - Percentage rollout
{
  flag: "ai-caching",
  config: {
    type: "percentage",
    percent: 25,
    description: "Cache AI responses for similar prompts"
  }
}

// Input - Allowlist
{
  flag: "beta-features",
  config: {
    type: "allowlist",
    brands: ["acme", "globex"],
    description: "Beta features for selected brands"
  }
}

// Output
{
  updated: true,
  flag: "new-prompt-builder",
  previousConfig: {...},
  newConfig: {...}
}
```

### getVariant(flag, context)

Get experiment variant for A/B testing.

```javascript
// Input
{
  flag: "checkout-flow-experiment",
  context: {
    userId: "user_123"
  }
}

// Output
{
  flag: "checkout-flow-experiment",
  variant: "B",  // Deterministic based on userId
  variants: ["A", "B"],
  weights: [50, 50]
}
```

### getFlags()

Get all feature flags.

```javascript
// Output
{
  flags: [
    {
      name: "new-prompt-builder",
      type: "percentage",
      enabled: true,
      percent: 50,
      description: "New prompt builder"
    },
    {
      name: "beta-features",
      type: "allowlist",
      enabled: true,
      brands: ["acme", "globex"],
      description: "Beta features"
    },
    {
      name: "old-export-format",
      type: "boolean",
      enabled: false,
      description: "Legacy export format"
    }
  ]
}
```

---

## Flag Definition

```json
// /repos/system/feature-flags.json
{
  "flags": {
    "new-prompt-builder": {
      "type": "percentage",
      "enabled": true,
      "percent": 50,
      "description": "New prompt builder implementation",
      "createdAt": "2026-01-10",
      "owner": "engineering"
    },
    "beta-features": {
      "type": "allowlist",
      "enabled": true,
      "brands": ["acme", "globex"],
      "description": "Beta features for selected brands"
    },
    "checkout-experiment": {
      "type": "experiment",
      "enabled": true,
      "variants": {
        "A": { "weight": 50, "description": "Original flow" },
        "B": { "weight": 50, "description": "New flow" }
      }
    }
  }
}
```

---

## Usage in Code

```javascript
// Simple check
if (await FeatureFlags.isEnabled("new-prompt-builder", { brandId })) {
  // Use new implementation
  await newPromptBuilder.compile(template);
} else {
  // Use old implementation
  await oldPromptBuilder.compile(template);
}

// Experiment
const variant = await FeatureFlags.getVariant("checkout-experiment", { userId });
if (variant.variant === "A") {
  showOriginalCheckout();
} else {
  showNewCheckout();
}
```

---

## Config

### module.config.json

```json
{
  "module": "FeatureFlags",
  "version": "1.0.0",
  "description": "Feature toggle management",
  "type": "system-service",

  "functions": [
    {
      "name": "isEnabled",
      "description": "Check if flag enabled",
      "input": ["flag", "context"],
      "output": "enabled"
    },
    {
      "name": "getFlags",
      "description": "Get all flags",
      "input": [],
      "output": "flags"
    },
    {
      "name": "setFlag",
      "description": "Set flag config",
      "input": ["flag", "config"],
      "output": "updated"
    },
    {
      "name": "getVariant",
      "description": "Get experiment variant",
      "input": ["flag", "context"],
      "output": "variant"
    },
    {
      "name": "getFlagUsage",
      "description": "Get flag usage stats",
      "input": ["flag"],
      "output": "stats"
    }
  ],

  "config": {
    "flagsPath": "/repos/system/feature-flags.json",
    "cacheFlags": true,
    "cacheTTL": 60000,
    "logChecks": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading flag config |
| Logger | Logging flag checks |
| CacheHandler | Caching flag state |

---

## Used By

| Component | How |
|-----------|-----|
| All modules | Feature checks |
| Experiments | A/B testing |

---

*Last updated: 2026-01-17*
