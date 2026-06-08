# Phase 3: Domain Detection - Implementation Plan

> Detailed implementation plan for Phase 3 of BrandKit Workflow build.
> Reference: [BUILD-TRACKER.md](./BUILD-TRACKER.md) | [PHASE-2-PLAN.md](./PHASE-2-PLAN.md) | [MASTER-PLAN.md](./MASTER-PLAN.md)

---

## Overview

Phase 3 implements domain detection - analyzing gathered context to determine the brand's industry/domain, which drives library selection and specialized content handling.

| Component | Purpose |
|-----------|---------|
| **3.1 Domain Detector** | Analyze context content and score against domain keywords |
| **3.2 Domain Selector UI Support** | Provide fallback options when detection confidence is low |

---

## Prerequisites

| Dependency | From Phase | Status |
|------------|------------|--------|
| **Phase 2: Input Gathering** | Phase 2 | Required - provides all context |
| Context folder with content | Phase 2 | Required - content to analyze |
| sources.json manifest | Phase 2.1 | Required - lists available sources |
| Website/document content | Phase 2.2/2.3 | Required - parsed text to analyze |
| Brand status utilities | Phase 1.5 | Required - update detection status |

---

## Existing Configuration

Domain detection rules exist at:
- **BrandKit AIGen:** `_config/domain-detection.json` ✓ Created
- **BrandLib:** `_config/domains.json` ✓ Created

**To add a new domain:** Update both files - see "Adding New Domains" section in Build Notes below.

### Current Domain Detection Config

```json
{
  "detection_method": {
    "primary": "keyword_matching",
    "fallback": "ask_user",
    "confidence_threshold": 0.6
  },
  "scoring": {
    "strong_keyword": 3,
    "moderate_keyword": 2,
    "weak_keyword": 1,
    "anti_keyword": -5,
    "minimum_score_to_detect": 5
  }
}
```

### Available Domains

| Domain | Description | Has Libraries |
|--------|-------------|---------------|
| `universal` | Works for any brand | Yes (9 libraries) |
| `therapeutic` | Mental health, wellbeing, counselling | Yes (5 libraries) |
| `ecommerce` | Online retail, products, shopping | Planned |
| `professional-services` | Consulting, legal, accounting | Planned |

### Fallback Options (User Selection)

When detection confidence is below threshold:
- Therapeutic / Mental Health / Wellbeing
- E-commerce / Online Retail
- Professional Services / Consulting
- Creative / Design / Agency
- Education / Training
- Non-profit / Charity
- Other / General

---

## 3.1 Domain Detector

### Objective
Analyze all gathered context (website content, uploaded documents) and score against domain keywords to determine the most likely business domain.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\domain-detector.ts`

### Core Functions

```typescript
interface DetectionOptions {
  brandPath: string;           // Path to brand folder
  forceRedetect?: boolean;     // Re-run even if already detected
}

interface DomainScore {
  domain: string;              // Domain key (therapeutic, ecommerce, etc.)
  name: string;                // Human-readable name
  score: number;               // Total score
  confidence: number;          // 0-1 confidence value
  matches: KeywordMatch[];     // Matched keywords for transparency
}

interface KeywordMatch {
  keyword: string;
  strength: 'strong' | 'moderate' | 'weak' | 'anti';
  points: number;
  source: string;              // Which source file contained it
}

interface DetectionResult {
  success: boolean;
  detected: DomainScore | null;       // Top scoring domain (if confident)
  alternatives: DomainScore[];        // Other domains with scores
  confidence: number;                 // Overall confidence (0-1)
  needsUserInput: boolean;            // True if below threshold
  allScores: DomainScore[];           // Complete scoring breakdown
}

// Main detection function
export async function detectDomain(options: DetectionOptions): Promise<DetectionResult>

// Load detection config from BrandKit AIGen
export async function loadDetectionConfig(): Promise<DetectionConfig>

// Load all context content for analysis
export async function loadContextContent(brandPath: string): Promise<ContextContent[]>

// Score content against domain keywords
export function scoreContent(content: string, config: DetectionConfig): DomainScore[]

// Calculate confidence from scores
export function calculateConfidence(scores: DomainScore[]): number

// Save detection result to brand config
export async function saveDetectionResult(brandPath: string, result: DetectionResult): Promise<void>
```

### Detection Algorithm

```typescript
export async function detectDomain(options: DetectionOptions): Promise<DetectionResult> {
  // 1. Load detection config from BrandKit AIGen
  const config = await loadDetectionConfig();

  // 2. Load all context content
  const contextContent = await loadContextContent(options.brandPath);

  // 3. Combine all content for analysis
  const combinedContent = contextContent
    .map(c => c.content.toLowerCase())
    .join(' ');

  // 4. Score against each domain
  const scores: DomainScore[] = [];

  for (const [domainKey, domainConfig] of Object.entries(config.domains)) {
    const score = scoreDomain(combinedContent, domainKey, domainConfig, contextContent);
    scores.push(score);
  }

  // 5. Sort by score (highest first)
  scores.sort((a, b) => b.score - a.score);

  // 6. Calculate confidence
  const confidence = calculateConfidence(scores);
  const topScore = scores[0];

  // 7. Determine if user input needed
  const needsUserInput = confidence < config.detection_method.confidence_threshold
    || topScore.score < config.scoring.minimum_score_to_detect;

  // 8. Build result
  const result: DetectionResult = {
    success: true,
    detected: needsUserInput ? null : topScore,
    alternatives: scores.slice(1, 4),  // Top 3 alternatives
    confidence,
    needsUserInput,
    allScores: scores
  };

  // 9. Save to brand config (if confident)
  if (!needsUserInput) {
    await saveDetectionResult(options.brandPath, result);
  }

  return result;
}
```

### Scoring Logic

```typescript
function scoreDomain(
  content: string,
  domainKey: string,
  domainConfig: DomainConfig,
  sources: ContextContent[]
): DomainScore {
  const matches: KeywordMatch[] = [];
  let totalScore = 0;

  // Score strong keywords (3 points each)
  for (const keyword of domainConfig.keywords.strong) {
    if (content.includes(keyword.toLowerCase())) {
      const source = findSourceContaining(keyword, sources);
      matches.push({
        keyword,
        strength: 'strong',
        points: 3,
        source: source?.file || 'combined'
      });
      totalScore += 3;
    }
  }

  // Score moderate keywords (2 points each)
  for (const keyword of domainConfig.keywords.moderate) {
    if (content.includes(keyword.toLowerCase())) {
      const source = findSourceContaining(keyword, sources);
      matches.push({
        keyword,
        strength: 'moderate',
        points: 2,
        source: source?.file || 'combined'
      });
      totalScore += 2;
    }
  }

  // Score weak keywords (1 point each)
  for (const keyword of domainConfig.keywords.weak) {
    if (content.includes(keyword.toLowerCase())) {
      const source = findSourceContaining(keyword, sources);
      matches.push({
        keyword,
        strength: 'weak',
        points: 1,
        source: source?.file || 'combined'
      });
      totalScore += 1;
    }
  }

  // Apply anti-keyword penalties (-5 points each)
  for (const keyword of domainConfig.anti_keywords || []) {
    if (content.includes(keyword.toLowerCase())) {
      const source = findSourceContaining(keyword, sources);
      matches.push({
        keyword,
        strength: 'anti',
        points: -5,
        source: source?.file || 'combined'
      });
      totalScore -= 5;
    }
  }

  // Calculate confidence relative to max possible score
  const maxPossibleScore =
    domainConfig.keywords.strong.length * 3 +
    domainConfig.keywords.moderate.length * 2 +
    domainConfig.keywords.weak.length * 1;

  const confidence = maxPossibleScore > 0
    ? Math.max(0, Math.min(1, totalScore / maxPossibleScore))
    : 0;

  return {
    domain: domainKey,
    name: domainConfig.name,
    score: Math.max(0, totalScore),
    confidence,
    matches
  };
}
```

### Confidence Calculation

```typescript
function calculateConfidence(scores: DomainScore[]): number {
  if (scores.length === 0) return 0;

  const topScore = scores[0].score;
  const secondScore = scores.length > 1 ? scores[1].score : 0;

  // No matches at all
  if (topScore === 0) return 0;

  // Clear winner (top score is significantly higher than second)
  const gap = topScore - secondScore;
  const gapRatio = gap / topScore;

  // Confidence factors:
  // 1. Absolute score (higher = more confident)
  // 2. Gap to second place (larger gap = more confident)
  // 3. Individual domain confidence

  const absoluteConfidence = Math.min(1, topScore / 20);  // Max confidence at 20 points
  const gapConfidence = gapRatio;
  const domainConfidence = scores[0].confidence;

  // Weighted average
  return (absoluteConfidence * 0.4) + (gapConfidence * 0.3) + (domainConfidence * 0.3);
}
```

### Detection Result Storage

**File:** `BrandData/{brand}/detection-result.json`

```json
{
  "detected_at": "2026-01-13T10:30:00Z",
  "domain": "therapeutic",
  "domain_name": "Therapeutic / Mental Health",
  "confidence": 0.85,
  "user_confirmed": false,
  "scores": [
    {
      "domain": "therapeutic",
      "score": 24,
      "confidence": 0.85,
      "top_matches": ["therapist", "mental health", "counselling", "anxiety"]
    },
    {
      "domain": "professional-services",
      "score": 6,
      "confidence": 0.15
    }
  ],
  "libraries_enabled": [
    "universal/tone",
    "universal/emoji",
    "therapeutic/vocabulary",
    "therapeutic/content-warnings",
    "therapeutic/crisis-resources"
  ]
}
```

### Tasks

- [x] Create `lib/server/domain-detector.ts`
- [x] Create `lib/server/types/domain.ts` with interfaces
- [x] Implement config loading from BrandKit AIGen
- [x] Implement context content loading (read all sources)
- [x] Implement keyword scoring algorithm
- [x] Implement confidence calculation
- [x] Implement anti-keyword penalties
- [x] Save detection results to brand folder
- [x] Update brand.json with detected domain
- [x] Test with sample content from different domains

### Dependencies
- Phase 2 content (website, documents)
- BrandKit AIGen `_config/domain-detection.json`
- BrandLib `_config/domains.json`

---

## 3.2 Domain Selector (UI Support)

### Objective
When detection confidence is low, provide data for user selection UI and handle user's domain choice.

### Location
`C:\Users\Business\BrandKit Workflow\lib\server\domain-selector.ts`

### Core Functions

```typescript
interface SelectionOptions {
  brandPath: string;
  selectedDomain: string;
  userConfirmed: boolean;
}

interface FallbackOption {
  value: string;
  label: string;
  description?: string;
  hasLibraries: boolean;
}

// Get fallback options for user selection
export async function getFallbackOptions(): Promise<FallbackOption[]>

// Get detection result with scoring details for UI
export async function getDetectionDetails(brandPath: string): Promise<DetectionResult>

// Save user's domain selection
export async function saveUserSelection(options: SelectionOptions): Promise<void>

// Get libraries enabled for a domain
export async function getEnabledLibraries(domain: string): Promise<string[]>
```

### Fallback Options Loading

```typescript
export async function getFallbackOptions(): Promise<FallbackOption[]> {
  const config = await loadDetectionConfig();
  const domainsConfig = await loadDomainsConfig();

  return config.fallback_options.map(option => ({
    value: option.value,
    label: option.label,
    description: domainsConfig.domains[option.value]?.description || '',
    hasLibraries: (domainsConfig.domains[option.value]?.libraries?.length || 0) > 0
  }));
}
```

### User Selection Handler

```typescript
export async function saveUserSelection(options: SelectionOptions): Promise<void> {
  const { brandPath, selectedDomain, userConfirmed } = options;

  // 1. Load domains config to get enabled libraries
  const domainsConfig = await loadDomainsConfig();
  const domainInfo = domainsConfig.domains[selectedDomain];

  // 2. Build libraries list (universal + domain-specific)
  const libraries: string[] = [];

  // Always include universal libraries
  if (domainsConfig.domains.universal) {
    libraries.push(...domainsConfig.domains.universal.libraries.map(l => `universal/${l}`));
  }

  // Add domain-specific libraries
  if (domainInfo?.libraries) {
    libraries.push(...domainInfo.libraries.map(l => `${selectedDomain}/${l}`));
  }

  // 3. Save detection result
  const result = {
    detected_at: new Date().toISOString(),
    domain: selectedDomain,
    domain_name: domainInfo?.name || selectedDomain,
    confidence: userConfirmed ? 1.0 : 0,
    user_confirmed: userConfirmed,
    scores: [],
    libraries_enabled: libraries
  };

  await fs.writeFile(
    path.join(brandPath, 'detection-result.json'),
    JSON.stringify(result, null, 2)
  );

  // 4. Update brand.json
  const brandConfigPath = path.join(brandPath, '_config', 'brand.json');
  const brandConfig = JSON.parse(await fs.readFile(brandConfigPath, 'utf-8'));
  brandConfig.domain = selectedDomain;
  brandConfig.updated_at = new Date().toISOString();
  await fs.writeFile(brandConfigPath, JSON.stringify(brandConfig, null, 2));
}
```

### Tasks

- [x] Create `lib/server/domain-selector.ts` (merged into domain-detector.ts)
- [x] Implement fallback options loading
- [x] Implement user selection saving
- [x] Implement library list generation
- [x] Update brand.json with selected domain
- [x] Test user selection flow

---

## Library Enabling Logic

When a domain is detected/selected, the following libraries are enabled:

```
Universal (always enabled):
├── tone
├── emoji
├── cta-patterns
├── accessibility
├── geographic
├── asset-locations
├── response-times
├── sentence-starters
└── hashtags

Domain-specific (if therapeutic):
├── therapeutic-vocabulary
├── therapeutic-content-warnings
├── therapeutic-crisis-resources
├── therapeutic-regulatory
└── therapeutic-client-outcomes
```

### Library Path Resolution

```typescript
function resolveLibraryPath(library: string, domain: string): string {
  // Universal libraries
  if (library.startsWith('universal/')) {
    return path.join(brandLibPath, library.replace('/', path.sep));
  }

  // Domain-specific libraries
  if (library.startsWith(`${domain}/`)) {
    const libName = library.split('/')[1];
    // Therapeutic libraries are in specific folders
    return path.join(brandLibPath, mapLibraryToFolder(libName));
  }

  return path.join(brandLibPath, library);
}

function mapLibraryToFolder(libName: string): string {
  const mappings: Record<string, string> = {
    'therapeutic-vocabulary': 'voice/vocabulary',
    'therapeutic-content-warnings': 'safety/content-warnings',
    'therapeutic-crisis-resources': 'safety/crisis-resources',
    'therapeutic-regulatory': 'compliance/regulatory',
    'therapeutic-client-outcomes': 'outcomes/client-outcomes'
  };
  return mappings[libName] || libName;
}
```

---

## Implementation Order

```
3.1 Domain Detector
         ↓
3.2 Domain Selector (UI Support)
```

**Sequence:**
1. Build 3.1 first (core detection logic)
2. Build 3.2 (handles user selection when detection uncertain)

---

## Verification Plan

### 3.1 Domain Detector
- [ ] Load therapeutic website content → detects "therapeutic" domain
- [ ] Load ecommerce website content → detects "ecommerce" domain
- [ ] Load ambiguous content → returns `needsUserInput: true`
- [ ] Anti-keywords reduce score (e.g., "spa" for therapeutic)
- [ ] Detection result saved to `detection-result.json`
- [ ] brand.json updated with domain

### 3.2 Domain Selector
- [ ] `getFallbackOptions()` returns all options with library status
- [ ] User selection saves domain correctly
- [ ] Libraries list generated correctly (universal + domain)
- [ ] Re-detection possible with `forceRedetect: true`

---

## Files to Create (Summary)

| File | Purpose |
|------|---------|
| `lib/server/domain-detector.ts` | Main detection logic |
| `lib/server/domain-selector.ts` | User selection handling |
| `lib/server/types/domain.ts` | Domain interfaces |

---

## Integration Points

### With Phase 2 (Input Gathering)
- Reads content from `context/website/fetched-content.md`
- Reads parsed content from `context/documents/*.txt`
- Uses `sources.json` to find all available content

### With Phase 5 (Library System)
- Detection result determines which libraries are loaded
- `libraries_enabled` array passed to library matcher

### With Phase 4 (Section Generation)
- Domain affects which section instructions are used
- Some sections are domain-specific (e.g., crisis resources for therapeutic)

---

## Notes

- Detection runs automatically after input gathering completes
- User can always override detected domain
- New domains can be added to config without code changes
- Anti-keywords are important for preventing false positives (e.g., wellness spa vs therapy)
- Confidence threshold (0.6) can be adjusted in config

---

## Build Notes (Completed 2026-01-14)

### Implementation Summary

Phase 3 completed with both 3.1 and 3.2 implemented in a single module (`domain-detector.ts`) since the functionality was closely related.

**Files Created:**
- `lib/server/types/domain.ts` - 18 type definitions
- `lib/server/domain-detector.ts` - 12 exported functions

**Design Decisions:**
1. **External Config Files**: Domain configuration is stored in external JSON files that can be edited without code changes:
   - `BrandKit AIGen/_config/domain-detection.json` - Detection keywords and scoring
   - `BrandLib/_config/domains.json` - Library mappings per domain

   Embedded defaults exist as fallback if files don't exist.

2. **Single Module**: Combined domain-detector.ts and domain-selector.ts into one file since the selector functions are closely related to detection (both deal with domain configuration).

3. **Confidence Calculation**: Uses weighted average of:
   - Absolute score (40%) - higher total score = more confident
   - Gap to second place (30%) - larger gap = clearer winner
   - Domain-specific confidence (30%) - score vs max possible for that domain

**Test Results:**
| Test | Result |
|------|--------|
| Therapeutic content detection | 81.6% confidence, score 39 |
| Ecommerce content detection | 83.2% confidence, score 33 |
| Anti-keywords (spa vs therapy) | Therapeutic score 0 (blocked correctly) |
| Ambiguous content | 37.3% confidence, needsUserInput: true |
| User selection save | Correctly saves to detection-result.json + brand.json |

**Libraries Enabled by Domain:**
- Universal (always): 9 libraries (tone, emoji, cta-patterns, accessibility, geographic, asset-locations, response-times, sentence-starters, hashtags)
- Therapeutic: +5 libraries (vocabulary, content-warnings, crisis-resources, regulatory, client-outcomes)
- Other domains: Universal only (domain-specific libraries planned for future)

### Verification Checklist (All Passed)

**3.1 Domain Detector:**
- [x] Load therapeutic website content → detects "therapeutic" domain
- [x] Load ecommerce website content → detects "ecommerce" domain
- [x] Load ambiguous content → returns `needsUserInput: true`
- [x] Anti-keywords reduce score (e.g., "spa" for therapeutic)
- [x] Detection result saved to `detection-result.json`
- [x] brand.json updated with domain

**3.2 Domain Selector:**
- [x] `getFallbackOptions()` returns all options with library status
- [x] User selection saves domain correctly
- [x] Libraries list generated correctly (universal + domain)
- [x] Re-detection possible with `forceRedetect: true`

### Adding New Domains

To add a new domain, update **both** config files:

**1. `BrandKit AIGen/_config/domain-detection.json`** - Add detection keywords:
```json
"hospitality": {
  "name": "Hospitality / Tourism",
  "weight": 1.0,
  "keywords": {
    "strong": ["hotel", "restaurant", "booking", "reservation"],
    "moderate": ["guest", "accommodation", "dining", "travel"],
    "weak": ["welcome", "stay", "visit"]
  },
  "anti_keywords": ["therapy", "counselling"],
  "libraries_to_use": []
}
```
Also add to `fallback_options` array:
```json
{"value": "hospitality", "label": "Hospitality / Tourism"}
```

**2. `BrandLib/_config/domains.json`** - Add library mappings:
```json
"hospitality": {
  "name": "Hospitality",
  "description": "Hotels, restaurants, travel, tourism",
  "indicators": ["hotel", "restaurant", "booking"],
  "libraries": [],
  "includes_universal": true
}
```

The detector loads these config files automatically - no code changes needed.
