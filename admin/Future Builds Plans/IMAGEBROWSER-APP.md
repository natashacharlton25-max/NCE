# ImageBrowser App - Build Plan

> Stock photography search and curation tool for BrandKit Workflow integration.
> **Status:** Not Started | **Priority:** MEDIUM
> **Location:** `C:\Users\Business\ImageBrowser\`

---

## Overview

ImageBrowser is a standalone utility app for searching stock photography platforms (Unsplash, Pexels) and curating brand image collections. It helps users find on-brand imagery that matches their photography style guidelines.

### Purpose
- Search Unsplash and Pexels for stock photography
- Filter by colour, category, and mood
- Curate collections for specific use cases (hero, blog, social)
- Track licensing and attribution requirements
- Download and organize selected images

---

## Integration with BrandKit Workflow

### Communication Pattern

```
┌─────────────────────────┐                    ┌─────────────────────────┐
│   BrandKit Workflow     │                    │    ImageBrowser App     │
│                         │                    │                         │
│  1. launchImageBrowser()│                    │                         │
│     writes input JSON   ├───────────────────►│  2. App reads input     │
│                         │                    │     - brand name        │
│                         │                    │     - domain/style      │
│                         │                    │     - colour filters    │
│                         │                    │     - categories        │
│                         │                    │                         │
│                         │                    │  3. User searches and   │
│                         │                    │     selects images      │
│                         │                    │                         │
│  5. handleComplete()    │◄───────────────────┤  4. App writes output   │
│     reads output JSON   │                    │     - selected images   │
│     generates attribution│                   │     - collection meta   │
└─────────────────────────┘                    └─────────────────────────┘
```

### File Locations

| File | Path | Written By |
|------|------|------------|
| Input | `{brand}/context/visual/_imagebrowser-input.json` | BrandKit Workflow |
| Output | `{brand}/context/visual/_imagebrowser-output.json` | ImageBrowser App |
| Downloaded images | `{brand}/context/images/` | ImageBrowser App |

### Integration Module

The integration layer is already built in Phase 7C:
- **File:** `BrandKit Workflow/lib/server/imagebrowser-integration.ts`
- **Exports:** `launchImageBrowser`, `handleImageBrowserComplete`, `isImageBrowserComplete`
- **Utilities:** `generateAttributionHTML`, `generateAttributionJSON`, `getHeroImages`

---

## Input/Output Specification

### Input JSON (`_imagebrowser-input.json`)

```typescript
interface ImageBrowserInput {
  /** Brand name for context */
  brandName: string;
  /** Brand domain for image suggestions */
  domain?: string;
  /** Photography style preferences */
  style?: PhotographyStyle;
  /** Colour palette for filtering */
  colours?: ImageColourFilter;
  /** Image categories to browse */
  categories?: ImageCategory[];
  /** Mood/tone preferences */
  mood?: string[];
  /** Mode: browse, search, or curate */
  mode: 'browse' | 'search' | 'curate';
  /** Search query for search mode */
  searchQuery?: string;
  /** Existing image collection (for curate mode) */
  existingImages?: SelectedImage[];
}

interface ImageColourFilter {
  primary?: string;          // Hex colour to match
  secondary?: string[];      // Additional colours
  temperature?: 'warm' | 'cool' | 'neutral';
}

type ImageCategory =
  | 'people' | 'lifestyle' | 'business' | 'technology'
  | 'nature' | 'architecture' | 'food' | 'travel'
  | 'abstract' | 'products' | 'healthcare' | 'education'
  | 'fitness' | 'fashion' | 'interior';
```

**Example Input:**
```json
{
  "brandName": "Wellness Studio",
  "domain": "therapeutic",
  "mode": "search",
  "style": {
    "mood": ["calm", "peaceful", "authentic"],
    "subjects": ["people", "nature", "quiet spaces"],
    "colorPalette": ["warm", "natural"],
    "composition": ["balanced", "natural lighting"],
    "avoid": ["busy backgrounds", "harsh lighting"]
  },
  "categories": ["people", "lifestyle", "nature"],
  "mood": ["calm", "peaceful"],
  "colours": {
    "primary": "#8FA68A",
    "temperature": "warm"
  }
}
```

### Output JSON (`_imagebrowser-output.json`)

```typescript
interface ImageBrowserOutput {
  success: boolean;
  images?: SelectedImage[];
  collection?: ImageCollection;
  error?: string;
}

interface SelectedImage {
  id: string;                   // "unsplash_abc123"
  source: ImageSource;          // 'unsplash' | 'pexels' | 'uploaded'
  url: string;                  // Full image URL
  thumbnailUrl: string;         // Thumbnail URL
  localPath?: string;           // Downloaded local path
  width: number;
  height: number;
  credit?: ImageCredit;
  license: ImageLicense;
  useCase?: ImageUseCase;       // 'hero' | 'blog' | 'social' | etc.
  colours?: string[];           // Dominant colours
  tags?: string[];
  altText?: string;             // Accessibility text
}

interface ImageCredit {
  name: string;                 // "John Smith"
  url?: string;                 // Photographer's profile URL
  platform?: string;            // "Unsplash"
}

interface ImageLicense {
  type: 'free' | 'attribution' | 'commercial' | 'editorial' | 'custom';
  attribution?: string;         // Required attribution text
  restrictions?: string[];      // Any usage restrictions
}

interface ImageCollection {
  name: string;
  description?: string;
  categories: ImageCategory[];
  style: PhotographyStyle;
  createdAt: string;
  updatedAt: string;
  count: number;
}
```

**Example Output:**
```json
{
  "success": true,
  "images": [
    {
      "id": "unsplash_wXCfD-123",
      "source": "unsplash",
      "url": "https://images.unsplash.com/photo-123?w=1920",
      "thumbnailUrl": "https://images.unsplash.com/photo-123?w=400",
      "localPath": "context/images/hero-wellness.jpg",
      "width": 1920,
      "height": 1280,
      "credit": {
        "name": "Jane Doe",
        "url": "https://unsplash.com/@janedoe",
        "platform": "Unsplash"
      },
      "license": {
        "type": "free",
        "attribution": "Photo by Jane Doe on Unsplash"
      },
      "useCase": "hero",
      "colours": ["#8FA68A", "#F5F0EB", "#4A6741"],
      "tags": ["wellness", "meditation", "peaceful"],
      "altText": "Woman meditating in a peaceful garden setting"
    }
  ],
  "collection": {
    "name": "Brand Images",
    "description": "Curated wellness imagery",
    "categories": ["lifestyle", "people", "nature"],
    "style": {
      "mood": ["calm", "peaceful"],
      "subjects": ["people", "nature"],
      "colorPalette": ["warm", "natural"],
      "composition": ["balanced"],
      "avoid": []
    },
    "createdAt": "2026-01-15T10:30:00.000Z",
    "updatedAt": "2026-01-15T10:30:00.000Z",
    "count": 12
  }
}
```

---

## Image Use Cases

| Use Case | Typical Size | Description |
|----------|-------------|-------------|
| `hero` | 1920×1080+ | Homepage hero images |
| `feature` | 1200×800 | Feature section images |
| `background` | 1920×1080 | Background images |
| `thumbnail` | 400×300 | Card/preview thumbnails |
| `social` | 1200×630 | Social media sharing |
| `blog` | 1200×675 | Blog post headers |
| `product` | 800×800 | Product photos (square) |
| `team` | 600×600 | Team member photos |
| `testimonial` | 400×400 | Testimonial avatars |
| `general` | Various | General purpose |

---

## Feature Requirements

### Core Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Unsplash search | HIGH | Search Unsplash API |
| Pexels search | HIGH | Search Pexels API |
| Category filtering | HIGH | Filter by image category |
| Colour filtering | HIGH | Filter by dominant colour |
| Image selection | HIGH | Select multiple images |
| Use case assignment | HIGH | Assign hero/blog/etc. |
| License tracking | HIGH | Track attribution requirements |
| Image download | HIGH | Download to brand folder |
| Export to JSON | HIGH | Write output file |

### Advanced Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Colour extraction | MEDIUM | Detect dominant colours |
| Similar images | MEDIUM | Find visually similar images |
| Collection management | MEDIUM | Organize into collections |
| Mood filtering | MEDIUM | Filter by mood tags |
| Batch download | MEDIUM | Download multiple at once |
| Preview grid | MEDIUM | Grid view of selections |
| Alt text generation | LOW | Auto-suggest alt text |
| Diversity check | LOW | Ensure diverse representation |

---

## UI Design

### Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│  ImageBrowser                                          [Save] [Cancel]│
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  Search: [                                                     🔍 ]  │
│                                                                       │
│  Filters:                                                             │
│  [People ▼] [Lifestyle ▼] [Nature ▼] [Warm ○] [Cool ○] [Neutral ○]  │
│                                                                       │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │  Results                                                         │ │
│  │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐       │ │
│  │  │     │ │     │ │     │ │     │ │     │ │     │ │     │       │ │
│  │  │ IMG │ │ IMG │ │ IMG │ │ IMG │ │ IMG │ │ IMG │ │ IMG │       │ │
│  │  │     │ │     │ │     │ │     │ │     │ │     │ │     │       │ │
│  │  └──+──┘ └─────┘ └──+──┘ └─────┘ └─────┘ └──+──┘ └─────┘       │ │
│  │     ✓               ✓                        ✓                   │ │
│  │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐               │ │
│  │  │     │ │     │ │     │ │     │ │     │ │     │               │ │
│  │  │ IMG │ │ IMG │ │ IMG │ │ IMG │ │ IMG │ │ IMG │               │ │
│  │  │     │ │     │ │     │ │     │ │     │ │     │               │ │
│  │  └─────┘ └─────┘ └─────┘ └─────┘ └─────┘ └─────┘               │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                       │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │  Selected (3)                                                   │   │
│  │                                                                  │   │
│  │  [IMG] wellness-hero.jpg    Use: [Hero ▼]     📥 Download       │   │
│  │        By Jane Doe on Unsplash                                  │   │
│  │                                                                  │   │
│  │  [IMG] meditation-class.jpg Use: [Feature ▼]  📥 Download       │   │
│  │        By John Smith on Pexels                                  │   │
│  │                                                                  │   │
│  │  [IMG] nature-path.jpg      Use: [Background ▼] 📥 Download     │   │
│  │        By Alex Chen on Unsplash                                 │   │
│  └───────────────────────────────────────────────────────────────┘   │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

---

## Technical Stack

| Component | Choice | Rationale |
|-----------|--------|-----------|
| Framework | Electron or Tauri | Desktop app with file access |
| UI | React or Svelte | Component-based |
| Image grid | react-photo-album or custom | Masonry layout |
| Image loading | react-lazy-load-image | Lazy loading |
| Download | Axios + Sharp | Download and resize |
| Build | Vite | Fast dev experience |

---

## APIs Required

| API | Purpose | Key Required |
|-----|---------|--------------|
| Unsplash | Stock photo search | Yes (free tier: 50/hour) |
| Pexels | Stock photo search | Yes (free tier: 200/hour) |
| Pixabay | Additional stock photos | Optional (free) |

### Unsplash API Usage

```typescript
// Search photos
GET https://api.unsplash.com/search/photos?query=wellness&per_page=30
Authorization: Client-ID YOUR_ACCESS_KEY

// Response includes:
// - id, urls (raw, full, regular, small, thumb)
// - width, height
// - user.name, user.links.html
// - color (dominant)
// - description, alt_description
```

### Pexels API Usage

```typescript
// Search photos
GET https://api.pexels.com/v1/search?query=wellness&per_page=30
Authorization: YOUR_API_KEY

// Response includes:
// - id, src (original, large2x, large, medium, small, etc.)
// - width, height
// - photographer, photographer_url
// - avg_color
// - alt
```

---

## License Attribution

### Attribution Requirements by Source

| Source | License | Attribution Required |
|--------|---------|---------------------|
| Unsplash | Free | Appreciated but not required |
| Pexels | Free | Not required |
| Pixabay | Free | Not required |

### Attribution Format

```html
<!-- Generated by BrandKit Workflow -->
<p class="image-attribution">
  Photo by <a href="https://unsplash.com/@janedoe">Jane Doe</a>
  on <a href="https://unsplash.com">Unsplash</a>
</p>
```

---

## Build Tasks

### Phase 1: Core (MVP)

- [ ] Initialize project with Electron/Tauri + Vite
- [ ] Create Unsplash API integration
- [ ] Create Pexels API integration
- [ ] Create search component
- [ ] Create image grid component
- [ ] Create selection panel
- [ ] Implement use case assignment
- [ ] Implement image download
- [ ] Create JSON input/output handling
- [ ] Test integration with BrandKit Workflow

### Phase 2: Filtering & Organization

- [ ] Add category filtering
- [ ] Add colour filtering
- [ ] Add colour temperature filter
- [ ] Add dominant colour extraction
- [ ] Create collection management

### Phase 3: Enhanced Features

- [ ] Add similar image suggestions
- [ ] Add mood-based filtering
- [ ] Add batch download
- [ ] Add alt text suggestion
- [ ] Add diversity indicators
- [ ] Add download progress tracking

---

## Domain-Based Suggestions

### Photography Style by Domain

| Domain | Mood | Subjects | Colours |
|--------|------|----------|---------|
| Therapeutic | calm, peaceful | people, nature, spaces | warm, soft |
| Ecommerce | clean, professional | products, lifestyle | neutral, bright |
| Professional | confident, trustworthy | people, offices | neutral, blue |
| Creative | bold, artistic | abstract, textures | vibrant |
| Education | engaging, friendly | people, learning | warm, varied |
| Healthcare | caring, professional | medical, people | blue, white |

---

## File Output Structure

```
{brand}/
├── context/
│   ├── images/
│   │   ├── hero-wellness.jpg
│   │   ├── feature-meditation.jpg
│   │   └── background-nature.jpg
│   └── visual/
│       ├── _imagebrowser-input.json
│       └── _imagebrowser-output.json
└── assets/
    └── images/
        └── attribution.html
```

---

## Testing

### Integration Test

```bash
# 1. Create test input file
echo '{"brandName":"Test","mode":"browse","categories":["lifestyle"]}' > test-input.json

# 2. Launch ImageBrowser with input
imagebrowser --input test-input.json --output test-output.json --images ./images

# 3. User selects images and saves

# 4. Verify output
cat test-output.json  # Should have images array
ls ./images/          # Should have downloaded images
```

### BrandKit Workflow Integration Test

```typescript
// In BrandKit Workflow
const result = await launchImageBrowser(brandPath, { mode: 'browse' });
// User completes in ImageBrowser app
const output = await handleImageBrowserComplete(brandPath);
const section = convertToPhotographySection(output);
const attributionHTML = generateAttributionHTML(output.images);
```

---

## Future Enhancements

| Enhancement | Complexity | Description |
|-------------|------------|-------------|
| **Image Diversity Scoring** | Medium | Check for representation diversity in selected photography |
| **AI-Powered Similar Images** | Medium | Use AI to find visually similar images based on style/mood |
| **Bulk Download with Progress** | Low | Download multiple images with progress tracking |
| **Colour-Based Search** | Medium | Search for images matching brand palette colours |

> **Note:** Unsplash and Pexels free licenses don't expire, so license expiry tracking is not needed.

---

## Notes

- The integration layer is already complete (Phase 7C.4)
- ImageBrowser should download images to context/images/ folder
- Track all licensing info for attribution generation
- Consider rate limiting awareness (Unsplash: 50/hr, Pexels: 200/hr)
- Downloaded images should be optimized (resize if > 2MB)
- Store original URLs in case re-download needed

---

*Last updated: 2026-01-15*
