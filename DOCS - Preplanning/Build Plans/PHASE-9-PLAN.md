# Phase 9: Integrations - Implementation Plan

> Detailed implementation plan for Phase 9 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)

---

## Overview

Phase 9 implements external integrations - connecting BrandKit Workflow to third-party services for syncing brand assets, fetching data, and extending functionality.

| Component | Purpose |
|-----------|---------|
| **9.1 Canva Integration** | Sync colours, typography, and logos to Canva Brand Kit |
| **9.2 Stock Photo APIs** | Unsplash and Pexels integration for ImageBrowser |
| **9.3 Google Fonts API** | Font search and metadata for TypographyBuilder |
| **9.4 External Registries** | Companies House, ABN lookup for legal sections |
| **9.5 Integration Manager** | Track connection status, handle OAuth, manage sync state |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| API Keys Manager | Phase 1.4 | Required - secure key storage |
| Visual sections complete | Phase 7 | Required for Canva sync |
| Legal sections | Phase 6 | Required for registry lookups |
| Global config | Phase 1.1 | Required - integration settings |

---

## API Keys Integration with Phase 1.4

All integrations retrieve API keys through the API Keys Manager (Phase 1.4). Here's how to use it:

### Importing API Keys Manager

```typescript
// Import from API Keys Manager
import { getKey, hasKey } from '@brandkit/api-keys';
// OR if using relative paths:
// import { getKey, hasKey } from 'C:/Users/Business/APIKeyManager/src';

// Check if key exists before making API calls
async function checkIntegrationReady(service: string): Promise<boolean> {
  return await hasKey(service);
}

// Retrieve key for API calls
async function getApiKey(service: string): Promise<string | null> {
  const key = await getKey(service);
  if (!key) {
    throw new Error(`API key not configured for ${service}. Please add it in Settings > API Keys.`);
  }
  return key;
}
```

### Service Names for API Keys

| Integration | Service Name | Used By |
|-------------|--------------|---------|
| Canva | `canva` | 9.1 Canva Integration |
| Unsplash | `unsplash` | 9.2 Stock Photo APIs |
| Pexels | `pexels` | 9.2 Stock Photo APIs |
| Google Fonts | `google_fonts` | 9.3 Google Fonts API |
| Companies House | `companies_house` | 9.4 External Registries |
| ABN Lookup | `abn_lookup` | 9.4 External Registries |

### Error Handling for Missing Keys

```typescript
import { getKey, hasKey } from '@brandkit/api-keys';

async function makeUnsplashRequest(query: string) {
  const key = await getKey('unsplash');

  if (!key) {
    return {
      success: false,
      error: 'API_KEY_MISSING',
      message: 'Unsplash API key not configured. Add it in Settings > API Keys.',
      configUrl: '/settings/api-keys'
    };
  }

  // Proceed with API call
  const response = await fetch(`https://api.unsplash.com/search/photos?query=${query}`, {
    headers: { 'Authorization': `Client-ID ${key}` }
  });

  return { success: true, data: await response.json() };
}
```

---

## 9.1 Canva Integration

### Objective
Sync brand visual assets (colours, typography, logos) to Canva Brand Kit, allowing users to use their brand in Canva designs.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\integrations\canva.ts`

### Canva API Reference
- API Docs: https://www.canva.dev/docs/connect/
- Existing code may need adjustment (noted in MASTER-PLAN)

### Core Interfaces

```typescript
interface CanvaConnection {
  connected: boolean;
  brand_kit_id?: string;
  access_token?: string;
  refresh_token?: string;
  expires_at?: string;
  last_sync?: string;
  user_id?: string;
}

interface CanvaSyncRequest {
  brandPath: string;
  items: CanvaSyncItem[];
}

type CanvaSyncItem = 'colours' | 'typography' | 'logos';

interface CanvaSyncResult {
  success: boolean;
  synced_items: CanvaSyncItem[];
  failed_items: { item: CanvaSyncItem; error: string }[];
  brand_kit_url?: string;
}

interface CanvaColourPayload {
  brand_colors: {
    name: string;
    hex: string;
  }[];
}

interface CanvaFontPayload {
  brand_fonts: {
    heading?: string;
    body?: string;
    accent?: string;
  };
}

interface CanvaLogoPayload {
  logos: {
    file: string;    // File path or base64
    type: 'primary' | 'transparent' | 'dark_background';
  }[];
}
```

### Core Functions

```typescript
// Check Canva connection status
export async function getCanvaConnection(
  brandPath: string
): Promise<CanvaConnection>

// Initiate OAuth flow
export async function initiateCanvaOAuth(
  brandPath: string,
  redirectUri: string
): Promise<{ authUrl: string }>

// Complete OAuth flow
export async function completeCanvaOAuth(
  brandPath: string,
  code: string
): Promise<CanvaConnection>

// Refresh access token
export async function refreshCanvaToken(
  brandPath: string
): Promise<CanvaConnection>

// Sync brand assets to Canva
export async function syncToCanva(
  request: CanvaSyncRequest
): Promise<CanvaSyncResult>

// Sync colours
export async function syncColoursToCanva(
  brandPath: string,
  connection: CanvaConnection
): Promise<{ success: boolean; error?: string }>

// Sync typography
export async function syncTypographyToCanva(
  brandPath: string,
  connection: CanvaConnection
): Promise<{ success: boolean; error?: string; warning?: string }>

// Sync logos
export async function syncLogosToCanva(
  brandPath: string,
  connection: CanvaConnection
): Promise<{ success: boolean; uploaded: number; failed: number }>

// Disconnect Canva
export async function disconnectCanva(
  brandPath: string
): Promise<void>
```

### Canva Sync Implementation

```typescript
export async function syncToCanva(
  request: CanvaSyncRequest
): Promise<CanvaSyncResult> {
  const { brandPath, items } = request;

  // 1. Check connection
  let connection = await getCanvaConnection(brandPath);
  if (!connection.connected) {
    throw new Error('Canva not connected. Please authenticate first.');
  }

  // 2. Refresh token if needed
  if (isTokenExpired(connection.expires_at)) {
    connection = await refreshCanvaToken(brandPath);
  }

  const synced: CanvaSyncItem[] = [];
  const failed: { item: CanvaSyncItem; error: string }[] = [];

  // 3. Sync each requested item
  for (const item of items) {
    try {
      switch (item) {
        case 'colours':
          const colourResult = await syncColoursToCanva(brandPath, connection);
          if (colourResult.success) synced.push('colours');
          else failed.push({ item: 'colours', error: colourResult.error! });
          break;

        case 'typography':
          const fontResult = await syncTypographyToCanva(brandPath, connection);
          if (fontResult.success) synced.push('typography');
          else failed.push({ item: 'typography', error: fontResult.error! });
          break;

        case 'logos':
          const logoResult = await syncLogosToCanva(brandPath, connection);
          if (logoResult.success) synced.push('logos');
          else failed.push({ item: 'logos', error: `${logoResult.failed} of ${logoResult.uploaded + logoResult.failed} failed` });
          break;
      }
    } catch (error) {
      failed.push({ item, error: error.message });
    }
  }

  // 4. Update sync status
  await updateCanvaSyncStatus(brandPath, synced);

  return {
    success: failed.length === 0,
    synced_items: synced,
    failed_items: failed,
    brand_kit_url: `https://www.canva.com/brand/${connection.brand_kit_id}`
  };
}

async function syncColoursToCanva(
  brandPath: string,
  connection: CanvaConnection
): Promise<{ success: boolean; error?: string }> {
  // 1. Load colour data from visual/colours section
  const coloursData = await loadSection(brandPath, 'visual/colours');
  if (!coloursData?.palettes) {
    return { success: false, error: 'No colour data found' };
  }

  // 2. Transform to Canva format
  const payload: CanvaColourPayload = {
    brand_colors: []
  };

  for (const palette of coloursData.palettes) {
    for (const colour of palette.colours || []) {
      payload.brand_colors.push({
        name: colour.name,
        hex: colour.hex.replace('#', '')
      });
    }
  }

  // 3. Call Canva API
  const apiKey = await getAPIKey('canva');
  const response = await fetch(`https://api.canva.com/v1/brand-kits/${connection.brand_kit_id}/colors`, {
    method: 'PUT',
    headers: {
      'Authorization': `Bearer ${connection.access_token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(payload)
  });

  if (!response.ok) {
    const error = await response.json();
    return { success: false, error: error.message };
  }

  return { success: true };
}

async function syncTypographyToCanva(
  brandPath: string,
  connection: CanvaConnection
): Promise<{ success: boolean; error?: string; warning?: string }> {
  // 1. Load typography data
  const typographyData = await loadSection(brandPath, 'visual/typography');
  if (!typographyData?.fonts) {
    return { success: false, error: 'No typography data found' };
  }

  // 2. Check Canva font availability
  const canvaFonts = await getCanvaAvailableFonts();
  const payload: CanvaFontPayload = { brand_fonts: {} };
  let warning: string | undefined;

  for (const font of typographyData.fonts) {
    const canvaFont = findCanvaFont(font.family, canvaFonts);
    if (canvaFont) {
      payload.brand_fonts[font.role] = canvaFont;
    } else {
      // Map to closest available font
      const fallback = findClosestCanvaFont(font.family, font.fallback, canvaFonts);
      payload.brand_fonts[font.role] = fallback;
      warning = `${font.family} not available in Canva, using ${fallback}`;
    }
  }

  // 3. Call Canva API
  const response = await fetch(`https://api.canva.com/v1/brand-kits/${connection.brand_kit_id}/fonts`, {
    method: 'PUT',
    headers: {
      'Authorization': `Bearer ${connection.access_token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(payload)
  });

  if (!response.ok) {
    const error = await response.json();
    return { success: false, error: error.message };
  }

  return { success: true, warning };
}
```

### Connection Storage

**File:** `BrandData/{brand}/_config/integrations.json`

```json
{
  "canva": {
    "connected": true,
    "brand_kit_id": "abc123",
    "access_token": "...",
    "refresh_token": "...",
    "expires_at": "2026-01-14T10:30:00Z",
    "last_sync": "2026-01-13T10:30:00Z",
    "synced_items": ["colours", "typography", "logos"],
    "user_id": "user_xyz"
  }
}
```

### Tasks

- [ ] Create `lib/server/integrations/canva.ts`
- [ ] Implement OAuth flow (initiate, complete, refresh)
- [ ] Implement colour sync to Canva
- [ ] Implement typography sync with font mapping
- [ ] Implement logo upload to Canva
- [ ] Handle Canva font availability checking
- [ ] Store connection and sync status
- [ ] Add error handling for API failures

### Dependencies
- Phase 1.4 API Keys Manager
- Phase 7 Visual Pathway (completed visual sections)
- Canva API credentials

---

## 9.2 Stock Photo APIs

### Objective
Integrate Unsplash and Pexels APIs for the ImageBrowser utility app.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\integrations\stock-photos.ts`

### API References
- Unsplash: https://unsplash.com/documentation
- Pexels: https://www.pexels.com/api/documentation/

### Core Interfaces

```typescript
type PhotoProvider = 'unsplash' | 'pexels';

interface PhotoSearchParams {
  query: string;
  page?: number;
  per_page?: number;
  orientation?: 'landscape' | 'portrait' | 'squarish';
  colour?: string;           // Filter by colour
}

interface PhotoResult {
  id: string;
  provider: PhotoProvider;
  url: string;               // Full size URL
  thumbnail: string;         // Preview URL
  width: number;
  height: number;
  photographer: string;
  photographer_url: string;
  download_url: string;      // Direct download link
  licence: string;
}

interface PhotoSearchResult {
  provider: PhotoProvider;
  total: number;
  page: number;
  per_page: number;
  photos: PhotoResult[];
}

interface DownloadResult {
  success: boolean;
  localPath: string;
  attribution: string;
}
```

### Core Functions

```typescript
// Search Unsplash
export async function searchUnsplash(
  params: PhotoSearchParams
): Promise<PhotoSearchResult>

// Search Pexels
export async function searchPexels(
  params: PhotoSearchParams
): Promise<PhotoSearchResult>

// Search both providers
export async function searchAllProviders(
  params: PhotoSearchParams
): Promise<PhotoSearchResult[]>

// Download photo to brand folder
export async function downloadPhoto(
  photo: PhotoResult,
  brandPath: string,
  filename?: string
): Promise<DownloadResult>

// Get attribution text for photo
export function getAttribution(photo: PhotoResult): string

// Track photo usage (for compliance)
export async function trackPhotoUsage(
  brandPath: string,
  photo: PhotoResult
): Promise<void>
```

### Unsplash Implementation

```typescript
export async function searchUnsplash(
  params: PhotoSearchParams
): Promise<PhotoSearchResult> {
  const apiKey = await getAPIKey('unsplash');
  if (!apiKey) {
    throw new Error('Unsplash API key not configured');
  }

  const { query, page = 1, per_page = 20, orientation } = params;

  const url = new URL('https://api.unsplash.com/search/photos');
  url.searchParams.set('query', query);
  url.searchParams.set('page', String(page));
  url.searchParams.set('per_page', String(per_page));
  if (orientation) url.searchParams.set('orientation', orientation);

  const response = await fetch(url.toString(), {
    headers: {
      'Authorization': `Client-ID ${apiKey}`
    }
  });

  if (!response.ok) {
    throw new Error(`Unsplash API error: ${response.status}`);
  }

  const data = await response.json();

  return {
    provider: 'unsplash',
    total: data.total,
    page,
    per_page,
    photos: data.results.map(photo => ({
      id: photo.id,
      provider: 'unsplash',
      url: photo.urls.full,
      thumbnail: photo.urls.thumb,
      width: photo.width,
      height: photo.height,
      photographer: photo.user.name,
      photographer_url: photo.user.links.html,
      download_url: photo.links.download,
      licence: 'Unsplash License'
    }))
  };
}
```

### Pexels Implementation

```typescript
export async function searchPexels(
  params: PhotoSearchParams
): Promise<PhotoSearchResult> {
  const apiKey = await getAPIKey('pexels');
  if (!apiKey) {
    throw new Error('Pexels API key not configured');
  }

  const { query, page = 1, per_page = 20, orientation } = params;

  const url = new URL('https://api.pexels.com/v1/search');
  url.searchParams.set('query', query);
  url.searchParams.set('page', String(page));
  url.searchParams.set('per_page', String(per_page));
  if (orientation) url.searchParams.set('orientation', orientation);

  const response = await fetch(url.toString(), {
    headers: {
      'Authorization': apiKey
    }
  });

  if (!response.ok) {
    throw new Error(`Pexels API error: ${response.status}`);
  }

  const data = await response.json();

  return {
    provider: 'pexels',
    total: data.total_results,
    page,
    per_page,
    photos: data.photos.map(photo => ({
      id: String(photo.id),
      provider: 'pexels',
      url: photo.src.original,
      thumbnail: photo.src.tiny,
      width: photo.width,
      height: photo.height,
      photographer: photo.photographer,
      photographer_url: photo.photographer_url,
      download_url: photo.src.original,
      licence: 'Pexels License'
    }))
  };
}
```

### Photo Usage Tracking

```typescript
// Track photo usage for attribution/compliance
export async function trackPhotoUsage(
  brandPath: string,
  photo: PhotoResult
): Promise<void> {
  const usagePath = path.join(brandPath, '_meta', 'photo-usage.json');

  let usage: PhotoUsage[] = [];
  if (await fs.exists(usagePath)) {
    usage = JSON.parse(await fs.readFile(usagePath, 'utf-8'));
  }

  usage.push({
    id: photo.id,
    provider: photo.provider,
    photographer: photo.photographer,
    photographer_url: photo.photographer_url,
    licence: photo.licence,
    downloaded_at: new Date().toISOString(),
    attribution: getAttribution(photo)
  });

  await fs.writeFile(usagePath, JSON.stringify(usage, null, 2));
}

export function getAttribution(photo: PhotoResult): string {
  if (photo.provider === 'unsplash') {
    return `Photo by ${photo.photographer} on Unsplash`;
  } else if (photo.provider === 'pexels') {
    return `Photo by ${photo.photographer} on Pexels`;
  }
  return `Photo by ${photo.photographer}`;
}
```

### Tasks

- [ ] Create `lib/server/integrations/stock-photos.ts`
- [ ] Implement Unsplash search
- [ ] Implement Pexels search
- [ ] Implement multi-provider search
- [ ] Implement photo download to brand folder
- [ ] Add attribution generation
- [ ] Add usage tracking for compliance
- [ ] Handle API rate limits

### Dependencies
- Phase 1.4 API Keys Manager
- Phase 7.6 Photography Handler (ImageBrowser)

---

## 9.3 Google Fonts API

### Objective
Integrate Google Fonts API for font search, preview, and metadata in TypographyBuilder.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\integrations\google-fonts.ts`

### API Reference
- https://developers.google.com/fonts/docs/developer_api

### Core Interfaces

```typescript
interface GoogleFont {
  family: string;
  variants: string[];        // ["regular", "italic", "700", "700italic"]
  subsets: string[];         // ["latin", "latin-ext", "cyrillic"]
  category: 'serif' | 'sans-serif' | 'display' | 'handwriting' | 'monospace';
  files: Record<string, string>;  // { "regular": "url...", "700": "url..." }
  popularity?: number;       // Ranking
}

interface FontSearchParams {
  query?: string;
  category?: string;
  sort?: 'alpha' | 'popularity' | 'trending';
  limit?: number;
}

interface FontPairing {
  heading: string;
  body: string;
  reason: string;
}
```

### Core Functions

```typescript
// Get all Google Fonts (cached)
export async function getAllGoogleFonts(): Promise<GoogleFont[]>

// Search fonts
export async function searchFonts(
  params: FontSearchParams
): Promise<GoogleFont[]>

// Get font details
export async function getFontDetails(
  family: string
): Promise<GoogleFont | null>

// Get font pairing suggestions
export async function getFontPairings(
  headingFont: string
): Promise<FontPairing[]>

// Get font CSS embed URL
export function getFontEmbedUrl(
  family: string,
  weights?: number[]
): string

// Check if font is available
export async function isFontAvailable(
  family: string
): Promise<boolean>

// Get popular fonts by category
export async function getPopularFonts(
  category: string,
  limit?: number
): Promise<GoogleFont[]>
```

### Implementation

```typescript
// Cache for font list
let fontsCache: GoogleFont[] | null = null;
let cacheTime: number = 0;
const CACHE_DURATION = 24 * 60 * 60 * 1000; // 24 hours

export async function getAllGoogleFonts(): Promise<GoogleFont[]> {
  // Check cache
  if (fontsCache && Date.now() - cacheTime < CACHE_DURATION) {
    return fontsCache;
  }

  const apiKey = await getAPIKey('google_fonts');
  if (!apiKey) {
    throw new Error('Google Fonts API key not configured');
  }

  const response = await fetch(
    `https://www.googleapis.com/webfonts/v1/webfonts?key=${apiKey}&sort=popularity`
  );

  if (!response.ok) {
    throw new Error(`Google Fonts API error: ${response.status}`);
  }

  const data = await response.json();
  fontsCache = data.items.map((font, index) => ({
    ...font,
    popularity: index + 1
  }));
  cacheTime = Date.now();

  return fontsCache;
}

export async function searchFonts(
  params: FontSearchParams
): Promise<GoogleFont[]> {
  const { query, category, sort = 'popularity', limit = 50 } = params;

  let fonts = await getAllGoogleFonts();

  // Filter by query
  if (query) {
    const q = query.toLowerCase();
    fonts = fonts.filter(f =>
      f.family.toLowerCase().includes(q)
    );
  }

  // Filter by category
  if (category) {
    fonts = fonts.filter(f => f.category === category);
  }

  // Sort
  if (sort === 'alpha') {
    fonts.sort((a, b) => a.family.localeCompare(b.family));
  } else if (sort === 'popularity') {
    fonts.sort((a, b) => (a.popularity || 999) - (b.popularity || 999));
  }

  return fonts.slice(0, limit);
}

// Predefined font pairings based on design best practices
const FONT_PAIRINGS: Record<string, FontPairing[]> = {
  'Playfair Display': [
    { heading: 'Playfair Display', body: 'Source Sans Pro', reason: 'Classic serif with clean sans-serif' },
    { heading: 'Playfair Display', body: 'Lato', reason: 'Elegant heading with friendly body' },
    { heading: 'Playfair Display', body: 'Raleway', reason: 'Traditional meets modern' }
  ],
  'Montserrat': [
    { heading: 'Montserrat', body: 'Open Sans', reason: 'Modern geometric with readable body' },
    { heading: 'Montserrat', body: 'Merriweather', reason: 'Bold heading with serif body for contrast' },
    { heading: 'Montserrat', body: 'Roboto', reason: 'Clean and professional' }
  ],
  'Roboto': [
    { heading: 'Roboto', body: 'Roboto', reason: 'Consistent, versatile' },
    { heading: 'Roboto', body: 'Open Sans', reason: 'Similar but softer body' },
    { heading: 'Roboto Slab', body: 'Roboto', reason: 'Slab heading for emphasis' }
  ]
};

export async function getFontPairings(
  headingFont: string
): Promise<FontPairing[]> {
  // Check predefined pairings
  if (FONT_PAIRINGS[headingFont]) {
    return FONT_PAIRINGS[headingFont];
  }

  // Generate suggestions based on category
  const font = await getFontDetails(headingFont);
  if (!font) return [];

  const allFonts = await getAllGoogleFonts();

  // If heading is serif, suggest sans-serif body (and vice versa)
  const bodyCategory = font.category === 'serif' ? 'sans-serif' : 'serif';
  const suggestions = allFonts
    .filter(f => f.category === bodyCategory)
    .slice(0, 5)
    .map(f => ({
      heading: headingFont,
      body: f.family,
      reason: `${font.category} heading with ${bodyCategory} body for contrast`
    }));

  return suggestions;
}

export function getFontEmbedUrl(
  family: string,
  weights: number[] = [400, 700]
): string {
  const weightStr = weights.join(';');
  const familyEncoded = encodeURIComponent(family);
  return `https://fonts.googleapis.com/css2?family=${familyEncoded}:wght@${weightStr}&display=swap`;
}
```

### Tasks

- [ ] Create `lib/server/integrations/google-fonts.ts`
- [ ] Implement font list fetching with caching
- [ ] Implement font search and filtering
- [ ] Add font pairing suggestions (predefined + algorithm)
- [ ] Generate embed URLs
- [ ] Handle API rate limits
- [ ] Cache management

### Dependencies
- Phase 1.4 API Keys Manager
- Phase 7.4 TypographyBuilder Integration

---

## 9.4 External Registries

### Objective
Integrate with business registries (Companies House UK, ABN Australia) to auto-fill legal entity information.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\integrations\registries.ts`

### Registry APIs
- Companies House (UK): https://developer.company-information.service.gov.uk/
- ABN Lookup (AU): https://abr.business.gov.au/json/

### Core Interfaces

```typescript
type RegistryProvider = 'companies_house' | 'abn_lookup';

interface CompanySearchParams {
  query: string;
  jurisdiction: string;    // 'UK', 'AU'
}

interface CompanyResult {
  provider: RegistryProvider;
  company_number: string;
  company_name: string;
  company_type: string;
  status: string;
  jurisdiction: string;
  registered_address?: Address;
  incorporation_date?: string;
  raw_data: object;        // Full API response
}

interface Address {
  line_1?: string;
  line_2?: string;
  city?: string;
  region?: string;
  postcode?: string;
  country?: string;
}
```

### Core Functions

```typescript
// Search company by name
export async function searchCompany(
  params: CompanySearchParams
): Promise<CompanyResult[]>

// Lookup by company number
export async function lookupCompanyByNumber(
  number: string,
  jurisdiction: string
): Promise<CompanyResult | null>

// Search Companies House (UK)
export async function searchCompaniesHouse(
  query: string
): Promise<CompanyResult[]>

// Lookup ABN (Australia)
export async function lookupABN(
  abn: string
): Promise<CompanyResult | null>

// Search ABN by name (Australia)
export async function searchABNByName(
  name: string
): Promise<CompanyResult[]>

// Convert registry data to legal section format
export function mapToLegalSection(
  company: CompanyResult
): Partial<LegalEntityData>
```

### Companies House Implementation

```typescript
export async function searchCompaniesHouse(
  query: string
): Promise<CompanyResult[]> {
  const apiKey = await getAPIKey('companies_house');
  if (!apiKey) {
    throw new Error('Companies House API key not configured');
  }

  const response = await fetch(
    `https://api.company-information.service.gov.uk/search/companies?q=${encodeURIComponent(query)}`,
    {
      headers: {
        'Authorization': `Basic ${Buffer.from(apiKey + ':').toString('base64')}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Companies House API error: ${response.status}`);
  }

  const data = await response.json();

  return data.items.map(item => ({
    provider: 'companies_house',
    company_number: item.company_number,
    company_name: item.title,
    company_type: mapCompanyType(item.company_type),
    status: item.company_status,
    jurisdiction: 'UK',
    registered_address: item.address_snippet ? parseAddressSnippet(item.address_snippet) : undefined,
    incorporation_date: item.date_of_creation,
    raw_data: item
  }));
}

export async function getCompaniesHouseProfile(
  companyNumber: string
): Promise<CompanyResult | null> {
  const apiKey = await getAPIKey('companies_house');

  const response = await fetch(
    `https://api.company-information.service.gov.uk/company/${companyNumber}`,
    {
      headers: {
        'Authorization': `Basic ${Buffer.from(apiKey + ':').toString('base64')}`
      }
    }
  );

  if (response.status === 404) return null;
  if (!response.ok) {
    throw new Error(`Companies House API error: ${response.status}`);
  }

  const data = await response.json();

  return {
    provider: 'companies_house',
    company_number: data.company_number,
    company_name: data.company_name,
    company_type: mapCompanyType(data.type),
    status: data.company_status,
    jurisdiction: 'UK',
    registered_address: data.registered_office_address ? {
      line_1: data.registered_office_address.address_line_1,
      line_2: data.registered_office_address.address_line_2,
      city: data.registered_office_address.locality,
      region: data.registered_office_address.region,
      postcode: data.registered_office_address.postal_code,
      country: data.registered_office_address.country
    } : undefined,
    incorporation_date: data.date_of_creation,
    raw_data: data
  };
}
```

### ABN Lookup Implementation

```typescript
export async function lookupABN(
  abn: string
): Promise<CompanyResult | null> {
  // ABN lookup is free, no API key required
  // Clean ABN (remove spaces)
  const cleanABN = abn.replace(/\s/g, '');

  const response = await fetch(
    `https://abr.business.gov.au/json/AbnDetails.aspx?abn=${cleanABN}&callback=`
  );

  if (!response.ok) {
    throw new Error(`ABN Lookup error: ${response.status}`);
  }

  const text = await response.text();
  // ABN API returns JSONP, need to extract JSON
  const jsonMatch = text.match(/callback\((.*)\)/);
  if (!jsonMatch) {
    throw new Error('Invalid ABN response format');
  }

  const data = JSON.parse(jsonMatch[1]);

  if (data.Message) {
    // Error message returned
    return null;
  }

  return {
    provider: 'abn_lookup',
    company_number: data.Abn,
    company_name: data.EntityName || data.BusinessName?.[0]?.Name,
    company_type: data.EntityTypeName,
    status: data.AbnStatus,
    jurisdiction: 'AU',
    registered_address: data.AddressState ? {
      region: data.AddressState,
      postcode: data.AddressPostcode
    } : undefined,
    raw_data: data
  };
}

export async function searchABNByName(
  name: string
): Promise<CompanyResult[]> {
  const response = await fetch(
    `https://abr.business.gov.au/json/MatchingNames.aspx?name=${encodeURIComponent(name)}&callback=`
  );

  if (!response.ok) {
    throw new Error(`ABN search error: ${response.status}`);
  }

  const text = await response.text();
  const jsonMatch = text.match(/callback\((.*)\)/);
  if (!jsonMatch) return [];

  const data = JSON.parse(jsonMatch[1]);
  if (!data.Names) return [];

  return data.Names.map(item => ({
    provider: 'abn_lookup',
    company_number: item.Abn,
    company_name: item.Name,
    company_type: item.Type,
    status: 'Active',
    jurisdiction: 'AU',
    raw_data: item
  }));
}
```

### Mapping to Legal Section

```typescript
export function mapToLegalSection(
  company: CompanyResult
): Partial<LegalEntityData> {
  return {
    entity: {
      legal_name: company.company_name,
      company_type: company.company_type,
      company_number: company.company_number,
      jurisdiction: company.jurisdiction,
      formation_date: company.incorporation_date
    },
    addresses: company.registered_address ? {
      registered_office: company.registered_address
    } : undefined
  };
}
```

### Tasks

- [ ] Create `lib/server/integrations/registries.ts`
- [ ] Implement Companies House search and lookup
- [ ] Implement ABN lookup
- [ ] Implement ABN name search
- [ ] Map registry data to legal section format
- [ ] Handle API errors gracefully
- [ ] Add rate limiting

### Dependencies
- Phase 1.4 API Keys Manager
- Phase 6 Legal Pathway

---

## 9.5 Integration Manager

### Objective
Central management for all integrations - connection status, OAuth flows, API health, and configuration.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\integration-manager.ts`

### Core Interfaces

```typescript
type IntegrationType = 'canva' | 'unsplash' | 'pexels' | 'google_fonts' | 'companies_house';

interface IntegrationStatus {
  type: IntegrationType;
  name: string;
  connected: boolean;
  requires_oauth: boolean;
  requires_api_key: boolean;
  api_key_configured: boolean;
  last_used?: string;
  error?: string;
}

interface IntegrationConfig {
  type: IntegrationType;
  enabled: boolean;
  api_key_name?: string;
  oauth_config?: OAuthConfig;
  rate_limit?: RateLimitConfig;
}

interface OAuthConfig {
  auth_url: string;
  token_url: string;
  scopes: string[];
  client_id_env: string;
  client_secret_env: string;
}

interface RateLimitConfig {
  requests_per_minute: number;
  requests_per_day: number;
}
```

### Core Functions

```typescript
// Get status of all integrations
export async function getAllIntegrationStatus(): Promise<IntegrationStatus[]>

// Get status of specific integration
export async function getIntegrationStatus(
  type: IntegrationType
): Promise<IntegrationStatus>

// Check if integration is ready to use
export async function isIntegrationReady(
  type: IntegrationType
): Promise<boolean>

// Get integration config
export function getIntegrationConfig(
  type: IntegrationType
): IntegrationConfig

// Initialize OAuth flow
export async function initiateOAuth(
  type: IntegrationType,
  brandPath: string,
  redirectUri: string
): Promise<{ authUrl: string }>

// Complete OAuth flow
export async function completeOAuth(
  type: IntegrationType,
  brandPath: string,
  code: string
): Promise<void>

// Test integration connectivity
export async function testIntegration(
  type: IntegrationType
): Promise<{ success: boolean; error?: string }>
```

### Integration Configuration

```typescript
const INTEGRATION_CONFIGS: Record<IntegrationType, IntegrationConfig> = {
  canva: {
    type: 'canva',
    enabled: true,
    requires_oauth: true,
    api_key_name: 'canva',
    oauth_config: {
      auth_url: 'https://www.canva.com/api/oauth/authorize',
      token_url: 'https://www.canva.com/api/oauth/token',
      scopes: ['brand:read', 'brand:write'],
      client_id_env: 'CANVA_CLIENT_ID',
      client_secret_env: 'CANVA_CLIENT_SECRET'
    },
    rate_limit: {
      requests_per_minute: 60,
      requests_per_day: 5000
    }
  },

  unsplash: {
    type: 'unsplash',
    enabled: true,
    requires_oauth: false,
    api_key_name: 'unsplash',
    rate_limit: {
      requests_per_minute: 50,
      requests_per_day: 5000
    }
  },

  pexels: {
    type: 'pexels',
    enabled: true,
    requires_oauth: false,
    api_key_name: 'pexels',
    rate_limit: {
      requests_per_minute: 200,
      requests_per_day: 20000
    }
  },

  google_fonts: {
    type: 'google_fonts',
    enabled: true,
    requires_oauth: false,
    api_key_name: 'google_fonts',
    rate_limit: {
      requests_per_minute: 100,
      requests_per_day: 10000
    }
  },

  companies_house: {
    type: 'companies_house',
    enabled: true,
    requires_oauth: false,
    api_key_name: 'companies_house',
    rate_limit: {
      requests_per_minute: 600,
      requests_per_day: 10000
    }
  }
};
```

### Implementation

```typescript
export async function getAllIntegrationStatus(): Promise<IntegrationStatus[]> {
  const statuses: IntegrationStatus[] = [];

  for (const [type, config] of Object.entries(INTEGRATION_CONFIGS)) {
    statuses.push(await getIntegrationStatus(type as IntegrationType));
  }

  return statuses;
}

export async function getIntegrationStatus(
  type: IntegrationType
): Promise<IntegrationStatus> {
  const config = INTEGRATION_CONFIGS[type];
  if (!config) {
    throw new Error(`Unknown integration: ${type}`);
  }

  const status: IntegrationStatus = {
    type,
    name: formatIntegrationName(type),
    connected: false,
    requires_oauth: config.requires_oauth || false,
    requires_api_key: !!config.api_key_name,
    api_key_configured: false
  };

  // Check API key
  if (config.api_key_name) {
    const hasKey = await hasAPIKey(config.api_key_name);
    status.api_key_configured = hasKey;
    status.connected = hasKey && !config.requires_oauth;
  }

  // For OAuth integrations, need to check per-brand connection
  // This would need brand context to check properly

  return status;
}

export async function isIntegrationReady(
  type: IntegrationType
): Promise<boolean> {
  const status = await getIntegrationStatus(type);
  return status.connected && !status.error;
}

export async function testIntegration(
  type: IntegrationType
): Promise<{ success: boolean; error?: string }> {
  try {
    switch (type) {
      case 'unsplash':
        await searchUnsplash({ query: 'test', per_page: 1 });
        return { success: true };

      case 'pexels':
        await searchPexels({ query: 'test', per_page: 1 });
        return { success: true };

      case 'google_fonts':
        const fonts = await getAllGoogleFonts();
        return { success: fonts.length > 0 };

      case 'companies_house':
        // Test with a known company number
        const company = await getCompaniesHouseProfile('00000006'); // Test company
        return { success: company !== null };

      case 'canva':
        // Canva requires OAuth, cannot test without brand context
        return { success: false, error: 'Canva requires per-brand OAuth authentication' };

      default:
        return { success: false, error: 'Unknown integration' };
    }
  } catch (error) {
    return { success: false, error: error.message };
  }
}
```

### Tasks

- [ ] Create `lib/server/integration-manager.ts`
- [ ] Define integration configurations
- [ ] Implement status checking for all integrations
- [ ] Implement connectivity testing
- [ ] Add OAuth flow management
- [ ] Add rate limiting tracking

### Dependencies
- Phase 1.4 API Keys Manager
- All integration modules (9.1-9.4)

---

## Implementation Order

```
9.5 Integration Manager (base)
         ↓
    ┌────┴────┬────────────┬────────────┐
    ↓         ↓            ↓            ↓
9.1 Canva  9.2 Stock    9.3 Google   9.4 External
           Photos       Fonts        Registries
```

**Sequence:**
1. Build 9.5 first (provides base infrastructure)
2. Build 9.2 and 9.3 in parallel (simpler API key auth)
3. Build 9.4 (registry lookups)
4. Build 9.1 last (OAuth flow more complex)

---

## API Keys Required

| Integration | API Key | How to Get |
|-------------|---------|------------|
| **Canva** | OAuth Client ID + Secret | https://www.canva.dev/docs/connect/ |
| **Unsplash** | Access Key | https://unsplash.com/developers |
| **Pexels** | API Key | https://www.pexels.com/api/ |
| **Google Fonts** | API Key | https://console.cloud.google.com/ |
| **Companies House** | API Key | https://developer.company-information.service.gov.uk/ |

All keys stored securely using API Keys Manager (Phase 1.4).

---

## Verification Plan

### 9.1 Canva Integration
- [ ] OAuth flow completes successfully
- [ ] Colours sync to Canva brand kit
- [ ] Fonts sync (with fallback for unavailable fonts)
- [ ] Logos upload successfully
- [ ] Connection status stored correctly

### 9.2 Stock Photo APIs
- [ ] Unsplash search returns results
- [ ] Pexels search returns results
- [ ] Photo download works
- [ ] Attribution generated correctly
- [ ] Usage tracked

### 9.3 Google Fonts API
- [ ] Font list loads and caches
- [ ] Search filters work
- [ ] Font pairings returned
- [ ] Embed URLs generated correctly

### 9.4 External Registries
- [ ] Companies House search works
- [ ] Companies House lookup by number works
- [ ] ABN lookup works
- [ ] Data maps to legal section format

### 9.5 Integration Manager
- [ ] All integration statuses returned
- [ ] API key presence detected
- [ ] Connectivity tests pass (when keys configured)

---

## Files to Create (Summary)

| File | Purpose |
|------|---------|
| `lib/server/integrations/canva.ts` | Canva Brand Kit sync |
| `lib/server/integrations/stock-photos.ts` | Unsplash/Pexels integration |
| `lib/server/integrations/google-fonts.ts` | Google Fonts API |
| `lib/server/integrations/registries.ts` | Companies House, ABN lookup |
| `lib/server/integration-manager.ts` | Central integration management |
| `lib/server/types/integrations.ts` | Integration type definitions |

---

## Notes

- All API keys managed by Phase 1.4 API Keys Manager
- Canva is the only OAuth integration (others use API keys)
- Stock photos track usage for attribution compliance
- Google Fonts response is cached (24 hours)
- Registry lookups may have rate limits
- Consider adding more registries in future (US, NZ, etc.)
- Two-way Canva sync is future consideration (currently one-way)

