# Notification Module

> **Purpose:** Handle all notifications to humans (alerts, updates, escalations)
> **Type:** Service Module
> **Status:** Planning

---

## Overview

Notification module is the single point for all human notifications. Whether it's an error alert, job completion, budget warning, or Level 4 escalation - all notifications route through here. It decides how to notify (email, Slack, SMS, dashboard) based on priority and preferences.

```
Event triggers → Notification.send() → Route to channels → Human receives
```

**Notification channels:**
- Email (via Emaillit)
- Slack/Discord
- SMS (for urgent)
- Dashboard/UI alerts
- Push notifications

---

## Roles & Rules

### Notification DOES:
- Send notifications via multiple channels (email, Slack, SMS, dashboard)
- Route notifications based on priority and user preferences
- Queue low-priority notifications for digests
- Handle urgent notifications immediately
- Track notification history and acknowledgments

### Notification does NOT:
- Decide when to notify (called by other modules)
- Generate notification content (receives ready content)
- Manage user accounts (just stores preferences)
- Send emails directly (uses Emaillit)

### Boundaries:
- Cannot bypass user preferences (except urgent)
- Cannot send without valid recipient
- Cannot access content beyond notification data
- Must respect quiet hours (except urgent)

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `send(notification)` | Send notification via appropriate channels | notification | sendResult |
| `sendUrgent(notification)` | Send high-priority notification | notification | sendResult |
| `queue(notification)` | Queue non-urgent notification | notification | queuedId |
| `getPreferences(userId)` | Get user notification preferences | userId | preferences |
| `setPreferences(userId, prefs)` | Update notification preferences | userId, prefs | updated |
| `getHistory(filter)` | Get notification history | filter | notifications |
| `acknowledge(notificationId)` | Mark notification as acknowledged | notificationId | acknowledged |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Notification.routeAndSend` | Route to channels then send | getPreferences → send |
| `Notification.escalate` | Escalate with increasing urgency | send → (wait) → sendUrgent |

---

## Detailed Function Specs

### send(notification)

Sends notification via appropriate channels based on type and preferences.

```javascript
// Input
{
  notification: {
    type: "job_complete",
    priority: "normal",  // low, normal, high, urgent
    title: "Brand Kit Complete",
    message: "The brand kit for Acme Corp has been generated successfully.",
    data: {
      jobId: "job_20260117_143052",
      brandId: "acme",
      downloadUrl: "https://..."
    },
    recipients: ["user_123"],  // or "all", or role-based
    channels: ["email", "dashboard"]  // optional override
  }
}

// Process
1. Get recipient preferences
2. Determine channels (prefs + priority rules)
3. Format for each channel
4. Send via each channel
5. Log notification

// Output
{
  success: true,
  notificationId: "notif_abc123",
  sentVia: ["email", "dashboard"],
  recipients: ["user_123"]
}
```

### sendUrgent(notification)

Sends high-priority notification through all urgent channels.

```javascript
// Input
{
  notification: {
    type: "system_error",
    priority: "urgent",
    title: "API Budget Exceeded",
    message: "Monthly API budget has been exceeded. All API calls are paused.",
    data: {
      budget: 50.00,
      used: 52.34,
      pausedJobs: ["job_1", "job_2"]
    },
    recipients: ["admin"]
  }
}

// Process
1. Override to all urgent channels (email + SMS + Slack)
2. Add urgent formatting/flags
3. Send immediately (bypass queue)
4. Log with urgent flag

// Output
{
  success: true,
  notificationId: "notif_urgent_123",
  sentVia: ["email", "sms", "slack"],
  urgentFlag: true
}
```

### queue(notification)

Queues notification for batch sending (digests).

```javascript
// Input
{
  notification: {
    type: "info",
    priority: "low",
    title: "Daily Summary Available",
    message: "Your daily usage summary is ready.",
    batchKey: "daily_digest"  // Group with similar notifications
  }
}

// Output
{
  queued: true,
  queueId: "queue_abc123",
  scheduledFor: "2026-01-17T18:00:00Z",  // Based on digest settings
  batchKey: "daily_digest"
}
```

---

## Notification Types

| Type | Default Priority | Default Channels |
|------|------------------|------------------|
| `job_complete` | normal | email, dashboard |
| `job_failed` | high | email, slack, dashboard |
| `budget_warning` | high | email, slack |
| `budget_exceeded` | urgent | email, sms, slack |
| `escalation` | high | email, slack |
| `system_error` | urgent | email, sms, slack |
| `daily_digest` | low | email |
| `info` | low | dashboard |

---

## User Preferences

```json
{
  "userId": "user_123",
  "channels": {
    "email": {
      "enabled": true,
      "address": "user@example.com",
      "digest": true,
      "digestTime": "18:00"
    },
    "slack": {
      "enabled": true,
      "webhook": "https://hooks.slack.com/...",
      "urgentOnly": false
    },
    "sms": {
      "enabled": true,
      "number": "+44...",
      "urgentOnly": true
    },
    "dashboard": {
      "enabled": true
    }
  },
  "quiet_hours": {
    "enabled": true,
    "start": "22:00",
    "end": "08:00",
    "timezone": "Europe/London",
    "exceptUrgent": true
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "Notification",
  "version": "1.0.0",
  "description": "Handle all human notifications",
  "type": "service",

  "functions": [
    {
      "name": "send",
      "description": "Send notification via appropriate channels",
      "input": ["notification"],
      "output": "sendResult"
    },
    {
      "name": "sendUrgent",
      "description": "Send high-priority notification",
      "input": ["notification"],
      "output": "sendResult"
    },
    {
      "name": "queue",
      "description": "Queue non-urgent notification",
      "input": ["notification"],
      "output": "queuedId"
    },
    {
      "name": "getPreferences",
      "description": "Get user notification preferences",
      "input": ["userId"],
      "output": "preferences"
    },
    {
      "name": "setPreferences",
      "description": "Update notification preferences",
      "input": ["userId", "prefs"],
      "output": "updated"
    },
    {
      "name": "getHistory",
      "description": "Get notification history",
      "input": ["filter"],
      "output": "notifications"
    },
    {
      "name": "acknowledge",
      "description": "Mark notification as acknowledged",
      "input": ["notificationId"],
      "output": "acknowledged"
    }
  ],

  "steps": [
    {
      "name": "routeAndSend",
      "description": "Route to channels then send",
      "sequence": ["getPreferences", "send"]
    },
    {
      "name": "escalate",
      "description": "Escalate with increasing urgency",
      "sequence": ["send", "sendUrgent"]
    }
  ],

  "config": {
    "defaultDigestTime": "18:00",
    "urgentBypassQuietHours": true,
    "maxRetries": 3
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Emaillit | Sending email notifications |
| Fetchers | Slack/webhook calls |
| FileManager | Loading user preferences |

---

## Used By

| Module | How |
|--------|-----|
| FailureHandler | Level 4 escalations |
| Orchestrator | Job completion notifications |
| TokenManager | Budget warnings |
| All modules | Error alerts |

---

*Last updated: 2026-01-17*
