# WebsiteContent Module

> **Purpose:** Generate website content - pages, sections, copy blocks
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

WebsiteContent generates content for websites - homepage copy, about pages, service descriptions, landing pages. It understands web content structure and SEO requirements. Output can be JSON, HTML, or CMS-ready formats.

```
Page spec → WebsiteContent.generate() → Structured web content
```

**Content types:**
- Homepage sections
- About/Company pages
- Service/Product pages
- Landing pages
- Blog posts
- FAQ sections
- CTA blocks

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `generatePage(pageType, context)` | Generate full page content | pageType, context | pageContent |
| `generateSection(sectionType, context)` | Generate single section | sectionType, context | sectionContent |
| `generateCTA(context, style)` | Generate call-to-action | context, style | ctaContent |
| `generateMeta(pageContent)` | Generate SEO meta tags | pageContent | metaTags |
| `toHTML(content, template)` | Convert to HTML | content, template | html |
| `toCMS(content, cmsType)` | Format for CMS import | content, cmsType | cmsData |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `WebsiteContent.generateHomepage` | Generate full homepage | generatePage → generateMeta |
| `WebsiteContent.generateLandingPage` | Generate landing page with CTA | generatePage → generateCTA → generateMeta |

---

## Detailed Function Specs

### generatePage(pageType, context)

Generates full page content.

```javascript
// Input
{
  pageType: "homepage",
  context: {
    brand: {
      name: "Acme Corp",
      industry: "technology",
      tagline: "Innovation starts here"
    },
    refs: {
      tone: "professional-tech"
    },
    structure: {
      sections: ["hero", "features", "about", "testimonials", "cta"]
    },
    keywords: ["technology solutions", "innovation", "digital transformation"]
  }
}

// Process
1. Load page template structure
2. Generate each section via AI
3. Apply SEO optimization
4. Validate content

// Output
{
  pageType: "homepage",
  sections: {
    hero: {
      headline: "Transform Your Business with Intelligent Technology",
      subheadline: "Acme Corp delivers cutting-edge solutions...",
      cta: { text: "Get Started", url: "#contact" }
    },
    features: {
      title: "Why Choose Acme",
      items: [
        { title: "Innovation", description: "..." },
        { title: "Reliability", description: "..." }
      ]
    },
    about: {
      title: "About Acme Corp",
      content: "Founded with a vision to transform..."
    },
    cta: {
      headline: "Ready to Innovate?",
      text: "Contact us today",
      buttonText: "Schedule a Demo"
    }
  },
  meta: {
    title: "Acme Corp | Technology Solutions",
    description: "Transform your business with Acme Corp's...",
    keywords: ["technology", "innovation", "digital transformation"]
  }
}
```

### generateSection(sectionType, context)

Generates a single section.

```javascript
// Input
{
  sectionType: "features",
  context: {
    brand: { name: "Acme Corp" },
    features: [
      { name: "Cloud Platform", brief: "Scalable infrastructure" },
      { name: "AI Analytics", brief: "Smart insights" },
      { name: "24/7 Support", brief: "Always available" }
    ],
    style: "cards"  // cards, list, grid
  }
}

// Output
{
  sectionType: "features",
  title: "Powerful Features Built for Growth",
  subtitle: "Everything you need to succeed",
  items: [
    {
      title: "Cloud Platform",
      description: "Scale effortlessly with our enterprise-grade cloud infrastructure...",
      icon: "cloud"
    },
    {
      title: "AI Analytics",
      description: "Make data-driven decisions with intelligent analytics...",
      icon: "chart"
    },
    {
      title: "24/7 Support",
      description: "Our expert team is always here when you need us...",
      icon: "support"
    }
  ]
}
```

### generateMeta(pageContent)

Generates SEO meta tags.

```javascript
// Input
{
  pageContent: {
    pageType: "homepage",
    sections: { hero: { headline: "Transform Your Business..." } },
    brand: { name: "Acme Corp" },
    keywords: ["technology", "innovation"]
  }
}

// Output
{
  title: "Acme Corp | Transform Your Business with Technology",
  description: "Acme Corp delivers cutting-edge technology solutions. Transform your business with innovation, reliability, and expert support.",
  keywords: "technology solutions, innovation, digital transformation, Acme Corp",
  og: {
    title: "Acme Corp | Technology Solutions",
    description: "Transform your business with Acme Corp",
    type: "website"
  },
  twitter: {
    card: "summary_large_image",
    title: "Acme Corp | Technology Solutions"
  }
}
```

### toCMS(content, cmsType)

Formats content for CMS import.

```javascript
// Input
{
  content: { ... },  // Generated page content
  cmsType: "wordpress"  // wordpress, contentful, strapi, webflow
}

// Output (WordPress format)
{
  post_title: "Homepage",
  post_content: "...",
  post_type: "page",
  meta_input: {
    _yoast_wpseo_title: "Acme Corp | Technology Solutions",
    _yoast_wpseo_metadesc: "..."
  },
  acf: {
    hero_headline: "Transform Your Business...",
    hero_subheadline: "...",
    features: [...]
  }
}
```

---

## Page Types

| Type | Sections | Purpose |
|------|----------|---------|
| `homepage` | hero, features, about, testimonials, cta | Main landing |
| `about` | story, team, values, timeline | Company info |
| `services` | overview, service-list, process, cta | Service showcase |
| `product` | hero, features, pricing, faq, cta | Product page |
| `landing` | hero, benefits, social-proof, cta | Campaign landing |
| `contact` | intro, form, locations, faq | Contact page |

---

## Config

### module.config.json

```json
{
  "module": "WebsiteContent",
  "version": "1.0.0",
  "description": "Generate website content",
  "type": "pipeline",

  "functions": [
    {
      "name": "generatePage",
      "description": "Generate full page content",
      "input": ["pageType", "context"],
      "output": "pageContent"
    },
    {
      "name": "generateSection",
      "description": "Generate single section",
      "input": ["sectionType", "context"],
      "output": "sectionContent"
    },
    {
      "name": "generateCTA",
      "description": "Generate call-to-action",
      "input": ["context", "style"],
      "output": "ctaContent"
    },
    {
      "name": "generateMeta",
      "description": "Generate SEO meta tags",
      "input": ["pageContent"],
      "output": "metaTags"
    },
    {
      "name": "toHTML",
      "description": "Convert to HTML",
      "input": ["content", "template"],
      "output": "html"
    },
    {
      "name": "toCMS",
      "description": "Format for CMS import",
      "input": ["content", "cmsType"],
      "output": "cmsData"
    }
  ],

  "steps": [
    {
      "name": "generateHomepage",
      "description": "Generate full homepage",
      "sequence": ["generatePage", "generateMeta"]
    },
    {
      "name": "generateLandingPage",
      "description": "Generate landing page with CTA",
      "sequence": ["generatePage", "generateCTA", "generateMeta"]
    }
  ],

  "config": {
    "defaultTone": "professional",
    "seoOptimize": true,
    "maxHeadlineLength": 60,
    "maxMetaDescLength": 160
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| PromptBuilder | Building content prompts |
| Content | Text generation |
| LanguageCheck | Content validation |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Website content generation |
| Export | CMS export |

---

*Last updated: 2026-01-17*
