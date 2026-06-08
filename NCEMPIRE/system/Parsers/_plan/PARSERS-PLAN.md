# Parsers Service Plan

> **Purpose:** Check structure, format data, validate format/structure.
> **Level:** Processing Layer
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Parsers handle all data formatting and structure validation. They check if data is in the right format, transform data between formats, and validate structure.

**Key Principle:** Parsers ensure data is correctly formatted and structured. In → Validated/Formatted Out.

---

## Parser Types

| Parser | Input | Output | Purpose |
|--------|-------|--------|---------|
| **ImageParser** | Image file/buffer | Resized/converted image | Process uploads |
| **CSSParser** | CSS/SCSS/tokens | Structured colour/font data | Extract design tokens |
| **DataParser** | Raw AI response | Validated section data | Process AI output |
| **JSONParser** | JSON string | Validated object | Safe JSON parsing |
| **MarkdownParser** | Markdown string | HTML or structured data | Convert markdown |

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Transform data formats | Read from disk (uses fs directly if needed) |
| Validate structure | Write to disk (Archivist/Writer do) |
| Convert between types | Make storage decisions |
| Report errors in input | Handle retries (FailSys does) |
| Return transformed output | Cache results (Librarian does) |

---

## ImageParser

Handles image processing: resize, convert, extract metadata.

### API

```typescript
// lib/server/parsers/image-parser.ts

interface ImageParseOptions {
  maxWidth?: number;
  maxHeight?: number;
  format?: 'png' | 'jpeg' | 'webp';
  quality?: number;  // 1-100 for jpeg/webp
}

interface ImageMetadata {
  width: number;
  height: number;
  format: string;
  size: number;
  hasAlpha: boolean;
}

interface ImageParseResult {
  success: boolean;
  buffer?: Buffer;
  metadata?: ImageMetadata;
  error?: string;
}

function parseImage(input: Buffer | string, options?: ImageParseOptions): Promise<ImageParseResult>;
function getImageMetadata(input: Buffer | string): Promise<ImageMetadata>;
function resizeImage(input: Buffer, width: number, height: number): Promise<Buffer>;
function convertImage(input: Buffer, format: 'png' | 'jpeg' | 'webp'): Promise<Buffer>;
```

### Usage

```typescript
// Resize uploaded logo
const result = await ImageParser.parseImage(uploadBuffer, {
  maxWidth: 512,
  maxHeight: 512,
  format: 'png'
});

if (result.success) {
  // Pass to Archivist for storage
  await archivist.store({
    data: result.buffer,
    category: 'image',
    metadata: result.metadata
  });
}
```

### Dependencies

- `sharp` package for image processing

---

## CSSParser

Extracts design tokens from CSS, SCSS, CSS-in-JS, and design token files.

**Note:** Most of this already exists in `css-token-parser.ts` from Phase 7.

### API

```typescript
// lib/server/parsers/css-parser.ts (extends existing)

interface CSSParseOptions {
  extractColours?: boolean;
  extractFonts?: boolean;
  extractSpacing?: boolean;
  format?: 'css' | 'scss' | 'cssInJs' | 'designTokens';
}

interface CSSParseResult {
  success: boolean;
  colours?: ParsedColor[];
  fonts?: ParsedFont[];
  spacing?: ParsedSpacing[];
  warnings?: string[];
  error?: string;
}

function parseCSS(input: string, options?: CSSParseOptions): CSSParseResult;
function parseCSSFile(filePath: string, options?: CSSParseOptions): Promise<CSSParseResult>;
function detectCSSFormat(input: string): 'css' | 'scss' | 'cssInJs' | 'designTokens';
```

### Current Implementation

Already built in Phase 7A:
- `parseCSSColorTokens()`
- `parseCSSFontTokens()`
- `parseSCSSVariables()`
- `parseCSSInJS()`
- `parseDesignTokens()`

May need wrapper for consistent interface.

---

## DataParser

Processes raw AI responses into validated section data.

**Note:** Most of this exists in `response-processor.ts` from Phase 4.

### API

```typescript
// lib/server/parsers/data-parser.ts (extends existing)

interface DataParseOptions {
  schema?: JSONSchema;
  repairAttempts?: number;
  fillDefaults?: boolean;
}

interface DataParseResult {
  success: boolean;
  data?: unknown;
  validation?: ValidationResult;
  repairs?: RepairAttempt[];
  error?: string;
}

function parseData(input: string, options?: DataParseOptions): DataParseResult;
function parseAIResponse(response: string, sectionId: string): Promise<DataParseResult>;
function validateAgainstSchema(data: unknown, schema: JSONSchema): ValidationResult;
```

### Current Implementation

Already built in Phase 4.5:
- `parseJSON()`
- `validateWithSchema()`
- `processResponse()`
- `fillDefaults()`

May need wrapper for consistent interface.

---

## JSONParser

Safe JSON parsing with error handling.

### API

```typescript
// lib/server/parsers/json-parser.ts

interface JSONParseResult<T = unknown> {
  success: boolean;
  data?: T;
  error?: string;
  errorPosition?: { line: number; column: number };
}

function parseJSON<T>(input: string): JSONParseResult<T>;
function tryRepairJSON(input: string): JSONParseResult;
function isValidJSON(input: string): boolean;
```

### Usage

```typescript
const result = JSONParser.parseJSON<BrandConfig>(jsonString);
if (result.success) {
  console.log(result.data.name);
} else {
  console.error(`Parse error at line ${result.errorPosition?.line}`);
}
```

---

## MarkdownParser

Convert markdown to other formats.

### API

```typescript
// lib/server/parsers/markdown-parser.ts

interface MarkdownParseOptions {
  sanitize?: boolean;
  allowHtml?: boolean;
}

interface MarkdownSection {
  level: number;
  title: string;
  content: string;
  children: MarkdownSection[];
}

function parseMarkdownToHTML(input: string, options?: MarkdownParseOptions): string;
function parseMarkdownToSections(input: string): MarkdownSection[];
function extractMarkdownTOC(input: string): string[];
```

---

## Common Pattern

All parsers follow the same pattern:

```typescript
interface ParseResult<T> {
  success: boolean;
  data?: T;
  error?: string;
  warnings?: string[];
}

// Pure function - no side effects
function parse(input: InputType, options?: Options): ParseResult<OutputType>;
```

- **Input:** Raw data
- **Output:** Transformed data + success/error
- **Side effects:** None
- **State:** None (stateless)

---

## Error Handling

Parsers catch errors and return them in the result:

```typescript
const result = ImageParser.parseImage(badBuffer);
if (!result.success) {
  // Report to FailSys
  await FailSys.report({
    service: 'parser',
    operation: 'image-parse',
    error: result.error,
    data: { inputSize: badBuffer.length }
  });
}
```

---

## Implementation Notes

### File Locations

```
lib/server/parsers/
  image-parser.ts
  css-parser.ts      (may wrap existing css-token-parser.ts)
  data-parser.ts     (may wrap existing response-processor.ts)
  json-parser.ts
  markdown-parser.ts
  index.ts           (re-exports all)
```

### Dependencies

| Parser | Dependencies |
|--------|--------------|
| ImageParser | `sharp` |
| CSSParser | (none - existing code) |
| DataParser | (none - existing code) |
| JSONParser | (none) |
| MarkdownParser | `marked` or similar |

### Size Estimate

- ImageParser: ~150 lines (new)
- CSSParser: ~50 lines wrapper (existing: 800+ lines)
- DataParser: ~50 lines wrapper (existing: 400+ lines)
- JSONParser: ~80 lines (new)
- MarkdownParser: ~100 lines (new)

---

## Build Phases

### P1: JSONParser
- Safe parsing
- Error position detection
- Repair attempts

### P2: ImageParser
- Resize
- Convert format
- Metadata extraction

### P3: Parser Wrappers
- CSSParser wrapper (around existing)
- DataParser wrapper (around existing)

### P4: MarkdownParser
- To HTML
- To sections
- TOC extraction

---

## Testing

| Test | Description |
|------|-------------|
| Parse valid JSON | Returns data |
| Parse invalid JSON | Returns error with position |
| Resize image | Correct dimensions |
| Convert image | Correct format |
| Invalid image | Returns error |
| Parse CSS | Extracts colours/fonts |
| Parse AI response | Validates against schema |
| Parse markdown | Converts to HTML |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [FAILSYS-PLAN.md](./FAILSYS-PLAN.md) - Receives parser errors
- [../Librarian/LIBRARIAN-ARCHITECTURE.md](../Librarian/LIBRARIAN-ARCHITECTURE.md) - May use parsed data

---

*Parsers are pure transformations - data in, data out, no side effects.*
