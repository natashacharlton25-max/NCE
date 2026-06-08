# PhotoManager Service

> Central service for ALL image operations (stock photos, processing, attribution).
> **Status:** Planning | **Priority:** LOW (Build Last)
> **Location:** `C:\Users\Business\PhotoManager\`

---

## Overview

The PhotoManager service handles all image operations. Apps request images by description - they never call stock APIs directly. PhotoManager handles search optimization, API routing, image selection, resizing, license tracking, and attribution management.

### Current State (BrandKit Workflow)

Image functionality is partially built:

```
BrandKit Workflow/lib/server/
├── imagebrowser-integration.ts     # Integration layer for ImageBrowser app
└── types/visual.ts                 # Some image types

# Not yet built (Phase 9):
# - Unsplash API integration
# - Pexels API integration
# - Image resizing
# - Attribution tracking
```

### Target State (PhotoManager Service)

```
PhotoManager/
├── src/
│   ├── index.ts                    # Public API
│   ├── scripts/
│   │   ├── search-stock.ts         # Query stock APIs
│   │   ├── optimize-search.ts      # AI-enhanced search terms
│   │   ├── select-best.ts          # Choose from results
│   │   ├── resize-image.ts         # Resize to specs
│   │   ├── download-image.ts       # Fetch from API
│   │   ├── check-license.ts        # Verify usage rights
│   │   ├── track-attribution.ts    # Record credits
│   │   └── get-sizes.ts            # Standard size presets
│   ├── providers/
│   │   ├── unsplash.ts             # Unsplash API
│   │   ├── pexels.ts               # Pexels API
│   │   └── index.ts                # Provider registry
│   └── types/
│       └── index.ts
├── _config/
│   ├── providers.json              # API endpoints
│   ├── sizes.json                  # Standard sizes
│   └── attribution.json            # License requirements
├── cache/
│   └── search-results/             # Cached searches
├── package.json
└── tsconfig.json
```

---

## Extraction Mapping

### Files to Extract

| Current Location | Target Location | What to Change |
|------------------|-----------------|----------------|
| `lib/server/imagebrowser-integration.ts` | `src/scripts/*.ts` | Split into focused scripts |
| `lib/server/types/visual.ts` (image types) | `src/types/index.ts` | Extract image types |

### Functions to Extract

| Current Function | Current File | Target Script |
|------------------|--------------|---------------|
| `createImageRequest()` | `imagebrowser-integration.ts` | `search-stock.ts` |
| `processImageSelection()` | `imagebrowser-integration.ts` | `select-best.ts` |
| `saveSelectedImages()` | `imagebrowser-integration.ts` | `download-image.ts` |
| `generateAttribution()` | `imagebrowser-integration.ts` | `track-attribution.ts` |
| `getImageSizes()` | `imagebrowser-integration.ts` | `get-sizes.ts` |

---

## Migration Steps

### Step 1: Create PhotoManager Shell

```bash
mkdir C:\Users\Business\PhotoManager
cd C:\Users\Business\PhotoManager
npm init -y

npm install typescript ts-node sharp
mkdir src src/scripts src/providers src/types _config cache cache/search-results
```

### Step 2: Create Provider Integrations

```typescript
// src/providers/unsplash.ts
// NEW - Build from scratch using Unsplash API

import { APIKeyManager } from '@services/api-key-manager';

const UNSPLASH_API = 'https://api.unsplash.com';

export interface UnsplashPhoto {
  id: string;
  urls: { raw: string; full: string; regular: string; small: string; thumb: string };
  alt_description: string;
  user: { name: string; username: string };
  links: { download_location: string };
}

export async function searchUnsplash(
  query: string,
  options: SearchOptions = {}
): Promise<UnsplashPhoto[]> {
  const apiKey = await APIKeyManager.getKey('unsplash');

  const params = new URLSearchParams({
    query,
    per_page: String(options.limit || 20),
    orientation: options.orientation || 'landscape'
  });

  const response = await fetch(`${UNSPLASH_API}/search/photos?${params}`, {
    headers: { Authorization: `Client-ID ${apiKey}` }
  });

  const data = await response.json();
  return data.results;
}

export async function downloadUnsplash(photo: UnsplashPhoto): Promise<Buffer> {
  // Trigger download endpoint (required by Unsplash API guidelines)
  const apiKey = await APIKeyManager.getKey('unsplash');
  await fetch(photo.links.download_location, {
    headers: { Authorization: `Client-ID ${apiKey}` }
  });

  // Download actual image
  const response = await fetch(photo.urls.full);
  return Buffer.from(await response.arrayBuffer());
}

export function getUnsplashAttribution(photo: UnsplashPhoto): Attribution {
  return {
    photographer: photo.user.name,
    photographerUrl: `https://unsplash.com/@${photo.user.username}`,
    source: 'Unsplash',
    sourceUrl: 'https://unsplash.com',
    license: 'Unsplash License',
    licenseUrl: 'https://unsplash.com/license'
  };
}
```

```typescript
// src/providers/pexels.ts
// NEW - Build from scratch using Pexels API

const PEXELS_API = 'https://api.pexels.com/v1';

export interface PexelsPhoto {
  id: number;
  src: { original: string; large2x: string; large: string; medium: string; small: string };
  alt: string;
  photographer: string;
  photographer_url: string;
}

export async function searchPexels(
  query: string,
  options: SearchOptions = {}
): Promise<PexelsPhoto[]> {
  const apiKey = await APIKeyManager.getKey('pexels');

  const params = new URLSearchParams({
    query,
    per_page: String(options.limit || 20),
    orientation: options.orientation || 'landscape'
  });

  const response = await fetch(`${PEXELS_API}/search?${params}`, {
    headers: { Authorization: apiKey }
  });

  const data = await response.json();
  return data.photos;
}

export async function downloadPexels(photo: PexelsPhoto): Promise<Buffer> {
  const response = await fetch(photo.src.original);
  return Buffer.from(await response.arrayBuffer());
}

export function getPexelsAttribution(photo: PexelsPhoto): Attribution {
  return {
    photographer: photo.photographer,
    photographerUrl: photo.photographer_url,
    source: 'Pexels',
    sourceUrl: 'https://pexels.com',
    license: 'Pexels License',
    licenseUrl: 'https://pexels.com/license'
  };
}
```

### Step 3: Create Search Script

```typescript
// src/scripts/search-stock.ts
import { searchUnsplash } from '../providers/unsplash';
import { searchPexels } from '../providers/pexels';

export interface SearchRequest {
  query: string;
  providers?: ('unsplash' | 'pexels')[];
  limit?: number;
  orientation?: 'landscape' | 'portrait' | 'square';
}

export interface SearchResult {
  id: string;
  provider: string;
  previewUrl: string;
  fullUrl: string;
  description: string;
  photographer: string;
  attribution: Attribution;
}

export async function searchStock(request: SearchRequest): Promise<SearchResult[]> {
  const providers = request.providers || ['unsplash', 'pexels'];
  const limitPerProvider = Math.ceil((request.limit || 20) / providers.length);

  const results: SearchResult[] = [];

  // Search in parallel
  const searches = providers.map(async (provider) => {
    if (provider === 'unsplash') {
      const photos = await searchUnsplash(request.query, { limit: limitPerProvider });
      return photos.map(p => normalizeUnsplash(p));
    }
    if (provider === 'pexels') {
      const photos = await searchPexels(request.query, { limit: limitPerProvider });
      return photos.map(p => normalizePexels(p));
    }
    return [];
  });

  const searchResults = await Promise.all(searches);
  return searchResults.flat().slice(0, request.limit || 20);
}
```

### Step 4: Create AI Search Optimization

```typescript
// src/scripts/optimize-search.ts
import { AICaller } from '@services/ai-caller';
import { PromptBuilder } from '@services/prompt-builder';

export async function optimizeSearchTerms(
  description: string,
  context?: { domain?: string; mood?: string; style?: string }
): Promise<string[]> {
  const prompt = await PromptBuilder.build('system/photo-search-terms', {
    description,
    domain: context?.domain || 'general',
    mood: context?.mood,
    style: context?.style
  });

  const response = await AICaller.call('photo-manager', prompt, {
    responseFormat: 'json'
  });

  // AI returns array of optimized search terms
  return JSON.parse(response.content).searchTerms;
}

// Example: "calming spa environment" with domain "therapeutic"
// Returns: ["spa relaxation", "wellness retreat", "peaceful massage room", "serene spa interior"]
```

### Step 5: Create Image Processing

```typescript
// src/scripts/resize-image.ts
import sharp from 'sharp';

export interface ResizeOptions {
  width: number;
  height: number;
  fit?: 'cover' | 'contain' | 'fill';
  format?: 'jpeg' | 'png' | 'webp';
  quality?: number;
}

export async function resizeImage(
  input: Buffer,
  options: ResizeOptions
): Promise<Buffer> {
  let processor = sharp(input)
    .resize(options.width, options.height, {
      fit: options.fit || 'cover',
      position: 'center'
    });

  const format = options.format || 'jpeg';
  const quality = options.quality || 85;

  switch (format) {
    case 'jpeg':
      processor = processor.jpeg({ quality });
      break;
    case 'png':
      processor = processor.png({ quality });
      break;
    case 'webp':
      processor = processor.webp({ quality });
      break;
  }

  return processor.toBuffer();
}

// src/scripts/get-sizes.ts
export const STANDARD_SIZES = {
  // Social media
  'instagram-post': { width: 1080, height: 1080 },
  'instagram-story': { width: 1080, height: 1920 },
  'facebook-post': { width: 1200, height: 630 },
  'facebook-cover': { width: 820, height: 312 },
  'twitter-post': { width: 1600, height: 900 },
  'linkedin-post': { width: 1200, height: 627 },

  // Web
  'hero-large': { width: 1920, height: 1080 },
  'hero-medium': { width: 1440, height: 810 },
  'blog-featured': { width: 1200, height: 630 },
  'thumbnail': { width: 400, height: 300 },

  // Documents
  'pdf-full': { width: 2480, height: 3508 },  // A4 at 300dpi
  'pdf-half': { width: 2480, height: 1754 }
};

export function getSize(name: string): { width: number; height: number } {
  return STANDARD_SIZES[name] || { width: 1200, height: 630 };
}
```

### Step 6: Create Attribution Tracking

```typescript
// src/scripts/track-attribution.ts
import { FileManager } from '@services/file-manager';

export interface Attribution {
  photographer: string;
  photographerUrl: string;
  source: string;
  sourceUrl: string;
  license: string;
  licenseUrl: string;
}

export interface ImageUsage {
  imageId: string;
  provider: string;
  attribution: Attribution;
  usedIn: string;         // brand/section/location
  downloadedAt: string;
  usedAt: string;
}

export async function trackImageUsage(
  brandPath: string,
  usage: ImageUsage
): Promise<void> {
  // Load existing attributions
  const attributionFile = `${brandPath}/_meta/image-attributions.json`;
  let attributions: ImageUsage[] = [];

  try {
    attributions = await FileManager.readJson('photo-manager', {
      brandPath,
      folder: '_meta',
      filename: 'image-attributions.json'
    });
  } catch {
    // File doesn't exist yet
  }

  // Add new usage
  attributions.push({
    ...usage,
    downloadedAt: new Date().toISOString()
  });

  // Save
  await FileManager.writeJson('photo-manager', {
    brandPath,
    folder: '_meta',
    filename: 'image-attributions.json',
    data: attributions
  });
}

export function generateAttributionText(attributions: Attribution[]): string {
  return attributions.map(a =>
    `Photo by [${a.photographer}](${a.photographerUrl}) on [${a.source}](${a.sourceUrl})`
  ).join('\n');
}
```

### Step 7: Create Public API

```typescript
// src/index.ts
// Search
export async function search(
  app: string,
  request: SearchRequest
): Promise<SearchResult[]>;

export async function searchWithAI(
  app: string,
  description: string,
  context?: SearchContext
): Promise<SearchResult[]>;

// Download
export async function download(
  app: string,
  imageId: string,
  provider: string
): Promise<DownloadResult>;

export async function downloadAndResize(
  app: string,
  imageId: string,
  provider: string,
  size: string | ResizeOptions
): Promise<DownloadResult>;

// Save to brand
export async function saveToband(
  app: string,
  brandPath: string,
  imageId: string,
  provider: string,
  options: SaveOptions
): Promise<SaveResult>;

// Attribution
export async function getAttributions(
  app: string,
  brandPath: string
): Promise<Attribution[]>;

export function generateAttributionText(attributions: Attribution[]): string;

// Sizes
export function getStandardSizes(): Record<string, Size>;
export function getSize(name: string): Size;
```

### Step 8: Update BrandKit Workflow

```typescript
// BEFORE - Via ImageBrowser integration
import { createImageRequest, processImageSelection } from '$lib/server/imagebrowser-integration';

const request = createImageRequest({ domain, mood, purpose });
// ... user selects in ImageBrowser app ...
const saved = await processImageSelection(selection, brandPath);

// AFTER - Via PhotoManager service
import { PhotoManager } from '@services/photo-manager';

// AI-enhanced search
const results = await PhotoManager.searchWithAI('brandkit-workflow', 'calming spa environment', {
  domain: 'therapeutic',
  mood: 'serene'
});

// User selects from results...

// Download, resize, and save
const saved = await PhotoManager.saveToBrand('brandkit-workflow', brandPath, selectedImage.id, selectedImage.provider, {
  size: 'hero-large',
  folder: 'context/images/hero'
});
```

---

## Integration with Current Build

### Feature Flag Migration

```typescript
// lib/server/imagebrowser-integration.ts - Add forwarding
export async function createImageRequest(options: ImageRequestOptions): Promise<ImageRequest> {
  if (process.env.USE_PHOTOMANAGER_SERVICE === 'true') {
    const results = await PhotoManager.searchWithAI('brandkit-workflow', options.description, {
      domain: options.domain
    });
    return { searchResults: results };
  }

  return localCreateImageRequest(options);
}
```

---

## Dependencies

| Service | Dependency Type | Purpose |
|---------|-----------------|---------|
| **APIKeyManager** | Required | API keys for Unsplash, Pexels |
| **AICaller** | Optional | AI-enhanced search terms |
| **PromptBuilder** | Optional | Build search optimization prompt |
| **FileManager** | Required | Save images to brand folders |

---

## Config Files

```json
// _config/providers.json
{
  "providers": {
    "unsplash": {
      "baseUrl": "https://api.unsplash.com",
      "keyService": "unsplash",
      "rateLimit": 50,
      "rateLimitPeriod": "hour"
    },
    "pexels": {
      "baseUrl": "https://api.pexels.com/v1",
      "keyService": "pexels",
      "rateLimit": 200,
      "rateLimitPeriod": "hour"
    }
  }
}

// _config/sizes.json
{
  "social": {
    "instagram-post": { "width": 1080, "height": 1080 },
    "instagram-story": { "width": 1080, "height": 1920 }
  },
  "web": {
    "hero-large": { "width": 1920, "height": 1080 }
  }
}

// _config/attribution.json
{
  "requirements": {
    "unsplash": {
      "required": false,
      "recommended": true,
      "format": "Photo by {photographer} on Unsplash"
    },
    "pexels": {
      "required": false,
      "recommended": true,
      "format": "Photo by {photographer} on Pexels"
    }
  }
}
```

---

## Notes

- PhotoManager is the LAST service to build (lowest priority)
- Stock APIs require API keys in APIKeyManager
- Always track attribution for compliance
- AI search optimization is optional but recommended
- Image resizing uses sharp library
- Cache search results to reduce API calls

---

*Last updated: 2026-01-16*
