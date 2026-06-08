# ImageCollections Module

> **Purpose:** Manage, search, and curate image collections from various sources
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ImageCollections manages image assets from multiple sources - local storage, stock photo APIs (Unsplash, Pexels), brand asset libraries. It provides unified search, tagging, and curation capabilities.

```
Search query → ImageCollections.search() → Results from all sources → Curated list
```

**Sources:**
- Local brand assets
- Unsplash API
- Pexels API
- Custom image repos
- Canva assets (via CanvaIntegration)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `search(query, sources)` | Search images across sources | query, sources | images |
| `getFromSource(source, imageId)` | Get specific image from source | source, imageId | image |
| `download(imageId, source, options)` | Download image | imageId, source, options | localPath |
| `curate(images, criteria)` | Filter/rank images by criteria | images, criteria | curatedImages |
| `tag(imageId, tags)` | Add tags to image | imageId, tags | tagged |
| `getMetadata(imageId, source)` | Get image metadata | imageId, source | metadata |
| `createCollection(name, images)` | Create named collection | name, images | collection |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `ImageCollections.searchAndCurate` | Search then curate results | search → curate |
| `ImageCollections.downloadBatch` | Download multiple images | search → download (multiple) |

---

## Detailed Function Specs

### search(query, sources)

Searches for images across specified sources.

```javascript
// Input
{
  query: {
    keywords: ["technology", "office", "modern"],
    color: "#1a73e8",  // Dominant color filter
    orientation: "landscape",
    minWidth: 1920,
    style: "photography"  // photography, illustration, vector
  },
  sources: ["unsplash", "pexels", "local"],
  limit: 20
}

// Process
1. Query each source in parallel
2. Normalize results to common format
3. Merge and dedupe
4. Return unified list

// Output
{
  images: [
    {
      id: "unsplash_abc123",
      source: "unsplash",
      url: "https://images.unsplash.com/...",
      thumbnail: "https://images.unsplash.com/...?w=400",
      width: 4000,
      height: 2667,
      color: "#1a5fb4",
      photographer: "John Doe",
      tags: ["office", "technology", "laptop"]
    },
    {
      id: "local_brand_001",
      source: "local",
      path: "/assets/images/hero-office.jpg",
      width: 1920,
      height: 1080,
      tags: ["brand", "office"]
    }
  ],
  total: 45,
  sources: {
    unsplash: 20,
    pexels: 15,
    local: 10
  }
}
```

### curate(images, criteria)

Filters and ranks images by brand criteria.

```javascript
// Input
{
  images: [...],  // From search
  criteria: {
    brandId: "acme",
    colorMatch: true,      // Match brand palette
    styleMatch: true,      // Match brand style
    excludeTags: ["people", "faces"],
    preferTags: ["minimal", "tech"],
    minQuality: 0.7
  }
}

// Process
1. Load brand profile for color/style
2. Score each image against criteria
3. Filter by minimums
4. Sort by score

// Output
{
  curated: [
    { ...image, score: 0.92, matchReasons: ["color match", "preferred tags"] },
    { ...image, score: 0.85, matchReasons: ["style match"] }
  ],
  excluded: 12,
  excludeReasons: {
    "colorMismatch": 5,
    "excludedTags": 7
  }
}
```

### download(imageId, source, options)

Downloads image to local storage.

```javascript
// Input
{
  imageId: "unsplash_abc123",
  source: "unsplash",
  options: {
    size: "full",  // thumb, small, regular, full, raw
    format: "jpg",
    destination: "/brands/acme/assets/images/"
  }
}

// Process
1. Get download URL from source
2. Download image
3. Save to destination
4. Track attribution (required for stock photos)

// Output
{
  success: true,
  localPath: "/brands/acme/assets/images/unsplash_abc123.jpg",
  size: 2456789,
  attribution: {
    photographer: "John Doe",
    source: "Unsplash",
    license: "Unsplash License",
    required: true
  }
}
```

---

## Source Configuration

### Unsplash

```json
{
  "source": "unsplash",
  "apiKeyRef": "UNSPLASH_API_KEY",
  "endpoint": "https://api.unsplash.com",
  "rateLimit": 50,
  "attribution": "required"
}
```

### Pexels

```json
{
  "source": "pexels",
  "apiKeyRef": "PEXELS_API_KEY",
  "endpoint": "https://api.pexels.com/v1",
  "rateLimit": 200,
  "attribution": "required"
}
```

### Local

```json
{
  "source": "local",
  "basePath": "/assets/images/",
  "indexPath": "/repos/image-index/",
  "attribution": "none"
}
```

---

## Image Metadata Format

```json
{
  "id": "img_local_001",
  "source": "local",
  "path": "/assets/images/hero.jpg",
  "filename": "hero.jpg",
  "width": 1920,
  "height": 1080,
  "format": "jpeg",
  "size": 456789,
  "colors": {
    "dominant": "#1a73e8",
    "palette": ["#1a73e8", "#ffffff", "#333333"]
  },
  "tags": ["hero", "brand", "technology"],
  "brandId": "acme",
  "created": "2026-01-15",
  "attribution": null
}
```

---

## Config

### module.config.json

```json
{
  "module": "ImageCollections",
  "version": "1.0.0",
  "description": "Manage and search image collections",
  "type": "service",

  "functions": [
    {
      "name": "search",
      "description": "Search images across sources",
      "input": ["query", "sources"],
      "output": "images"
    },
    {
      "name": "getFromSource",
      "description": "Get specific image from source",
      "input": ["source", "imageId"],
      "output": "image"
    },
    {
      "name": "download",
      "description": "Download image",
      "input": ["imageId", "source", "options"],
      "output": "localPath"
    },
    {
      "name": "curate",
      "description": "Filter/rank images by criteria",
      "input": ["images", "criteria"],
      "output": "curatedImages"
    },
    {
      "name": "tag",
      "description": "Add tags to image",
      "input": ["imageId", "tags"],
      "output": "tagged"
    },
    {
      "name": "getMetadata",
      "description": "Get image metadata",
      "input": ["imageId", "source"],
      "output": "metadata"
    },
    {
      "name": "createCollection",
      "description": "Create named collection",
      "input": ["name", "images"],
      "output": "collection"
    }
  ],

  "steps": [
    {
      "name": "searchAndCurate",
      "description": "Search then curate results",
      "sequence": ["search", "curate"]
    },
    {
      "name": "downloadBatch",
      "description": "Download multiple images",
      "sequence": ["search", "download"]
    }
  ],

  "config": {
    "defaultSources": ["local", "unsplash"],
    "cacheResults": true,
    "cacheMaxAge": 3600000
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Getting API keys for stock services |
| Fetchers | Making API requests |
| FileManager | Managing local images |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Finding brand imagery |
| Visual | Image selection for designs |
| Content | Hero images for content |

---

*Last updated: 2026-01-17*
