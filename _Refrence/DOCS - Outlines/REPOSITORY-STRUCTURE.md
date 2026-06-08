# Repository Structure

> **Purpose:** Foundational patterns for content libraries and data storage
> **Status:** Active
> **Version:** 1.2.0

---

## Terminology

| Term | Definition | Example |
|------|------------|---------|
| **System** | Major functional domain, owns one repository | Brand System, Content System, AI System |
| **Repository** | Logical container owned by a system, holds related libraries | Brand Repository (owned by Brand System) |
| **Library** | Curated collection within a repository, serves specific purpose | Voice Library, Audience Library, Theme Library |
| **Storage** | Physical mechanism - either JSON files or SQLite database | `/repos/drafts/` or `/data/themes.sqlite` |
| **Log** | Append-only audit trail, separate from content storage | `/logs/daily/` |

### Hierarchy

```
System
└── Repository (logical container)
    └── Library (curated collection)
        └── Storage (physical: JSON or SQLite)
```

### Example: Brand System

```
Brand System
└── Brand Repository
    ├── Brand Library        → /data/brands.sqlite
    ├── Voice Library        → /data/voices.sqlite
    ├── Audience Library     → /data/audiences.sqlite
    └── Tone Library         → /data/tones.sqlite
```

---

## Storage Philosophy

| Layer | Format | Container | Mutable | Purpose |
|-------|--------|-----------|---------|---------|
| **Repo** | JSON | File system | Yes | Working drafts, human-curated content |
| **SQLite** | JSON | Database | No | Permanent records, queryable archive |
| **Log** | JSON | Append-only | No | Audit trail, debugging |

**Core Rule:** Once data moves from Repo → SQLite, it becomes immutable. New versions = new records.

---

## Folder Structure

```
/project-root/
│
├── /repos/                    ← Working drafts (JSON files, mutable)
│   ├── /drafts/               ← Temp content before finalization
│   │   ├── /brands/           ← Brand drafts
│   │   ├── /themes/           ← Theme drafts
│   │   └── /...               ← Per-library draft folders
│   └── /imports/              ← Incoming data to be processed
│
├── /data/                     ← SQLite libraries (one file per library)
│   │
│   ├── _index.sqlite          ← Master index of all libraries
│   │
│   │── # Brand System
│   ├── brands.sqlite
│   ├── audiences.sqlite
│   ├── voices.sqlite
│   ├── tones.sqlite
│   │
│   │── # Content System
│   ├── themes.sqlite
│   ├── outlines.sqlite
│   ├── templates.sqlite
│   │
│   │── # AI System
│   ├── prompts.sqlite
│   ├── model-configs.sqlite
│   │
│   │── # Template System
│   ├── section-specs.sqlite
│   ├── content-types.sqlite
│   ├── cognitive-types.sqlite
│   │
│   │── # Social System
│   ├── social-templates.sqlite
│   │
│   │── # Visual System
│   └── visual-brands.sqlite
│
├── /logs/                     ← Audit logs (append-only, per system)
│   ├── /brand-system/
│   ├── /content-system/
│   ├── /ai-system/
│   ├── /template-system/
│   └── /errors/               ← Cross-system errors
│
└── /exports/                  ← Generated output files
```

### System-Repository-Library Map

| System | Repository | Libraries | Manager |
|--------|------------|-----------|---------|
| **Brand** | Brand Repository | brands, audiences, voices, tones | BrandManager |
| **Content** | Content Repository | themes, outlines, templates | ContentManager |
| **AI** | AI Repository | prompts, model-configs | AIManager |
| **Template** | Template Repository | section-specs, content-types, cognitive-types | TemplateEngine |
| **Social** | Social Repository | social-templates | SocialMediaManager |
| **Visual** | Visual Repository | visual-brands | VisualManager |

### One File Per Library (Current Phase)

**Pattern:** Each library gets its own SQLite file.

| Approach | Pros | Cons |
|----------|------|------|
| One file per library | Isolated testing, easy backup, clear ownership | More files to manage |
| One file per repository | Fewer files, shared transactions | Tighter coupling |

**Current choice:** One file per library for development flexibility. Can consolidate to one file per repository later if needed.

---

## Naming Conventions

### File Extensions

| Type | Extension | Example |
|------|-----------|---------|
| Library database | `.sqlite` | `brands.sqlite` |
| Master index | `.sqlite` | `_index.sqlite` |
| Draft file | `.json` | `theme-draft-123.json` |
| Log file | `.sqlite` | `2026-01-20.sqlite` |

### File Prefixes

| Prefix | Meaning | Example |
|--------|---------|---------|
| `_` (underscore) | System/index file, not user content | `_index.sqlite` |
| No prefix | User content library | `brands.sqlite` |

### Library Files (SQLite)

| Pattern | Example | Rule |
|---------|---------|------|
| `{library}.sqlite` | `brands.sqlite` | Plural, matches library name |
| `_index.sqlite` | `_index.sqlite` | Master index (underscore = system file) |

### Draft Files (JSON)

| Pattern | Example | Rule |
|---------|---------|------|
| `{type}-{slug}-{timestamp}.json` | `theme-productivity-20260120.json` | Prevents collisions |
| `{id}-v{n}.json` | `theme_123-v2.json` | For versioned edits |

**Draft folders:** `/repos/drafts/{library}/` (e.g., `/repos/drafts/themes/`)

### Record IDs

| Pattern | Example | Rule |
|---------|---------|------|
| `{type}_{slug}` | `brand_mind-the-box` | Underscore separates type from slug |
| Kebab-case slug | `audience_first-time-founders` | Readable, URL-safe |

**Note:** Single separator style (underscore between type and slug, kebab within slug).

### Systems and Repositories

| Level | Pattern | Example |
|-------|---------|---------|
| System | PascalCase | Brand System, Content System |
| Repository | Title Case | Brand Repository |
| Library | lowercase plural | brands, themes, prompts |

---

## Index Structure

### Master Index (`_index.sqlite`)

The master index is the registry/coordination layer for all libraries.

**Location:** `/data/_index.sqlite`

**Purpose:**
- Track what libraries exist
- Store file paths for each SQLite library
- Version information per library
- Last updated timestamps
- Dependency relationships between libraries
- File integrity verification (hash)
- Environment tagging (dev/staging/prod)

**Schema:**

```sql
-- Library registry
CREATE TABLE libraries (
  id TEXT PRIMARY KEY,           -- e.g., "brands", "themes"
  system TEXT,                   -- e.g., "Brand", "Content"
  repository TEXT,               -- e.g., "Brand Repository"
  file_path TEXT,                -- e.g., "/data/brands.sqlite"
  library_type TEXT,             -- 'core', 'derived', 'generated', 'reference'
  schema_version TEXT,
  updated_at TEXT,
  entry_count INTEGER,
  file_hash TEXT,                -- SHA-256 of .sqlite file for integrity
  environment TEXT DEFAULT 'development',  -- 'development', 'staging', 'production'
  status TEXT DEFAULT 'active'
);

-- Cross-library dependencies
CREATE TABLE library_dependencies (
  library_id TEXT,
  depends_on TEXT,
  dependency_type TEXT,          -- 'hard', 'soft', 'reference'
  PRIMARY KEY (library_id, depends_on)
);

-- Reserved IDs (prevent draft collisions)
CREATE TABLE reserved_ids (
  id TEXT PRIMARY KEY,
  library_id TEXT,
  reserved_by TEXT,
  reserved_at TEXT,
  expires_at TEXT                -- Auto-release after timeout
);
```

**Library Types:**

| Type | Description | Can Rebuild? |
|------|-------------|--------------|
| `core` | Human-curated source content | No |
| `derived` | Computed from other libraries | Yes |
| `generated` | AI-generated content | Yes |
| `reference` | System configuration | No |

**Example Data:**

| id | system | library_type | file_path | schema_version | entry_count |
|----|--------|--------------|-----------|----------------|-------------|
| brands | Brand | core | /data/brands.sqlite | 1.0.0 | 5 |
| themes | Content | generated | /data/themes.sqlite | 1.7.0 | 23 |
| prompts | AI | reference | /data/prompts.sqlite | 1.0.0 | 48 |

### Per-Library Index (Within Each SQLite File)

Each library maintains its own lightweight index table for fast lookups:

```sql
-- Inside brands.sqlite
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT,
  latest_version INTEGER,        -- Avoids join to get version number
  updated_at TEXT,
  tags TEXT,                     -- JSON array for filtering
  preview JSON,                  -- Key fields for UI display without loading full record
  ref_status TEXT                -- 'valid', 'stale', 'orphaned' (for dependency tracking)
);

-- Full data table
CREATE TABLE brands (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT,
  created_by TEXT,
  PRIMARY KEY (id, version)
);

-- Latest version view
CREATE VIEW brands_latest AS
SELECT * FROM brands t1
WHERE version = (SELECT MAX(version) FROM brands t2 WHERE t2.id = t1.id);
```

**Index vs Full Data:**

| In `_index` Table | In `brands` Table |
|-------------------|-------------------|
| id, name, status, latest_version | Full JSON blob |
| preview (3-5 key fields) | Complete record |
| ref_status (dependency health) | Versioned history |
| Fast filtering, UI display | Full data access |

**Preview Field Example:**

```json
{
  "title": "Mind the Box",
  "owner": "user_nce",
  "brandId": "brand_mtb"
}
```

The `preview` JSON contains the 3-5 most frequently queried fields to avoid loading the full `data` blob for list views.

### Access Pattern

```
Manager needs to list brands:
    │
    ├── 1. Check _index.sqlite → libraries table
    │       → Get file_path, schema_version
    │
    ├── 2. Open /data/brands.sqlite
    │       → Query _index table for fast list (includes preview)
    │
    └── 3. Load full record when needed
            → Query brands table by id
```

---

## SQLite JSON Storage

### Table Pattern

```sql
CREATE TABLE {items} (
  id TEXT,
  version INTEGER,
  data JSON,                    -- Full object as JSON
  status TEXT DEFAULT 'active',
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

-- Latest version view
CREATE VIEW {items}_latest AS
SELECT * FROM {items} t1
WHERE version = (SELECT MAX(version) FROM {items} t2 WHERE t2.id = t1.id);
```

### Querying JSON

```sql
-- Extract field
SELECT json_extract(data, '$.name') FROM themes;

-- Filter by nested value
SELECT * FROM themes
WHERE json_extract(data, '$.brandId') = 'brand_mtb';

-- Search in array
SELECT * FROM themes
WHERE EXISTS (
  SELECT 1 FROM json_each(json_extract(data, '$.truths'))
  WHERE json_extract(value, '$.statement') LIKE '%productivity%'
);
```

### Immutability Rules

| Operation | Allowed | Pattern |
|-----------|---------|---------|
| INSERT | Yes | New record |
| UPDATE status | Yes | Archive only (`active` → `archived`) |
| UPDATE data | No | Create new version instead |
| DELETE | No | Archive instead |

---

## Referential Integrity

### The Ghost Dependency Problem

When `theme_123` references `brand_mtb`, what happens if `brand_mtb` is archived?

**Risk:** Orphaned references, stale data, broken lookups.

### Solution: ref_status Tracking

Each record in `_index` has a `ref_status` field:

| Status | Meaning | Action |
|--------|---------|--------|
| `valid` | All dependencies active | Normal use |
| `stale` | A dependency was updated | May need refresh |
| `orphaned` | A dependency was archived | Cannot use, needs review |

### Cascade Rules

| When This Happens | Do This |
|-------------------|---------|
| Brand archived | Mark all themes referencing it as `orphaned` |
| Brand updated (new version) | Mark referencing themes as `stale` |
| Theme refresh requested | Re-validate all dependencies, update `ref_status` |

### Validation on Access

```javascript
// Librarian.get() checks ref_status
const theme = Librarian.get('themes', 'theme_123');
if (theme.ref_status === 'orphaned') {
  throw THEME_DEPENDENCY_ORPHANED;
}
if (theme.ref_status === 'stale') {
  Logger.warn('Theme references updated dependency');
}
```

### Cross-Library Dependency Validation

Before finalizing a draft that references another library:

```javascript
// On draft → library promotion
const brandRef = draft.brandId;
const brand = Librarian.get('brands', brandRef);
if (!brand || brand.status !== 'active') {
  throw DRAFT_INVALID_REFERENCE;
}
```

---

## Access Patterns

### Locating a Library

```
Module needs brand data:
    │
    ├── 1. Librarian.getLibrary('brands')
    │       → Queries _index.db for file_path, schema_version
    │
    ├── 2. DatabaseHandler.open('/data/brands.db')
    │       → Opens library file
    │
    └── 3. Query as needed
            → SELECT from _index (list) or brands (full record)
```

### Reading a Record

```
ThemeGenerator needs brand context:
    │
    ├── Librarian.get('brands', 'brand_mind-the-box')
    │       → Returns full JSON from brands.data column
    │
    └── Librarian.list('brands', { status: 'active' })
            → Returns array from brands._index table
```

### Writing a Record (Owner Only)

```
BrandManager saves new brand:
    │
    ├── 1. Validate against schema
    │
    ├── 2. INSERT INTO brands (id, version, data, created_by)
    │       → Immutable record created
    │
    ├── 3. UPDATE _index SET ... WHERE id = ?
    │       → Per-library index updated
    │
    └── 4. UPDATE _index.db libraries SET entry_count = ?, updated_at = ?
            → Master index updated
```

### Registration (New Library)

When creating a new library:

1. Create SQLite file: `/data/{library}.db`
2. Create tables: `_index`, `{items}`, `{items}_latest` view
3. Register in master index:

```sql
INSERT INTO libraries (id, system, repository, file_path, schema_version)
VALUES ('new-library', 'System', 'Repository', '/data/new-library.db', '1.0.0');
```

4. Document in _REPOS.md

### Version Checking

Before accessing library:

```javascript
// Librarian checks schema version
const lib = getLibrary('brands');
if (lib.schema_version !== EXPECTED_VERSION) {
  // Trigger migration or warn
}
```

---

## System Ownership

| System | Repository | Libraries Owned | Manager Module |
|--------|------------|-----------------|----------------|
| **Brand** | Brand Repository | brands, audiences, voices, tones | BrandManager |
| **Content** | Content Repository | themes, outlines, templates | ContentManager |
| **AI** | AI Repository | prompts, model-configs | AIManager |
| **Template** | Template Repository | section-specs, content-types, cognitive-types | TemplateEngine |
| **Social** | Social Repository | social-templates | SocialMediaManager |
| **Visual** | Visual Repository | visual-brands | VisualManager |

### Ownership Rules

| Rule | Description |
|------|-------------|
| One system per repository | Brand System owns Brand Repository |
| One manager writes | Only BrandManager writes to brands.sqlite |
| Others read via Librarian | ThemeGenerator reads brands.sqlite through Librarian |
| Index updated on write | Manager updates _index.sqlite when library changes |
| Cascade ref_status | Owner notifies dependents when records archived |

### Schema Ownership

| Aspect | Owner | Notes |
|--------|-------|-------|
| Schema structure | System owner | BrandManager owns brands schema |
| Schema version bumps | Human-approved | Major changes need review |
| Migration scripts | System owner | Each manager migrates its own libraries |

---

## Lifecycle: Draft → Library

### When Content is Finalized

```
Draft (mutable)                      Library (immutable)
────────────────────────────────────────────────────────────────────
/repos/drafts/theme-123.json
        │
        ▼  [User approves]
        │
        └──→ INSERT INTO themes (id, version, data, created_by)
             VALUES ('theme_123', 1, '{...}', 'user_nce')
        │
        ▼  [Update indexes]
        │
        ├── UPDATE themes._index
        └── UPDATE _index.db libraries
        │
        ▼  [Delete draft]
        │
DELETE /repos/drafts/theme-123.json
```

### When Record Needs Update

```
Existing record                      New version
────────────────────────────────────────────────────────────────────
themes.db: theme_123, v1
        │
        ▼  [User edits via UI]
        │
Create draft: /repos/drafts/theme-123-v2.json
        │
        ▼  [User approves]
        │
        └──→ INSERT INTO themes (id, version, data)
             VALUES ('theme_123', 2, '{...}')
        │
        ▼  [v1 stays forever, v2 is latest]
```

### Why Drafts Live in /repos/

| Draft Location | Library Location | Reason |
|----------------|------------------|--------|
| `/repos/drafts/` | `/data/themes.db` | Drafts are temporary, editable |
| JSON file | SQLite record | Easy to inspect, delete, version control |
| Can be abandoned | Permanent once approved | No half-finished records in library |

---

## Summary

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Terminology** | System → Repository → Library | Clear ownership hierarchy |
| **File extension** | `.sqlite` for all databases | Consistent, explicit |
| **Draft storage** | JSON in /repos/drafts/{library}/ | Per-library folders, timestamped |
| **Library storage** | SQLite with JSON columns | Queryable, immutable |
| **One file per library** | brands.sqlite, themes.sqlite | Isolated testing, easy backup |
| **Master index** | _index.sqlite tracks all libraries | Central registry, dependencies, integrity |
| **Per-library index** | _index table with preview, ref_status | Fast UI, dependency health |
| **Naming** | `{type}_{kebab-slug}` | Single separator style |
| **Immutability** | SQLite records never edited | Audit trail, data integrity |
| **Referential integrity** | ref_status tracking | Prevent ghost dependencies |
| **Ownership** | One system per repository | Clear responsibility |
| **Logs** | Per-system folders | Isolated debugging |

---

*Created: 2026-01-20*
*Updated: 2026-01-20 - v1.1.0 Added terminology, system ownership, master index*
*Updated: 2026-01-20 - v1.2.0 Added .sqlite extension, referential integrity, preview fields, all systems*
*Version: 1.2.0*
