# LegalDocCreator Module

> **Purpose:** Generate legal documents from templates (contracts, terms, policies)
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

LegalDocCreator generates legal documents from templates - contracts, terms of service, privacy policies, NDAs, and other legal documents. It populates templates with brand/party information and can customize clauses based on requirements.

```
Template + Party info → LegalDocCreator.generate() → Legal document
```

**Document types:**
- Contracts (service, freelance, employment)
- Terms of Service
- Privacy Policies
- NDAs / Confidentiality
- Licensing Agreements
- Disclaimers

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `generate(templateId, data)` | Generate document | templateId, data | document |
| `customize(templateId, clauses)` | Customize template clauses | templateId, clauses | customizedTemplate |
| `preview(templateId, data)` | Preview document | templateId, data | preview |
| `export(document, format)` | Export document | document, format | exported |
| `listTemplates(type)` | List legal templates | type | templates |
| `validateData(data, templateId)` | Validate required data | data, templateId | validation |
| `compareVersions(doc1, doc2)` | Compare document versions | doc1, doc2 | comparison |

---

## Detailed Function Specs

### generate(templateId, data)

Generates legal document from template.

```javascript
// Input
{
  templateId: "service-agreement-standard",
  data: {
    parties: {
      provider: {
        name: "Acme Corp Ltd",
        address: "123 Business Street, London, EC1A 1BB",
        company_number: "12345678",
        contact: "John Smith"
      },
      client: {
        name: "Client Company Ltd",
        address: "456 Client Road, Manchester, M1 1AA",
        company_number: "87654321",
        contact: "Jane Doe"
      }
    },
    terms: {
      startDate: "2026-02-01",
      duration: "12 months",
      fee: "£5,000 per month",
      paymentTerms: "30 days",
      noticePeriod: "30 days"
    },
    services: [
      "Brand strategy development",
      "Visual identity design",
      "Brand guidelines creation"
    ],
    specialClauses: [
      "intellectual-property-transfer",
      "confidentiality-enhanced"
    ]
  }
}

// Output
{
  documentId: "doc_agreement_20260117_abc",
  type: "service-agreement",
  status: "draft",
  content: {
    html: "<html>...<h1>Service Agreement</h1>...",
    text: "SERVICE AGREEMENT\n\nThis Agreement is made on...",
    sections: [
      { id: "parties", title: "Parties", content: "..." },
      { id: "services", title: "Services", content: "..." },
      { id: "fees", title: "Fees and Payment", content: "..." }
    ]
  },
  metadata: {
    generated: "2026-01-17",
    template: "service-agreement-standard",
    version: "1.0"
  },
  warnings: [],
  exports: {
    pdf: "/docs/service-agreement-draft.pdf",
    docx: "/docs/service-agreement-draft.docx"
  }
}
```

### customize(templateId, clauses)

Customizes template with specific clauses.

```javascript
// Input
{
  templateId: "service-agreement-standard",
  clauses: {
    add: [
      {
        id: "data-protection",
        section: "compliance",
        content: "Both parties agree to comply with applicable data protection legislation including GDPR..."
      }
    ],
    remove: ["limitation-of-liability-standard"],
    replace: [
      {
        id: "termination",
        newContent: "Either party may terminate with 60 days written notice..."
      }
    ]
  }
}

// Output
{
  customizedTemplateId: "service-agreement-custom-001",
  baseTemplate: "service-agreement-standard",
  changes: {
    added: ["data-protection"],
    removed: ["limitation-of-liability-standard"],
    replaced: ["termination"]
  },
  warnings: [
    {
      type: "legal-review",
      message: "Removal of limitation of liability clause - recommend legal review"
    }
  ]
}
```

### validateData(data, templateId)

Validates required data for template.

```javascript
// Input
{
  data: {
    parties: {
      provider: { name: "Acme Corp" }
      // Missing client
    }
  },
  templateId: "service-agreement-standard"
}

// Output
{
  valid: false,
  errors: [
    {
      field: "parties.client",
      message: "Client party information is required",
      required: ["name", "address"]
    },
    {
      field: "parties.provider.address",
      message: "Provider address is required"
    },
    {
      field: "terms.startDate",
      message: "Agreement start date is required"
    }
  ],
  warnings: [
    {
      field: "parties.provider.company_number",
      message: "Company number recommended for business entities"
    }
  ]
}
```

---

## Template Types

| Type | Templates |
|------|-----------|
| Contracts | service-agreement, freelance-contract, employment-contract |
| Terms | terms-of-service, terms-and-conditions, acceptable-use |
| Privacy | privacy-policy, cookie-policy, data-processing-agreement |
| Confidentiality | nda-mutual, nda-one-way, confidentiality-agreement |
| Licensing | license-agreement, software-license, content-license |
| Other | disclaimer, consent-form, release-form |

---

## Template Structure

```json
{
  "templateId": "service-agreement-standard",
  "name": "Standard Service Agreement",
  "type": "contract",
  "jurisdiction": "UK",
  "version": "2.0",

  "sections": [
    {
      "id": "parties",
      "title": "1. Parties",
      "content": "This Agreement is made between {{provider.name}} (\"Provider\") of {{provider.address}} and {{client.name}} (\"Client\") of {{client.address}}.",
      "required": true
    },
    {
      "id": "services",
      "title": "2. Services",
      "content": "The Provider agrees to provide the following services:\n{{#each services}}\n- {{this}}\n{{/each}}",
      "required": true
    },
    {
      "id": "fees",
      "title": "3. Fees and Payment",
      "content": "The Client shall pay the Provider {{terms.fee}} in accordance with the payment terms...",
      "required": true
    }
  ],

  "requiredData": [
    "parties.provider.name",
    "parties.provider.address",
    "parties.client.name",
    "parties.client.address",
    "terms.startDate",
    "terms.fee"
  ],

  "optionalData": [
    "parties.provider.company_number",
    "terms.specialConditions"
  ],

  "clauseLibrary": [
    "intellectual-property-transfer",
    "confidentiality-enhanced",
    "data-protection-gdpr"
  ]
}
```

---

## Config

### module.config.json

```json
{
  "module": "LegalDocCreator",
  "version": "1.0.0",
  "description": "Generate legal documents from templates",
  "type": "pipeline",

  "functions": [
    { "name": "generate", "input": ["templateId", "data"], "output": "document" },
    { "name": "customize", "input": ["templateId", "clauses"], "output": "customizedTemplate" },
    { "name": "preview", "input": ["templateId", "data"], "output": "preview" },
    { "name": "export", "input": ["document", "format"], "output": "exported" },
    { "name": "listTemplates", "input": ["type"], "output": "templates" },
    { "name": "validateData", "input": ["data", "templateId"], "output": "validation" },
    { "name": "compareVersions", "input": ["doc1", "doc2"], "output": "comparison" }
  ],

  "config": {
    "templatesRepo": "legal-templates",
    "defaultJurisdiction": "UK",
    "exportFormats": ["pdf", "docx", "html"],
    "requireReviewWarning": true
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Templates | Loading legal templates |
| PDFMaker | PDF export |
| FileManager | Document storage |

---

## Used By

| Module | How |
|--------|-----|
| BrandKit | Client agreements |
| Orchestrator | Contract generation chains |

---

## Important Notes

- Generated documents are templates and should be reviewed by legal professionals
- Module adds warnings for clauses that may need legal review
- Not a substitute for legal advice

---

*Last updated: 2026-01-17*
