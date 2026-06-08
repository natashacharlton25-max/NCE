# SocialMediaPlanner Module

> **Purpose:** Plan, organize, and manage social media content calendars
> **Type:** Pipeline Module
> **Status:** Planning

---

## Overview

SocialMediaPlanner is the strategic layer for social media. It plans content calendars, optimizes posting times, manages campaigns, and coordinates content across platforms. It decides WHAT to post and WHEN - SocialMediaManager then does the actual posting.

```
Content strategy → SocialMediaPlanner.plan() → Content calendar → SocialMediaManager.post()
```

**Capabilities:**
- Content calendar management
- Optimal time scheduling
- Campaign planning
- Cross-platform coordination
- Content recycling/repurposing

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `createCalendar(brandId, period)` | Create content calendar | brandId, period | calendar |
| `addToCalendar(calendarId, post)` | Add post to calendar | calendarId, post | added |
| `suggestTimes(platform, content)` | Suggest optimal posting times | platform, content | times |
| `planCampaign(campaign)` | Plan multi-post campaign | campaign | campaignPlan |
| `repurpose(content, platforms)` | Adapt content for platforms | content, platforms | adaptedContent |
| `getCalendar(calendarId, filter)` | Get calendar view | calendarId, filter | calendar |
| `optimize(calendarId)` | Optimize calendar timing | calendarId | optimizedCalendar |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `SocialMediaPlanner.planWeek` | Plan a week of content | createCalendar → suggestTimes → addToCalendar |
| `SocialMediaPlanner.planCampaign` | Plan full campaign | planCampaign → repurpose → optimize |

---

## Detailed Function Specs

### createCalendar(brandId, period)

Creates a content calendar for a period.

```javascript
// Input
{
  brandId: "acme",
  period: {
    start: "2026-01-20",
    end: "2026-01-26",
    type: "week"
  },
  platforms: ["instagram", "linkedin", "twitter"],
  postsPerDay: {
    instagram: 1,
    linkedin: 1,
    twitter: 3
  }
}

// Output
{
  calendarId: "cal_acme_w04_2026",
  brandId: "acme",
  period: {
    start: "2026-01-20",
    end: "2026-01-26"
  },
  slots: [
    {
      date: "2026-01-20",
      platform: "instagram",
      suggestedTime: "12:00",
      status: "empty"
    },
    {
      date: "2026-01-20",
      platform: "linkedin",
      suggestedTime: "09:00",
      status: "empty"
    },
    // ... more slots
  ],
  totalSlots: 35,
  filledSlots: 0
}
```

### suggestTimes(platform, content)

Suggests optimal posting times based on analytics and content type.

```javascript
// Input
{
  platform: "instagram",
  content: {
    type: "image",
    category: "promotional",
    targetAudience: "professionals"
  },
  brandId: "acme"
}

// Process
1. Get historical analytics for brand on platform
2. Analyze when similar content performed best
3. Consider audience timezone
4. Avoid oversaturation

// Output
{
  suggestions: [
    {
      time: "12:00",
      day: "Tuesday",
      score: 0.92,
      reason: "Peak engagement for promotional content"
    },
    {
      time: "18:30",
      day: "Wednesday",
      score: 0.87,
      reason: "High professional audience activity"
    },
    {
      time: "09:00",
      day: "Thursday",
      score: 0.85,
      reason: "Good reach for image posts"
    }
  ],
  avoidTimes: [
    { time: "14:00-16:00", reason: "Low engagement historically" }
  ]
}
```

### planCampaign(campaign)

Plans a multi-post campaign across platforms.

```javascript
// Input
{
  campaign: {
    name: "Product Launch Q1",
    brandId: "acme",
    startDate: "2026-02-01",
    endDate: "2026-02-14",
    goals: ["awareness", "engagement"],
    theme: "New Product Launch",
    contentTypes: ["teaser", "announcement", "features", "testimonials"],
    platforms: ["instagram", "linkedin", "twitter"]
  }
}

// Output
{
  campaignId: "camp_acme_launch_q1",
  name: "Product Launch Q1",
  phases: [
    {
      name: "Teaser",
      dates: "2026-02-01 to 2026-02-03",
      posts: [
        { platform: "instagram", type: "story", date: "2026-02-01", time: "12:00" },
        { platform: "twitter", type: "tweet", date: "2026-02-01", time: "09:00" },
        { platform: "twitter", type: "tweet", date: "2026-02-02", time: "12:00" }
      ]
    },
    {
      name: "Announcement",
      dates: "2026-02-04 to 2026-02-07",
      posts: [...]
    },
    {
      name: "Features",
      dates: "2026-02-08 to 2026-02-11",
      posts: [...]
    },
    {
      name: "Social Proof",
      dates: "2026-02-12 to 2026-02-14",
      posts: [...]
    }
  ],
  totalPosts: 28,
  contentNeeded: [
    { type: "teaser-image", count: 3 },
    { type: "announcement-video", count: 1 },
    { type: "feature-carousel", count: 4 }
  ]
}
```

### repurpose(content, platforms)

Adapts content for different platforms.

```javascript
// Input
{
  content: {
    original: {
      platform: "linkedin",
      type: "article",
      title: "5 Branding Trends for 2026",
      body: "Long form article content...",
      image: "/assets/article-cover.jpg"
    }
  },
  platforms: ["instagram", "twitter"]
}

// Output
{
  adapted: {
    instagram: {
      type: "carousel",
      slides: [
        { image: "trend-1.jpg", caption: "Trend 1: Minimalist Design" },
        { image: "trend-2.jpg", caption: "Trend 2: Bold Typography" },
        // ... 5 slides
      ],
      caption: "5 Branding Trends You Need to Know for 2026 👇\n\nSwipe through..."
    },
    twitter: {
      type: "thread",
      tweets: [
        "🧵 5 Branding Trends for 2026\n\nHere's what's shaping the future of brand design:",
        "1/ Minimalist Design\n\nLess is more. Brands are stripping back to essentials...",
        "2/ Bold Typography\n\nTypography is becoming the hero element...",
        // ... more tweets
        "5/ Read the full article: [link]\n\nWhich trend are you most excited about?"
      ]
    }
  }
}
```

---

## Calendar View

```json
{
  "calendarId": "cal_acme_w04_2026",
  "view": "week",
  "days": [
    {
      "date": "2026-01-20",
      "dayName": "Monday",
      "posts": [
        {
          "slotId": "slot_001",
          "platform": "instagram",
          "time": "12:00",
          "status": "scheduled",
          "content": {
            "type": "image",
            "preview": "Brand update post...",
            "media": ["preview.jpg"]
          }
        },
        {
          "slotId": "slot_002",
          "platform": "linkedin",
          "time": "09:00",
          "status": "draft",
          "content": null
        }
      ]
    }
  ]
}
```

---

## Config

### module.config.json

```json
{
  "module": "SocialMediaPlanner",
  "version": "1.0.0",
  "description": "Plan and manage social media content calendars",
  "type": "pipeline",

  "functions": [
    {
      "name": "createCalendar",
      "description": "Create content calendar",
      "input": ["brandId", "period"],
      "output": "calendar"
    },
    {
      "name": "addToCalendar",
      "description": "Add post to calendar",
      "input": ["calendarId", "post"],
      "output": "added"
    },
    {
      "name": "suggestTimes",
      "description": "Suggest optimal posting times",
      "input": ["platform", "content"],
      "output": "times"
    },
    {
      "name": "planCampaign",
      "description": "Plan multi-post campaign",
      "input": ["campaign"],
      "output": "campaignPlan"
    },
    {
      "name": "repurpose",
      "description": "Adapt content for platforms",
      "input": ["content", "platforms"],
      "output": "adaptedContent"
    },
    {
      "name": "getCalendar",
      "description": "Get calendar view",
      "input": ["calendarId", "filter"],
      "output": "calendar"
    },
    {
      "name": "optimize",
      "description": "Optimize calendar timing",
      "input": ["calendarId"],
      "output": "optimizedCalendar"
    }
  ],

  "steps": [
    {
      "name": "planWeek",
      "description": "Plan a week of content",
      "sequence": ["createCalendar", "suggestTimes", "addToCalendar"]
    },
    {
      "name": "planCampaignFull",
      "description": "Plan full campaign",
      "sequence": ["planCampaign", "repurpose", "optimize"]
    }
  ],

  "config": {
    "defaultTimezone": "Europe/London",
    "optimizationWindow": 7
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| SocialMediaManager | Getting analytics for optimization |
| Content | Content generation |
| FileManager | Storing calendars |

---

## Used By

| Module | How |
|--------|-----|
| Scheduler | Executing planned posts |
| BrandKit chains | Social media strategy |

---

*Last updated: 2026-01-17*
