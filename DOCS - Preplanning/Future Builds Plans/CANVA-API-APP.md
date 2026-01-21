# Canva API App - Build Plan

> Integration app for generating Canva Brand Kits from BrandKit Workflow data.
> **Status:** Not Started | **Priority:** MEDIUM
> **Location:** `C:\Users\Business\CanvaAPI\`

---

## Overview

The Canva API App is a standalone integration that takes exported brand data from BrandKit Workflow and creates a complete Canva Brand Kit, including uploading logos, colour palettes, and font selections.

### Purpose
- Generate Canva Brand Kit from BrandKit Workflow exports
- Upload brand assets (logos, colours, fonts) to Canva
- Create pre-configured social media templates
- Generate brand design PDF optimized for Canva upload
- Automate brand asset distribution to design teams

---

## Integration with BrandKit Ecosystem

### Data Flow

```
┌─────────────────────────┐     ┌─────────────────────────┐
│  BrandKit Workflow      │     │     Canva API App       │
│                         │     │                         │
│  Phase 8 Export         │     │                         │
│  ├─ brand-export.json   ├────►│  1. Read export data    │
│  ├─ prompt-pack.md      │     │                         │
│  └─ brand-assets/       │     │  2. Transform to Canva  │
│     ├─ logos/           │     │     API format          │
│     └─ colours.css      │     │                         │
│                         │     │  3. Upload via API      │
└─────────────────────────┘     │     - Brand Kit         │
                                │     - Logos             │
                                │     - Colours           │
                                │     - Fonts             │
                                │                         │
                                │  4. Generate templates  │
                                └────────────┬────────────┘
                                             │
                                             ▼
                                ┌─────────────────────────┐
                                │       Canva             │
                                │                         │
                                │  Brand Kit created:     │
                                │  ├─ Logo variants       │
                                │  ├─ Colour palette      │
                                │  ├─ Font styles         │
                                │  └─ Templates (optional)│
                                └─────────────────────────┘
```

### Input Requirements

| Input | Source | Description |
|-------|--------|-------------|
| `brand-export.json` | Phase 8 Export | Full brand data including visual section |
| Logo files | Brand `/context/visual/logos/` | PNG, SVG logo variants |
| Colour palette | Visual section | Hex values with roles |
| Font selections | Visual section | Font families and weights |

---

## Canva API Integration

### API Endpoints Required

| Endpoint | Purpose | Scope |
|----------|---------|-------|
| `POST /v1/brand-templates` | Create brand kit | `brand_template:write` |
| `POST /v1/assets` | Upload logos/images | `asset:write` |
| `GET /v1/brands` | List existing brands | `brand:read` |
| `POST /v1/designs` | Create template designs | `design:write` |

### Authentication
- OAuth 2.0 with Canva Connect
- Requires Canva API key (stored in APIKeyManager)
- User must authorize app connection to their Canva account

---

## Feature Requirements

### Core Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Brand Kit Creation | HIGH | Create new Canva Brand Kit from export data |
| Logo Upload | HIGH | Upload all logo variants with proper naming |
| Colour Palette | HIGH | Set brand colours with semantic roles |
| Font Selection | HIGH | Configure brand fonts (if available in Canva) |
| Asset Organization | HIGH | Create organized folder structure in Canva |

### Advanced Features

| Feature | Priority | Description |
|---------|----------|-------------|
| Template Generation | MEDIUM | Create social media templates using brand |
| Social Size Library | MEDIUM | Pre-configured sizes for all platforms |
| PDF Brand Guide | MEDIUM | Generate Canva-optimized brand guidelines PDF |
| Team Sharing | LOW | Auto-share brand kit with team members |
| Update Sync | LOW | Sync changes when brand is updated |

---

## Social Media Size Library

### Pre-configured Templates

| Platform | Size | Dimensions | Template Type |
|----------|------|------------|---------------|
| Instagram | Post | 1080x1080 | Quote, Promo, Announcement |
| Instagram | Story | 1080x1920 | Story, Highlight Cover |
| Instagram | Reel Cover | 1080x1920 | Cover image |
| Facebook | Post | 1200x630 | Quote, Promo, Event |
| Facebook | Cover | 820x312 | Banner |
| Twitter/X | Post | 1600x900 | Quote, Announcement |
| Twitter/X | Header | 1500x500 | Banner |
| LinkedIn | Post | 1200x627 | Professional, Article |
| LinkedIn | Banner | 1584x396 | Cover |
| YouTube | Thumbnail | 1280x720 | Video thumbnail |
| Pinterest | Pin | 1000x1500 | Standard pin |
| TikTok | Video Cover | 1080x1920 | Cover image |

---

## Implementation Phases

### Phase 1: Core Integration

- [ ] Initialize project with TypeScript
- [ ] Set up Canva API client
- [ ] Implement OAuth flow
- [ ] Create brand kit upload function
- [ ] Implement logo upload with variants
- [ ] Implement colour palette creation
- [ ] Test with sample brand export

### Phase 2: Font & Template Support

- [ ] Implement font mapping (brand fonts → Canva fonts)
- [ ] Create basic template generator
- [ ] Add social media size configurations
- [ ] Generate starter templates per platform

### Phase 3: Advanced Features

- [ ] PDF brand guide generation
- [ ] Team sharing automation
- [ ] Update/sync functionality
- [ ] Batch processing for multiple brands

---

## API Response Handling

### Canva Font Mapping

```typescript
interface FontMapping {
  brandFont: string;       // From BrandKit (e.g., "Roboto")
  canvaFont: string;       // Canva equivalent
  available: boolean;      // Is font available in Canva?
  fallback?: string;       // Fallback if not available
}

// Example mapping
const fontMap: Record<string, FontMapping> = {
  'Roboto': { brandFont: 'Roboto', canvaFont: 'Roboto', available: true },
  'Open Sans': { brandFont: 'Open Sans', canvaFont: 'Open Sans', available: true },
  'Custom Font': { brandFont: 'Custom Font', canvaFont: 'Arial', available: false, fallback: 'Arial' }
};
```

### Colour Format Conversion

```typescript
// BrandKit format → Canva API format
interface CanvaColour {
  color_code: string;      // Hex without #
  name: string;            // Semantic name
}

function convertToCanvaColours(brandColours: ParsedColor[]): CanvaColour[] {
  return brandColours.map(c => ({
    color_code: c.hex.replace('#', ''),
    name: c.role || c.name || 'Brand Colour'
  }));
}
```

---

## Integration Points

### With BrandKit Workflow

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Export JSON | Read | Source brand data |
| Asset files | Read | Logo and image files |
| Status callback | Write | Report upload status back |

### With FolderCreator

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Asset paths | Read | Locate brand assets |
| Export folder | Read | Find export files |

### With APIKeyManager

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Canva API key | Read | Authentication |
| OAuth tokens | Read/Write | Store user tokens |

---

## Error Handling

| Error Type | Handling |
|------------|----------|
| API rate limit | Exponential backoff, queue retries |
| Font not available | Use fallback, warn user |
| Logo too large | Compress/resize before upload |
| OAuth expired | Refresh token or re-authenticate |
| Partial upload failure | Rollback, retry failed items |

---

## Future Enhancements

| Enhancement | Complexity | Description |
|-------------|------------|-------------|
| **Figma Export** | Medium | Similar integration for Figma brand libraries |
| **Adobe CC Export** | High | Create Adobe CC brand libraries |
| **Design System Sync** | High | Two-way sync with design tools |
| **Template AI** | Medium | AI-generated templates based on brand |
| **Analytics** | Low | Track template usage and engagement |

---

## Related Documentation

| Document | Purpose |
|----------|---------|
| [PHASE-8-PLAN.md](../Build%20Plans/PHASE-8-PLAN.md) | Export system (provides input data) |
| [COLOURTOKEN-APP.md](COLOURTOKEN-APP.md) | Colour palette source |
| [FOLDER-CREATOR-APP.md](FOLDER-CREATOR-APP.md) | Asset organization |
| [PLAN-OVERVIEW.md](PLAN-OVERVIEW.md) | System architecture |

---

## Notes

- Canva API has rate limits - implement request queuing
- Some fonts may not be available - always provide fallbacks
- Logo upload requires specific formats (PNG, SVG preferred)
- Brand Kit feature requires Canva Pro or Teams subscription
- OAuth tokens should be stored securely via APIKeyManager
- Consider batch mode for agency/multi-brand use cases

---

*Last updated: 2026-01-15*
