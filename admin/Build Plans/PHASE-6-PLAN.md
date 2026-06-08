# Phase 6: Legal & Business Details - Implementation Plan

> Detailed implementation plan for Phase 6 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [PHASE-5-PLAN.md](./PHASE-5-PLAN.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)
>
> **Scope Change (2026-01-15):** Simplified from complex compliance system to practical UK-focused business details and website legal templates.
>
> **Improvements Added (2026-01-15):** Caching, APIKeyManager integration, result types, manual fallback, template versioning, test planning.
>
> **Completed:** 2026-01-15 - All 54 tests passing. Companies House API verified with TESCO PLC.

---

## Overview

Phase 6 provides practical legal/business functionality for UK companies:
- Look up and verify company details via Companies House API
- Store business registration info (company number, VAT, address)
- Generate common website legal pages (cookie policy, privacy, GDPR, terms)
- Domain-specific disclaimers (wellness, ecommerce, etc.)
- Placeholder system for customisable templates

**This is NOT:** A full legal compliance system requiring lawyer review. It's practical website legal boilerplate.

---

## Design Decision: Templates vs AI vs Libraries

### Why Templates (Not AI)?

Legal content uses **pre-written templates with placeholders** - NOT AI-generated content.

| Approach | Used For | Reason |
|----------|----------|--------|
| **Templates** | Cookie policy, privacy policy, terms, GDPR notice | Legal text must be consistent, vetted, and predictable. AI-generated legal content would be a liability risk. |
| **Libraries (Phase 5)** | NOT used for core legal text | Legal documents need specific structure and wording - not mix-and-match like tone/vocabulary. |
| **AI** | NOT generating legal text | AI should never write legal documents - too risky, inconsistent, potentially wrong. |

### Content Sources

| Source | Method | Use Case |
|--------|--------|----------|
| **Templates (Phase 6)** | Pre-written with placeholders | New brands needing basic legal pages |
| **Website extraction (Phase 2)** | `fetchWebsite()` + extract `/privacy`, `/terms` | Brands with existing legal content on website |
| **Future: AI Legal App** | Separate dedicated app | AI-assisted legal content creation with human review |

**For existing brands:** Use website fetcher to extract their current legal pages, then enhance with Phase 6 templates/disclaimers.

**For new brands:** Use Phase 6 templates as starting point, customize with business details.

### Why NOT a Library System?

Phase 5 Library system works well for:
- Tone words (warm, encouraging, professional)
- Vocabulary preferences (terms to use/avoid)
- Brand characteristics

But legal content is different:
- Legal documents have **specific structure** (not interchangeable clauses)
- Wording must be **legally precise** (not creative variations)
- Templates ensure **consistency** across all brands

### Current Implementation

```
Templates (human-written)     →    Placeholders filled    →    Generated Content
├── cookie_policy.md               {{business_name}}          Ready for website
├── privacy_policy.md              {{company_number}}
├── gdpr_notice.md                 {{registered_address}}
├── terms_of_service.md            {{current_year}}
└── footer_legal.md                {{contact_email}}
```

### Disclaimers Are Different

Disclaimers DO use a library-like approach because they are:
- **Modular** - can add/remove based on domain
- **Domain-specific** - therapeutic needs different disclaimers than ecommerce
- **Placement-flexible** - some go in footer, some on dedicated pages

```
Domain Detection → Select Relevant Disclaimers → Place Appropriately
     ↓                      ↓                          ↓
therapeutic        not_medical_advice            footer + forms
                   crisis_resources              dedicated page
                   confidentiality               forms
```

| Component | Purpose |
|-----------|---------|
| **6.1 Companies House Integration** | Search/verify UK company registration |
| **6.2 Website Legal Templates** | Cookie, privacy, GDPR, terms templates |
| **6.3 Domain Disclaimers** | Industry-specific disclaimer text |
| **6.4 Placeholder System** | {{business_name}}, {{current_year}}, etc. |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| Brand folder created | Phase 1.3 | Required - brand data location |
| Domain detection | Phase 3 | Required - determines which disclaimers apply |
| Section save/load | Phase 4.2 | Required - save legal sections |

---

## 6.1 Companies House Integration

### Objective
Search and retrieve UK company information from the free Companies House API.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\companies-house.ts`

### Companies House API

**Which API:** Companies House **Public Data API** (free, read-only)
- NOT the Streaming API (real-time updates - not needed)
- NOT the Filing API (submitting documents - not needed)

**Base URL:** `https://api.company-information.service.gov.uk`

**Authentication:** Basic auth with API key (free registration at https://developer.company-information.service.gov.uk)

**Rate Limits:** 600 requests per 5 minutes (plenty for our use case)

**Key Endpoints:**
- `GET /search/companies?q={query}` - Search by name
- `GET /company/{company_number}` - Get company details
- `GET /company/{company_number}/registered-office-address` - Get address

### Core Interfaces

```typescript
interface CompanySearchResult {
  company_number: string;
  title: string;                    // Company name
  company_status: string;           // 'active', 'dissolved', etc.
  company_type: string;             // 'ltd', 'plc', etc.
  date_of_creation: string;         // YYYY-MM-DD
  address_snippet: string;          // Brief address
}

interface CompanyDetails {
  company_number: string;
  company_name: string;
  company_status: string;
  type: string;
  date_of_creation: string;
  registered_office_address: {
    address_line_1: string;
    address_line_2?: string;
    locality: string;              // City/town
    region?: string;               // County
    postal_code: string;
    country: string;
  };
  sic_codes?: string[];            // Industry codes
  companies_house_url: string;     // Direct link
}

interface StoredBusinessDetails {
  company_number: string;
  company_name: string;
  trading_name?: string;           // "Trading as" name
  company_status: string;
  company_type: string;
  registered_address: {
    line_1: string;
    line_2?: string;
    city: string;
    county?: string;
    postcode: string;
    country: string;
  };
  vat_number?: string;             // Manual entry - for invoices
  ico_registration?: string;       // ICO registration number (manual)
  contact_email?: string;          // Contact email override
  companies_house_url: string;
  fetched_at: string;              // When data was retrieved
  verified: boolean;               // User confirmed details
  source: 'companies_house' | 'manual';  // Where data came from
  manual_override?: boolean;       // User edited after fetch
}

// Consistent result types (like Phase 5)
interface CompanySearchResponse {
  success: boolean;
  companies: CompanySearchResult[];
  total: number;
  error?: string;
}

interface CompanyDetailsResponse {
  success: boolean;
  company?: CompanyDetails;
  error?: string;
  cached?: boolean;               // Was this from cache?
}
```

### Core Functions

```typescript
// Search Companies House by name
export async function searchCompanies(
  query: string,
  options?: { limit?: number }
): Promise<CompanySearchResult[]>

// Get full company details by number
export async function getCompanyDetails(
  companyNumber: string
): Promise<CompanyDetails>

// Save company details to brand
export async function saveBusinessDetails(
  brandPath: string,
  details: StoredBusinessDetails
): Promise<void>

// Load saved business details
export async function loadBusinessDetails(
  brandPath: string
): Promise<StoredBusinessDetails | null>

// Generate Companies House URL
export function getCompaniesHouseUrl(companyNumber: string): string
```

### Caching Strategy

Cache company details to reduce API calls (company data changes rarely):

```typescript
// Cache company details for 24 hours
const companyCache = new Map<string, { data: CompanyDetails; fetchedAt: number }>();
const COMPANY_CACHE_TTL = 24 * 60 * 60 * 1000; // 24 hours

function getCachedCompany(companyNumber: string): CompanyDetails | null {
  const cached = companyCache.get(companyNumber);
  if (cached && Date.now() - cached.fetchedAt < COMPANY_CACHE_TTL) {
    return cached.data;
  }
  return null;
}

function cacheCompany(companyNumber: string, data: CompanyDetails): void {
  companyCache.set(companyNumber, { data, fetchedAt: Date.now() });
}

export function clearCompanyCache(): void {
  companyCache.clear();
}
```

### API Key Resolution

Integrates with APIKeyManager (Phase 1.4) with env fallback:

```typescript
async function getApiKey(): Promise<string> {
  // Try APIKeyManager first (if available)
  try {
    const { getKey } = await import('../../APIKeyManager/src');
    const key = await getKey('companies_house');
    if (key) return key;
  } catch {
    // APIKeyManager not available, use env
  }

  // Fallback to environment variable
  const envKey = process.env.COMPANIES_HOUSE_API_KEY;
  if (!envKey) {
    throw new Error('COMPANIES_HOUSE_API_KEY not configured');
  }
  return envKey;
}
```

### Implementation

```typescript
const COMPANIES_HOUSE_BASE = 'https://api.company-information.service.gov.uk';

export async function searchCompanies(
  query: string,
  options: { limit?: number } = {}
): Promise<CompanySearchResponse> {
  const { limit = 10 } = options;

  try {
    const apiKey = await getApiKey();

    const response = await fetch(
      `${COMPANIES_HOUSE_BASE}/search/companies?q=${encodeURIComponent(query)}&items_per_page=${limit}`,
      {
        headers: {
          'Authorization': `Basic ${Buffer.from(apiKey + ':').toString('base64')}`
        }
      }
    );

    if (!response.ok) {
      return {
        success: false,
        companies: [],
        total: 0,
        error: `Companies House API error: ${response.status}`
      };
    }

    const data = await response.json();
    return {
      success: true,
      companies: data.items || [],
      total: data.total_results || 0
    };
  } catch (error) {
    return {
      success: false,
      companies: [],
      total: 0,
      error: error instanceof Error ? error.message : 'Unknown error'
    };
  }
}

export async function getCompanyDetails(
  companyNumber: string
): Promise<CompanyDetailsResponse> {
  // Check cache first
  const paddedNumber = companyNumber.padStart(8, '0');
  const cached = getCachedCompany(paddedNumber);
  if (cached) {
    return { success: true, company: cached, cached: true };
  }

  try {
    const apiKey = await getApiKey();

    const response = await fetch(
      `${COMPANIES_HOUSE_BASE}/company/${paddedNumber}`,
      {
        headers: {
          'Authorization': `Basic ${Buffer.from(apiKey + ':').toString('base64')}`
        }
      }
    );

    if (!response.ok) {
      if (response.status === 404) {
        return { success: false, error: `Company not found: ${companyNumber}` };
      }
      return { success: false, error: `Companies House API error: ${response.status}` };
    }

    const data = await response.json();

    const company: CompanyDetails = {
      company_number: data.company_number,
      company_name: data.company_name,
      company_status: data.company_status,
      type: data.type,
      date_of_creation: data.date_of_creation,
      registered_office_address: data.registered_office_address,
      sic_codes: data.sic_codes,
      companies_house_url: getCompaniesHouseUrl(data.company_number)
    };

    // Cache the result
    cacheCompany(paddedNumber, company);

    return { success: true, company, cached: false };
  } catch (error) {
    return {
      success: false,
      error: error instanceof Error ? error.message : 'Unknown error'
    };
  }
}

export function getCompaniesHouseUrl(companyNumber: string): string {
  const paddedNumber = companyNumber.padStart(8, '0');
  return `https://find-and-update.company-information.service.gov.uk/company/${paddedNumber}`;
}
```

### Storage Location

**File:** `BrandData/{brand}/_config/business.json`

```json
{
  "company_number": "12345678",
  "company_name": "Example Ltd",
  "trading_name": "Example",
  "company_status": "active",
  "company_type": "ltd",
  "registered_address": {
    "line_1": "123 High Street",
    "line_2": "Suite 4",
    "city": "London",
    "county": "Greater London",
    "postcode": "EC1A 1BB",
    "country": "United Kingdom"
  },
  "vat_number": "GB123456789",
  "companies_house_url": "https://find-and-update.company-information.service.gov.uk/company/12345678",
  "fetched_at": "2026-01-15T10:30:00Z",
  "verified": true
}
```

### Manual Entry Fallback

Support manual business details entry when API is unavailable or for non-UK companies:

```typescript
// Create manual business details (no API required)
export async function createManualBusinessDetails(
  brandPath: string,
  details: Omit<StoredBusinessDetails, 'source' | 'fetched_at' | 'companies_house_url'>
): Promise<{ success: boolean; error?: string }> {
  const fullDetails: StoredBusinessDetails = {
    ...details,
    source: 'manual',
    fetched_at: new Date().toISOString(),
    companies_house_url: details.company_number
      ? getCompaniesHouseUrl(details.company_number)
      : ''
  };
  return saveBusinessDetails(brandPath, fullDetails);
}

// Check if business details are from API or manual
export function isManualEntry(details: StoredBusinessDetails): boolean {
  return details.source === 'manual';
}
```

### Tasks

- [x] Create `lib/server/companies-house.ts`
- [x] Create `lib/server/types/legal.ts` with interfaces
- [x] Implement `searchCompanies()` with result type
- [x] Implement `getCompanyDetails()` with caching
- [x] Implement `saveBusinessDetails()` function
- [x] Implement `loadBusinessDetails()` function
- [x] Implement `createManualBusinessDetails()` fallback
- [x] Implement `clearCompanyCache()` function
- [x] Add `companies_house` to APIKeyManager services.json
- [x] Add COMPANIES_HOUSE_API_KEY to .env
- [x] Test with real company lookups

---

## 6.2 Website Legal Templates

### Objective
Generate common website legal pages using templates with placeholders.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\legal-templates.ts`

### Templates Available

| Template | Purpose | Typical Placement |
|----------|---------|-------------------|
| Cookie Policy | What cookies are used, consent info | /cookie-policy |
| Privacy Policy | GDPR-compliant data handling | /privacy-policy |
| GDPR Notice | Data controller, lawful basis, rights | Part of privacy policy |
| Privacy Notice | Short form - how data is processed | Footer or forms |
| Terms of Service | Website usage rules | /terms |
| Footer Legal | Registration statement | Site footer |

### Core Interfaces

```typescript
type LegalTemplateType =
  | 'cookie_policy'
  | 'privacy_policy'
  | 'gdpr_notice'
  | 'privacy_notice'
  | 'terms_of_service'
  | 'footer_legal';

interface LegalTemplateOptions {
  templateType: LegalTemplateType;
  brandPath: string;
  customFields?: Record<string, string>;  // Additional placeholders
}

interface GeneratedLegalContent {
  templateType: LegalTemplateType;
  title: string;
  content: string;                         // With placeholders replaced
  placeholders_used: string[];
  placeholders_missing: string[];          // Placeholders that couldn't be filled
  last_updated: string;
  template_version: string;                // e.g., "1.0.0" - for tracking updates
  generated_at: string;                    // ISO timestamp
  needs_review: boolean;                   // Flag if template updated since generation
}
```

### Core Functions

```typescript
// Generate legal content from template
export async function generateLegalContent(
  options: LegalTemplateOptions
): Promise<GeneratedLegalContent>

// Get available templates
export function getAvailableTemplates(): LegalTemplateType[]

// Get template with raw placeholders (for preview)
export function getTemplateRaw(templateType: LegalTemplateType): string

// Replace placeholders in content
export function replacePlaceholders(
  content: string,
  values: Record<string, string>
): string

// Save generated legal content to brand
export async function saveLegalContent(
  brandPath: string,
  content: GeneratedLegalContent
): Promise<void>

// Load saved legal content
export async function loadLegalContent(
  brandPath: string,
  templateType: LegalTemplateType
): Promise<GeneratedLegalContent | null>
```

### Template Examples

#### Footer Legal Template
```
{{business_name}} is a company registered in England and Wales (Company No. {{company_number}}).
{{#if vat_number}}VAT Registration: {{vat_number}}.{{/if}}
Registered office: {{registered_address}}.
© {{current_year}} {{business_name}}. All rights reserved.
```

#### Cookie Policy Template (Abbreviated)
```markdown
# Cookie Policy

**Last updated:** {{last_updated}}

{{business_name}} ("we", "us", or "our") uses cookies on {{website_url}} (the "Site").

## What Are Cookies

Cookies are small text files stored on your device when you visit a website.

## How We Use Cookies

We use cookies to:
- Remember your preferences
- Understand how you use our site
- Improve your experience

## Types of Cookies We Use

### Essential Cookies
Required for the site to function. Cannot be disabled.

### Analytics Cookies
Help us understand how visitors interact with our site.
{{#if uses_google_analytics}}
We use Google Analytics. [Learn more](https://policies.google.com/privacy)
{{/if}}

### Marketing Cookies
{{#if uses_marketing_cookies}}
Used to deliver relevant advertisements.
{{else}}
We do not currently use marketing cookies.
{{/if}}

## Managing Cookies

You can control cookies through your browser settings.

## Contact Us

{{contact_email}}
{{registered_address}}
```

#### Privacy Policy Template (Abbreviated)
```markdown
# Privacy Policy

**Last updated:** {{last_updated}}

{{business_name}} ("we", "us", or "our") is committed to protecting your privacy.

## Data Controller

{{business_name}}
{{registered_address}}
{{contact_email}}
{{#if ico_registration}}ICO Registration: {{ico_registration}}{{/if}}

## What Data We Collect

- Contact information (name, email)
- Usage data (pages visited, time on site)
{{#if collects_payment}}
- Payment information (processed securely via {{payment_processor}})
{{/if}}

## How We Use Your Data

- To provide our services
- To communicate with you
- To improve our website
{{#if sends_marketing}}
- To send marketing communications (with your consent)
{{/if}}

## Your Rights (GDPR)

You have the right to:
- Access your personal data
- Correct inaccurate data
- Request deletion of your data
- Object to processing
- Data portability
- Withdraw consent

To exercise these rights, contact us at {{contact_email}}.

## Data Retention

We retain your data for {{retention_period}} or as required by law.

## Third Parties

{{#if uses_third_parties}}
We share data with:
{{third_party_list}}
{{else}}
We do not share your data with third parties except as required by law.
{{/if}}

## Contact Us

For privacy inquiries: {{contact_email}}
```

#### GDPR Notice Template
```markdown
## GDPR Information

**Data Controller:** {{business_name}}, {{registered_address}}

**Lawful Basis:** We process your data based on:
- Consent (for marketing communications)
- Contract (to provide services you've requested)
- Legitimate interests (to improve our services)

**Your Rights:** Access, rectification, erasure, restriction, portability, objection.

**Contact:** {{contact_email}}

**Supervisory Authority:** You may lodge a complaint with the Information Commissioner's Office (ICO) at ico.org.uk.
```

### Tasks

- [x] Create `lib/server/legal-templates.ts`
- [x] Create template files in `_config/legal-templates/` (embedded in code)
- [x] Implement `generateLegalContent()` function
- [x] Implement `replacePlaceholders()` function
- [x] Implement `saveLegalContent()` function
- [x] Create cookie policy template
- [x] Create privacy policy template
- [x] Create GDPR notice template
- [x] Create terms of service template
- [x] Create footer legal template

---

## 6.3 Domain-Specific Disclaimers

### Objective
Provide industry-appropriate disclaimers based on detected domain.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\disclaimers.ts`

### Disclaimers by Domain

| Domain | Disclaimers |
|--------|-------------|
| **Universal** | Copyright, affiliate disclosure, testimonials, general liability |
| **Therapeutic/Wellness** | Not medical advice, not therapy, crisis resources, confidentiality |
| **Ecommerce** | Shipping, returns, pricing, product accuracy |
| **Professional Services** | Results may vary, not legal/financial advice, liability limits |
| **Education** | Educational purposes only, not professional advice |

### Core Interfaces

```typescript
type DisclaimerType =
  // Universal
  | 'copyright'
  | 'affiliate'
  | 'testimonial'
  | 'general_liability'
  // Therapeutic
  | 'not_medical_advice'
  | 'not_therapy'
  | 'crisis_resources'
  | 'confidentiality'
  // Ecommerce
  | 'shipping'
  | 'returns'
  | 'pricing'
  | 'product_accuracy'
  // Professional
  | 'results_vary'
  | 'not_legal_advice'
  | 'not_financial_advice'
  | 'liability_limit';

interface Disclaimer {
  type: DisclaimerType;
  title: string;
  short_text: string;           // For footers, inline use
  full_text: string;            // For dedicated page
  placement: ('footer' | 'checkout' | 'forms' | 'dedicated_page')[];
  required_for_domains: string[];
}

interface DomainDisclaimers {
  domain: string;
  disclaimers: Disclaimer[];
}
```

### Core Functions

```typescript
// Get disclaimers for a domain
export async function getDisclaimersForDomain(
  domain: string
): Promise<Disclaimer[]>

// Get specific disclaimer
export function getDisclaimer(type: DisclaimerType): Disclaimer

// Get all disclaimer types
export function getAllDisclaimerTypes(): DisclaimerType[]

// Generate disclaimers page content
export async function generateDisclaimersPage(
  brandPath: string,
  domain: string
): Promise<string>

// Get footer disclaimers (short versions)
export async function getFooterDisclaimers(
  domain: string
): Promise<string[]>
```

### Disclaimer Content Examples

#### Not Medical Advice (Therapeutic)
```
**Short:** This content is for informational purposes only and is not a substitute for professional medical advice.

**Full:** The information provided on this website is for general informational and educational purposes only. It is not intended to be a substitute for professional medical advice, diagnosis, or treatment. Always seek the advice of your physician or other qualified health provider with any questions you may have regarding a medical condition. Never disregard professional medical advice or delay in seeking it because of something you have read on this website.
```

#### Crisis Resources (Therapeutic)
```
**Short:** If you're in crisis, please contact emergency services or a crisis helpline immediately.

**Full:** If you are experiencing a mental health crisis or having thoughts of self-harm or suicide, please seek immediate help:
- **Emergency Services:** 999
- **Samaritans:** 116 123 (free, 24/7)
- **Crisis Text Line:** Text SHOUT to 85258
- **Mind Infoline:** 0300 123 3393

This website is not a crisis service and cannot provide emergency support.
```

#### Results May Vary (Professional)
```
**Short:** Individual results may vary. Past performance does not guarantee future results.

**Full:** The results described on this website are individual experiences and may not be typical. Your results will vary based on your individual circumstances, effort, and other factors. We make no guarantees regarding the results you may achieve. Past performance is not indicative of future results.
```

#### Shipping Policy (Ecommerce)
```
**Short:** Delivery times are estimates only. See our full shipping policy for details.

**Full:** Delivery times shown are estimates and not guaranteed. {{business_name}} is not responsible for delays caused by shipping carriers, customs, or circumstances beyond our control. For full details, see our Shipping Policy page.
```

### Tasks

- [x] Create `lib/server/disclaimers.ts`
- [x] Define universal disclaimers
- [x] Define therapeutic/wellness disclaimers
- [x] Define ecommerce disclaimers
- [x] Define professional services disclaimers
- [x] Implement `getDisclaimersForDomain()` function
- [x] Implement `generateDisclaimersPage()` function
- [x] Implement `getFooterDisclaimers()` function

---

## 6.4 Placeholder System

### Objective
Provide a consistent placeholder system for all legal templates.

### Location
Part of `lib/server/legal-templates.ts`

### Available Placeholders

| Placeholder | Source | Example |
|-------------|--------|---------|
| `{{business_name}}` | business.json | "Example Ltd" |
| `{{trading_name}}` | business.json | "Example" |
| `{{company_number}}` | business.json | "12345678" |
| `{{vat_number}}` | business.json | "GB123456789" |
| `{{registered_address}}` | business.json (formatted) | "123 High Street, London, EC1A 1BB" |
| `{{contact_email}}` | brand.json or business.json | "hello@example.com" |
| `{{website_url}}` | brand.json | "https://example.com" |
| `{{current_year}}` | Auto-generated | "2026" |
| `{{last_updated}}` | Auto-generated | "15 January 2026" |
| `{{data_controller}}` | business.json | "Example Ltd" |
| `{{ico_registration}}` | business.json (optional) | "ZA123456" |

### Placeholder Resolution

```typescript
interface PlaceholderValues {
  business_name: string;
  trading_name?: string;
  company_number?: string;
  vat_number?: string;
  registered_address: string;
  contact_email: string;
  website_url?: string;
  current_year: string;
  last_updated: string;
  data_controller?: string;
  ico_registration?: string;
  // Custom values
  [key: string]: string | undefined;
}

// Load all placeholder values for a brand
export async function loadPlaceholderValues(
  brandPath: string
): Promise<PlaceholderValues>

// Replace all placeholders in content
export function replacePlaceholders(
  content: string,
  values: PlaceholderValues
): string

// Find missing placeholders in content
export function findMissingPlaceholders(
  content: string,
  values: PlaceholderValues
): string[]

// Format address as single line
export function formatAddressSingleLine(
  address: StoredBusinessDetails['registered_address']
): string
```

### Conditional Placeholders

Templates support simple conditionals:

```
{{#if vat_number}}VAT: {{vat_number}}{{/if}}
{{#if uses_google_analytics}}We use Google Analytics.{{/if}}
{{#else}}Alternative text{{/else}}
```

### Conditional Parser

Simple parser for `{{#if}}...{{/if}}` blocks:

```typescript
interface ConditionalBlock {
  condition: string;           // Variable name to check
  ifContent: string;           // Content if truthy
  elseContent?: string;        // Content if falsy
}

// Parse and evaluate conditionals
export function processConditionals(
  content: string,
  values: Record<string, string | boolean | undefined>
): string {
  // Match {{#if var}}...{{/if}} or {{#if var}}...{{#else}}...{{/if}}
  const conditionalRegex = /\{\{#if\s+(\w+)\}\}([\s\S]*?)(?:\{\{#else\}\}([\s\S]*?))?\{\{\/if\}\}/g;

  return content.replace(conditionalRegex, (_, condition, ifContent, elseContent) => {
    const value = values[condition];
    const isTruthy = value !== undefined && value !== '' && value !== false;
    return isTruthy ? ifContent.trim() : (elseContent?.trim() || '');
  });
}
```

### Placeholder Validation

Validate placeholders before generating content:

```typescript
interface PlaceholderValidation {
  valid: boolean;
  missing: string[];           // Required placeholders not provided
  warnings: string[];          // Optional placeholders not provided
  unknown: string[];           // Placeholders in template not recognized
}

// Required placeholders by template type
const REQUIRED_PLACEHOLDERS: Record<LegalTemplateType, string[]> = {
  cookie_policy: ['business_name', 'contact_email'],
  privacy_policy: ['business_name', 'contact_email', 'registered_address'],
  gdpr_notice: ['business_name', 'registered_address', 'contact_email'],
  privacy_notice: ['business_name'],
  terms_of_service: ['business_name', 'website_url'],
  footer_legal: ['business_name', 'company_number', 'registered_address']
};

export function validateTemplatePlaceholders(
  templateType: LegalTemplateType,
  values: PlaceholderValues
): PlaceholderValidation {
  const required = REQUIRED_PLACEHOLDERS[templateType];
  const missing = required.filter(p => !values[p]);

  return {
    valid: missing.length === 0,
    missing,
    warnings: [],  // Add optional placeholder checks
    unknown: []    // Check for unrecognized placeholders in values
  };
}
```

### Tasks

- [x] Implement `loadPlaceholderValues()` function
- [x] Implement `replacePlaceholders()` function
- [x] Implement `findMissingPlaceholders()` function
- [x] Implement `processConditionals()` parser
- [x] Implement `validateTemplatePlaceholders()` function
- [x] Implement `formatAddressSingleLine()` function
- [x] Define REQUIRED_PLACEHOLDERS per template type

---

## Implementation Order

```
6.1 Companies House Integration
         ↓
6.4 Placeholder System
         ↓
6.2 Website Legal Templates
         ↓
6.3 Domain Disclaimers
```

**Sequence:**
1. Build 6.1 first (business data needed by templates)
2. Build 6.4 (placeholder system used by everything)
3. Build 6.2 (templates use placeholders)
4. Build 6.3 (disclaimers integrate with templates)

---

## Files to Create

| File | Purpose |
|------|---------|
| `lib/server/companies-house.ts` | Companies House API integration |
| `lib/server/legal-templates.ts` | Template generation + placeholders |
| `lib/server/disclaimers.ts` | Domain-specific disclaimers |
| `lib/server/types/legal.ts` | Legal type definitions |
| `_config/legal-templates/` | Template files (markdown) |

---

## Storage Structure

```
BrandData/{brand}/
├── _config/
│   ├── brand.json           # Existing - contact email, website
│   └── business.json        # NEW - Companies House data, VAT
├── legal/
│   ├── cookie-policy.json   # Generated cookie policy
│   ├── privacy-policy.json  # Generated privacy policy
│   ├── terms.json           # Generated terms of service
│   ├── disclaimers.json     # Domain disclaimers
│   └── footer.json          # Footer legal text
```

---

## Verification Plan

### 6.1 Companies House Integration
- [x] Search returns results for "Example Ltd"
- [x] Get details returns full company info
- [x] Company number padded to 8 digits
- [x] Companies House URL generated correctly
- [x] Business details saved to brand/_config/business.json
- [x] VAT number can be manually added

### 6.2 Website Legal Templates
- [x] Cookie policy template generates correctly
- [x] Privacy policy includes GDPR sections
- [x] Footer legal shows company registration
- [x] Placeholders replaced with actual values
- [x] Missing placeholders flagged

### 6.3 Domain Disclaimers
- [x] Universal disclaimers returned for all domains
- [x] Therapeutic domain gets mental health disclaimers
- [x] Ecommerce domain gets shipping/returns disclaimers
- [x] Footer disclaimers are short versions
- [x] Disclaimers page combines all relevant text

### 6.4 Placeholder System
- [x] All business details loaded from business.json
- [x] Current year auto-generates
- [x] Address formatted as single line
- [x] Conditional placeholders work
- [x] Missing values detected and reported

---

## Future Enhancements

**Not in scope for Phase 6, but planned for future builds:**

### AI Legal App (Separate Utility App)

A dedicated utility app for AI-assisted legal content creation:

| Feature | Description |
|---------|-------------|
| **Template Generation** | AI generates initial draft based on brand context |
| **Human Review** | All AI-generated content requires human review before approval |
| **Version Control** | Track changes, revisions, and approval history |
| **Multi-format Export** | Export to markdown, PDF, Word formats |

> See [PLAN-OVERVIEW.md](../Future%20Builds%20Plans/PLAN-OVERVIEW.md#ai-legal-app-future) for architecture diagram.

### Default Libraries for Legal Content

Pre-populated library entries with `weight: 1.0` and `source: 'curated'`:

| Library | Content |
|---------|---------|
| `legal/templates` | Cookie, privacy, terms, GDPR templates |
| `legal/disclaimers` | Domain-specific disclaimer text |
| `legal/clauses` | Common legal clauses for mix-and-match |

### Other Future Enhancements

- Multi-jurisdiction support (AU ABN/ACN, US EIN, EU registries)
- ICO registration lookup
- VAT validation via HMRC API
- Auto-detect changes to Companies House data
- Legal document version history
- Export to PDF/Word formats

---

## Notes

- Companies House API is free but requires registration
- VAT number is manual entry (no free validation API)
- Templates are starting points - users should review
- This is NOT legal advice - just common boilerplate
- GDPR templates follow UK ICO guidance patterns
- Crisis resources use UK helpline numbers
- Company data cached for 24 hours (reduces API calls)
- Manual entry fallback for non-UK companies or API outages

---

## Test Script Planning

### File: `scripts/test-legal.ts`

Planned test coverage (~25-30 tests):

**6.1 Companies House Integration (8 tests)**

| Test | Description |
|------|-------------|
| Search returns results | `searchCompanies('Example')` returns companies |
| Search handles no results | Empty query returns empty array |
| Get company details | `getCompanyDetails('12345678')` returns full info |
| Company number padding | `'123'` padded to `'00000123'` |
| Companies House URL generated | Correct format with padded number |
| Caching works | Second call returns cached: true |
| Cache can be cleared | `clearCompanyCache()` empties cache |
| Manual entry creates details | `createManualBusinessDetails()` works |

**6.2 Website Legal Templates (8 tests)**

| Test | Description |
|------|-------------|
| Cookie policy generates | Template produces content |
| Privacy policy generates | Template produces content |
| GDPR notice generates | Template produces content |
| Footer legal generates | Template produces content |
| Template versioning tracked | `template_version` populated |
| Missing placeholders flagged | Returns `placeholders_missing` array |
| Save legal content | Writes to `legal/` folder |
| Load legal content | Reads back saved content |

**6.3 Domain Disclaimers (6 tests)**

| Test | Description |
|------|-------------|
| Universal disclaimers returned | All domains get copyright, etc. |
| Therapeutic disclaimers | Domain gets mental health disclaimers |
| Ecommerce disclaimers | Domain gets shipping/returns |
| Footer disclaimers short | Returns short text versions |
| Full disclaimers page | Combines all relevant disclaimers |
| Domain integration | Uses Phase 3 detection result |

**6.4 Placeholder System (6 tests)**

| Test | Description |
|------|-------------|
| Load placeholder values | Reads from business.json + brand.json |
| Replace simple placeholders | `{{business_name}}` → "Example Ltd" |
| Conditional if/true | `{{#if vat}}...{{/if}}` renders content |
| Conditional if/false | Missing var removes block |
| Conditional else | `{{#else}}` fallback works |
| Address formatting | Single line format correct |

**Integration (2-4 tests)**

| Test | Description |
|------|-------------|
| Full template generation | API → placeholders → template → save |
| Progress tracking | Legal section updates progress.json |
| Domain → Disclaimers | Detected domain selects correct disclaimers |

### Estimated Total: ~28 tests

---

## Files Summary

| File | Phase | Status |
|------|-------|--------|
| `lib/server/companies-house.ts` | 6.1 | ✅ Complete |
| `lib/server/legal-templates.ts` | 6.2/6.4 | ✅ Complete |
| `lib/server/disclaimers.ts` | 6.3 | ✅ Complete |
| `lib/server/types/legal.ts` | 6.x | ✅ Complete |
| `scripts/test-legal.ts` | 6.x | ✅ Complete (54 tests) |

**Note:** Templates are embedded in `legal-templates.ts` rather than separate files in `_config/legal-templates/`. This simplifies deployment and keeps templates versioned with the code.

---

## Completion Summary

**Phase 6 completed: 2026-01-15**

### Test Results: 54/54 Passing

| Category | Tests | Description |
|----------|-------|-------------|
| 6.1 Companies House | 12 | Search, details, caching, storage, manual entry |
| 6.2 Legal Templates | 11 | Template info, generation, save/load, versioning |
| 6.3 Disclaimers | 12 | Domain selection, placement, page generation |
| 6.4 Placeholders | 8 | Replacement, conditionals, validation |
| Helpers | 11 | Formatting, company type/status, cache operations |

### Key Functions Implemented

**Companies House (6.1):**
- `searchCompanies(query)` → Search by company name
- `getCompanyDetails(number)` → Full company info with 24-hour cache
- `saveBusinessDetails(brandPath, details)` → Store in brand/_config/business.json
- `createManualBusinessDetails(brandPath, details)` → Fallback for non-UK companies

**Legal Templates (6.2):**
- `generateLegalContent({ templateType, brandPath })` → Generate from template
- `saveLegalContent(brandPath, content)` → Store in brand/legal/
- `getAvailableTemplates()` → List: cookie_policy, privacy_policy, gdpr_notice, privacy_notice, terms_of_service, footer_legal

**Disclaimers (6.3):**
- `getDisclaimersForDomain(domain)` → Universal + domain-specific
- `generateDisclaimersPage(brandPath, domain)` → Full page with all relevant disclaimers
- `getFooterDisclaimers(domain)` → Short versions for footer

**Placeholders (6.4):**
- `loadPlaceholderValues(brandPath)` → Combine business.json + brand.json
- `replacePlaceholders(content, values)` → Fill in {{placeholders}}
- `processConditionals(content, values)` → Handle {{#if}}...{{#else}}...{{/if}}

### Verified With Real Data

- **TESCO PLC** (Company No. 00445790)
- Founded: 1947-11-27
- Status: Active
- Type: PLC

---

*Phase 6 simplified: 2026-01-15 - UK-focused business details and website legal templates*
*Improvements added: 2026-01-15 - Caching, APIKeyManager, result types, manual fallback, template versioning*
*Completed: 2026-01-15 - 54/54 tests passing*
