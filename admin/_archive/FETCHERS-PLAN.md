# Fetchers Service Plan

> **Purpose:** Get data from external sources.
> **Level:** Processing Layer
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Fetchers handle all external data retrieval. They make HTTP requests, download files, and call external APIs. They return raw data that other services (Parsers, Archivist) then process.

**Key Principle:** Fetchers get external data. They don't process or store it.

---

## Fetcher Types

| Fetcher | Source | Output | Purpose |
|---------|--------|--------|---------|
| **WebFetcher** | URLs | HTML/text | Fetch web pages |
| **ImageFetcher** | Image URLs | Buffer | Download images |
| **APIFetcher** | REST APIs | JSON response | Call external APIs |
| **AIFetcher** | AI APIs | AI response | Call Claude/OpenAI |

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Make HTTP requests | Process/parse responses (Parsers do) |
| Download files | Store files (Archivist/Writer do) |
| Handle timeouts | Decide what to do with data |
| Report failures to FailSys | Handle retries (FailSys does) |
| Return raw response | Cache responses (Librarian does) |

---

## WebFetcher

Fetches web pages and extracts content.

**Note:** Much of this exists in `website-fetcher.ts` from Phase 2.

### API

```typescript
// lib/server/fetchers/web-fetcher.ts

interface WebFetchOptions {
  timeout?: number;       // Default: 30000ms
  userAgent?: string;
  followRedirects?: boolean;
  maxRedirects?: number;
}

interface WebFetchResult {
  success: boolean;
  url: string;
  finalUrl?: string;      // After redirects
  statusCode?: number;
  contentType?: string;
  content?: string;
  headers?: Record<string, string>;
  error?: string;
}

function fetchPage(url: string, options?: WebFetchOptions): Promise<WebFetchResult>;
function fetchPages(urls: string[], options?: WebFetchOptions): Promise<WebFetchResult[]>;
function isValidUrl(url: string): boolean;
function normalizeUrl(url: string): string;
```

### Current Implementation

Already built in Phase 2:
- `fetchWebsite()`
- `fetchSinglePage()`
- `fetchPage()`
- `normalizeUrl()`

May need wrapper for consistent interface.

---

## ImageFetcher

Downloads images from URLs.

### API

```typescript
// lib/server/fetchers/image-fetcher.ts

interface ImageFetchOptions {
  timeout?: number;
  maxSize?: number;       // Max bytes to download
  allowedTypes?: string[]; // ['image/png', 'image/jpeg', etc.]
}

interface ImageFetchResult {
  success: boolean;
  url: string;
  buffer?: Buffer;
  contentType?: string;
  size?: number;
  error?: string;
}

function fetchImage(url: string, options?: ImageFetchOptions): Promise<ImageFetchResult>;
function fetchImages(urls: string[], options?: ImageFetchOptions): Promise<ImageFetchResult[]>;
function isImageUrl(url: string): boolean;
```

### Usage

```typescript
const result = await ImageFetcher.fetchImage('https://example.com/logo.png', {
  maxSize: 10 * 1024 * 1024  // 10MB max
});

if (result.success) {
  // Pass to ImageParser for processing
  const parsed = await ImageParser.parseImage(result.buffer, {
    maxWidth: 512
  });
}
```

---

## APIFetcher

Calls external REST APIs.

### API

```typescript
// lib/server/fetchers/api-fetcher.ts

interface APIFetchOptions {
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE';
  headers?: Record<string, string>;
  body?: unknown;
  timeout?: number;
  retryOnRateLimit?: boolean;
}

interface APIFetchResult<T = unknown> {
  success: boolean;
  url: string;
  statusCode?: number;
  data?: T;
  headers?: Record<string, string>;
  error?: string;
  rateLimited?: boolean;
}

function fetchAPI<T>(url: string, options?: APIFetchOptions): Promise<APIFetchResult<T>>;
function fetchAPIWithAuth<T>(url: string, apiKey: string, options?: APIFetchOptions): Promise<APIFetchResult<T>>;
```

### Usage Examples

```typescript
// Companies House API (already built in Phase 6)
const result = await APIFetcher.fetchAPIWithAuth<CompanySearchResponse>(
  'https://api.company-information.service.gov.uk/search/companies',
  process.env.COMPANIES_HOUSE_API_KEY,
  { method: 'GET' }
);

// Unsplash API (Phase 9)
const photos = await APIFetcher.fetchAPIWithAuth<UnsplashResponse>(
  'https://api.unsplash.com/search/photos?query=office',
  process.env.UNSPLASH_ACCESS_KEY
);
```

---

## AIFetcher

Calls AI APIs (Claude, OpenAI).

**Note:** This exists in `ai-client.ts` from Phase 4.

### API

```typescript
// lib/server/fetchers/ai-fetcher.ts

interface AIFetchOptions {
  provider?: 'anthropic' | 'openai';
  model?: string;
  maxTokens?: number;
  temperature?: number;
  timeout?: number;
}

interface AIFetchResult {
  success: boolean;
  content?: string;
  usage?: {
    inputTokens: number;
    outputTokens: number;
  };
  error?: string;
  rateLimited?: boolean;
}

function fetchAI(systemPrompt: string, userPrompt: string, options?: AIFetchOptions): Promise<AIFetchResult>;
function fetchAIWithSchema(systemPrompt: string, userPrompt: string, schema: JSONSchema, options?: AIFetchOptions): Promise<AIFetchResult>;
```

### Current Implementation

Already built in Phase 4.3:
- `callAI()`
- `generateSection()`

May need wrapper for consistent interface.

---

## Common Pattern

All fetchers follow the same pattern:

```typescript
interface FetchResult<T> {
  success: boolean;
  data?: T;
  error?: string;
  statusCode?: number;
  rateLimited?: boolean;
}

// Async function - makes external request
async function fetch(url: string, options?: Options): Promise<FetchResult<T>>;
```

- **Input:** URL/endpoint + options
- **Output:** Raw response + success/error
- **Side effects:** Network request only
- **State:** None (stateless)

---

## Error Handling

Fetchers catch network errors and return them:

```typescript
const result = await WebFetcher.fetchPage('https://example.com');
if (!result.success) {
  // Report to FailSys (which may retry based on rules)
  await FailSys.report({
    service: 'fetcher',
    operation: 'web-fetch',
    error: result.error,
    context: { url: 'https://example.com', statusCode: result.statusCode }
  });
}
```

### Rate Limiting

Fetchers detect rate limiting and report it:

```typescript
if (result.rateLimited) {
  // FailSys has rules for rate limit handling
  await FailSys.report({
    service: 'fetcher',
    operation: 'api-fetch',
    error: 'Rate limited',
    severity: 'medium',
    context: { retryAfter: result.headers?.['retry-after'] }
  });
}
```

---

## Implementation Notes

### File Locations

```
lib/server/fetchers/
  web-fetcher.ts     (may wrap existing website-fetcher.ts)
  image-fetcher.ts   (new)
  api-fetcher.ts     (new, companies-house uses internally)
  ai-fetcher.ts      (may wrap existing ai-client.ts)
  index.ts           (re-exports all)
```

### Dependencies

| Fetcher | Dependencies |
|---------|--------------|
| WebFetcher | `node-fetch` or built-in fetch |
| ImageFetcher | `node-fetch` or built-in fetch |
| APIFetcher | `node-fetch` or built-in fetch |
| AIFetcher | `@anthropic-ai/sdk`, `openai` |

### Size Estimate

- WebFetcher: ~50 lines wrapper (existing: 300+ lines)
- ImageFetcher: ~100 lines (new)
- APIFetcher: ~150 lines (new)
- AIFetcher: ~50 lines wrapper (existing: 200+ lines)

---

## Build Phases

### Fe1: APIFetcher
- Generic API calls
- Auth header handling
- Rate limit detection

### Fe2: ImageFetcher
- Image download
- Size limits
- Type validation

### Fe3: Fetcher Wrappers
- WebFetcher wrapper (around existing)
- AIFetcher wrapper (around existing)

---

## Testing

| Test | Description |
|------|-------------|
| Fetch valid URL | Returns content |
| Fetch invalid URL | Returns error |
| Fetch with timeout | Times out correctly |
| Fetch image | Returns buffer |
| Fetch too large | Returns error |
| API with auth | Includes auth header |
| Rate limited | Detects rate limit |
| Follow redirects | Follows correctly |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [FAILSYS-PLAN.md](./FAILSYS-PLAN.md) - Receives fetch errors
- [PARSERS-PLAN.md](./PARSERS-PLAN.md) - Processes fetched data

---

*Fetchers are the external interface - they get data from the outside world.*
