# ImageBrowser Module

> **Purpose:** Browse, search, organize, and select images from all sources
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ImageBrowser is the unified interface for browsing and selecting images across all sources - local storage, brand assets, stock libraries, AI-generated images. It provides search, filtering, organization, and selection capabilities.

```
User search → ImageBrowser.browse() → Results from all sources → Selection
```

**Capabilities:**
- Unified search across sources
- Visual browsing interface data
- Filtering and sorting
- Collections and favorites
- Usage tracking
- License management

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `browse(query, options)` | Browse images with filters | query, options | browseResults |
| `search(keywords, sources)` | Search across sources | keywords, sources | searchResults |
| `filter(images, criteria)` | Filter image results | images, criteria | filteredImages |
| `getDetails(imageId, source)` | Get full image details | imageId, source | imageDetails |
| `addToCollection(imageId, collectionId)` | Add to collection | imageId, collectionId | added |
| `createCollection(name, brandId)` | Create new collection | name, brandId | collection |
| `trackUsage(imageId, usage)` | Track image usage | imageId, usage | tracked |
| `checkLicense(imageId)` | Check image license | imageId | licenseInfo |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `ImageBrowser.searchAndFilter` | Search then filter results | search → filter |
| `ImageBrowser.selectForProject` | Select images for project | browse → checkLicense → trackUsage |

---

## Detailed Function Specs

### browse(query, options)

Browses images with comprehensive options.

```javascript
// Input
{
  query: {
    keywords: "office workspace technology",
    brandId: "acme"
  },
  options: {
    sources: ["brand-assets", "unsplash", "generated"],
    filters: {
      orientation: "landscape",
      color: "#1a73e8",
      minWidth: 1920,
      style: "photography",
      license: "commercial"
    },
    sort: "relevance",  // relevance, date, popularity
    page: 1,
    perPage: 24
  }
}

// Output
{
  results: [
    {
      id: "img_brand_001",
      source: "brand-assets",
      thumbnail: "/thumbs/brand_001.jpg",
      preview: "/preview/brand_001.jpg",
      width: 3840,
      height: 2160,
      orientation: "landscape",
      colors: ["#1a73e8", "#ffffff"],
      tags: ["office", "technology", "workspace"],
      license: "owned",
      usageCount: 5
    },
    {
      id: "unsplash_abc123",
      source: "unsplash",
      thumbnail: "https://images.unsplash.com/...?w=400",
      preview: "https://images.unsplash.com/...?w=800",
      width: 4000,
      height: 2667,
      orientation: "landscape",
      colors: ["#2d5a7b", "#f4e4bc"],
      tags: ["office", "modern", "desk"],
      license: "unsplash",
      photographer: "John Doe"
    }
  ],
  total: 156,
  page: 1,
  pages: 7,
  sources: {
    "brand-assets": 12,
    "unsplash": 89,
    "pexels": 45,
    "generated": 10
  }
}
```

### filter(images, criteria)

Filters image results.

```javascript
// Input
{
  images: [...],  // From search
  criteria: {
    orientation: "landscape",
    aspectRatio: { min: 1.5, max: 2.0 },
    minWidth: 1920,
    colors: {
      dominant: "#1a73e8",
      tolerance: 30
    },
    excludeTags: ["people", "faces"],
    license: ["owned", "commercial", "unsplash"]
  }
}

// Output
{
  filtered: [...],
  removed: 45,
  reasons: {
    orientation: 12,
    aspectRatio: 8,
    minWidth: 5,
    colors: 10,
    excludeTags: 7,
    license: 3
  }
}
```

### getDetails(imageId, source)

Gets full details for an image.

```javascript
// Input
{
  imageId: "img_brand_001",
  source: "brand-assets"
}

// Output
{
  id: "img_brand_001",
  source: "brand-assets",
  paths: {
    original: "/assets/images/brand_001.jpg",
    preview: "/preview/brand_001.jpg",
    thumbnail: "/thumbs/brand_001.jpg"
  },
  metadata: {
    width: 3840,
    height: 2160,
    format: "jpeg",
    size: 2456789,
    created: "2026-01-10",
    modified: "2026-01-15"
  },
  colors: {
    dominant: "#1a73e8",
    palette: ["#1a73e8", "#ffffff", "#333333", "#f5f5f5"]
  },
  tags: ["office", "technology", "workspace", "modern"],
  collections: ["hero-images", "brand-photos"],
  usage: {
    count: 5,
    locations: [
      { type: "website", page: "homepage", date: "2026-01-12" },
      { type: "social", platform: "linkedin", date: "2026-01-14" }
    ]
  },
  license: {
    type: "owned",
    restrictions: "none",
    attribution: false
  },
  brandId: "acme"
}
```

### createCollection(name, brandId)

Creates a new image collection.

```javascript
// Input
{
  name: "Q1 Campaign Images",
  brandId: "acme",
  description: "Selected images for Q1 marketing campaign",
  tags: ["campaign", "q1-2026"]
}

// Output
{
  collectionId: "col_acme_q1campaign",
  name: "Q1 Campaign Images",
  brandId: "acme",
  created: "2026-01-17",
  imageCount: 0
}
```

### trackUsage(imageId, usage)

Tracks where an image is used.

```javascript
// Input
{
  imageId: "img_brand_001",
  usage: {
    type: "social",
    platform: "instagram",
    postId: "ig_post_123",
    date: "2026-01-17",
    brandId: "acme"
  }
}

// Output
{
  tracked: true,
  imageId: "img_brand_001",
  totalUsage: 6
}
```

---

## Image Sources

| Source | Type | License | API |
|--------|------|---------|-----|
| `brand-assets` | Local | Owned | FileManager |
| `unsplash` | Stock | Unsplash License | ImageCollections |
| `pexels` | Stock | Pexels License | ImageCollections |
| `generated` | AI | Owned | AIImageCreation |
| `canva` | Cloud | Varies | CanvaIntegration |

---

## Collection Structure

```json
{
  "collectionId": "col_acme_heroes",
  "name": "Hero Images",
  "brandId": "acme",
  "description": "Approved hero images for website and marketing",
  "images": [
    { "imageId": "img_brand_001", "source": "brand-assets", "addedAt": "2026-01-10" },
    { "imageId": "unsplash_abc", "source": "unsplash", "addedAt": "2026-01-12" }
  ],
  "tags": ["hero", "marketing", "approved"],
  "created": "2026-01-01",
  "updated": "2026-01-17",
  "imageCount": 15
}
```

---

## Config

### module.config.json

```json
{
  "module": "ImageBrowser",
  "version": "1.0.0",
  "description": "Browse and select images from all sources",
  "type": "service",

  "functions": [
    {
      "name": "browse",
      "description": "Browse images with filters",
      "input": ["query", "options"],
      "output": "browseResults"
    },
    {
      "name": "search",
      "description": "Search across sources",
      "input": ["keywords", "sources"],
      "output": "searchResults"
    },
    {
      "name": "filter",
      "description": "Filter image results",
      "input": ["images", "criteria"],
      "output": "filteredImages"
    },
    {
      "name": "getDetails",
      "description": "Get full image details",
      "input": ["imageId", "source"],
      "output": "imageDetails"
    },
    {
      "name": "addToCollection",
      "description": "Add to collection",
      "input": ["imageId", "collectionId"],
      "output": "added"
    },
    {
      "name": "createCollection",
      "description": "Create new collection",
      "input": ["name", "brandId"],
      "output": "collection"
    },
    {
      "name": "trackUsage",
      "description": "Track image usage",
      "input": ["imageId", "usage"],
      "output": "tracked"
    },
    {
      "name": "checkLicense",
      "description": "Check image license",
      "input": ["imageId"],
      "output": "licenseInfo"
    }
  ],

  "steps": [
    {
      "name": "searchAndFilter",
      "description": "Search then filter results",
      "sequence": ["search", "filter"]
    },
    {
      "name": "selectForProject",
      "description": "Select images for project",
      "sequence": ["browse", "checkLicense", "trackUsage"]
    }
  ],

  "config": {
    "defaultSources": ["brand-assets", "unsplash"],
    "defaultPerPage": 24,
    "thumbnailSize": 400,
    "previewSize": 800
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| ImageCollections | Stock photo APIs |
| FileManager | Local image access |
| AIImageCreation | Generated images |
| CanvaIntegration | Canva assets |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Image selection for brand |
| Content | Content image selection |
| SocialMediaPlanner | Social media images |

---

*Last updated: 2026-01-17*
