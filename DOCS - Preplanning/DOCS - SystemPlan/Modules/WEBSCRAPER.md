# WebScraper Module

> **Purpose:** Web scraping for content extraction and research
> **Type:** Integration Module
> **Status:** Planning (Future)

---

## Overview

WebScraper provides **web content extraction** capabilities. It fetches web pages, extracts structured data, and handles dynamic content.

```
URL → WebScraper.scrape() → Extracted content → Structured data
```

**WebScraper enables:**
- Page content extraction
- Competitor research
- Data gathering
- Content inspiration

---

## Roles & Rules

### WebScraper DOES:
- Fetch web pages
- Extract text content
- Parse HTML structure
- Handle JavaScript-rendered pages
- Respect robots.txt
- Rate limit requests

### WebScraper does NOT:
- Store scraped content permanently
- Bypass access controls
- Scrape personal data
- Ignore rate limits

### Boundaries:
- Respects robots.txt
- Rate limits all requests
- No authentication bypass
- Temporary storage only

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `scrape(url, options)` | Scrape a single URL | url, options | content |
| `scrapeMultiple(urls, options)` | Scrape multiple URLs | urls, options | contents |
| `extractText(html)` | Extract text from HTML | html | text |
| `extractLinks(html, baseUrl)` | Extract links from page | html, baseUrl | links |
| `extractMetadata(html)` | Extract page metadata | html | metadata |
| `checkRobotsTxt(domain)` | Check robots.txt rules | domain | rules |

---

## Detailed Function Specs

### scrape(url, options)

Scrape a single URL.

```javascript
// Input
{
  url: "https://example.com/blog/ai-marketing",
  options: {
    waitForSelector: ".article-content",  // Wait for element
    javascript: true,                      // Render JavaScript
    timeout: 30000,
    extractors: ["text", "metadata", "images"]
  }
}

// Output
{
  success: true,
  url: "https://example.com/blog/ai-marketing",
  content: {
    title: "How AI is Revolutionizing Marketing",
    text: "In recent years, artificial intelligence has...",
    metadata: {
      description: "Learn how AI is changing...",
      author: "Jane Doe",
      publishDate: "2026-01-15"
    },
    images: [
      { src: "https://example.com/img/hero.jpg", alt: "AI Marketing" }
    ],
    wordCount: 1500
  },
  timing: {
    fetch: 2500,
    render: 1500,
    extract: 200
  }
}
```

### extractMetadata(html)

Extract page metadata.

```javascript
// Input
{
  html: "<html>...</html>"
}

// Output
{
  title: "Page Title",
  description: "Meta description",
  keywords: ["ai", "marketing"],
  ogTitle: "Open Graph Title",
  ogDescription: "OG Description",
  ogImage: "https://example.com/og-image.jpg",
  canonical: "https://example.com/page",
  author: "Author Name",
  publishDate: "2026-01-15"
}
```

### checkRobotsTxt(domain)

Check robots.txt rules.

```javascript
// Input
{
  domain: "example.com"
}

// Output
{
  allowed: true,
  rules: {
    userAgent: "*",
    disallow: ["/admin/", "/private/"],
    crawlDelay: 2
  },
  sitemaps: ["https://example.com/sitemap.xml"]
}
```

---

## Ethical Scraping

```javascript
{
  ethics: {
    respectRobotsTxt: true,
    maxRequestsPerDomain: 10,
    delayBetweenRequests: 2000,
    userAgent: "NCE-Research-Bot/1.0 (+https://nce.com/bot)",
    cacheResponses: true,
    cacheDuration: 86400000  // 24 hours
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "WebScraper",
  "version": "1.0.0",
  "description": "Web scraping for content extraction",
  "type": "integration",

  "functions": [
    {
      "name": "scrape",
      "description": "Scrape a single URL",
      "input": ["url", "options"],
      "output": "content"
    },
    {
      "name": "scrapeMultiple",
      "description": "Scrape multiple URLs",
      "input": ["urls", "options"],
      "output": "contents"
    },
    {
      "name": "extractText",
      "description": "Extract text from HTML",
      "input": ["html"],
      "output": "text"
    },
    {
      "name": "extractLinks",
      "description": "Extract links from page",
      "input": ["html", "baseUrl"],
      "output": "links"
    },
    {
      "name": "extractMetadata",
      "description": "Extract page metadata",
      "input": ["html"],
      "output": "metadata"
    },
    {
      "name": "checkRobotsTxt",
      "description": "Check robots.txt rules",
      "input": ["domain"],
      "output": "rules"
    }
  ],

  "config": {
    "respectRobotsTxt": true,
    "defaultTimeout": 30000,
    "requestDelay": 2000,
    "maxConcurrent": 3,
    "userAgent": "NCE-Research-Bot/1.0",
    "cacheEnabled": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| RequestHandler | HTTP requests |
| CacheHandler | Response caching |
| RateLimiter | Request throttling |
| Logger | Logging operations |

---

## Used By

| Component | How |
|-----------|-----|
| ResearchTools | Content research |
| BrandManager | Competitor analysis |

---

*Last updated: 2026-01-17*
