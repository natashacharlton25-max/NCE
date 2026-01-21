# Social Templates Library

> **System:** Social
> **Repository:** Social Repository
> **Path:** `/data/social-templates.sqlite`
> **Library Type:** reference
> **Status:** Draft

---

## Overview

Platform-specific social media templates. While `contenttype_social` in the Template System defines the generic structure, this library provides platform-specific formatting, character limits, and post type variations (carousel, reel, story, thread, etc.).

---

## Used By

| Module | Access | Purpose |
|--------|--------|---------|
| `SocialMediaManager` | Read/Write (Owner) | Template selection for post generation |
| `SocialMediaPlanner` | Read via FileManager | Template options for calendar planning |
| `Builders` | Read via FileManager | Social post building |

---

## SQLite Schema

### Tables

```sql
CREATE TABLE _index (
  id TEXT PRIMARY KEY,
  name TEXT,
  status TEXT DEFAULT 'active',
  latest_version INTEGER DEFAULT 1,
  updated_at TEXT,
  tags TEXT,
  preview JSON,
  ref_status TEXT DEFAULT 'valid'
);

CREATE TABLE social_templates (
  id TEXT,
  version INTEGER,
  data JSON,
  created_at TEXT DEFAULT (datetime('now')),
  created_by TEXT,
  PRIMARY KEY (id, version)
);

CREATE VIEW social_templates_latest AS
SELECT * FROM social_templates t1
WHERE version = (SELECT MAX(version) FROM social_templates t2 WHERE t2.id = t1.id);
```

---

## Entry Schema (JSON in `data` column)

> Validated by: `/src/schemas/SocialTemplate.ts`

### Required Fields

| Field | Type | Token Priority | Description |
|-------|------|----------------|-------------|
| `id` | string | Essential | Unique identifier (e.g., "social_instagram-carousel") |
| `name` | string | Essential | Human-readable name |
| `schemaVersion` | string | Essential | Schema version |
| `status` | EntryStatus | Essential | Lifecycle state |
| `platform` | string | Essential | Target platform (instagram, linkedin, twitter, etc.) |
| `postType` | string | Essential | Post format (post, carousel, reel, story, thread, article) |

### Optional Fields

| Field | Type | Default | Token Priority | Description |
|-------|------|---------|----------------|-------------|
| `description` | string | null | Supporting | Template description |
| `constraints` | PlatformConstraints | {} | Essential | Platform-specific limits |
| `structure` | StructureElement[] | [] | Essential | Content structure |
| `bestFor` | string[] | [] | Supporting | Use cases |
| `examplePost` | string | null | Supporting | Example output |
| `hashtags` | HashtagConfig | {} | Supporting | Hashtag guidance |
| `mediaRequirements` | MediaConfig | {} | Supporting | Image/video specs |

### PlatformConstraints Object

```json
{
  "maxCharacters": 2200,
  "maxHashtags": 30,
  "maxMentions": 20,
  "maxLinks": 1,
  "linkInBio": true,
  "aspectRatios": ["1:1", "4:5", "9:16"]
}
```

### StructureElement Object

```json
{
  "element": "hook",
  "position": 1,
  "maxLength": 125,
  "guidance": "First line visible before 'more' - make it count",
  "required": true
}
```

### MediaConfig Object

```json
{
  "type": "image",
  "dimensions": { "width": 1080, "height": 1080 },
  "maxFileSize": "8MB",
  "formats": ["jpg", "png"],
  "minImages": 1,
  "maxImages": 10
}
```

---

## Preview Fields

| Field | Purpose |
|-------|---------|
| `name` | Display name in lists |
| `platform` | Filter by platform |
| `postType` | Filter by format |

**Example preview JSON:**

```json
{
  "name": "Instagram Carousel",
  "platform": "instagram",
  "postType": "carousel"
}
```

---

## Staging Area

**Path:** `/repos/drafts/social-templates/`

---

## Queryable Fields

| Field | FTS Indexed | Filter | Sort | Notes |
|-------|-------------|--------|------|-------|
| `id` | No | Yes | Yes | Exact match |
| `name` | Yes | Yes | Yes | Full-text |
| `platform` | No | Yes | No | Exact match |
| `postType` | No | Yes | No | Exact match |
| `status` | No | Yes | No | Enum filter |

---

## Performance & Scale

| Aspect | Value | Notes |
|--------|-------|-------|
| **Expected Volume** | < 100 | Finite platform/format combinations |
| **Entry Size** | small-medium | 1-3KB typical |
| **FTS Enabled** | yes | name |
| **Lazy Loading** | no | Small entries |

---

## Example Entry

### Minimal (required fields only)

```json
{
  "id": "social_instagram-post",
  "name": "Instagram Single Post",
  "schemaVersion": "1.0.0",
  "status": "active",
  "platform": "instagram",
  "postType": "post"
}
```

### Complete (all fields)

```json
{
  "id": "social_instagram-carousel",
  "name": "Instagram Carousel",
  "schemaVersion": "1.0.0",
  "status": "active",
  "platform": "instagram",
  "postType": "carousel",
  "description": "Multi-image post for educational content, storytelling, or step-by-step guides",
  "constraints": {
    "maxCharacters": 2200,
    "maxHashtags": 30,
    "maxMentions": 20,
    "linkInBio": true
  },
  "structure": [
    {
      "element": "hook",
      "position": 1,
      "maxLength": 125,
      "guidance": "First line visible before 'more' - pattern interrupt or curiosity gap",
      "required": true
    },
    {
      "element": "body",
      "position": 2,
      "guidance": "Expand on the hook, provide value, tell the story",
      "required": true
    },
    {
      "element": "cta",
      "position": 3,
      "guidance": "Engagement prompt (save, share, comment) or link-in-bio direction",
      "required": true
    },
    {
      "element": "hashtags",
      "position": 4,
      "guidance": "Mix of reach hashtags (500k+) and niche hashtags (10k-100k)",
      "required": false
    }
  ],
  "bestFor": ["educational content", "step-by-step guides", "storytelling", "list posts"],
  "examplePost": "The 5 signs you're ready for change (that most people miss)...\n\n1. You keep having the same thought\n2. Small things feel bigger than they should\n3. You're researching instead of doing\n4. The discomfort of staying is growing\n5. You're reading posts like this one\n\nWhich one resonates? Drop a number below.",
  "hashtags": {
    "recommended": 15,
    "mix": ["3-5 broad reach", "5-7 niche", "3-5 community"]
  },
  "mediaRequirements": {
    "type": "image",
    "dimensions": { "width": 1080, "height": 1350 },
    "aspectRatio": "4:5",
    "maxFileSize": "8MB",
    "formats": ["jpg", "png"],
    "minImages": 2,
    "maxImages": 10,
    "slideGuidance": [
      "Slide 1: Hook/title slide - bold text, pattern interrupt",
      "Slides 2-9: One point per slide, minimal text",
      "Final slide: CTA or summary"
    ]
  }
}
```

### Additional Social Templates

```json
{
  "id": "social_linkedin-post",
  "name": "LinkedIn Post",
  "platform": "linkedin",
  "postType": "post",
  "description": "Professional network post - thought leadership, insights, stories",
  "constraints": {
    "maxCharacters": 3000,
    "maxHashtags": 5,
    "linksAllowed": true
  },
  "structure": [
    {
      "element": "hook",
      "position": 1,
      "maxLength": 150,
      "guidance": "First 2-3 lines visible - open with insight, question, or bold statement"
    },
    {
      "element": "story_or_insight",
      "position": 2,
      "guidance": "Personal experience, lesson learned, or industry observation"
    },
    {
      "element": "takeaway",
      "position": 3,
      "guidance": "Clear value for the reader - what they can use"
    },
    {
      "element": "engagement_prompt",
      "position": 4,
      "guidance": "Question that invites meaningful response"
    }
  ],
  "bestFor": ["thought leadership", "professional insights", "career stories", "industry commentary"]
}
```

```json
{
  "id": "social_twitter-thread",
  "name": "Twitter/X Thread",
  "platform": "twitter",
  "postType": "thread",
  "description": "Connected tweets for longer-form content on X",
  "constraints": {
    "maxCharactersPerTweet": 280,
    "maxTweets": 25,
    "maxHashtags": 2
  },
  "structure": [
    {
      "element": "hook_tweet",
      "position": 1,
      "maxLength": 280,
      "guidance": "Must stand alone AND promise value - this gets retweeted"
    },
    {
      "element": "content_tweets",
      "position": 2,
      "guidance": "One idea per tweet, numbered, easy to screenshot"
    },
    {
      "element": "summary_tweet",
      "position": 3,
      "guidance": "Recap the value, add the CTA"
    },
    {
      "element": "retweet_cta",
      "position": 4,
      "guidance": "Ask for RT/share to help others find it"
    }
  ],
  "bestFor": ["educational content", "frameworks", "stories", "breaking down complex topics"]
}
```

```json
{
  "id": "social_instagram-reel",
  "name": "Instagram Reel",
  "platform": "instagram",
  "postType": "reel",
  "description": "Short-form video content with caption",
  "constraints": {
    "maxCharacters": 2200,
    "maxHashtags": 30
  },
  "structure": [
    {
      "element": "caption_hook",
      "position": 1,
      "maxLength": 125,
      "guidance": "Text that complements video - don't repeat, add value"
    },
    {
      "element": "caption_body",
      "position": 2,
      "guidance": "Context, additional tips, or story behind the reel"
    },
    {
      "element": "cta",
      "position": 3,
      "guidance": "Follow for more, save this, share with someone who needs it"
    }
  ],
  "mediaRequirements": {
    "type": "video",
    "aspectRatio": "9:16",
    "dimensions": { "width": 1080, "height": 1920 },
    "maxLength": "90 seconds",
    "formats": ["mp4", "mov"]
  },
  "bestFor": ["quick tips", "behind the scenes", "trending audio", "relatable moments"]
}
```

---

## Platform Reference

| Platform | Post Types Available |
|----------|---------------------|
| Instagram | post, carousel, reel, story |
| LinkedIn | post, article, carousel |
| Twitter/X | post, thread |
| Facebook | post, reel, story |
| TikTok | video |
| Threads | post, thread |

---

## Validation Rules

| Check | Rule | Error Code |
|-------|------|------------|
| ID format | Must match `social_{platform}-{posttype}` pattern | `SOCIAL_INVALID_ID` |
| Required fields | All required fields present | `SOCIAL_MISSING_FIELD` |
| Unique ID | No duplicate IDs in library | `SOCIAL_DUPLICATE_ID` |
| Platform valid | Must be from allowed platforms | `SOCIAL_INVALID_PLATFORM` |
| PostType valid | Must be valid for platform | `SOCIAL_INVALID_POSTTYPE` |

---

## Write Policy

| Operation | Allowed By | Notes |
|-----------|------------|-------|
| Create | Human (manual) | System-defined templates |
| Update | Human (manual) | Creates new version |
| Archive | Human only | Soft delete |
| Delete | Never | Archive instead |

---

## Related

| Related To | Relationship |
|------------|--------------|
| `content-types` | Social templates implement `contenttype_social` |
| `section-specs` | Structure elements map to section specs |
| `brands` | Posts use brand voice/tone |

---

## Relationship to Template System

```
Template System                    Social System
┌─────────────────────┐           ┌─────────────────────┐
│ contenttype_social  │           │ social-templates    │
│ (generic structure) │──────────▶│ (platform-specific) │
│                     │           │                     │
│ • planDepth: minimal│           │ • constraints       │
│ • hook → body → cta │           │ • media specs       │
│ • platform-agnostic │           │ • character limits  │
└─────────────────────┘           └─────────────────────┘
```

---

*Created: 2026-01-20*
*Updated: 2026-01-20*
