# PDFMaker Module

> **Purpose:** Generate PDF documents from templates and data
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

PDFMaker creates PDF documents - brand guides, reports, invoices, proposals. It takes templates and data, renders them, and outputs professional PDFs. Works with HTML/CSS templates converted to PDF.

```
Template + Data → PDFMaker.generate() → PDF file
```

**Document types:**
- Brand guides
- Reports/analytics
- Invoices
- Proposals
- Certificates
- Export packages

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `generate(templateId, data, options)` | Generate PDF from template | templateId, data, options | pdfPath |
| `fromHTML(html, options)` | Convert HTML to PDF | html, options | pdfPath |
| `merge(pdfs)` | Merge multiple PDFs | pdfs | mergedPdf |
| `addWatermark(pdf, watermark)` | Add watermark to PDF | pdf, watermark | watermarkedPdf |
| `addPageNumbers(pdf, options)` | Add page numbers | pdf, options | numberedPdf |
| `compress(pdf, quality)` | Compress PDF | pdf, quality | compressedPdf |
| `protect(pdf, password, permissions)` | Password protect PDF | pdf, password, permissions | protectedPdf |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `PDFMaker.generateBrandGuide` | Generate full brand guide | generate → addPageNumbers → compress |
| `PDFMaker.generateReport` | Generate report with cover | generate → merge → addWatermark |

---

## Detailed Function Specs

### generate(templateId, data, options)

Generates PDF from template.

```javascript
// Input
{
  templateId: "brand-guide-standard",
  data: {
    brand: {
      name: "Acme Corp",
      tagline: "Innovation starts here",
      logo: "/brands/acme/assets/logo.svg"
    },
    colors: {
      primary: "#1a73e8",
      secondary: "#34a853",
      palette: [...]
    },
    typography: {
      heading: "Montserrat",
      body: "Open Sans"
    },
    content: {
      intro: "Acme Corp is a leading...",
      values: ["Innovation", "Reliability"]
    }
  },
  options: {
    format: "A4",
    orientation: "portrait",
    margins: { top: 20, right: 20, bottom: 20, left: 20 },
    outputPath: "/brands/acme/exports/"
  }
}

// Process
1. Load PDF template
2. Render HTML with data
3. Apply brand styles (colors, fonts)
4. Convert to PDF
5. Save to outputPath

// Output
{
  success: true,
  path: "/brands/acme/exports/brand-guide-acme-20260117.pdf",
  pages: 12,
  size: 2456789
}
```

### fromHTML(html, options)

Converts raw HTML to PDF.

```javascript
// Input
{
  html: "<html><head><style>...</style></head><body>...</body></html>",
  options: {
    format: "A4",
    printBackground: true,
    displayHeaderFooter: true,
    headerTemplate: "<div style='font-size:10px'>{{brandName}}</div>",
    footerTemplate: "<div style='font-size:10px'>Page <span class='pageNumber'></span></div>"
  }
}

// Output
{
  success: true,
  buffer: <Buffer>,  // PDF as buffer
  pages: 5
}
```

### merge(pdfs)

Merges multiple PDFs into one.

```javascript
// Input
{
  pdfs: [
    "/exports/cover.pdf",
    "/exports/content.pdf",
    "/exports/appendix.pdf"
  ]
}

// Output
{
  success: true,
  path: "/exports/merged.pdf",
  totalPages: 24
}
```

---

## PDF Templates

Stored in `/repos/pdf-templates/`:

```json
{
  "id": "brand-guide-standard",
  "name": "Standard Brand Guide",
  "type": "brand-guide",
  "pages": [
    {
      "type": "cover",
      "template": "cover-centered",
      "fields": ["brand.logo", "brand.name", "brand.tagline"]
    },
    {
      "type": "toc",
      "template": "toc-standard",
      "auto": true
    },
    {
      "type": "section",
      "title": "Brand Overview",
      "template": "content-standard",
      "fields": ["content.intro", "content.values"]
    },
    {
      "type": "section",
      "title": "Color Palette",
      "template": "color-showcase",
      "fields": ["colors.primary", "colors.palette"]
    },
    {
      "type": "section",
      "title": "Typography",
      "template": "typography-showcase",
      "fields": ["typography.heading", "typography.body"]
    }
  ],
  "styles": {
    "primaryColor": "{{colors.primary}}",
    "headingFont": "{{typography.heading}}",
    "bodyFont": "{{typography.body}}"
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "PDFMaker",
  "version": "1.0.0",
  "description": "Generate PDF documents",
  "type": "pipeline",

  "functions": [
    {
      "name": "generate",
      "description": "Generate PDF from template",
      "input": ["templateId", "data", "options"],
      "output": "pdfPath"
    },
    {
      "name": "fromHTML",
      "description": "Convert HTML to PDF",
      "input": ["html", "options"],
      "output": "pdfPath"
    },
    {
      "name": "merge",
      "description": "Merge multiple PDFs",
      "input": ["pdfs"],
      "output": "mergedPdf"
    },
    {
      "name": "addWatermark",
      "description": "Add watermark to PDF",
      "input": ["pdf", "watermark"],
      "output": "watermarkedPdf"
    },
    {
      "name": "addPageNumbers",
      "description": "Add page numbers",
      "input": ["pdf", "options"],
      "output": "numberedPdf"
    },
    {
      "name": "compress",
      "description": "Compress PDF",
      "input": ["pdf", "quality"],
      "output": "compressedPdf"
    },
    {
      "name": "protect",
      "description": "Password protect PDF",
      "input": ["pdf", "password", "permissions"],
      "output": "protectedPdf"
    }
  ],

  "steps": [
    {
      "name": "generateBrandGuide",
      "description": "Generate full brand guide",
      "sequence": ["generate", "addPageNumbers", "compress"]
    },
    {
      "name": "generateReport",
      "description": "Generate report with cover",
      "sequence": ["generate", "merge", "addWatermark"]
    }
  ],

  "config": {
    "defaultFormat": "A4",
    "defaultMargins": { "top": 20, "right": 20, "bottom": 20, "left": 20 },
    "templatesRepo": "pdf-templates",
    "compressionQuality": 0.8
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Templates | Loading PDF templates |
| FileManager | Loading brand assets |
| Writer | Saving PDF files |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Generating brand guides |
| Export | PDF exports |
| Reports | Analytics reports |

---

*Last updated: 2026-01-17*
