# ImageTemplate Module

> **Purpose:** Create and manage image templates for consistent branded graphics
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

ImageTemplate manages reusable image templates for social media, marketing, and brand graphics. It allows creating templates with dynamic placeholders that can be populated with different content while maintaining consistent branding.

```
Template + Data → ImageTemplate.render() → Branded image
```

**Template types:**
- Social media posts
- Story/reel templates
- Ad templates
- Quote graphics
- Announcement graphics
- Event graphics

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `createTemplate(spec)` | Create new template | spec | templateId |
| `render(templateId, data)` | Render template with data | templateId, data | renderedImage |
| `preview(templateId, data)` | Generate preview | templateId, data | previewUrl |
| `listTemplates(filter)` | List templates | filter | templates |
| `duplicateTemplate(templateId)` | Duplicate template | templateId | newTemplateId |
| `updateTemplate(templateId, changes)` | Update template | templateId, changes | updated |
| `exportTemplate(templateId, format)` | Export template | templateId, format | exported |

---

## Detailed Function Specs

### createTemplate(spec)

Creates a new image template.

```javascript
// Input
{
  spec: {
    name: "Quote Post - Instagram",
    type: "social-post",
    platform: "instagram",
    dimensions: { width: 1080, height: 1080 },
    brandId: "acme",

    layers: [
      {
        type: "background",
        fill: "{{brand.primaryColor}}",
        gradient: {
          type: "linear",
          from: "{{brand.primaryColor}}",
          to: "{{brand.secondaryColor}}",
          angle: 135
        }
      },
      {
        type: "shape",
        shape: "rectangle",
        x: 80, y: 80,
        width: 920, height: 920,
        fill: "#ffffff",
        opacity: 0.95,
        cornerRadius: 20
      },
      {
        type: "text",
        id: "quote",
        content: "{{quote}}",
        x: 120, y: 200,
        width: 840,
        font: "{{brand.headingFont}}",
        fontSize: 48,
        fontWeight: 600,
        color: "{{brand.textColor}}",
        align: "center",
        lineHeight: 1.4
      },
      {
        type: "text",
        id: "author",
        content: "— {{author}}",
        x: 120, y: 750,
        width: 840,
        font: "{{brand.bodyFont}}",
        fontSize: 24,
        color: "{{brand.secondaryColor}}",
        align: "center"
      },
      {
        type: "image",
        id: "logo",
        src: "{{brand.logo}}",
        x: 440, y: 900,
        width: 200,
        height: "auto"
      }
    ],

    placeholders: {
      "quote": { type: "text", maxLength: 200, required: true },
      "author": { type: "text", maxLength: 50, required: false, default: "" }
    }
  }
}

// Output
{
  templateId: "tmpl_quote_ig_001",
  name: "Quote Post - Instagram",
  created: "2026-01-17",
  preview: "/previews/tmpl_quote_ig_001.png"
}
```

### render(templateId, data)

Renders template with data to final image.

```javascript
// Input
{
  templateId: "tmpl_quote_ig_001",
  data: {
    quote: "Innovation distinguishes between a leader and a follower.",
    author: "Steve Jobs"
  },
  brandId: "acme",
  output: {
    format: "png",
    quality: 100
  }
}

// Process
1. Load template
2. Load brand assets
3. Replace placeholders
4. Render layers
5. Export image

// Output
{
  success: true,
  image: {
    path: "/generated/quote_20260117_143052.png",
    width: 1080,
    height: 1080,
    format: "png",
    size: 245678
  },
  template: "tmpl_quote_ig_001",
  data: { quote: "...", author: "..." }
}
```

### preview(templateId, data)

Generates quick preview without full rendering.

```javascript
// Input
{
  templateId: "tmpl_quote_ig_001",
  data: {
    quote: "Preview text here",
    author: "Author Name"
  }
}

// Output
{
  previewUrl: "/previews/tmpl_quote_ig_001_preview_abc123.jpg",
  dimensions: { width: 540, height: 540 },  // Half size preview
  expires: "2026-01-17T15:30:00Z"
}
```

---

## Template Structure

```json
{
  "templateId": "tmpl_quote_ig_001",
  "name": "Quote Post - Instagram",
  "type": "social-post",
  "platform": "instagram",
  "dimensions": { "width": 1080, "height": 1080 },
  "brandId": "acme",

  "layers": [
    { "type": "background", ... },
    { "type": "shape", ... },
    { "type": "text", "id": "quote", ... },
    { "type": "text", "id": "author", ... },
    { "type": "image", "id": "logo", ... }
  ],

  "placeholders": {
    "quote": { "type": "text", "maxLength": 200, "required": true },
    "author": { "type": "text", "maxLength": 50, "required": false }
  },

  "brandBindings": {
    "{{brand.primaryColor}}": "colors.primary.500",
    "{{brand.secondaryColor}}": "colors.secondary.500",
    "{{brand.headingFont}}": "typography.fonts.heading",
    "{{brand.logo}}": "logo.primary"
  },

  "tags": ["social", "instagram", "quote"],
  "created": "2026-01-17",
  "updated": "2026-01-17"
}
```

---

## Layer Types

| Type | Properties |
|------|------------|
| `background` | fill, gradient, image |
| `shape` | rectangle, circle, polygon |
| `text` | content, font, size, color, align |
| `image` | src, position, size, mask |
| `group` | layers (nested), transform |

---

## Platform Dimensions

| Platform | Type | Dimensions |
|----------|------|------------|
| Instagram | Post | 1080x1080 |
| Instagram | Story | 1080x1920 |
| Facebook | Post | 1200x630 |
| LinkedIn | Post | 1200x627 |
| Twitter | Post | 1200x675 |
| Pinterest | Pin | 1000x1500 |

---

## Config

### module.config.json

```json
{
  "module": "ImageTemplate",
  "version": "1.0.0",
  "description": "Create and manage image templates",
  "type": "pipeline",

  "functions": [
    { "name": "createTemplate", "input": ["spec"], "output": "templateId" },
    { "name": "render", "input": ["templateId", "data"], "output": "renderedImage" },
    { "name": "preview", "input": ["templateId", "data"], "output": "previewUrl" },
    { "name": "listTemplates", "input": ["filter"], "output": "templates" },
    { "name": "duplicateTemplate", "input": ["templateId"], "output": "newTemplateId" },
    { "name": "updateTemplate", "input": ["templateId", "changes"], "output": "updated" },
    { "name": "exportTemplate", "input": ["templateId", "format"], "output": "exported" }
  ],

  "config": {
    "templatesRepo": "image-templates",
    "previewsPath": "/previews/",
    "defaultFormat": "png",
    "defaultQuality": 90
  }
}
```

---

## Used By

| Module | How |
|--------|-----|
| SocialMediaPlanner | Creating social graphics |
| Content | Marketing graphics |
| BrandKit | Template generation |

---

*Last updated: 2026-01-17*
