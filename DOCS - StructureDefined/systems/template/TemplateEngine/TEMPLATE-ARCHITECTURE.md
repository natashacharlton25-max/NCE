# Template Architecture

> **Purpose:** Define the template system - repos, modules, and rendering pipeline
> **Created:** 2026-01-19
> **Status:** Draft - Awaiting Approval

---

## Core Concept

**Templates = Section Assembly Instructions**

```
Template = "Put these sections in this order"
```

That's it.

---

## The Simple Model

| Component | What It Does |
|-----------|--------------|
| **Section Types** | Universal building blocks (title, body, exercise, stats, quote, cta, etc.) |
| **Templates** | Ordered list of sections |
| **AI** | Fills each section with content |
| **Renderer** | Turns sections into pages/slides/documents |

---

## Example Templates

### Entrepreneur Workbook
```json
{
  "id": "template_entrepreneur-workbook",
  "sections": ["title", "body", "body", "exercise", "closing"]
}
```

### Blog Post
```json
{
  "id": "template_blog-post",
  "sections": ["heading", "body", "body", "body", "cta"]
}
```

### Sales Page
```json
{
  "id": "template_sales-page",
  "sections": ["title", "body", "stats", "quote", "checklist", "cta", "footer"]
}
```

### Case Study
```json
{
  "id": "template_case-study",
  "sections": ["title", "body", "stats", "quote", "body", "closing"]
}
```

### Pitch Deck
```json
{
  "id": "template_pitch-deck",
  "sections": ["title", "body", "stats", "diagram", "comparison", "timeline", "cta", "closing"]
}
```

---

## Flow

```
TEMPLATE                    AI                      RENDERER
────────                    ──                      ────────

["title",          →    Fills each section    →    Maps to page layouts
 "body",                with content               and builds document
 "body",
 "exercise",
 "closing"]
```

---

## AI Already Knows

```
Section type: "testimonial"
Content type: "podcast-show-notes"

AI knows what these are. No custom prompt needed.
```

**Generic prompt handles it:**
```
Create a {{section}} for this {{contentType}}.
Topic: {{topic}}
```

AI understands what a testimonial is, what podcast show notes are, how to combine them.

---

## Libraries (Data, Not Code)

| Library | Repo | Contains |
|---------|------|----------|
| **Section Specs** | `/repos/section-specs/` | Field definitions, constraints, variant sizes |
| **Content Types** | `/repos/content-types/` | Document descriptions, typical sections |
| **Templates** | `/repos/templates/` | Section assembly instructions |
| **Brands** | `/repos/brands/` | Voice, tone, values |
| **Audiences** | `/repos/audiences/` | Who, language, motivators |
| **Prompts** | `/repos/prompts/` | AI prompts (minimal - generic works 99%) |
| **Global Rules** | `/repos/global-rules/` | Always/never constraints |

---

## Section Specs Library

Section specs live in `/repos/section-specs/` with detailed field definitions.

```
/repos/section-specs/
├── title.json
├── body.json
├── cta.json
├── exercise.json
├── quote.json
├── image.json
├── link.json
├── stats.json
├── checklist.json
├── testimonial.json
└── ...
```

### Example Section Specs

**title.json**
```json
{
  "id": "section_title",
  "name": "Title",
  "fields": {
    "title": { "type": "text", "words": { "min": 2, "max": 12 }, "required": true },
    "subtitle": { "type": "text", "words": { "min": 5, "max": 25 }, "required": false }
  }
}
```

**image.json** (with variant sizes)
```json
{
  "id": "section_image",
  "name": "Image",
  "fields": {
    "image": { "type": "image", "source": ["brand-library", "unsplash", "ai-generated"], "required": true },
    "caption": { "type": "text", "words": { "max": 20 }, "required": false },
    "alt": { "type": "text", "words": { "max": 15 }, "required": true }
  },
  "sizes": {
    "instagram-post": { "width": 1080, "height": 1080 },
    "instagram-story": { "width": 1080, "height": 1920 },
    "facebook-post": { "width": 1200, "height": 630 },
    "linkedin-post": { "width": 1200, "height": 627 },
    "twitter-post": { "width": 1200, "height": 675 },
    "blog-featured": { "width": 1200, "height": 630 }
  }
}
```

**testimonial.json**
```json
{
  "id": "section_testimonial",
  "name": "Testimonial",
  "fields": {
    "quote": { "type": "text", "words": { "min": 10, "max": 75 }, "required": true },
    "author": { "type": "text", "words": { "max": 5 }, "required": true },
    "role": { "type": "text", "words": { "max": 8 }, "required": false },
    "company": { "type": "text", "words": { "max": 5 }, "required": false },
    "image": { "type": "image", "size": { "width": 100, "height": 100 }, "required": false },
    "rating": { "type": "number", "min": 1, "max": 5, "required": false }
  }
}
```

**stats.json**
```json
{
  "id": "section_stats",
  "name": "Statistics",
  "fields": {
    "title": { "type": "text", "words": { "max": 8 }, "required": false },
    "stats": {
      "type": "array",
      "items": {
        "number": { "type": "text", "required": true },
        "label": { "type": "text", "words": { "max": 5 }, "required": true }
      },
      "count": { "min": 2, "max": 6 },
      "required": true
    },
    "source": { "type": "text", "words": { "max": 15 }, "required": false }
  }
}
```

### Field Types

| Type | Description | Constraints |
|------|-------------|-------------|
| `text` | Plain text | words, chars |
| `url` | Link | allowedDomains |
| `image` | Image file | source, size |
| `array` | List of items | count, items |
| `number` | Numeric value | min, max |
| `input-area` | User input space | lines |

---

## Content Type Library

Content types live in `/repos/content-types/` with typical sections and variants.

```json
{
  "entrepreneur-workbook": {
    "description": "Educational guide with exercises",
    "typical-sections": ["title", "body", "body", "exercise", "closing"]
  },
  "podcast-show-notes": {
    "description": "Summary and resources from podcast episode",
    "typical-sections": ["title", "body", "timestamps", "resources", "cta"]
  },
  "case-study": {
    "description": "Client success story",
    "typical-sections": ["title", "body", "stats", "quote", "body", "closing"]
  },
  "sales-page": {
    "description": "Conversion-focused landing page",
    "typical-sections": ["title", "body", "stats", "testimonial", "checklist", "cta"]
  }
}
```

---

## When You Need a Specific Prompt

| Scenario | Solution |
|----------|----------|
| AI understands section type | Use generic prompt ✓ |
| AI needs guidance | Add hints to section type library |
| AI consistently fails | Create specific prompt (rare) |

**99% of the time: generic prompt + context = done.**

---

## To Create Something New

| Want | Do |
|------|-----|
| New section type | Add to section type library |
| New content type | Add to content type library |
| New template | Define section order + content type |
| New brand | Add to brand library |
| New audience | Add to audience library |

**No new prompts. Just data.**

---

## Template Repos

| Repo | Contains | ID Prefix |
|------|----------|-----------|
| `/repos/section-specs/` | Field definitions, constraints, sizes per section type | `section_` |
| `/repos/content-types/` | Variants, total word counts, typical sections | `content_` |
| `/repos/templates/` | Section assembly instructions | `template_` |
| `/repos/prompts/` | AI prompts (minimal - generic works most of the time) | `prompt_` |
| `/repos/brands/` | Voice, tone, values | `brand_` |
| `/repos/audiences/` | Who, language, motivators | `audience_` |
| `/repos/external-templates/` | Canva/Google template metadata | `ext_` |
| `/repos/image-prompts/` | AI image generation prompts | `imgprompt_` |
| `/repos/image-templates/` | Image composition specs | `imgtmpl_` |
| `/repos/render-templates/` | PDF/DOCX/HTML layout specs | `render_` |

---

## One-Off to Reusable Template

```
┌─────────────────────────────────────────────────────────────────┐
│ STEP 1: AI Creates One-Off Document                              │
├─────────────────────────────────────────────────────────────────┤
│ User: "Create a workbook about mindfulness"                     │
│                                                                 │
│ AI generates:                                                   │
│ - Title page (mindfulness content)                              │
│ - Body page (why it matters)                                    │
│ - Body page (the science)                                       │
│ - Exercise page (morning reset)                                 │
│ - Closing page (your journey)                                   │
│                                                                 │
│ Renderer builds → PDF delivered                                 │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ STEP 2: Ask User                                                 │
├─────────────────────────────────────────────────────────────────┤
│ "Save this structure as a reusable template?"                   │
│ User: "Yes, call it entrepreneur-workbook"                      │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ STEP 3: TemplateFactory Extracts Structure                       │
├─────────────────────────────────────────────────────────────────┤
│ Detects: ["title", "body", "body", "exercise", "closing"]       │
│ Saves to: /repos/templates/entrepreneur-workbook.json           │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ STEP 4: Reuse                                                    │
├─────────────────────────────────────────────────────────────────┤
│ User: "Create another entrepreneur workbook about time mgmt"    │
│ Same structure, different content.                              │
└─────────────────────────────────────────────────────────────────┘
```

---

## Modules

### TemplateEngine
Orchestrates the flow: reads template → AI fills sections → hands to renderer

| Function | Input | Output |
|----------|-------|--------|
| `run(templateId, context)` | templateId, context | renderedOutput |
| `fillSections(sections, context)` | sections[], context | filledSections[] |
| `handToRenderer(renderer, data)` | renderer, data | outputPath |

### TemplateFactory
Creates templates from one-off outputs

| Function | Input | Output |
|----------|-------|--------|
| `createFromOutput(aiOutput, name)` | aiOutput, name | template |
| `detectSections(output)` | output | sections[] |
| `save(template)` | template | templateId |

### Renderers
Each renderer knows how to layout section types for its format

| Renderer | Output | Uses |
|----------|--------|------|
| **CanvaRenderer** | Canva designs | CanvaIntegration |
| **GoogleDocsRenderer** | Google Docs | GoogleIntegration |
| **GoogleSheetsRenderer** | Google Sheets | GoogleIntegration |
| **GoogleSlidesRenderer** | Google Slides | GoogleIntegration |
| **ImageRenderer** | PNG/JPG | Sharp/Canvas |
| **PDFRenderer** | PDF | PDFKit |
| **DOCXRenderer** | Word docs | docx lib |
| **MarkdownRenderer** | MD files | - |
| **HTMLRenderer** | HTML files | - |

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         TEMPLATE                                 │
│              ["title", "body", "body", "exercise", "closing"]   │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      TEMPLATEENGINE                              │
│                                                                 │
│  For each section:                                              │
│  → PromptBuilder gets prompt for section type                   │
│  → AICaller fills section with content                          │
│  → Parsers structures the response                              │
│                                                                 │
│  Assembles all filled sections                                  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                         RENDERER                                 │
│                                                                 │
│  Maps section types to page layouts                             │
│  Applies brand styling                                          │
│  Builds final document                                          │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      FINAL OUTPUT                                │
│                                                                 │
│  PDF / Google Slides / Canva / Word / HTML / etc.               │
└─────────────────────────────────────────────────────────────────┘
```

---

## How Section Specs Are Used

```
Request: "Create instagram image about mindfulness"

1. Template/content type → section: image, variant: instagram-post
2. Section spec → fields, size (1080x1080), sources allowed
3. Brand → voice, tone
4. Audience → who
5. PromptBuilder → assembles with all specs
6. AI → generates caption, alt text
7. ImageSystem → fetches/generates at 1080x1080
8. Renderer → assembles
```

**Built Prompt Includes Specs:**

```
SYSTEM:
[brand context...]
[audience context...]

USER:
Create an image section for instagram-post.

Specs:
- Image: required, size 1080x1080, source: brand-library or unsplash or ai-generated
- Caption: optional, max 20 words
- Alt text: required, max 15 words
```

---

## Module Count

| Category | Modules |
|----------|---------|
| Template System | TemplateEngine, TemplateFactory (2) |
| Renderers | 9 (Canva, GoogleDocs, GoogleSheets, GoogleSlides, Image, PDF, DOCX, Markdown, HTML) |
| **Total New** | **11** |

### Net Change from Original 73
- Removed: 1 (generic Templates module)
- Added: 11 (2 template system + 9 renderers)
- **New Total: 83 modules**

---

*Created: 2026-01-19*
*Status: Draft - Awaiting Approval*
