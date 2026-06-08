# DataParser Service

> Central service for ALL format conversion and data parsing.
> **Status:** Planning | **Priority:** MEDIUM
> **Location:** `C:\Users\Business\DataParser\`

---

## Overview

The DataParser service handles all format conversion and parsing. Apps request conversions by input/output format - they never parse directly. DataParser handles format detection, conversion, AI response parsing, schema validation, and placeholder replacement.

### Current State (BrandKit Workflow)

Parsing functionality is scattered across multiple locations:

```
BrandKit Workflow/
├── lib/server/
│   ├── response-processor.ts       # Parse AI responses
│   ├── css-token-parser.ts         # Parse CSS/SCSS to colours
│   ├── legal-templates.ts          # Placeholder replacement
│   ├── website-fetcher.ts          # Fetch and parse websites
│   └── types/
│       ├── response-processor.ts
│       └── visual.ts
│
└── lib/parsers/
    ├── index.ts                    # Parser registry
    ├── pdf-parser.ts               # PDF text extraction
    ├── docx-parser.ts              # DOCX parsing
    ├── json-parser.ts              # JSON to text
    ├── csv-parser.ts               # CSV parsing
    ├── text-parser.ts              # Text passthrough
    └── types/parser.ts             # Parser types
```

### Target State (DataParser Service)

```
DataParser/
├── src/
│   ├── index.ts                    # Public API
│   ├── scripts/
│   │   ├── detect-format.ts        # Auto-detect input format
│   │   ├── parse-file.ts           # Parse uploaded files
│   │   ├── ai-to-json.ts           # Parse AI responses
│   │   ├── json-to-md.ts           # JSON → Markdown
│   │   ├── json-to-pdf.ts          # JSON → PDF
│   │   ├── json-to-html.ts         # JSON → HTML
│   │   ├── json-to-csv.ts          # JSON → CSV
│   │   ├── md-to-html.ts           # Markdown → HTML
│   │   ├── css-to-colours.ts       # CSS → colour objects
│   │   ├── scss-to-colours.ts      # SCSS → colour objects
│   │   ├── fetch-website.ts        # Fetch & parse websites
│   │   ├── replace-placeholders.ts # {{var}} replacement
│   │   └── validate-schema.ts      # JSON Schema validation
│   └── types/
│       └── index.ts
├── templates/
│   ├── markdown/
│   │   ├── brand-doc.md
│   │   ├── prompt-pack.md
│   │   └── section-export.md
│   ├── pdf/
│   │   └── brand-guidelines.html
│   └── html/
│       └── export.html
├── _config/
│   ├── schemas/                    # Validation schemas
│   ├── format-rules.json           # Conversion rules
│   └── placeholders.json           # Placeholder definitions
├── package.json
└── tsconfig.json
```

---

## Extraction Mapping

### Files to Move

| Current Location | Target Location | What to Change |
|------------------|-----------------|----------------|
| `lib/parsers/pdf-parser.ts` | `src/scripts/parse-file.ts` | Merge into unified parser |
| `lib/parsers/docx-parser.ts` | `src/scripts/parse-file.ts` | Merge into unified parser |
| `lib/parsers/json-parser.ts` | `src/scripts/parse-file.ts` | Merge into unified parser |
| `lib/parsers/csv-parser.ts` | `src/scripts/parse-file.ts` | Merge into unified parser |
| `lib/parsers/text-parser.ts` | `src/scripts/parse-file.ts` | Merge into unified parser |
| `lib/parsers/index.ts` | `src/scripts/parse-file.ts` | Registry becomes internal |
| `lib/server/response-processor.ts` | `src/scripts/ai-to-json.ts` | Add logging |
| `lib/server/css-token-parser.ts` | `src/scripts/css-to-colours.ts` | Split CSS/SCSS |
| `lib/server/legal-templates.ts` (partial) | `src/scripts/replace-placeholders.ts` | Extract placeholder logic |
| `lib/server/website-fetcher.ts` | `src/scripts/fetch-website.ts` | Add caching |

### Functions to Extract

| Current Function | Current File | Target Script |
|------------------|--------------|---------------|
| `parseFile()` | `lib/parsers/index.ts` | `parse-file.ts` |
| `parseJSON()` | `response-processor.ts` | `ai-to-json.ts` |
| `validateWithSchema()` | `response-processor.ts` | `validate-schema.ts` |
| `extractInferredFields()` | `response-processor.ts` | `ai-to-json.ts` |
| `processResponse()` | `response-processor.ts` | `ai-to-json.ts` |
| `parseCSSColorTokens()` | `css-token-parser.ts` | `css-to-colours.ts` |
| `parseSCSSVariables()` | `css-token-parser.ts` | `scss-to-colours.ts` |
| `parseCSSInJS()` | `css-token-parser.ts` | `css-to-colours.ts` |
| `parseDesignTokens()` | `css-token-parser.ts` | `css-to-colours.ts` |
| `replacePlaceholders()` | `legal-templates.ts` | `replace-placeholders.ts` |
| `processConditionals()` | `legal-templates.ts` | `replace-placeholders.ts` |
| `fetchWebsite()` | `website-fetcher.ts` | `fetch-website.ts` |

---

## Migration Steps

### Step 1: Create DataParser Shell

```bash
mkdir C:\Users\Business\DataParser
cd C:\Users\Business\DataParser
npm init -y

# Install dependencies
npm install typescript ts-node
npm install pdf-parse mammoth turndown cheerio gray-matter

mkdir src src/scripts src/types templates templates/markdown templates/pdf templates/html _config _config/schemas
```

### Step 2: Merge File Parsers

```typescript
// src/scripts/parse-file.ts
// Combine all parsers from lib/parsers/ into one file

import pdfParse from 'pdf-parse';
import mammoth from 'mammoth';

type ParserType = 'pdf' | 'docx' | 'json' | 'csv' | 'txt' | 'md';

export async function parseFile(
  content: Buffer | string,
  filename: string
): Promise<ParseResult> {
  const ext = getExtension(filename);
  const parser = getParser(ext);
  return parser(content, filename);
}

// Copy parser implementations from:
// - lib/parsers/pdf-parser.ts → parsePDF()
// - lib/parsers/docx-parser.ts → parseDOCX()
// - lib/parsers/json-parser.ts → parseJSON()
// - lib/parsers/csv-parser.ts → parseCSV()
// - lib/parsers/text-parser.ts → parseText()
```

### Step 3: Extract AI Response Parser

```typescript
// src/scripts/ai-to-json.ts
// Copy from lib/server/response-processor.ts

// COPY these functions:
// - parseJSON() - JSON extraction with repair
// - extractInferredFields() - Find [INFERRED] markers
// - analyzePartialness() - Check completeness
// - processResponse() - Main entry point

// ADD:
// - Logging of parse operations
// - Better error messages
```

### Step 4: Extract CSS Parsers

```typescript
// src/scripts/css-to-colours.ts
// Copy from lib/server/css-token-parser.ts

// COPY these functions:
// - parseCSSColorTokens()
// - parseCSSFontTokens()
// - parseCSSInJS()
// - parseDesignTokens()
// - parseAnyFormat()
// - detectFormat()
// - All helper functions (hexToRgb, rgbToHsl, etc.)

// KEEP types from lib/server/types/visual.ts
```

### Step 5: Extract Placeholder System

```typescript
// src/scripts/replace-placeholders.ts
// Extract from lib/server/legal-templates.ts

// COPY these functions:
// - replacePlaceholders()
// - processConditionals()
// - findMissingPlaceholders()
// - validateTemplatePlaceholders()

// DO NOT COPY:
// - Template content (stays in Librarian)
// - generateLegalContent() (uses templates)
```

### Step 6: Create New Conversion Scripts

```typescript
// src/scripts/json-to-md.ts - NEW
export function jsonToMarkdown(
  data: any,
  template?: string
): string {
  if (template) {
    return applyTemplate(data, template);
  }
  return generateDefaultMarkdown(data);
}

// src/scripts/json-to-pdf.ts - NEW
export async function jsonToPDF(
  data: any,
  template?: string
): Promise<Buffer> {
  const html = jsonToHTML(data, template);
  return htmlToPDF(html);
}

// src/scripts/json-to-html.ts - NEW
export function jsonToHTML(
  data: any,
  template?: string
): string {
  // Use template or generate default
}
```

### Step 7: Create Public API

```typescript
// src/index.ts
export async function parse(
  input: Buffer | string,
  options: ParseOptions
): Promise<ParseResult> {
  const format = options.inputFormat || detectFormat(input, options.filename);

  switch (format) {
    case 'pdf':
    case 'docx':
    case 'csv':
    case 'txt':
    case 'md':
      return parseFile(input, options.filename);

    case 'ai-response':
      return parseAIResponse(input as string, options.schema);

    case 'css':
    case 'scss':
      return parseColours(input as string, format);

    default:
      throw new Error(`Unknown format: ${format}`);
  }
}

export async function convert(
  data: any,
  options: ConvertOptions
): Promise<ConvertResult> {
  const { outputFormat, template } = options;

  switch (outputFormat) {
    case 'markdown':
      return { data: jsonToMarkdown(data, template), format: 'markdown' };
    case 'pdf':
      return { data: await jsonToPDF(data, template), format: 'pdf' };
    case 'html':
      return { data: jsonToHTML(data, template), format: 'html' };
    case 'csv':
      return { data: jsonToCSV(data), format: 'csv' };
    default:
      throw new Error(`Unknown output format: ${outputFormat}`);
  }
}

export function replacePlaceholders(
  template: string,
  values: Record<string, any>
): string;

export function validateSchema(
  data: any,
  schema: JSONSchema
): ValidationResult;
```

### Step 8: Update BrandKit Workflow

```typescript
// BEFORE - Direct parser usage
import { parseFile } from '$lib/parsers';
import { processResponse } from '$lib/server/response-processor';
import { parseCSSColorTokens } from '$lib/server/css-token-parser';

const parsed = await parseFile({ brandPath, sourceId, filePath });
const aiData = processResponse(aiResponse, { section });
const colours = parseCSSColorTokens(cssContent);

// AFTER - Via DataParser service
import { DataParser } from '@services/data-parser';

const parsed = await DataParser.parse(fileBuffer, { filename: 'doc.pdf' });
const aiData = await DataParser.parse(aiResponse, { inputFormat: 'ai-response', schema });
const colours = await DataParser.parse(cssContent, { inputFormat: 'css' });
```

---

## Integration with Current Build

### Feature Flag Migration

```typescript
// lib/parsers/index.ts - Add forwarding
export async function parseFile(options: ParseOptions): Promise<ParseResult> {
  if (process.env.USE_DATAPARSER_SERVICE === 'true') {
    const content = await fs.readFile(options.filePath);
    return DataParser.parse(content, {
      filename: path.basename(options.filePath)
    });
  }

  // Local implementation
  return localParseFile(options);
}

// lib/server/response-processor.ts - Add forwarding
export function processResponse(response: string, options: ProcessOptions): ProcessedResponse {
  if (process.env.USE_DATAPARSER_SERVICE === 'true') {
    return DataParser.parse(response, {
      inputFormat: 'ai-response',
      schema: options.section
    });
  }

  return localProcessResponse(response, options);
}
```

### File Location Reference

After extraction, update these imports in BrandKit Workflow:

| Old Import | New Import |
|------------|------------|
| `from '$lib/parsers'` | `from '@services/data-parser'` |
| `from '$lib/server/response-processor'` | `from '@services/data-parser'` |
| `from '$lib/server/css-token-parser'` | `from '@services/data-parser'` |
| `from '$lib/server/website-fetcher'` | `from '@services/data-parser'` |

---

## Public API Summary

```typescript
// Parsing
parse(input, options): Promise<ParseResult>
parseFile(buffer, filename): Promise<ParseResult>
parseAIResponse(response, schema?): Promise<ParsedAIResponse>
parseColours(content, format): Promise<ParsedColours>

// Converting
convert(data, options): Promise<ConvertResult>
jsonToMarkdown(data, template?): string
jsonToPDF(data, template?): Promise<Buffer>
jsonToHTML(data, template?): string
jsonToCSV(data): string

// Utilities
detectFormat(content, filename?): FormatType
replacePlaceholders(template, values): string
validateSchema(data, schema): ValidationResult
fetchWebsite(url, options): Promise<WebsiteContent>
```

---

## Dependencies

| Service | Dependency Type | Purpose |
|---------|-----------------|---------|
| **None required** | - | DataParser is self-contained |

---

## Notes

- All parsers merged into unified `parse()` function
- Format auto-detection from content or filename
- Templates stored in `templates/` folder
- Schema validation uses JSON Schema
- PDF generation requires html-to-pdf library
- Website fetching includes rate limiting

---

*Last updated: 2026-01-16*
