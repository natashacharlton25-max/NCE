# Extensibility Guide (Technical)

> Technical reference for Claude Code and developers.
> For a simplified version, see [EXTENSIBILITY-GUIDE-SIMPLE.md](./EXTENSIBILITY-GUIDE-SIMPLE.md)

---

## Overview

The BrandKit system is designed to be extended without code changes in most cases. New domains, sections, libraries, and integrations can be added by creating configuration files.

---

## Adding a New Domain

### Required Files

1. **BrandLib Domain Directory**

   Create domain folder with libraries:
   ```
   BrandLib/{domain-name}/
   ├── tone.json
   ├── vocabulary.json
   ├── disclaimers.json
   └── ... (domain-specific libraries)
   ```

2. **Domain Registration**

   Add to `BrandLib/_config/domains.json`:
   ```json
   {
     "domains": {
       "new-domain": {
         "name": "New Domain",
         "description": "Description of this domain",
         "parent": null,
         "keywords": {
           "strong": ["keyword1", "keyword2"],
           "moderate": ["keyword3"],
           "weak": ["keyword4"],
           "anti": ["unrelated-keyword"]
         }
       }
     }
   }
   ```

3. **Detection Keywords**

   Add to `BrandKit AIGen/_config/domain-detection.json`:
   ```json
   {
     "domains": {
       "new-domain": {
         "keywords": {
           "strong": ["keyword1", "keyword2"],
           "moderate": ["keyword3"],
           "weak": ["keyword4"],
           "anti": ["unrelated-keyword"]
         },
         "minimum_score": 5
       }
     }
   }
   ```

### No Code Changes Required

The system auto-discovers domains from:
- `BrandLib/_config/domains.json` for domain list
- `BrandLib/{domain}/` for libraries
- `BrandKit AIGen/_config/domain-detection.json` for auto-detection

---

## Adding a New Section

### Required Files

1. **Schema Definition**

   Create in BrandKit:
   ```
   BrandKit/_system/{category}/{section}/default/
   ├── schema.json           # JSON Schema for the section
   ├── instructions.json     # AI instructions (optional)
   └── example-{name}.json   # Example data (optional)
   ```

   **schema.json structure:**
   ```json
   {
     "$schema": "http://json-schema.org/draft-07/schema#",
     "$id": "{category}-{section}",
     "title": "Section Title",
     "description": "What this section contains",
     "type": "object",
     "properties": {
       "field_name": {
         "type": "string",
         "title": "Field Title",
         "description": "What to enter here",
         "minLength": 1,
         "maxLength": 500
       }
     },
     "required": ["field_name"]
   }
   ```

2. **AI Generation Config**

   Create in BrandKit AIGen:
   ```
   BrandKit AIGen/sections/{category}--{section}.json
   ```

   **Section config structure:**
   ```json
   {
     "section": "{category}/{section}",
     "title": "Section Title",
     "description": "What this section is for",
     "generation_approach": "extract|generate|hybrid",
     "context_sources": ["website", "documents", "completed_sections"],
     "dependencies": ["other/section"],
     "prompts": {
       "system": "You are generating {section} content...",
       "extraction": "Extract the following from the provided content...",
       "generation": "Generate the following based on brand identity..."
     }
   }
   ```

3. **Generation Order**

   Add to `BrandKit AIGen/_config/generation-order.json`:
   ```json
   {
     "order": 50,
     "section": "{category}/{section}",
     "depends_on": ["dependency/section"],
     "tier": 3
   }
   ```

### Section Categories

| Category | Purpose |
|----------|---------|
| `core` | Essential brand identity |
| `voice` | Brand voice and tone |
| `audience` | Target audience definitions |
| `legal` | Legal and compliance |
| `visual` | Visual brand elements |
| `content` | Content guidelines |
| `communication` | Communication rules |
| `products` | Product/service info |
| `operations` | Operational guidelines |

---

## Adding a New Library

### Create Library File

Location: `BrandLib/{domain}/{library-name}.json`

**Library structure:**
```json
{
  "$schema": "../_schemas/library.schema.json",
  "library": "library-name",
  "domain": "domain-name",
  "description": "What this library contains",
  "entries": [
    {
      "id": "unique-id",
      "value": "The actual content",
      "label": "Display label",
      "description": "When to use this",
      "tags": ["tag1", "tag2"],
      "domain_tags": ["domain-name"],
      "examples": ["Example usage 1"],
      "related": ["other-entry-id"]
    }
  ]
}
```

### Add Field Mapping

Add to `BrandLib/_config/field-mappings.json`:
```json
{
  "field_mappings": {
    "{section}/{field}": {
      "library": "library-name",
      "match_type": "semantic|keyword|exact",
      "multiple": false,
      "required": false
    }
  }
}
```

### Match Types

| Type | Description |
|------|-------------|
| `semantic` | AI-powered meaning matching |
| `keyword` | Keyword overlap scoring |
| `exact` | Exact string matching |
| `category` | Match by tags/categories |

---

## Adding a New Export Format

### Create Exporter

Location: `BrandKit Workflow/lib/server/export/{format}.ts`

**Exporter interface:**
```typescript
import type { Brand, ExportOptions, ExportResult } from '$lib/types';

export async function exportBrand(
  brand: Brand,
  options: ExportOptions
): Promise<ExportResult> {
  // 1. Load requested sections
  // 2. Transform to target format
  // 3. Write output file
  // 4. Return result

  return {
    success: true,
    format: 'new-format',
    outputPath: '/path/to/output.ext',
    fileSize: 1234,
    sectionsIncluded: ['core/identity', 'voice/tone']
  };
}
```

### Register in Export Router

Update `BrandKit Workflow/lib/server/export/router.ts`:
```typescript
import { exportBrand as exportNewFormat } from './new-format';

export async function routeExport(format: string, brand: Brand, options: ExportOptions) {
  switch (format) {
    case 'json': return exportJSON(brand, options);
    case 'markdown': return exportMarkdown(brand, options);
    case 'pdf': return exportPDF(brand, options);
    case 'prompt_pack': return exportPromptPack(brand, options);
    case 'new-format': return exportNewFormat(brand, options);  // Add this
    default: throw new Error(`Unknown export format: ${format}`);
  }
}
```

### Add UI Option

Update Phase 10 export interface to include new format option.

---

## Adding a New Integration

### Create Integration Handler

Location: `BrandKit Workflow/lib/server/integrations/{service}.ts`

**Integration interface:**
```typescript
import { getKey } from '@brandkit/api-keys';

interface ServiceConnection {
  connected: boolean;
  lastSync?: string;
  error?: string;
}

export async function connect(): Promise<ServiceConnection> {
  const apiKey = await getKey('service-name');
  if (!apiKey) {
    return { connected: false, error: 'API key not configured' };
  }
  // Perform connection
  return { connected: true };
}

export async function sync(brandPath: string, data: any): Promise<SyncResult> {
  // Perform sync operation
}

export async function healthCheck(): Promise<boolean> {
  // Check if service is accessible
}
```

### Register API Key Service

Add to `APIKeyManager/_config/services.json`:
```json
{
  "service-name": {
    "name": "Service Display Name",
    "description": "What this service does",
    "required_for": ["feature-name"],
    "docs_url": "https://docs.service.com/api"
  }
}
```

### Add to Integration Manager

Update `BrandKit Workflow/lib/server/integrations/manager.ts`:
```typescript
import * as newService from './new-service';

export const integrations = {
  canva,
  unsplash,
  pexels,
  google_fonts,
  'new-service': newService  // Add this
};
```

---

## File Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Multi-word files | kebab-case | `brand-status.ts` |
| Single word | lowercase | `progress.json` |
| Config folders | underscore prefix | `_config/` |
| Section IDs | category/name | `voice/tone` |
| Library IDs | lowercase | `vocabulary` |

---

## Validation

All new configuration files should:

1. **Have valid JSON syntax**
2. **Include `$schema` reference where applicable**
3. **Follow naming conventions**
4. **Include required fields per schema**

Test new additions by:
1. Running schema validation
2. Testing in isolation before integration
3. Verifying auto-discovery picks up new items
