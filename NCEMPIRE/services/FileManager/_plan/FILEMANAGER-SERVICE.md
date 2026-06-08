# FileManager Service

> Central service for ALL file system operations.
> **Status:** Planning | **Priority:** HIGH
> **Location:** `C:\Users\Business\FileManager\`

---

## Overview

The FileManager service handles all file system operations. Apps request file operations - they never touch the file system directly. FileManager handles path resolution, folder creation, file read/write, validation, and logging.

### Current State (Scattered Across Projects)

File operations are currently in multiple places:

```
FolderCreator/                      # Standalone app ✓
├── src/
│   ├── create-brand.ts             # Brand folder creation
│   ├── utils/paths.ts              # Path utilities
│   └── utils/json-utils.ts         # JSON read/write

BrandKit Workflow/lib/server/
├── upload-handler.ts               # File uploads
├── section-utils.ts                # Section file operations
├── brand-status.ts                 # Brand config read/write
└── config.ts                       # Config loading
```

### Target State (FileManager Service)

```
FileManager/
├── src/
│   ├── index.ts                    # Public API
│   ├── scripts/
│   │   ├── check-exists.ts         # File/folder existence
│   │   ├── create-folder.ts        # Create folder structure
│   │   ├── create-brand.ts         # Full brand folder setup
│   │   ├── write-file.ts           # Write with validation
│   │   ├── read-file.ts            # Read with error handling
│   │   ├── move-file.ts            # Relocate files
│   │   ├── copy-file.ts            # Copy with options
│   │   ├── delete-file.ts          # Safe delete
│   │   ├── get-path.ts             # Resolve correct location
│   │   ├── validate-file.ts        # Type/size validation
│   │   └── log-operation.ts        # Track all operations
│   ├── utils/
│   │   ├── paths.ts                # Path utilities
│   │   └── json.ts                 # JSON utilities
│   └── types/
│       └── index.ts
├── _config/
│   ├── locations.json              # Base paths
│   ├── structures.json             # Folder templates
│   ├── permissions.json            # App write permissions
│   └── naming.json                 # Naming conventions
├── logs/
│   ├── operations.log              # All operations
│   ├── errors.log                  # Failed operations
│   └── created.log                 # New files/folders
├── package.json
└── tsconfig.json
```

---

## Extraction Mapping

### Files to Move/Merge

| Current Location | Target Location | What to Change |
|------------------|-----------------|----------------|
| `FolderCreator/src/create-brand.ts` | `src/scripts/create-brand.ts` | Add logging, permissions |
| `FolderCreator/src/utils/paths.ts` | `src/utils/paths.ts` | Minimal changes |
| `FolderCreator/src/utils/json-utils.ts` | `src/utils/json.ts` | Minimal changes |
| `FolderCreator/_config/templates.json` | `_config/structures.json` | Rename |
| `lib/server/upload-handler.ts` | `src/scripts/write-file.ts` | Merge upload logic |
| `lib/server/section-utils.ts` (file ops) | `src/scripts/*.ts` | Split into read/write |
| `lib/server/brand-status.ts` (file ops) | `src/scripts/read-file.ts` | Extract file operations |

### Functions to Extract

| Current Function | Current File | Target Script |
|------------------|--------------|---------------|
| `createBrandFolder()` | `FolderCreator/create-brand.ts` | `create-brand.ts` |
| `slugify()` | `FolderCreator/utils/paths.ts` | `utils/paths.ts` |
| `getBrandPath()` | `FolderCreator/utils/paths.ts` | `get-path.ts` |
| `brandExists()` | `FolderCreator/utils/paths.ts` | `check-exists.ts` |
| `readJson()` | `FolderCreator/utils/json-utils.ts` | `utils/json.ts` |
| `writeJson()` | `FolderCreator/utils/json-utils.ts` | `utils/json.ts` |
| `handleUpload()` | `upload-handler.ts` | `write-file.ts` |
| `validateFile()` | `upload-handler.ts` | `validate-file.ts` |
| `saveToDraft()` | `section-utils.ts` | `write-file.ts` |
| `loadFromDraft()` | `section-utils.ts` | `read-file.ts` |
| `approveSection()` | `section-utils.ts` | `move-file.ts` |
| `getBrandConfig()` | `brand-status.ts` | `read-file.ts` |
| `updateSectionStatus()` | `brand-status.ts` | `write-file.ts` |

---

## Migration Steps

### Step 1: Create FileManager Shell

```bash
mkdir C:\Users\Business\FileManager
cd C:\Users\Business\FileManager
npm init -y

npm install typescript ts-node
mkdir src src/scripts src/utils src/types _config logs
```

### Step 2: Copy FolderCreator Core

```bash
# Copy from FolderCreator/
cp FolderCreator/src/utils/paths.ts FileManager/src/utils/paths.ts
cp FolderCreator/src/utils/json-utils.ts FileManager/src/utils/json.ts
cp FolderCreator/_config/templates.json FileManager/_config/structures.json

# Copy and modify create-brand.ts
# ADD: Logging, permission checks
```

```typescript
// src/scripts/create-brand.ts
// Copy from FolderCreator/src/create-brand.ts

// ADD at start:
import { checkPermission } from '../permissions';
import { logOperation } from './log-operation';

export async function createBrandFolder(
  app: string,  // NEW: which app is calling
  options: CreateBrandOptions
): Promise<CreateBrandResult> {
  // NEW: Check permission
  if (!checkPermission(app, 'brands', 'create')) {
    throw new PermissionError(`${app} cannot create brands`);
  }

  // ... existing logic ...

  // NEW: Log operation
  await logOperation({
    app,
    operation: 'create-brand',
    path: brandPath,
    success: true
  });

  return result;
}
```

### Step 3: Extract Upload Handler

```typescript
// src/scripts/write-file.ts
// Extract from lib/server/upload-handler.ts

// COPY these functions and modify:
export async function writeFile(
  app: string,
  options: WriteFileOptions
): Promise<WriteResult> {
  const { brandPath, folder, filename, content } = options;

  // Check permission
  if (!checkPermission(app, `brands/${folder}`, 'write')) {
    throw new PermissionError(`${app} cannot write to ${folder}`);
  }

  // Resolve full path
  const fullPath = resolvePath(brandPath, folder, filename);

  // Create folder if needed
  await ensureFolder(path.dirname(fullPath));

  // Write file
  await fs.writeFile(fullPath, content);

  // Log operation
  await logOperation({ app, operation: 'write', path: fullPath, success: true });

  return { success: true, path: fullPath };
}

// Also include upload validation from handleUpload()
export async function uploadFile(
  app: string,
  options: UploadOptions
): Promise<UploadResult> {
  // Validate file type and size (from upload-handler.ts)
  const validation = validateFile(options.file, options.filename);
  if (!validation.valid) {
    return { success: false, error: validation.error };
  }

  // Use writeFile for actual writing
  return writeFile(app, {
    brandPath: options.brandPath,
    folder: validation.folder,  // determined by file type
    filename: generateUniqueFilename(options.filename),
    content: options.file
  });
}
```

### Step 4: Extract Section File Operations

```typescript
// src/scripts/read-file.ts
// Extract file operations from section-utils.ts and brand-status.ts

export async function readFile(
  app: string,
  options: ReadFileOptions
): Promise<ReadResult> {
  const { brandPath, folder, filename } = options;

  // Check permission
  if (!checkPermission(app, `brands/${folder}`, 'read')) {
    throw new PermissionError(`${app} cannot read from ${folder}`);
  }

  const fullPath = resolvePath(brandPath, folder, filename);

  if (!await exists(fullPath)) {
    return { success: false, error: 'File not found' };
  }

  const content = await fs.readFile(fullPath);

  // Log operation
  await logOperation({ app, operation: 'read', path: fullPath, success: true });

  return { success: true, content, path: fullPath };
}

export async function readJson<T>(
  app: string,
  options: ReadFileOptions
): Promise<T> {
  const result = await readFile(app, options);
  if (!result.success) throw new Error(result.error);
  return JSON.parse(result.content.toString());
}

// Specific helpers (extracted from brand-status.ts)
export async function readBrandConfig(app: string, brandPath: string): Promise<BrandConfig> {
  return readJson(app, { brandPath, folder: '_config', filename: 'brand.json' });
}

export async function readProgressConfig(app: string, brandPath: string): Promise<ProgressConfig> {
  return readJson(app, { brandPath, folder: '_meta', filename: 'progress.json' });
}
```

### Step 5: Extract Move/Copy Operations

```typescript
// src/scripts/move-file.ts
// Extract from section-utils.ts (approveSection logic)

export async function moveFile(
  app: string,
  options: MoveFileOptions
): Promise<MoveResult> {
  const { sourcePath, destPath, overwrite } = options;

  // Check permissions for both locations
  if (!checkPermission(app, sourcePath, 'read')) {
    throw new PermissionError(`${app} cannot read from ${sourcePath}`);
  }
  if (!checkPermission(app, destPath, 'write')) {
    throw new PermissionError(`${app} cannot write to ${destPath}`);
  }

  // Check destination
  if (await exists(destPath) && !overwrite) {
    return { success: false, error: 'Destination exists' };
  }

  // Ensure destination folder
  await ensureFolder(path.dirname(destPath));

  // Move file
  await fs.rename(sourcePath, destPath);

  // Log operation
  await logOperation({ app, operation: 'move', from: sourcePath, to: destPath, success: true });

  return { success: true, path: destPath };
}

// Section-specific helper (was approveSection file logic)
export async function moveDraftToBrandkit(
  app: string,
  brandPath: string,
  section: string
): Promise<MoveResult> {
  const [category, name] = section.split('/');
  const sourcePath = path.join(brandPath, 'draft', category, name);
  const destPath = path.join(brandPath, 'brandkit', category, name);

  return moveFile(app, { sourcePath, destPath, overwrite: true });
}
```

### Step 6: Create Logging System

```typescript
// src/scripts/log-operation.ts
interface OperationLog {
  timestamp: string;
  app: string;
  operation: 'create' | 'read' | 'write' | 'move' | 'copy' | 'delete';
  path: string;
  from?: string;
  to?: string;
  success: boolean;
  error?: string;
  size?: number;
}

export async function logOperation(log: OperationLog): Promise<void> {
  const logLine = JSON.stringify({
    ...log,
    timestamp: new Date().toISOString()
  });

  // Append to operations log
  await fs.appendFile(
    path.join(__dirname, '../../logs/operations.log'),
    logLine + '\n'
  );

  // If error, also log to errors
  if (!log.success) {
    await fs.appendFile(
      path.join(__dirname, '../../logs/errors.log'),
      logLine + '\n'
    );
  }

  // If create, log to created
  if (log.operation === 'create') {
    await fs.appendFile(
      path.join(__dirname, '../../logs/created.log'),
      logLine + '\n'
    );
  }
}
```

### Step 7: Create Config Files

```json
// _config/locations.json
{
  "bases": {
    "brands": "C:\\Users\\Business\\BrandData",
    "libraries": "C:\\Users\\Business\\BrandLib",
    "exports": "C:\\Users\\Business\\Exports"
  }
}

// _config/permissions.json
{
  "permissions": {
    "brandkit-workflow": {
      "brands/*": "read-write",
      "exports/*": "write"
    },
    "content-pipeline": {
      "brands/*/content": "read-write"
    },
    "folder-creator": {
      "brands": "create"
    }
  }
}

// _config/naming.json
{
  "rules": {
    "brands": {
      "pattern": "^[a-z0-9-]+$",
      "maxLength": 50
    },
    "files": {
      "sanitize": true,
      "uniqueSuffix": true
    }
  }
}
```

### Step 8: Create Public API

```typescript
// src/index.ts
// Brand operations
export async function createBrand(app: string, options: CreateBrandOptions): Promise<CreateBrandResult>;
export async function brandExists(app: string, brandName: string): Promise<boolean>;
export async function listBrands(app: string): Promise<BrandInfo[]>;

// File operations
export async function readFile(app: string, options: ReadFileOptions): Promise<ReadResult>;
export async function writeFile(app: string, options: WriteFileOptions): Promise<WriteResult>;
export async function moveFile(app: string, options: MoveFileOptions): Promise<MoveResult>;
export async function copyFile(app: string, options: CopyFileOptions): Promise<CopyResult>;
export async function deleteFile(app: string, options: DeleteFileOptions): Promise<DeleteResult>;

// Upload operations
export async function uploadFile(app: string, options: UploadOptions): Promise<UploadResult>;
export async function validateUpload(file: Buffer, filename: string): ValidationResult;

// Folder operations
export async function createFolder(app: string, options: CreateFolderOptions): Promise<CreateFolderResult>;
export async function ensureFolder(app: string, path: string): Promise<void>;
export async function folderExists(app: string, path: string): Promise<boolean>;

// Path utilities
export function resolvePath(...parts: string[]): string;
export function getBrandPath(brandName: string): string;
export function slugify(name: string): string;

// JSON helpers
export async function readJson<T>(app: string, options: ReadFileOptions): Promise<T>;
export async function writeJson(app: string, options: WriteFileOptions & { data: any }): Promise<WriteResult>;

// Specific helpers
export async function readBrandConfig(app: string, brandPath: string): Promise<BrandConfig>;
export async function readProgressConfig(app: string, brandPath: string): Promise<ProgressConfig>;
export async function updateProgressConfig(app: string, brandPath: string, updates: Partial<ProgressConfig>): Promise<void>;
```

### Step 9: Update BrandKit Workflow

```typescript
// BEFORE - Direct file operations
import { createBrandFolder } from '@brandkit/folder-creator';
import { handleUpload } from '$lib/server/upload-handler';
import { saveToDraft, loadFromDraft, approveSection } from '$lib/server/section-utils';
import { getBrandConfig } from '$lib/server/brand-status';

await createBrandFolder({ name, domain, region });
await handleUpload({ brandPath, file, filename });
await saveToDraft({ brandPath, section, data });
const config = await getBrandConfig(brandPath);

// AFTER - Via FileManager service
import { FileManager } from '@services/file-manager';

await FileManager.createBrand('brandkit-workflow', { name, domain, region });
await FileManager.uploadFile('brandkit-workflow', { brandPath, file, filename });
await FileManager.writeFile('brandkit-workflow', {
  brandPath,
  folder: `draft/${section}`,
  filename: 'data.json',
  content: JSON.stringify(data)
});
const config = await FileManager.readBrandConfig('brandkit-workflow', brandPath);
```

---

## What Happens to FolderCreator?

FolderCreator gets **merged into FileManager**:

1. **Core logic** → `FileManager/src/scripts/create-brand.ts`
2. **Utilities** → `FileManager/src/utils/`
3. **Config** → `FileManager/_config/structures.json`
4. **FolderCreator folder** → Can be archived or deleted

Apps that imported from FolderCreator now import from FileManager:

```typescript
// BEFORE
import { createBrandFolder } from '@brandkit/folder-creator';

// AFTER
import { FileManager } from '@services/file-manager';
FileManager.createBrand('app-name', options);
```

---

## Integration with Current Build

### Feature Flag Migration

```typescript
// FolderCreator/src/index.ts - Add forwarding
export async function createBrandFolder(options: CreateBrandOptions): Promise<CreateBrandResult> {
  if (process.env.USE_FILEMANAGER_SERVICE === 'true') {
    return FileManager.createBrand('folder-creator-legacy', options);
  }
  return localCreateBrandFolder(options);
}

// lib/server/section-utils.ts - Add forwarding
export async function saveToDraft(options: SaveOptions): Promise<SaveResult> {
  if (process.env.USE_FILEMANAGER_SERVICE === 'true') {
    return FileManager.writeFile('brandkit-workflow', {
      brandPath: options.brandPath,
      folder: `draft/${options.section}`,
      filename: 'data.json',
      content: JSON.stringify(options.data)
    });
  }
  return localSaveToDraft(options);
}
```

---

## Dependencies

| Service | Dependency Type | Purpose |
|---------|-----------------|---------|
| **None required** | - | FileManager is self-contained |

---

## Notes

- Every operation requires app identification
- All operations are logged
- Permissions checked before every write
- FolderCreator merges into this service
- Path resolution is centralized
- Folder creation is automatic on write

---

*Last updated: 2026-01-16*
