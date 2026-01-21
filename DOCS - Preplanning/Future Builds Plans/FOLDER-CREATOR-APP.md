# FolderCreator App - Enhancement Plan

> Brand folder structure creator with future cloud storage integration.
> **Status:** ⚠️ SUPERSEDED - Core functionality moved to Builder service
> **Location:** `C:\Users\Business\FolderCreator\` (legacy)

---

## Architecture Update

> **Note:** As of 2026-01-16, folder creation functionality has been incorporated into the **Builder service** (specifically **FolderBuilder**) as part of the unified service architecture. See [BUILDERS-PLAN.md](../Services/BUILDERS-PLAN.md).

### What Moved to Builder Service

| Feature | Now In |
|---------|--------|
| Brand folder structure creation | `FolderBuilder.buildBrandStructure()` |
| Path resolution | `Builder.getPath()` |
| Directory creation | `Builder.ensureDir()` |
| Template-based structure | `BRAND_STRUCTURE` template |

### What Remains Here (Future)

This document now covers **future cloud integration enhancements** only:
- Google Drive folder synchronization
- Asset organization and categorization
- Cloud-first brand folder creation
- Team collaboration folder structures

---

## Legacy Overview

FolderCreator was a standalone utility app that creates standardized brand folder structures. The core functionality (Phase 1.3) has been migrated to the Builder service for better integration with the service architecture.

### Original Capabilities (Now in Builder)
- Create brand folder structures in BrandData
- CLI mode: `npx folder-creator create-brand --name "My Brand" --domain therapeutic`
- Library mode: `import { createBrandFolder } from '@brandkit/folder-creator'`
- Template-based folder creation (31 folders, 3 config files)
- Domain validation against known domains

### Future Enhancements (Planned - Cloud Integration)
- Google Drive folder synchronization
- Asset organization and categorization
- Cloud-first brand folder creation
- Team collaboration folder structures

---

## Current Architecture

### Existing Structure

```
FolderCreator/
├── src/
│   ├── index.ts                    # Library exports
│   ├── cli.ts                      # CLI entry point
│   ├── create-brand.ts             # Brand folder creation logic
│   ├── create-content.ts           # Future: content folder creation
│   ├── create-product.ts           # Future: product folder creation
│   └── utils/
│       ├── slug.ts                 # Name → slug conversion
│       ├── validators.ts           # Domain validation
│       └── templates.ts            # Folder template loader
├── _config/
│   └── templates.json              # Folder structure templates
├── package.json
└── tsconfig.json
```

### Current Brand Folder Output

```
BrandData/{brand-slug}/
├── _config/
│   └── brand.json              # Brand metadata
├── _meta/
│   ├── progress.json           # Section tracking
│   └── legal-reviews.json      # Legal status
├── brandkit/                   # Approved sections
│   ├── core/
│   ├── voice/
│   ├── legal/
│   └── visual/
├── draft/                      # Work in progress
├── context/
│   ├── sources.json
│   ├── website/
│   ├── documents/
│   └── visual/
└── libraries/                  # Brand-specific entries
```

---

## Future Enhancement: Google Drive Integration

### Feature Overview

Sync brand folder structures with Google Drive, enabling:
- Team access to brand assets
- Cloud backup of brand data
- Collaboration with external designers
- Mobile access to brand files

### Data Flow

```
┌─────────────────────────┐     ┌─────────────────────────┐
│  FolderCreator          │     │     Google Drive        │
│                         │     │                         │
│  1. Create local brand  │     │                         │
│     folder structure    │     │                         │
│                         │     │                         │
│  2. Sync to Google      │     │  Brand Folders/         │
│     Drive (optional)    ├────►│  ├─ {Brand Name}/       │
│                         │     │  │   ├─ Assets/         │
│  3. Track sync status   │     │  │   │   ├─ Logos/      │
│                         │     │  │   │   ├─ Colours/    │
│                         │     │  │   │   └─ Fonts/      │
│  4. Handle conflicts    │◄───┤│  │   ├─ Documents/     │
│                         │     │  │   └─ Exports/        │
└─────────────────────────┘     │  └─ {Another Brand}/    │
                                └─────────────────────────┘
```

### Google Drive Folder Structure

Different from local structure - optimized for team collaboration:

```
Brand Folders/
├── {Brand Name}/
│   ├── 📁 Brand Guidelines/
│   │   ├─ brand-guidelines.pdf     # Generated from Phase 8
│   │   └─ quick-reference.pdf
│   │
│   ├── 📁 Logos/
│   │   ├─ Primary/
│   │   │   ├─ logo-full-colour.png
│   │   │   ├─ logo-full-colour.svg
│   │   │   └─ logo-transparent.png
│   │   ├─ Variants/
│   │   │   ├─ logo-dark-bg.png
│   │   │   ├─ logo-mono-black.png
│   │   │   └─ logo-mono-white.png
│   │   └─ Social/
│   │       ├─ favicon.ico
│   │       └─ social-avatar.png
│   │
│   ├── 📁 Colours/
│   │   ├─ palette-preview.png
│   │   ├─ tokens.css
│   │   └─ tailwind.colors.js
│   │
│   ├── 📁 Fonts/
│   │   ├─ font-files/              # If custom fonts
│   │   └─ web-fonts.css
│   │
│   ├── 📁 Templates/
│   │   ├─ Social Media/
│   │   ├─ Print/
│   │   └─ Web/
│   │
│   ├── 📁 Photography/
│   │   ├─ Hero Images/
│   │   ├─ Product Shots/
│   │   └─ Team Photos/
│   │
│   └── 📁 Documents/
│       ├─ Legal/
│       └─ Internal/
```

### API Integration

| Endpoint | Purpose | Scope |
|----------|---------|-------|
| Google Drive API v3 | Folder/file operations | `drive.file` |
| Google OAuth 2.0 | Authentication | Standard OAuth flow |

### Implementation Approach

```typescript
interface DriveSync {
  brandSlug: string;
  localPath: string;
  driveFolderId: string;
  lastSync: Date;
  status: 'synced' | 'pending' | 'conflict';
}

// Sync brand folder to Google Drive
async function syncBrandToDrive(
  brandPath: string,
  options: DriveSyncOptions
): Promise<DriveSync> {
  // 1. Authenticate with Google
  // 2. Create/find brand folder in Drive
  // 3. Sync asset files
  // 4. Track sync status
}

// Create cloud-first brand folder
async function createCloudBrand(
  name: string,
  options: CloudBrandOptions
): Promise<{ local: string; drive: string }> {
  // 1. Create local structure
  // 2. Create Drive structure
  // 3. Link them via sync config
}
```

---

## Future Enhancement: Asset Organization

### Asset Categories

| Category | File Types | Subfolder |
|----------|------------|-----------|
| Logos | PNG, SVG, EPS, AI | `Logos/` |
| Colours | CSS, JSON, SCSS | `Colours/` |
| Fonts | WOFF, WOFF2, TTF, OTF | `Fonts/` |
| Photos | JPG, PNG, WEBP | `Photography/` |
| Documents | PDF, DOCX | `Documents/` |
| Templates | Various | `Templates/` |

### Auto-Organization Rules

```typescript
interface AssetRule {
  pattern: RegExp;           // Match file patterns
  category: AssetCategory;   // Target category
  subfolder?: string;        // Further organization
}

const assetRules: AssetRule[] = [
  { pattern: /logo.*\.(png|svg)$/i, category: 'logos', subfolder: 'Primary' },
  { pattern: /logo.*dark/i, category: 'logos', subfolder: 'Variants' },
  { pattern: /favicon\.(ico|png)$/i, category: 'logos', subfolder: 'Social' },
  { pattern: /\.(woff2?|ttf|otf)$/i, category: 'fonts', subfolder: 'font-files' },
  { pattern: /hero.*\.(jpg|png|webp)$/i, category: 'photography', subfolder: 'Hero Images' },
];
```

---

## Implementation Phases

### Phase 1: Asset Organization (Local)

- [ ] Add asset categorization rules
- [ ] Implement auto-organize function
- [ ] Add watch mode for new files
- [ ] Create asset manifest (inventory)

### Phase 2: Google Drive Integration

- [ ] Set up Google API credentials
- [ ] Implement OAuth flow (stored in APIKeyManager)
- [ ] Create Drive folder sync function
- [ ] Implement conflict resolution
- [ ] Add sync status tracking

### Phase 3: Team Features

- [ ] Folder sharing with team members
- [ ] Permission management
- [ ] Collaborative asset uploads
- [ ] Notification on changes

---

## Integration Points

### With BrandKit Workflow

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Create brand | Called by | Create initial structure |
| Asset paths | Provides to | Locate brand assets |
| Sync status | Reports to | Track cloud sync state |

### With Phase 8 Export

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Export files | Receives | PDF, JSON exports to organize |
| Asset bundle | Receives | Packaged assets for Drive |

### With Canva API App

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Asset locations | Provides to | Where to find logos, etc. |
| Organized structure | Provides to | Consistent file naming |

### With APIKeyManager

| Integration | Direction | Purpose |
|-------------|-----------|---------|
| Google credentials | Reads | OAuth tokens for Drive |

---

## Configuration

### templates.json Enhancement

```json
{
  "brand": {
    "folders": [ /* existing */ ],
    "files": [ /* existing */ ],
    "cloudFolders": {
      "drive": [
        { "name": "Brand Guidelines", "icon": "📁" },
        { "name": "Logos", "icon": "📁", "subfolders": ["Primary", "Variants", "Social"] },
        { "name": "Colours", "icon": "📁" },
        { "name": "Fonts", "icon": "📁" },
        { "name": "Templates", "icon": "📁", "subfolders": ["Social Media", "Print", "Web"] },
        { "name": "Photography", "icon": "📁", "subfolders": ["Hero Images", "Product Shots"] },
        { "name": "Documents", "icon": "📁", "subfolders": ["Legal", "Internal"] }
      ]
    }
  }
}
```

### Sync Configuration

```typescript
interface SyncConfig {
  enabled: boolean;
  provider: 'google-drive' | 'dropbox' | 'onedrive';
  rootFolderId: string;       // Parent folder for all brands
  autoSync: boolean;          // Sync on file change
  syncInterval?: number;      // Periodic sync (minutes)
  excludePatterns: string[];  // Files to skip
}
```

---

## Error Handling

| Error | Handling |
|-------|----------|
| Drive quota exceeded | Warn user, pause sync |
| Network offline | Queue changes for later |
| Permission denied | Request re-authorization |
| Conflict detected | Show diff, let user choose |
| File too large | Compress or skip with warning |

---

## Related Documentation

| Document | Purpose |
|----------|---------|
| [BUILDERS-PLAN.md](../Services/BUILDERS-PLAN.md) | **Builder service - now owns folder creation** |
| [SERVICE-MAP.md](../Services/SERVICE-MAP.md) | Service architecture overview |
| [PHASE-1-PLAN.md](../Build%20Plans/PHASE-1-PLAN.md) | Original FolderCreator build (1.3) |
| [PHASE-8-PLAN.md](../Build%20Plans/PHASE-8-PLAN.md) | Export system (provides assets) |
| [CANVA-API-APP.md](CANVA-API-APP.md) | Consumes organized assets |
| [PLAN-OVERVIEW.md](PLAN-OVERVIEW.md) | System architecture |

---

## Notes

- **Core folder creation functionality moved to Builder service (FolderBuilder)**
- Google Drive integration is a separate enhancement phase (future)
- Consider supporting other providers (Dropbox, OneDrive) in future
- Asset organization should work locally first, cloud sync optional
- OAuth tokens managed by APIKeyManager for security

---

*Last updated: 2026-01-16*
