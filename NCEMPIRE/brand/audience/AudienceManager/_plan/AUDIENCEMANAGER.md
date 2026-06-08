# AudienceManager Module

> **Purpose:** Define, segment, and manage target audiences for brands
> **Type:** Service Module
> **Status:** Planning

---

## Overview

AudienceManager handles audience definition and segmentation. It stores audience profiles, personas, demographics, and psychographics. Used across content creation, social media, and marketing to ensure messaging targets the right people.

```
Brand → AudienceManager.getAudience() → Audience profile → Content tailoring
```

**Capabilities:**
- Audience profile management
- Persona creation
- Segment definition
- Audience insights
- Cross-brand audience comparison

---

## Roles & Rules

### AudienceManager DOES:
- Create and manage audience profiles
- Create buyer personas
- Segment audiences by criteria
- Analyze audience overlap
- Suggest content strategies for audiences
- Link audiences to brands

### AudienceManager does NOT:
- Create content (Content module does that)
- Post to social media (SocialMediaManager does that)
- Build prompts (PromptBuilder includes audience context)
- Check content alignment (ContentAlignment does that)

### Boundaries:
- Cannot modify brand profiles
- Cannot create content directly
- Cannot access PII beyond what's in profiles
- Cannot make marketing decisions (suggests only)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `createAudience(brandId, audience)` | Create audience profile | brandId, audience | audienceId |
| `getAudience(brandId, audienceId)` | Get audience profile | brandId, audienceId | audience |
| `createPersona(brandId, persona)` | Create buyer persona | brandId, persona | personaId |
| `getPersonas(brandId)` | Get all personas for brand | brandId | personas |
| `segmentAudience(audience, criteria)` | Segment audience | audience, criteria | segments |
| `analyzeOverlap(audiences)` | Analyze audience overlap | audiences | overlap |
| `suggestContent(audienceId, contentType)` | Suggest content for audience | audienceId, contentType | suggestions |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `AudienceManager.defineAudience` | Full audience definition | createAudience → createPersona |
| `AudienceManager.getInsights` | Get audience insights | getAudience → analyzeOverlap → suggestContent |

---

## Detailed Function Specs

### createAudience(brandId, audience)

Creates an audience profile for a brand.

```javascript
// Input
{
  brandId: "acme",
  audience: {
    name: "Enterprise Decision Makers",
    type: "primary",
    demographics: {
      ageRange: { min: 35, max: 55 },
      gender: "all",
      income: "high",
      education: "degree+",
      location: {
        countries: ["UK", "US", "DE"],
        type: "urban"
      }
    },
    professional: {
      industries: ["technology", "finance", "manufacturing"],
      roles: ["CTO", "CIO", "VP Engineering", "IT Director"],
      companySize: "enterprise",
      decisionMaking: "key-influencer"
    },
    psychographics: {
      values: ["innovation", "efficiency", "reliability"],
      challenges: [
        "Digital transformation",
        "Legacy system modernization",
        "Team productivity"
      ],
      goals: [
        "Reduce operational costs",
        "Improve competitive advantage",
        "Enable remote work"
      ],
      buyingMotivations: ["ROI", "scalability", "security"]
    },
    behaviors: {
      contentPreferences: ["whitepapers", "case studies", "webinars"],
      platforms: ["linkedin", "industry-publications"],
      researchStyle: "thorough",
      decisionTimeline: "3-6 months"
    }
  }
}

// Output
{
  audienceId: "aud_acme_enterprise_001",
  brandId: "acme",
  name: "Enterprise Decision Makers",
  type: "primary",
  created: "2026-01-17"
}
```

### createPersona(brandId, persona)

Creates a detailed buyer persona.

```javascript
// Input
{
  brandId: "acme",
  persona: {
    name: "Tech Director Tom",
    audienceId: "aud_acme_enterprise_001",
    demographics: {
      age: 42,
      gender: "male",
      location: "London, UK",
      education: "Computer Science degree, MBA"
    },
    professional: {
      title: "Director of Technology",
      company: "Mid-size financial services firm",
      yearsInRole: 5,
      teamSize: 25,
      reportsTo: "CIO"
    },
    dayInLife: "Spends mornings in leadership meetings, afternoons reviewing team progress and vendor solutions. Constantly balancing innovation with security requirements.",
    goals: [
      "Modernize tech stack without disrupting operations",
      "Prove ROI on new initiatives to board",
      "Attract and retain top engineering talent"
    ],
    painPoints: [
      "Legacy systems slowing innovation",
      "Budget constraints vs expectations",
      "Vendor lock-in concerns"
    ],
    objections: [
      "What's the migration path?",
      "How does this integrate with our existing systems?",
      "What's the total cost of ownership?"
    ],
    contentJourney: {
      awareness: ["Industry reports", "Thought leadership articles"],
      consideration: ["Case studies", "Technical comparisons", "Webinars"],
      decision: ["ROI calculators", "Implementation guides", "References"]
    },
    quote: "I need solutions that make my team more productive, not more tools to manage."
  }
}

// Output
{
  personaId: "persona_acme_tom_001",
  brandId: "acme",
  name: "Tech Director Tom",
  audienceId: "aud_acme_enterprise_001"
}
```

### suggestContent(audienceId, contentType)

Suggests content tailored to audience.

```javascript
// Input
{
  audienceId: "aud_acme_enterprise_001",
  contentType: "social-media"
}

// Process
1. Get audience profile
2. Analyze preferences and pain points
3. Match with content strategies
4. Return suggestions

// Output
{
  audienceId: "aud_acme_enterprise_001",
  contentType: "social-media",
  suggestions: {
    tone: "professional, thought-leadership",
    themes: [
      "Digital transformation success stories",
      "ROI and efficiency metrics",
      "Industry trends and predictions"
    ],
    formats: {
      linkedin: ["articles", "carousel posts", "video testimonials"],
      twitter: ["industry news commentary", "stat highlights"]
    },
    avoidTopics: ["overly technical jargon", "aggressive sales messaging"],
    callToActions: ["Download case study", "Schedule consultation", "Watch demo"],
    bestTimes: {
      linkedin: "Tuesday-Thursday 8-10am, 5-6pm",
      twitter: "Weekdays during business hours"
    }
  }
}
```

---

## Audience Profile Structure

```json
{
  "audienceId": "aud_acme_enterprise_001",
  "brandId": "acme",
  "name": "Enterprise Decision Makers",
  "type": "primary",
  "demographics": {
    "ageRange": { "min": 35, "max": 55 },
    "gender": "all",
    "location": { "countries": ["UK", "US"], "type": "urban" }
  },
  "professional": {
    "industries": ["technology", "finance"],
    "roles": ["CTO", "CIO"],
    "companySize": "enterprise"
  },
  "psychographics": {
    "values": ["innovation", "efficiency"],
    "challenges": ["Digital transformation"],
    "goals": ["Reduce costs"]
  },
  "behaviors": {
    "contentPreferences": ["whitepapers", "case studies"],
    "platforms": ["linkedin"]
  },
  "personas": ["persona_acme_tom_001", "persona_acme_sarah_001"],
  "created": "2026-01-17",
  "updated": "2026-01-17"
}
```

---

## Config

### module.config.json

```json
{
  "module": "AudienceManager",
  "version": "1.0.0",
  "description": "Define and manage target audiences",
  "type": "service",

  "functions": [
    {
      "name": "createAudience",
      "description": "Create audience profile",
      "input": ["brandId", "audience"],
      "output": "audienceId"
    },
    {
      "name": "getAudience",
      "description": "Get audience profile",
      "input": ["brandId", "audienceId"],
      "output": "audience"
    },
    {
      "name": "createPersona",
      "description": "Create buyer persona",
      "input": ["brandId", "persona"],
      "output": "personaId"
    },
    {
      "name": "getPersonas",
      "description": "Get all personas for brand",
      "input": ["brandId"],
      "output": "personas"
    },
    {
      "name": "segmentAudience",
      "description": "Segment audience",
      "input": ["audience", "criteria"],
      "output": "segments"
    },
    {
      "name": "analyzeOverlap",
      "description": "Analyze audience overlap",
      "input": ["audiences"],
      "output": "overlap"
    },
    {
      "name": "suggestContent",
      "description": "Suggest content for audience",
      "input": ["audienceId", "contentType"],
      "output": "suggestions"
    }
  ],

  "steps": [
    {
      "name": "defineAudience",
      "description": "Full audience definition",
      "sequence": ["createAudience", "createPersona"]
    },
    {
      "name": "getInsights",
      "description": "Get audience insights",
      "sequence": ["getAudience", "suggestContent"]
    }
  ],

  "config": {
    "audiencesRepo": "audiences",
    "personasRepo": "personas"
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Storing audience profiles |
| Archivist | Saving audience data |

---

## Used By

| Module | How |
|--------|-----|
| Content | Tailoring content to audience |
| SocialMediaPlanner | Audience-aware planning |
| PromptBuilder | Including audience context in prompts |
| ContentAlignmentManager | Checking audience fit |

---

*Last updated: 2026-01-17*
