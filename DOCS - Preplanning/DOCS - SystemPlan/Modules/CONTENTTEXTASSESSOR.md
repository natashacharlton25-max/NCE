# ContentTextAssessor Module

> **Purpose:** Assess and score text content quality, effectiveness, and optimization
> **Type:** Service Module
> **Status:** Planning

---

## Overview

ContentTextAssessor evaluates text content for quality, effectiveness, and optimization opportunities. It goes beyond grammar checking to assess persuasiveness, clarity, engagement, and goal achievement.

```
Content → ContentTextAssessor.assess() → Quality scores + Improvement suggestions
```

**Assesses:**
- Clarity and readability
- Persuasiveness and engagement
- SEO optimization
- Call-to-action effectiveness
- Emotional impact
- Goal alignment

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `assess(content, criteria)` | Full content assessment | content, criteria | assessment |
| `scoreClarity(text)` | Assess clarity/readability | text | clarityScore |
| `scoreEngagement(text)` | Assess engagement potential | text | engagementScore |
| `scorePersua(text, goal)` | Assess persuasiveness | text, goal | persuasivenessScore |
| `scoreSEO(text, keywords)` | Assess SEO optimization | text, keywords | seoScore |
| `scoreCTA(text)` | Assess call-to-action | text | ctaScore |
| `suggest(assessment)` | Generate improvements | assessment | suggestions |

---

## Detailed Function Specs

### assess(content, criteria)

Full content assessment.

```javascript
// Input
{
  content: {
    text: "Transform your business with our innovative platform. Start your free trial today!",
    type: "landing-page-hero",
    goal: "conversion"
  },
  criteria: {
    audience: "business-professionals",
    tone: "professional-confident",
    keywords: ["business platform", "innovation"]
  }
}

// Output
{
  overall: {
    score: 0.78,
    grade: "B+",
    summary: "Good headline with clear CTA, could improve specificity"
  },
  scores: {
    clarity: {
      score: 0.85,
      strengths: ["Clear value proposition", "Concise"],
      weaknesses: ["'Transform' is generic"]
    },
    engagement: {
      score: 0.72,
      strengths: ["Action-oriented language"],
      weaknesses: ["Lacks emotional hook", "Missing unique angle"]
    },
    persuasiveness: {
      score: 0.75,
      strengths: ["Clear benefit implied", "Strong CTA"],
      weaknesses: ["No proof points", "Missing urgency"]
    },
    seo: {
      score: 0.80,
      keywords: {
        "business platform": { found: true, position: "early" },
        "innovation": { found: true, position: "mid" }
      }
    },
    cta: {
      score: 0.82,
      found: "Start your free trial today!",
      strengths: ["Clear action", "Low friction (free)"],
      weaknesses: ["Could add urgency"]
    }
  },
  suggestions: [
    {
      type: "clarity",
      original: "Transform your business",
      suggested: "Scale your business 3x faster",
      reason: "More specific, quantifiable benefit"
    },
    {
      type: "engagement",
      suggestion: "Add a pain point before the solution",
      example: "Tired of slow growth? Scale your business 3x faster..."
    },
    {
      type: "cta",
      original: "Start your free trial today!",
      suggested: "Start your free 14-day trial - no credit card needed",
      reason: "Reduces friction, adds specificity"
    }
  ]
}
```

### scoreEngagement(text)

Assesses engagement potential.

```javascript
// Input
{
  text: "Our software helps businesses grow."
}

// Output
{
  score: 0.45,
  grade: "C",
  factors: {
    emotionalAppeal: {
      score: 0.3,
      present: false,
      suggestion: "Add emotional trigger words"
    },
    curiosity: {
      score: 0.4,
      present: false,
      suggestion: "Create information gap"
    },
    specificity: {
      score: 0.3,
      level: "vague",
      suggestion: "Add specific numbers or outcomes"
    },
    storyElement: {
      score: 0.2,
      present: false,
      suggestion: "Include narrative element"
    },
    uniqueness: {
      score: 0.35,
      level: "generic",
      suggestion: "Differentiate from competitors"
    }
  },
  improved: "In just 90 days, 500 businesses doubled their revenue using our AI-powered growth platform."
}
```

### scorePersua(text, goal)

Assesses persuasiveness for specific goal.

```javascript
// Input
{
  text: "Join thousands of happy customers who have transformed their workflow.",
  goal: "signup"
}

// Output
{
  score: 0.68,
  persuasionTechniques: {
    socialProof: { present: true, strength: "medium", example: "thousands of happy customers" },
    authority: { present: false, suggestion: "Add expert endorsement" },
    scarcity: { present: false, suggestion: "Add limited-time element" },
    reciprocity: { present: false, suggestion: "Offer free value first" },
    consistency: { present: false, suggestion: "Reference previous commitment" },
    liking: { present: true, strength: "weak", example: "happy customers" }
  },
  goalAlignment: {
    goal: "signup",
    pathClarity: 0.6,
    barrierAddressed: false,
    suggestion: "Address signup hesitation directly"
  }
}
```

### scoreSEO(text, keywords)

Assesses SEO optimization.

```javascript
// Input
{
  text: "Acme Corp provides innovative business solutions for modern enterprises. Our platform helps companies scale efficiently.",
  keywords: ["business solutions", "enterprise platform", "scale"]
}

// Output
{
  score: 0.72,
  keywords: {
    "business solutions": {
      found: true,
      count: 1,
      position: "early",
      naturalUsage: true
    },
    "enterprise platform": {
      found: false,
      suggestion: "Include in content naturally"
    },
    "scale": {
      found: true,
      count: 1,
      position: "late",
      naturalUsage: true
    }
  },
  structure: {
    length: 120,
    optimal: "150-160 for meta description",
    suggestion: "Expand slightly for meta description use"
  },
  readability: {
    sentences: 2,
    avgLength: 11,
    optimal: true
  }
}
```

---

## Assessment Criteria

| Criterion | Weight | Measures |
|-----------|--------|----------|
| Clarity | 25% | Readability, structure, jargon |
| Engagement | 25% | Hooks, emotion, curiosity |
| Persuasiveness | 20% | Techniques, proof, urgency |
| SEO | 15% | Keywords, structure, length |
| CTA | 15% | Clarity, friction, urgency |

---

## Config

### module.config.json

```json
{
  "module": "ContentTextAssessor",
  "version": "1.0.0",
  "description": "Assess text content quality and effectiveness",
  "type": "service",

  "functions": [
    { "name": "assess", "input": ["content", "criteria"], "output": "assessment" },
    { "name": "scoreClarity", "input": ["text"], "output": "clarityScore" },
    { "name": "scoreEngagement", "input": ["text"], "output": "engagementScore" },
    { "name": "scorePersua", "input": ["text", "goal"], "output": "persuasivenessScore" },
    { "name": "scoreSEO", "input": ["text", "keywords"], "output": "seoScore" },
    { "name": "scoreCTA", "input": ["text"], "output": "ctaScore" },
    { "name": "suggest", "input": ["assessment"], "output": "suggestions" }
  ],

  "config": {
    "minimumQualityScore": 0.7,
    "weightings": {
      "clarity": 0.25,
      "engagement": 0.25,
      "persuasiveness": 0.20,
      "seo": 0.15,
      "cta": 0.15
    }
  }
}
```

---

## Used By

| Module | How |
|--------|-----|
| Content | Post-generation assessment |
| WebsiteContent | Page content optimization |
| ContentAlignmentManager | Quality gate |

---

*Last updated: 2026-01-17*
