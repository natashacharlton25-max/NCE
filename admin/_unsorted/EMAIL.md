# Email Module

> **Purpose:** Compose emails from templates and data
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

Email module composes emails - it takes templates, data, and brand context to create email content. It doesn't send emails (that's Emaillit), it creates them. Think of it as the letter writer - it composes the message, addresses the envelope.

```
Template + Data → Email.compose() → Ready-to-send email object → Emaillit.send()
```

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `compose(templateId, data, brandId)` | Create email from template | templateId, data, brandId | composedEmail |
| `composeFromJSON(jsonSpec)` | Create email from JSON spec | jsonSpec | composedEmail |
| `applyBranding(email, brandId)` | Apply brand styles to email | email, brandId | brandedEmail |
| `generatePlainText(html)` | Generate plain text from HTML | html | plainText |
| `validateEmail(email)` | Validate email structure | email | validationResult |
| `preview(email)` | Generate preview URL | email | previewUrl |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Email.composeAndSend` | Compose then send via Emaillit | compose → applyBranding → validateEmail → (Emaillit.send via chain) |
| `Email.composeBatch` | Compose multiple emails | compose (multiple) → validateEmail (each) |

---

## Detailed Function Specs

### compose(templateId, data, brandId)

Composes email from template with data.

```javascript
// Input
{
  templateId: "brand-kit-delivery",
  data: {
    clientName: "John Smith",
    brandName: "Acme Corp",
    deliverables: ["Brand Guide", "Logo Pack", "Color Palette"],
    downloadLink: "https://..."
  },
  brandId: "acme"
}

// Process
1. Templates.get('email', templateId)
2. PromptBuilder.resolveVariables(template, data)
3. applyBranding(resolved, brandId)
4. generatePlainText(html)

// Output
{
  to: "{{recipient}}",  // Set by caller
  from: "hello@nce.com",
  replyTo: "support@nce.com",
  subject: "Your Acme Corp Brand Kit is Ready!",
  html: "<html>...<h1>Hi John Smith</h1>...</html>",
  text: "Hi John Smith\n\nYour Acme Corp brand kit is ready...",
  headers: {
    "X-Brand-Id": "acme",
    "X-Template-Id": "brand-kit-delivery"
  }
}
```

### composeFromJSON(jsonSpec)

Creates email from a JSON specification (for dynamic/custom emails).

```javascript
// Input
{
  jsonSpec: {
    template: "generic-notification",
    subject: "Update on {{projectName}}",
    sections: [
      { type: "heading", content: "Project Update" },
      { type: "paragraph", content: "{{updateText}}" },
      { type: "button", text: "View Details", url: "{{detailsUrl}}" }
    ],
    data: {
      projectName: "Acme Rebrand",
      updateText: "The logo designs are ready for review.",
      detailsUrl: "https://..."
    },
    brandId: "acme"
  }
}

// Process
1. Parse JSON sections
2. Build HTML from section types
3. Resolve variables
4. Apply branding

// Output
{
  subject: "Update on Acme Rebrand",
  html: "<html>...</html>",
  text: "..."
}
```

### applyBranding(email, brandId)

Applies brand colors, fonts, logo to email.

```javascript
// Input
{
  email: { html: "<html>...</html>" },
  brandId: "acme"
}

// Process
1. FileManager.get('brand-profiles', brandId)
2. Get brand colors, logo URL, fonts
3. Inject into email HTML
4. Apply CSS variables

// Output
{
  ...email,
  html: "<html><!-- Brand: acme --><style>:root{--primary:#1a73e8}...</style>...</html>"
}
```

---

## Email Template Format

Stored in `/repos/email-templates/`:

```json
{
  "id": "brand-kit-delivery",
  "name": "Brand Kit Delivery",
  "type": "transactional",
  "subject": "Your {{brandName}} Brand Kit is Ready!",

  "html": {
    "layout": "standard",
    "sections": [
      {
        "type": "header",
        "logo": "{{brand.logo}}",
        "backgroundColor": "{{brand.primaryColor}}"
      },
      {
        "type": "content",
        "template": "<h1>Hi {{clientName}}</h1><p>Your brand kit for <strong>{{brandName}}</strong> is ready!</p>"
      },
      {
        "type": "list",
        "title": "What's Included:",
        "items": "{{deliverables}}"
      },
      {
        "type": "button",
        "text": "Download Your Kit",
        "url": "{{downloadLink}}",
        "style": "primary"
      },
      {
        "type": "footer",
        "template": "standard-footer"
      }
    ]
  },

  "requiredData": ["clientName", "brandName", "downloadLink"],
  "optionalData": ["deliverables"],

  "status": "active",
  "version": "1.0"
}
```

---

## Email Types

| Type | Purpose | Example Templates |
|------|---------|-------------------|
| `transactional` | One-time notifications | brand-kit-delivery, invoice, welcome |
| `marketing` | Campaigns, newsletters | monthly-update, promotion |
| `system` | System notifications | error-alert, backup-complete |
| `reminder` | Scheduled reminders | review-due, payment-due |

---

## Config

### module.config.json

```json
{
  "module": "Email",
  "version": "1.0.0",
  "description": "Compose emails from templates",
  "type": "pipeline",

  "functions": [
    {
      "name": "compose",
      "description": "Create email from template",
      "input": ["templateId", "data", "brandId"],
      "output": "composedEmail"
    },
    {
      "name": "composeFromJSON",
      "description": "Create email from JSON spec",
      "input": ["jsonSpec"],
      "output": "composedEmail"
    },
    {
      "name": "applyBranding",
      "description": "Apply brand styles to email",
      "input": ["email", "brandId"],
      "output": "brandedEmail"
    },
    {
      "name": "generatePlainText",
      "description": "Generate plain text from HTML",
      "input": ["html"],
      "output": "plainText"
    },
    {
      "name": "validateEmail",
      "description": "Validate email structure",
      "input": ["email"],
      "output": "validationResult"
    },
    {
      "name": "preview",
      "description": "Generate preview URL",
      "input": ["email"],
      "output": "previewUrl"
    }
  ],

  "steps": [
    {
      "name": "composeAndSend",
      "description": "Compose then send via Emaillit",
      "sequence": ["compose", "applyBranding", "validateEmail"]
    },
    {
      "name": "composeBatch",
      "description": "Compose multiple emails",
      "sequence": ["compose", "validateEmail"]
    }
  ],

  "config": {
    "defaultFrom": "hello@nce.com",
    "defaultReplyTo": "support@nce.com",
    "templatesRepo": "email-templates"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Templates | Loading email templates |
| FileManager | Getting brand profiles |
| PromptBuilder | Variable resolution |

---

## Used By

| Module | How |
|--------|-----|
| Notification chains | Email notifications |
| BrandKit chains | Delivery emails |

---

*Last updated: 2026-01-17*
