# SocialMediaManager Module

> **Purpose:** Manage social media accounts, posting, and engagement
> **Type:** Integration Module
> **Status:** Planning

---

## Overview

SocialMediaManager handles direct interaction with social media platforms - posting content, scheduling posts, managing engagement, retrieving analytics. It's the hands that actually touch the platforms.

```
Content ready → SocialMediaManager.post() → Platform API → Published
```

**Platforms supported:**
- Instagram (via Meta API)
- Facebook (via Meta API)
- LinkedIn
- Twitter/X
- TikTok
- Pinterest

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `connect(platform, credentials)` | Connect to platform | platform, credentials | connection |
| `post(platform, content)` | Post content to platform | platform, content | postResult |
| `schedulePost(platform, content, time)` | Schedule post | platform, content, time | scheduleId |
| `getAnalytics(platform, postId)` | Get post analytics | platform, postId | analytics |
| `getAccountAnalytics(platform, period)` | Get account analytics | platform, period | analytics |
| `deletePost(platform, postId)` | Delete post | platform, postId | deleted |
| `getComments(platform, postId)` | Get post comments | platform, postId | comments |
| `reply(platform, postId, commentId, text)` | Reply to comment | platform, postId, commentId, text | reply |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `SocialMediaManager.postToAll` | Post to multiple platforms | post (multiple platforms) |
| `SocialMediaManager.scheduleAcross` | Schedule across platforms | schedulePost (multiple) |

---

## Detailed Function Specs

### post(platform, content)

Posts content to a platform.

```javascript
// Input
{
  platform: "instagram",
  content: {
    type: "image",  // image, video, carousel, story, reel
    caption: "Introducing our new brand identity! ✨\n\n#branding #design #newlook",
    media: [
      { type: "image", url: "/assets/post-image.jpg", altText: "New logo reveal" }
    ],
    location: "London, UK",  // optional
    tags: ["@partnerbrand"]  // optional mentions
  }
}

// Process
1. Validate content for platform requirements
2. Upload media if needed
3. Create post via platform API
4. Return post details

// Output
{
  success: true,
  platform: "instagram",
  postId: "ig_post_123456",
  url: "https://instagram.com/p/abc123",
  publishedAt: "2026-01-17T14:30:00Z",
  mediaIds: ["media_001"]
}
```

### schedulePost(platform, content, time)

Schedules post for future publishing.

```javascript
// Input
{
  platform: "linkedin",
  content: {
    type: "article",
    title: "5 Branding Trends for 2026",
    body: "As we move into 2026...",
    link: "https://nce.com/blog/branding-trends",
    image: "/assets/article-cover.jpg"
  },
  time: {
    publishAt: "2026-01-20T09:00:00Z",
    timezone: "Europe/London"
  }
}

// Output
{
  scheduled: true,
  scheduleId: "sched_li_001",
  platform: "linkedin",
  publishAt: "2026-01-20T09:00:00Z",
  status: "pending"
}
```

### getAnalytics(platform, postId)

Gets analytics for a specific post.

```javascript
// Input
{
  platform: "instagram",
  postId: "ig_post_123456"
}

// Output
{
  platform: "instagram",
  postId: "ig_post_123456",
  metrics: {
    impressions: 5420,
    reach: 4200,
    engagement: {
      likes: 342,
      comments: 28,
      shares: 15,
      saves: 89
    },
    engagementRate: 8.7,
    clicks: 156
  },
  audience: {
    topLocations: ["London", "Manchester", "Birmingham"],
    ageGroups: { "25-34": 45, "35-44": 30 },
    peakTime: "18:00"
  },
  period: {
    start: "2026-01-17T14:30:00Z",
    end: "2026-01-17T23:59:59Z"
  }
}
```

---

## Platform Configuration

### Instagram/Facebook (Meta)

```json
{
  "platform": "instagram",
  "provider": "meta",
  "apiVersion": "v18.0",
  "credentials": {
    "accessTokenRef": "META_ACCESS_TOKEN",
    "appIdRef": "META_APP_ID",
    "appSecretRef": "META_APP_SECRET"
  },
  "accountId": "ig_account_123",
  "limits": {
    "postsPerDay": 25,
    "mediaSize": 8388608,  // 8MB
    "captionLength": 2200
  }
}
```

### LinkedIn

```json
{
  "platform": "linkedin",
  "credentials": {
    "accessTokenRef": "LINKEDIN_ACCESS_TOKEN",
    "organizationId": "org_123"
  },
  "limits": {
    "postsPerDay": 20,
    "articleLength": 120000
  }
}
```

---

## Content Types by Platform

| Platform | Types | Media Specs |
|----------|-------|-------------|
| Instagram | image, video, carousel, story, reel | 1080x1080, 1080x1920 |
| Facebook | post, photo, video, story, reel | Various |
| LinkedIn | post, article, document, video | 1200x627 recommended |
| Twitter/X | tweet, thread, image, video | 1200x675 |
| TikTok | video | 1080x1920, 9:16 |
| Pinterest | pin, idea pin | 1000x1500 |

---

## Config

### module.config.json

```json
{
  "module": "SocialMediaManager",
  "version": "1.0.0",
  "description": "Manage social media accounts and posting",
  "type": "integration",

  "functions": [
    {
      "name": "connect",
      "description": "Connect to platform",
      "input": ["platform", "credentials"],
      "output": "connection"
    },
    {
      "name": "post",
      "description": "Post content to platform",
      "input": ["platform", "content"],
      "output": "postResult"
    },
    {
      "name": "schedulePost",
      "description": "Schedule post",
      "input": ["platform", "content", "time"],
      "output": "scheduleId"
    },
    {
      "name": "getAnalytics",
      "description": "Get post analytics",
      "input": ["platform", "postId"],
      "output": "analytics"
    },
    {
      "name": "getAccountAnalytics",
      "description": "Get account analytics",
      "input": ["platform", "period"],
      "output": "analytics"
    },
    {
      "name": "deletePost",
      "description": "Delete post",
      "input": ["platform", "postId"],
      "output": "deleted"
    },
    {
      "name": "getComments",
      "description": "Get post comments",
      "input": ["platform", "postId"],
      "output": "comments"
    },
    {
      "name": "reply",
      "description": "Reply to comment",
      "input": ["platform", "postId", "commentId", "text"],
      "output": "reply"
    }
  ],

  "steps": [
    {
      "name": "postToAll",
      "description": "Post to multiple platforms",
      "sequence": ["post"]
    },
    {
      "name": "scheduleAcross",
      "description": "Schedule across platforms",
      "sequence": ["schedulePost"]
    }
  ],

  "config": {
    "defaultPlatforms": ["instagram", "linkedin"],
    "retryOnFail": true,
    "maxRetries": 3
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Platform credentials |
| Fetchers | API requests |
| FileManager | Media storage |

---

## Used By

| Module | How |
|--------|-----|
| SocialMediaPlanner | Executing planned posts |
| Scheduler | Scheduled posting |

---

*Last updated: 2026-01-17*
