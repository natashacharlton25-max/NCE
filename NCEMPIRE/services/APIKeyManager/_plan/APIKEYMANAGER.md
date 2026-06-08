# APIKeyManager Module

> **Purpose:** Secure access to API keys and credentials
> **Type:** System Service
> **Status:** Planning

---

## Overview

APIKeyManager provides secure access to API keys. Keys are stored in environment variables or secure storage, never in repos. Modules request keys by reference name, never handling raw key values directly in configs.

```
Model config has: apiKeyRef: "OPENAI_API_KEY"
AICaller: APIKeyManager.get("OPENAI_API_KEY") → actual key value
```

**Security:**
- Keys in .env or secure storage
- Never logged
- Never in repo files
- Access by reference only

---

## Roles & Rules

### APIKeyManager DOES:
- Retrieve API keys from environment/.env
- Check if keys exist
- Validate key format
- Support key rotation
- List available key references

### APIKeyManager does NOT:
- Store keys in repos (never!)
- Log key values (never logged)
- Make API calls (AICaller does that)
- Decide which key to use (ModelManager specifies)

### Boundaries:
- Never logs API key values
- Never writes keys to files
- Never exposes keys in error messages
- Read-only access to secure storage

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `get(keyRef)` | Get API key by reference | keyRef | keyValue |
| `exists(keyRef)` | Check if key exists | keyRef | boolean |
| `validate(keyRef)` | Validate key format/status | keyRef | validationResult |
| `rotate(keyRef, newValue)` | Rotate to new key | keyRef, newValue | rotated |
| `listRefs()` | List available key references | - | refList |

---

## Detailed Function Specs

### get(keyRef)

Gets API key value by reference name.

```javascript
// Input
"OPENAI_API_KEY"

// Process
1. Check .env for key
2. If not in .env → check secure storage
3. Return value

// Output
"sk-..." // The actual API key

// Error if not found
{
  error: true,
  code: "AUTH_KEY_NOT_FOUND",
  message: "API key 'OPENAI_API_KEY' not found",
  recoverable: false
}
```

### exists(keyRef)

Checks if key exists without returning value.

```javascript
// Input
"OPENAI_API_KEY"

// Output
true  // or false
```

### validate(keyRef)

Validates key format and optionally tests it.

```javascript
// Input
"OPENAI_API_KEY"

// Process
1. get(keyRef)
2. Check format (prefix, length)
3. Optionally make test request

// Output (valid)
{
  valid: true,
  keyRef: "OPENAI_API_KEY",
  format: "valid",
  provider: "openai"
}

// Output (invalid)
{
  valid: false,
  keyRef: "OPENAI_API_KEY",
  reason: "Key format invalid or expired"
}
```

---

## Key Reference Naming

| Provider | Reference Name |
|----------|----------------|
| OpenAI | `OPENAI_API_KEY` |
| Anthropic | `ANTHROPIC_API_KEY` |
| Google | `GOOGLE_API_KEY` |
| Canva | `CANVA_API_KEY` |
| Companies House | `COMPANIES_HOUSE_API_KEY` |

---

## Storage Locations

```javascript
// Priority order for key lookup:
1. Environment variables (.env)
2. Secure storage (if configured)
3. Error if not found

// .env example
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
CANVA_API_KEY=...
```

---

## Config

### module.config.json

```json
{
  "module": "APIKeyManager",
  "version": "1.0.0",
  "description": "Secure access to API keys",

  "functions": [
    {
      "name": "get",
      "description": "Get API key by reference",
      "input": ["keyRef"],
      "output": "keyValue"
    },
    {
      "name": "exists",
      "description": "Check if key exists",
      "input": ["keyRef"],
      "output": "boolean"
    },
    {
      "name": "validate",
      "description": "Validate key format/status",
      "input": ["keyRef"],
      "output": "validationResult"
    },
    {
      "name": "rotate",
      "description": "Rotate to new key",
      "input": ["keyRef", "newValue"],
      "output": "rotated"
    },
    {
      "name": "listRefs",
      "description": "List available key references",
      "input": [],
      "output": "refList"
    }
  ],

  "config": {
    "envFile": ".env",
    "validateOnStartup": true,
    "neverLog": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| (environment) | Reading .env |

---

## Used By

| Module | How |
|--------|-----|
| AICaller | Getting API keys for requests |
| Integration modules | Getting service credentials |

---

*Last updated: 2026-01-17*
