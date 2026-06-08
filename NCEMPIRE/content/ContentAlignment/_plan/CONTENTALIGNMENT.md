# ContentAlignmentManager Module

> **Purpose:** Ensure content aligns with brand, audience, and strategy
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ContentAlignmentManager validates that content aligns with brand guidelines, audience expectations, and strategic goals. It's the quality gate that checks content before it goes out - ensuring consistency and appropriateness.

```
Content → ContentAlignmentManager.check() → Alignment score + Issues → Approve/Revise
```

**Checks for:**
- Brand voice/tone alignment
- Audience appropriateness
- Message consistency
- Visual guideline compliance
- Strategic goal alignment

---

## Roles & Rules

### ContentAlignment DOES:
- Check content against brand voice/tone
- Validate audience appropriateness
- Check visual guideline compliance
- Verify message consistency
- Suggest fixes for alignment issues
- Approve or flag content

### ContentAlignment does NOT:
- Create content (Content module does that)
- Modify content directly (only suggests)
- Define brand guidelines (BrandKit does that)
- Publish content (SocialMediaManager does that)

### Boundaries:
- Read-only analysis of content
- Cannot auto-fix content (only suggests)
- Cannot bypass manual approval requirements
- Cannot approve content that fails minimum threshold

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `checkAlignment(content, brandId)` | Full alignment check | content, brandId | alignmentResult |
| `checkTone(content, toneId)` | Check tone alignment | content, toneId | toneResult |
| `checkAudience(content, audienceId)` | Check audience fit | content, audienceId | audienceResult |
| `checkVisual(assets, guidelineId)` | Check visual compliance | assets, guidelineId | visualResult |
| `checkMessage(content, messaging)` | Check message consistency | content, messaging | messageResult |
| `suggestFixes(issues)` | Suggest fixes for issues | issues | suggestions |
| `approve(contentId, checks)` | Approve content | contentId, checks | approval |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `ContentAlignmentManager.fullCheck` | Complete alignment check | checkTone → checkAudience → checkMessage → checkVisual |
| `ContentAlignmentManager.quickCheck` | Fast tone/audience check | checkTone → checkAudience |

---

## Detailed Function Specs

### checkAlignment(content, brandId)

Performs full alignment check against brand.

```javascript
// Input
{
  content: {
    type: "social-post",
    platform: "linkedin",
    text: "🚀 Exciting news! We're totally stoked to announce our rad new product!",
    images: ["/assets/post-image.jpg"],
    hashtags: ["#Innovation", "#Tech"]
  },
  brandId: "acme"
}

// Process
1. Load brand profile, tone, audience
2. Check each alignment dimension
3. Score and identify issues
4. Return comprehensive result

// Output
{
  aligned: false,
  overallScore: 0.45,
  checks: {
    tone: {
      score: 0.3,
      aligned: false,
      issues: [
        {
          severity: "high",
          issue: "Informal language doesn't match professional tone",
          examples: ["totally stoked", "rad"],
          expected: "Professional, measured enthusiasm"
        }
      ]
    },
    audience: {
      score: 0.5,
      aligned: false,
      issues: [
        {
          severity: "medium",
          issue: "Casual language may not resonate with enterprise decision makers",
          suggestion: "Use more professional terminology"
        }
      ]
    },
    message: {
      score: 0.6,
      aligned: true,
      notes: "Product announcement aligns with brand messaging"
    },
    visual: {
      score: 0.8,
      aligned: true,
      notes: "Image follows brand guidelines"
    }
  },
  suggestions: [
    "Replace 'totally stoked' with 'pleased'",
    "Replace 'rad' with 'innovative'",
    "Consider more professional emoji or none"
  ],
  revisedContent: {
    text: "Exciting news! We're pleased to announce our innovative new product!"
  }
}
```

### checkTone(content, toneId)

Checks if content matches specified tone.

```javascript
// Input
{
  content: {
    text: "Our cutting-edge solutions leverage AI to transform your business."
  },
  toneId: "professional-tech"
}

// Process
1. Load tone profile
2. Analyze content vocabulary, style, formality
3. Compare against tone requirements
4. Score alignment

// Output
{
  toneId: "professional-tech",
  score: 0.85,
  aligned: true,
  analysis: {
    formality: { expected: "formal", actual: "formal", match: true },
    vocabulary: {
      expectedStyle: "technical-professional",
      actualStyle: "technical-professional",
      match: true,
      flaggedWords: []
    },
    sentiment: { expected: "confident", actual: "confident", match: true }
  },
  suggestions: []
}
```

### checkAudience(content, audienceId)

Checks if content is appropriate for audience.

```javascript
// Input
{
  content: {
    text: "Check out our new API! npm install acme-sdk && acme init",
    type: "social-post",
    platform: "linkedin"
  },
  audienceId: "aud_acme_enterprise_001"  // Enterprise Decision Makers
}

// Output
{
  audienceId: "aud_acme_enterprise_001",
  score: 0.4,
  aligned: false,
  analysis: {
    technicalLevel: {
      content: "developer-focused",
      audience: "executive-focused",
      match: false,
      issue: "Content too technical for target audience"
    },
    relevance: {
      painPoints: ["none addressed"],
      goals: ["none addressed"],
      score: 0.3
    },
    platform: {
      audiencePresence: "high",
      formatFit: "poor for executives"
    }
  },
  suggestions: [
    "Focus on business benefits rather than technical implementation",
    "Lead with ROI or efficiency gains",
    "Save technical details for developer-focused content"
  ]
}
```

### checkVisual(assets, guidelineId)

Checks visual assets against brand guidelines.

```javascript
// Input
{
  assets: [
    { type: "image", path: "/assets/social-post.jpg" }
  ],
  guidelineId: "acme-visual-guidelines"
}

// Output
{
  guidelineId: "acme-visual-guidelines",
  score: 0.9,
  aligned: true,
  checks: {
    colors: {
      detected: ["#1a73e8", "#ffffff", "#333333"],
      allowed: ["#1a73e8", "#34a853", "#ffffff", "#333333"],
      match: true
    },
    logo: {
      present: true,
      placement: "correct",
      clearSpace: "sufficient"
    },
    typography: {
      detected: ["Montserrat", "Open Sans"],
      allowed: ["Montserrat", "Open Sans", "Roboto"],
      match: true
    },
    composition: {
      balanced: true,
      brandConsistent: true
    }
  },
  issues: []
}
```

---

## Alignment Dimensions

| Dimension | What it Checks | Sources |
|-----------|----------------|---------|
| Tone | Voice, vocabulary, formality | Tone profiles |
| Audience | Relevance, appropriateness, level | Audience profiles |
| Message | Consistency, key messages | Messaging framework |
| Visual | Colors, logo, typography | Visual guidelines |
| Strategy | Goal alignment, timing | Marketing strategy |

---

## Config

### module.config.json

```json
{
  "module": "ContentAlignmentManager",
  "version": "1.0.0",
  "description": "Ensure content aligns with brand and audience",
  "type": "service",

  "functions": [
    {
      "name": "checkAlignment",
      "description": "Full alignment check",
      "input": ["content", "brandId"],
      "output": "alignmentResult"
    },
    {
      "name": "checkTone",
      "description": "Check tone alignment",
      "input": ["content", "toneId"],
      "output": "toneResult"
    },
    {
      "name": "checkAudience",
      "description": "Check audience fit",
      "input": ["content", "audienceId"],
      "output": "audienceResult"
    },
    {
      "name": "checkVisual",
      "description": "Check visual compliance",
      "input": ["assets", "guidelineId"],
      "output": "visualResult"
    },
    {
      "name": "checkMessage",
      "description": "Check message consistency",
      "input": ["content", "messaging"],
      "output": "messageResult"
    },
    {
      "name": "suggestFixes",
      "description": "Suggest fixes for issues",
      "input": ["issues"],
      "output": "suggestions"
    },
    {
      "name": "approve",
      "description": "Approve content",
      "input": ["contentId", "checks"],
      "output": "approval"
    }
  ],

  "steps": [
    {
      "name": "fullCheck",
      "description": "Complete alignment check",
      "sequence": ["checkTone", "checkAudience", "checkMessage", "checkVisual"]
    },
    {
      "name": "quickCheck",
      "description": "Fast tone/audience check",
      "sequence": ["checkTone", "checkAudience"]
    }
  ],

  "config": {
    "minimumAlignmentScore": 0.7,
    "autoApproveThreshold": 0.9,
    "requireManualApproval": ["high-visibility", "campaign"]
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Loading brand profiles, guidelines |
| AudienceManager | Getting audience profiles |
| LanguageCheck | Tone analysis |

---

## Used By

| Module | How |
|--------|-----|
| Content | Pre-publish validation |
| SocialMediaPlanner | Content approval |
| BrandKit | Output validation |

---

*Last updated: 2026-01-17*
