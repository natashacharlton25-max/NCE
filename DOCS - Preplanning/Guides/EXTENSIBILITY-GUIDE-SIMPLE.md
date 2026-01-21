# Extensibility Guide (Simple)

> Plain English guide for adding new things to BrandKit.
> For the technical version, see [EXTENSIBILITY-GUIDE.md](./EXTENSIBILITY-GUIDE.md)

---

## What Can I Add?

You can extend the system by adding:
- **New domains** (industries/niches like "hospitality" or "education")
- **New sections** (more fields for brands to fill out)
- **New libraries** (reusable content like tone words or phrases)
- **New export formats** (ways to get your brand data out)
- **New integrations** (connections to other apps/services)

Most of these just need new JSON files - no coding required!

---

## Adding a New Domain

**What's a domain?** An industry or niche that affects which libraries are available. Examples: therapeutic, ecommerce, professional-services.

### Steps

1. **Create a folder** in BrandLib with your domain name
   - Example: `BrandLib/hospitality/`

2. **Copy some library files** from `BrandLib/universal/` into your new folder and customise them

3. **Register the domain** by adding it to `BrandLib/_config/domains.json`
   - Give it a name and description
   - Add keywords the system uses to auto-detect this domain

### Example

If adding "hospitality" domain:

```
BrandLib/
├── universal/          (already exists)
├── therapeutic/        (already exists)
└── hospitality/        (NEW - create this)
    ├── tone.json       (hospitality-specific tone options)
    └── vocabulary.json (hospitality-specific words)
```

### Notes

- The system automatically finds new domains - no code changes needed
- Similar domains can share libraries (hospitality might use some therapeutic content for spa services)

---

## Adding a New Section

**What's a section?** A group of related fields in a brand kit. Examples: core/identity, voice/tone, legal/entity.

### Steps

1. **Decide which category** it belongs to (core, voice, audience, legal, visual, content, etc.)

2. **Create the schema file** that defines what fields the section has
   - Location: `BrandKit/_system/{category}/{section}/default/schema.json`

3. **Create AI instructions** (optional) for how to generate content
   - Location: `BrandKit AIGen/sections/{category}--{section}.json`

4. **Add to generation order** so the system knows when to process it
   - Location: `BrandKit AIGen/_config/generation-order.json`

### Example

Adding a "sustainability" section under content:

```
BrandKit/_system/content/sustainability/default/
├── schema.json         (what fields it has)
└── instructions.json   (how to fill it)
```

### Notes

- Sections can depend on other sections (sustainability might need brand values first)
- Visual sections work differently (use builder apps instead of AI)

---

## Adding a New Library

**What's a library?** A collection of reusable content entries. Examples: tone words, vocabulary, disclaimers.

### Steps

1. **Create a JSON file** in the appropriate domain folder
   - Universal libraries go in `BrandLib/universal/`
   - Domain-specific libraries go in `BrandLib/{domain}/`

2. **Add entries** with values, descriptions, and tags

3. **Map to fields** (optional) - tell the system which fields can use this library

### Example

Adding a "hospitality vocabulary" library:

```json
{
  "library": "vocabulary",
  "domain": "hospitality",
  "entries": [
    {
      "id": "warm-welcome",
      "value": "warm welcome",
      "description": "Greeting guests",
      "tags": ["greeting", "positive"]
    },
    {
      "id": "guest-experience",
      "value": "guest experience",
      "description": "How guests feel",
      "tags": ["experience", "service"]
    }
  ]
}
```

### Notes

- Libraries are auto-discovered - just drop the file in the right folder
- Entries can have multiple tags for better matching
- Use `domain_tags` if an entry works for multiple domains

---

## Adding a New Export Format

**What's an export format?** A way to get brand data out of the system. Examples: JSON, PDF, Markdown.

### Steps

1. **This requires code** - create a new exporter file
2. **Register it** in the export router
3. **Add UI option** for users to select it

### Notes

- Most common formats are already included
- Ask Claude Code to help with new export formats
- Consider what software will read the exported file

---

## Adding a New Integration

**What's an integration?** A connection to another app or service. Examples: Canva, Unsplash, Companies House.

### Steps

1. **Get API access** - sign up for the service and get an API key
2. **Register the service** in API Key Manager
3. **Create handler code** for the integration
4. **Add to Integration Manager**

### Notes

- Most integrations require API keys (stored securely)
- Some integrations need OAuth (user logs in to connect)
- Ask Claude Code to help with new integrations

---

## Quick Reference

| I want to add... | Requires code? | Difficulty |
|-----------------|----------------|------------|
| New domain | No | Easy |
| New library | No | Easy |
| New section | No (mostly) | Medium |
| New export format | Yes | Medium |
| New integration | Yes | Hard |

---

## Tips

1. **Start with existing examples** - copy a similar file and modify it
2. **Use valid JSON** - use a JSON validator if you're not sure
3. **Test small changes** - add one thing at a time
4. **Ask Claude Code** for help with anything technical

---

## Getting Help

- Check the technical guide for exact file formats
- Look at existing files for examples
- Ask Claude Code to help create new configurations
