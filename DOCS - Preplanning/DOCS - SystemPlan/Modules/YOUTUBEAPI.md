# YouTubeAPI Module

> **Purpose:** YouTube API integration for video content and channel management
> **Type:** Integration Module
> **Status:** Planning (Future)

---

## Overview

YouTubeAPI provides **YouTube integration** for video content management. It handles video uploads, channel analytics, and content scheduling.

```
Content ready → YouTubeAPI.upload() → YouTube → Published
```

**YouTubeAPI enables:**
- Video uploads
- Thumbnail management
- Channel analytics
- Playlist management
- Comment moderation

---

## Roles & Rules

### YouTubeAPI DOES:
- Upload videos to YouTube
- Manage video metadata
- Retrieve channel analytics
- Handle playlists
- Moderate comments

### YouTubeAPI does NOT:
- Create video content (other modules do that)
- Edit videos (external tools)
- Make content decisions
- Store videos locally long-term

### Boundaries:
- YouTube API operations only
- Respects YouTube quota limits
- OAuth required for write operations
- Read operations may use API key

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `uploadVideo(video)` | Upload video to YouTube | video | uploadResult |
| `updateMetadata(videoId, metadata)` | Update video metadata | videoId, metadata | updated |
| `getAnalytics(channelId, period)` | Get channel analytics | channelId, period | analytics |
| `createPlaylist(playlist)` | Create playlist | playlist | playlist |
| `addToPlaylist(playlistId, videoId)` | Add video to playlist | playlistId, videoId | added |
| `getComments(videoId)` | Get video comments | videoId | comments |
| `moderateComment(commentId, action)` | Moderate a comment | commentId, action | moderated |

---

## Detailed Function Specs

### uploadVideo(video)

Upload a video to YouTube.

```javascript
// Input
{
  file: "/temp/video-20260117.mp4",
  metadata: {
    title: "How AI is Changing Marketing",
    description: "In this video...",
    tags: ["AI", "marketing", "technology"],
    categoryId: "28",  // Science & Technology
    privacyStatus: "private",  // "public" | "private" | "unlisted"
    publishAt: "2026-01-20T15:00:00Z"  // Scheduled publish
  },
  thumbnail: "/temp/thumbnail-20260117.jpg"
}

// Output
{
  success: true,
  videoId: "dQw4w9WgXcQ",
  status: "processing",
  publishAt: "2026-01-20T15:00:00Z",
  thumbnailUrl: "https://i.ytimg.com/vi/dQw4w9WgXcQ/maxresdefault.jpg"
}
```

### getAnalytics(channelId, period)

Get channel analytics.

```javascript
// Input
{
  channelId: "UC_channel_id",
  period: "last_28_days"
}

// Output
{
  channelId: "UC_channel_id",
  period: { start: "2025-12-20", end: "2026-01-17" },
  metrics: {
    views: 125000,
    watchTime: 450000,  // minutes
    subscribers: { gained: 500, lost: 50, net: 450 },
    estimatedRevenue: 350.00
  },
  topVideos: [
    { videoId: "abc123", title: "Top Video", views: 25000 }
  ]
}
```

---

## YouTube Quota Management

YouTube API has strict quotas:

```javascript
{
  quotas: {
    daily: 10000,
    upload: 1600,  // per video
    read: 1        // most read operations
  },
  currentUsage: {
    today: 3500,
    remaining: 6500
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "YouTubeAPI",
  "version": "1.0.0",
  "description": "YouTube API integration",
  "type": "integration",

  "functions": [
    {
      "name": "uploadVideo",
      "description": "Upload video to YouTube",
      "input": ["video"],
      "output": "uploadResult"
    },
    {
      "name": "updateMetadata",
      "description": "Update video metadata",
      "input": ["videoId", "metadata"],
      "output": "updated"
    },
    {
      "name": "getAnalytics",
      "description": "Get channel analytics",
      "input": ["channelId", "period"],
      "output": "analytics"
    },
    {
      "name": "createPlaylist",
      "description": "Create playlist",
      "input": ["playlist"],
      "output": "playlist"
    },
    {
      "name": "addToPlaylist",
      "description": "Add video to playlist",
      "input": ["playlistId", "videoId"],
      "output": "added"
    },
    {
      "name": "getComments",
      "description": "Get video comments",
      "input": ["videoId"],
      "output": "comments"
    },
    {
      "name": "moderateComment",
      "description": "Moderate a comment",
      "input": ["commentId", "action"],
      "output": "moderated"
    }
  ],

  "config": {
    "apiKeyRef": "YOUTUBE_API_KEY",
    "oauthClientId": "YOUTUBE_OAUTH_CLIENT_ID",
    "dailyQuotaLimit": 10000,
    "uploadChunkSize": 10485760
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | API credentials |
| RequestHandler | HTTP requests |
| RateLimiter | Quota management |
| Logger | Logging operations |

---

## Used By

| Component | How |
|-----------|-----|
| ContentManager | Video publishing |
| SocialManager | Cross-platform content |

---

*Last updated: 2026-01-17*
