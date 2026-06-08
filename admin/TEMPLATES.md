# Templates Module

> **Purpose:** Manage, load, and process templates across the system
> **Type:** Service Module
> **Status:** Planning

---

## Overview

Templates module is the central template manager. It handles loading templates from repos, validating them, processing inheritance, and caching. All modules that use templates go through this module.

```
Request template → Templates.get() → Load → Validate → Process inheritance → Return
```

**Template types managed:**
- Prompt templates (for AI calls)
- Email templates
- Document templates
- Data scaffolding templates
- Export templates

---

## Roles & Rules

### Templates DOES:
- Load templates by type and ID
- Process template inheritance (extends)
- Resolve template includes (partials)
- Validate template structure
- Render templates with data
- Cache templates for performance

### Templates does NOT:
- Create new templates (Archivist does that)
- Store template content (FileManager does that)
- Build prompts (PromptBuilder uses templates)
- Send emails (Email module does that)

### Boundaries:
- Read-only access to template repos
- Cannot modify template files
- Cannot execute template logic (just variable replacement)
- Cannot access non-template repos

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `get(type, templateId)` | Get template by type and ID | type, templateId | template |
| `list(type, filter)` | List templates of type | type, filter | templates |
| `validate(template, type)` | Validate template structure | template, type | validationResult |
| `processInheritance(template)` | Resolve extends/includes | template | processedTemplate |
| `render(template, data)` | Render template with data | template, data | rendered |
| `getSchema(type)` | Get schema for template type | type | schema |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Templates.getProcessed` | Get fully processed template | get → processInheritance → validate |
| `Templates.renderWithValidation` | Render with data validation | get → validate → render |

---

## Detailed Function Specs

### get(type, templateId)

Gets template by type and ID.

```javascript
// Input
{
  type: "email",
  templateId: "brand-kit-delivery"
}

// Process
1. Determine repo: type → repo mapping
2. FileManager.get(repo, templateId)
3. Return template

// Output
{
  id: "brand-kit-delivery",
  name: "Brand Kit Delivery",
  type: "transactional",
  subject: "Your {{brandName}} Brand Kit is Ready!",
  html: { ... },
  requiredData: ["clientName", "brandName"],
  version: "1.0"
}
```

### list(type, filter)

Lists templates of a type with optional filter.

```javascript
// Input
{
  type: "prompt",
  filter: {
    tags: ["brand"],
    status: "active"
  }
}

// Process
1. Determine repo
2. Librarian.search(repo, filter)
3. Return list

// Output
{
  templates: [
    { id: "brand-intro-v2", name: "Brand Introduction", tags: ["brand", "intro"] },
    { id: "brand-tagline", name: "Brand Tagline Generator", tags: ["brand", "tagline"] }
  ],
  total: 2
}
```

### processInheritance(template)

Resolves template inheritance (extends) and includes.

```javascript
// Input
{
  template: {
    id: "brand-intro-detailed",
    extends: "brand-intro-v2",
    overrides: {
      constraints: { maxTokens: 800 }
    }
  }
}

// Process
1. Load parent template
2. Deep merge with overrides
3. Process any includes
4. Return merged template

// Output
{
  id: "brand-intro-detailed",
  name: "Brand Introduction",
  system: "You are a brand strategist...",  // from parent
  user: "Create an introduction...",         // from parent
  constraints: {
    maxTokens: 800,      // overridden
    temperature: 0.7     // from parent
  }
}
```

### render(template, data)

Renders template with data (simple variable replacement).

```javascript
// Input
{
  template: {
    subject: "Welcome to {{brandName}}, {{userName}}!",
    body: "Thank you for choosing {{brandName}}..."
  },
  data: {
    brandName: "Acme Corp",
    userName: "John"
  }
}

// Output
{
  subject: "Welcome to Acme Corp, John!",
  body: "Thank you for choosing Acme Corp..."
}
```

---

## Template Type Mapping

| Type | Repo | Schema |
|------|------|--------|
| `prompt` | prompts | prompt.schema.json |
| `email` | email-templates | email-template.schema.json |
| `document` | document-templates | document.schema.json |
| `scaffold` | scaffold-templates | scaffold.schema.json |
| `export` | export-templates | export.schema.json |

---

## Template Inheritance

Templates can extend other templates:

```json
{
  "id": "brand-intro-formal",
  "extends": "brand-intro-v2",
  "overrides": {
    "constraints": {
      "temperature": 0.5
    }
  },
  "additions": {
    "tags": ["formal"]
  }
}
```

Templates can include partials:

```json
{
  "id": "full-email",
  "html": {
    "sections": [
      { "include": "email-header" },
      { "type": "content", "template": "..." },
      { "include": "email-footer" }
    ]
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "Templates",
  "version": "1.0.0",
  "description": "Central template management",
  "type": "service",

  "functions": [
    {
      "name": "get",
      "description": "Get template by type and ID",
      "input": ["type", "templateId"],
      "output": "template"
    },
    {
      "name": "list",
      "description": "List templates of type",
      "input": ["type", "filter"],
      "output": "templates"
    },
    {
      "name": "validate",
      "description": "Validate template structure",
      "input": ["template", "type"],
      "output": "validationResult"
    },
    {
      "name": "processInheritance",
      "description": "Resolve extends/includes",
      "input": ["template"],
      "output": "processedTemplate"
    },
    {
      "name": "render",
      "description": "Render template with data",
      "input": ["template", "data"],
      "output": "rendered"
    },
    {
      "name": "getSchema",
      "description": "Get schema for template type",
      "input": ["type"],
      "output": "schema"
    }
  ],

  "steps": [
    {
      "name": "getProcessed",
      "description": "Get fully processed template",
      "sequence": ["get", "processInheritance", "validate"]
    },
    {
      "name": "renderWithValidation",
      "description": "Render with data validation",
      "sequence": ["get", "validate", "render"]
    }
  ],

  "config": {
    "cacheTemplates": true,
    "cacheMaxAge": 300000,
    "variableSyntax": "{{variable}}"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Loading templates from repos |
| Librarian | Searching templates |
| Parsers | Validating templates |

---

## Used By

| Module | How |
|--------|-----|
| PromptBuilder | Loading prompt templates |
| Email | Loading email templates |
| Builders | Loading scaffold templates |
| Export | Loading export templates |

---

*Last updated: 2026-01-17*
