# Canva Integration Module

> **Purpose:** Interface with Canva API for design operations
> **Type:** Integration Module
> **Status:** Planning

---

## Overview

CanvaIntegration handles all Canva API interactions. It has its own functions specific to Canva operations - fetching folders, creating designs, uploading assets, etc. Each Integration Module is self-contained with its own processes.

```
Chain calls CanvaIntegration.function → Authenticate → Canva API → Parse response
```

**Integration modules are independent** - they don't know about other modules' business logic, just their own API operations.

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `authenticate()` | Get/refresh auth token | - | token |
| `listFolders(parentId)` | List folders in location | parentId | folders |
| `createFolder(name, parentId)` | Create new folder | name, parentId | folderId |
| `listDesigns(folderId)` | List designs in folder | folderId | designs |
| `getDesign(designId)` | Get design details | designId | design |
| `createDesign(template, data)` | Create design from template | template, data | designId |
| `uploadAsset(file, folderId)` | Upload asset to Canva | file, folderId | assetId |
| `exportDesign(designId, format)` | Export design to format | designId, format | exportUrl |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `CanvaIntegration.setupBrand` | Create brand folder structure | authenticate → createFolder (multiple) |
| `CanvaIntegration.exportAll` | Export all designs in folder | listDesigns → exportDesign (each) |
| `CanvaIntegration.syncAssets` | Sync local assets to Canva | authenticate → uploadAsset (each) |

---

## Detailed Function Specs

### authenticate()

Gets or refreshes Canva authentication token.

```javascript
// Process
1. APIKeyManager.get('CANVA_API_KEY')
2. Check if existing token valid
3. If expired → refresh
4. Return valid token

// Output
{
  token: "...",
  expiresAt: "2026-01-17T15:30:00Z",
  scope: ["folder:read", "folder:write", "design:read", "design:write"]
}
```

### listFolders(parentId)

Lists folders in a Canva location.

```javascript
// Input
{
  parentId: "folder_abc123"  // or null for root
}

// Process
1. authenticate()
2. GET /folders/{parentId}/children
3. Parse response

// Output
{
  folders: [
    { id: "folder_def456", name: "Brand Assets", created: "..." },
    { id: "folder_ghi789", name: "Exports", created: "..." }
  ],
  total: 2
}
```

### createFolder(name, parentId)

Creates a new folder in Canva.

```javascript
// Input
{
  name: "Acme Corp",
  parentId: "folder_root"
}

// Process
1. authenticate()
2. POST /folders { name, parent_id }
3. Return new folder ID

// Output
{
  success: true,
  folderId: "folder_new123",
  name: "Acme Corp",
  path: "/Brands/Acme Corp"
}
```

### createDesign(template, data)

Creates a design from Canva template.

```javascript
// Input
{
  template: "brand-intro-slide",
  data: {
    headline: "Acme Corp",
    tagline: "Innovation starts here",
    primaryColor: "#1a73e8"
  }
}

// Process
1. authenticate()
2. Find template ID
3. POST /designs { template_id, variables: data }
4. Return new design

// Output
{
  success: true,
  designId: "design_abc123",
  editUrl: "https://canva.com/design/...",
  thumbnailUrl: "https://..."
}
```

### exportDesign(designId, format)

Exports design to specified format.

```javascript
// Input
{
  designId: "design_abc123",
  format: "pdf"  // pdf, png, jpg
}

// Process
1. authenticate()
2. POST /designs/{designId}/exports { format }
3. Poll for completion
4. Return download URL

// Output
{
  success: true,
  format: "pdf",
  downloadUrl: "https://export.canva.com/...",
  expiresAt: "2026-01-17T16:00:00Z"
}
```

---

## Canva Folder Structure

Standard brand folder structure in Canva:

```
/Brands/
  /{brandName}/
    /Assets/
      /Logos/
      /Icons/
      /Photos/
    /Templates/
    /Exports/
    /Archive/
```

---

## Error Handling

| Canva Error | Action |
|-------------|--------|
| 401 Unauthorized | Re-authenticate |
| 429 Rate Limited | Retry with backoff |
| 404 Not Found | Return not found error |
| 500 Server Error | Retry, then escalate |

---

## Config

### module.config.json

```json
{
  "module": "CanvaIntegration",
  "version": "1.0.0",
  "description": "Interface with Canva API",
  "type": "integration",

  "functions": [
    {
      "name": "authenticate",
      "description": "Get/refresh auth token",
      "input": [],
      "output": "token"
    },
    {
      "name": "listFolders",
      "description": "List folders in location",
      "input": ["parentId"],
      "output": "folders"
    },
    {
      "name": "createFolder",
      "description": "Create new folder",
      "input": ["name", "parentId"],
      "output": "folderId"
    },
    {
      "name": "listDesigns",
      "description": "List designs in folder",
      "input": ["folderId"],
      "output": "designs"
    },
    {
      "name": "getDesign",
      "description": "Get design details",
      "input": ["designId"],
      "output": "design"
    },
    {
      "name": "createDesign",
      "description": "Create design from template",
      "input": ["template", "data"],
      "output": "designId"
    },
    {
      "name": "uploadAsset",
      "description": "Upload asset to Canva",
      "input": ["file", "folderId"],
      "output": "assetId"
    },
    {
      "name": "exportDesign",
      "description": "Export design to format",
      "input": ["designId", "format"],
      "output": "exportUrl"
    }
  ],

  "steps": [
    {
      "name": "setupBrand",
      "description": "Create brand folder structure",
      "sequence": ["authenticate", "createFolder"]
    },
    {
      "name": "exportAll",
      "description": "Export all designs in folder",
      "sequence": ["listDesigns", "exportDesign"]
    },
    {
      "name": "syncAssets",
      "description": "Sync local assets to Canva",
      "sequence": ["authenticate", "uploadAsset"]
    }
  ],

  "config": {
    "apiBase": "https://api.canva.com/v1",
    "apiKeyRef": "CANVA_API_KEY",
    "defaultExportFormat": "pdf",
    "rateLimitPerMinute": 100
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Getting Canva credentials |
| Fetchers | Making HTTP requests |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit chains | Creating brand assets in Canva |
| Export chains | Exporting designs |

---

*Last updated: 2026-01-17*
