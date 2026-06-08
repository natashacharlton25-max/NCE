# Parsers Module

> **Purpose:** Parse and validate data formats (JSON, responses, schemas)
> **Type:** System Service
> **Status:** Planning

---

## Overview

Parsers handles all data parsing and format validation. When AI returns a response, Parsers extracts the data. When configs are loaded, Parsers validates the structure. Central place for all format handling.

```
Raw response → Parsers.parse() → Structured data
Data → Parsers.validate() → Valid or errors
```

---

## Roles & Rules

### Parsers DOES:
- Parse JSON with helpful error messages
- Extract JSON from mixed content (AI responses)
- Validate data against JSON schemas
- Sanitize input data
- Transform data structures

### Parsers does NOT:
- Store data (other modules do that)
- Make decisions based on content
- Modify source data (only returns parsed result)
- Handle files (works with data in memory)

### Boundaries:
- Pure data operations (no side effects)
- Cannot write files
- Cannot make external calls
- Cannot modify input (returns new data)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `parseJSON(text)` | Parse JSON with error handling | text | parsed |
| `parseResponse(response, format)` | Parse AI response to format | response, format | parsedData |
| `validateSchema(data, schema)` | Validate data against JSON schema | data, schema | validationResult |
| `extractJSON(text)` | Extract JSON from mixed content | text | extracted |
| `sanitize(data)` | Clean/sanitize input data | data | sanitized |
| `transform(data, mapping)` | Transform data structure | data, mapping | transformed |

---

## Detailed Function Specs

### parseJSON(text)

Parses JSON with helpful error messages.

```javascript
// Input
'{"name": "Test", "value": 42}'

// Output (success)
{
  success: true,
  data: { name: "Test", value: 42 }
}

// Output (error)
{
  success: false,
  error: {
    message: "Unexpected token at position 15",
    position: 15,
    context: '..."name": "Tes[HERE]t", "value"...'
  }
}
```

### parseResponse(response, format)

Parses AI response to expected format.

```javascript
// Input
{
  response: {
    choices: [{
      message: {
        content: '{"headline": "Welcome to Acme", "tagline": "Innovation starts here"}'
      }
    }]
  },
  format: "json"
}

// Process
1. Extract content from response structure
2. Parse based on format
3. Validate structure

// Output
{
  success: true,
  data: {
    headline: "Welcome to Acme",
    tagline: "Innovation starts here"
  }
}
```

### validateSchema(data, schema)

Validates data against JSON Schema.

```javascript
// Input
{
  data: { id: "test", name: "Test Tone" },
  schema: {
    type: "object",
    required: ["id", "name", "style"],
    properties: { ... }
  }
}

// Output (valid)
{
  valid: true,
  data: { ... }
}

// Output (invalid)
{
  valid: false,
  errors: [
    { path: "/style", message: "Required property missing" }
  ]
}
```

### extractJSON(text)

Extracts JSON from mixed content (useful for AI responses with explanation).

```javascript
// Input
"Here's the brand intro:\n```json\n{\"headline\": \"Welcome\"}\n```\nLet me know if you need changes."

// Process
1. Find JSON block markers (```, {})
2. Extract JSON portion
3. Parse

// Output
{
  success: true,
  data: { headline: "Welcome" },
  extractedFrom: { start: 28, end: 52 }
}
```

---

## Config

### module.config.json

```json
{
  "module": "Parsers",
  "version": "1.0.0",
  "description": "Parse and validate data formats",

  "functions": [
    {
      "name": "parseJSON",
      "description": "Parse JSON with error handling",
      "input": ["text"],
      "output": "parsed"
    },
    {
      "name": "parseResponse",
      "description": "Parse AI response to format",
      "input": ["response", "format"],
      "output": "parsedData"
    },
    {
      "name": "validateSchema",
      "description": "Validate data against JSON schema",
      "input": ["data", "schema"],
      "output": "validationResult"
    },
    {
      "name": "extractJSON",
      "description": "Extract JSON from mixed content",
      "input": ["text"],
      "output": "extracted"
    },
    {
      "name": "sanitize",
      "description": "Clean/sanitize input data",
      "input": ["data"],
      "output": "sanitized"
    },
    {
      "name": "transform",
      "description": "Transform data structure",
      "input": ["data", "mapping"],
      "output": "transformed"
    }
  ],

  "config": {
    "maxJSONSize": 1000000,
    "strictMode": false
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| (none) | Pure data operations |

---

## Used By

| Module | How |
|--------|-----|
| AICaller | Parsing API responses |
| FileManager | Validating loaded data |
| PromptBuilder | Validating templates |
| All modules | Data validation |

---

*Last updated: 2026-01-17*
