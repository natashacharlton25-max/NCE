# Google Integration Module

> **Purpose:** Interface with Google APIs (Calendar, Drive, etc.)
> **Type:** Integration Module
> **Status:** Planning

---

## Overview

GoogleIntegration handles all Google API interactions. Currently focused on Google Calendar operations but extensible to Drive, Sheets, etc. Each Google service could be a sub-module or separate functions within this module.

```
Chain calls GoogleIntegration.function → Authenticate → Google API → Parse response
```

---

## Functions (Bricks)

### Calendar Functions

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `authenticate(scope)` | Get/refresh OAuth token | scope | token |
| `listCalendars()` | List available calendars | - | calendars |
| `getCalendar(calendarId)` | Get calendar details | calendarId | calendar |
| `listEvents(calendarId, options)` | List events in calendar | calendarId, options | events |
| `createEvent(calendarId, event)` | Create calendar event | calendarId, event | eventId |
| `updateEvent(calendarId, eventId, changes)` | Update event | calendarId, eventId, changes | updated |
| `deleteEvent(calendarId, eventId)` | Delete event | calendarId, eventId | deleted |

### Future: Drive Functions

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `listFiles(folderId)` | List files in folder | folderId | files |
| `uploadFile(file, folderId)` | Upload file to Drive | file, folderId | fileId |
| `downloadFile(fileId)` | Download file | fileId | file |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `GoogleIntegration.syncCalendar` | Sync events with system | authenticate → listEvents |
| `GoogleIntegration.scheduleBatch` | Schedule multiple events | authenticate → createEvent (multiple) |

---

## Detailed Function Specs

### authenticate(scope)

Gets or refreshes Google OAuth token.

```javascript
// Input
{
  scope: "calendar"  // calendar, drive, sheets
}

// Process
1. Get stored OAuth token
2. Check if expired
3. If expired → refresh via refresh_token
4. Return valid token

// Output
{
  token: "ya29...",
  expiresAt: "2026-01-17T15:30:00Z",
  scope: "https://www.googleapis.com/auth/calendar"
}
```

### listCalendars()

Lists all calendars the user has access to.

```javascript
// Process
1. authenticate('calendar')
2. GET /calendar/v3/users/me/calendarList
3. Parse response

// Output
{
  calendars: [
    {
      id: "primary",
      name: "Main Calendar",
      accessRole: "owner"
    },
    {
      id: "brand-schedule@group.calendar.google.com",
      name: "Brand Schedule",
      accessRole: "writer"
    }
  ]
}
```

### listEvents(calendarId, options)

Lists events from a calendar.

```javascript
// Input
{
  calendarId: "brand-schedule@group.calendar.google.com",
  options: {
    timeMin: "2026-01-17T00:00:00Z",
    timeMax: "2026-01-31T23:59:59Z",
    maxResults: 50
  }
}

// Process
1. authenticate('calendar')
2. GET /calendar/v3/calendars/{calendarId}/events
3. Parse response

// Output
{
  events: [
    {
      id: "event123",
      summary: "Brand Review - Acme",
      start: { dateTime: "2026-01-20T10:00:00Z" },
      end: { dateTime: "2026-01-20T11:00:00Z" },
      description: "Review brand kit for Acme Corp"
    }
  ],
  total: 1,
  nextPageToken: null
}
```

### createEvent(calendarId, event)

Creates a new calendar event.

```javascript
// Input
{
  calendarId: "brand-schedule@group.calendar.google.com",
  event: {
    summary: "Brand Kit Delivery - Acme",
    description: "Deliver completed brand kit",
    start: {
      dateTime: "2026-01-25T14:00:00Z",
      timeZone: "Europe/London"
    },
    end: {
      dateTime: "2026-01-25T14:30:00Z",
      timeZone: "Europe/London"
    },
    attendees: [
      { email: "client@acme.com" }
    ]
  }
}

// Process
1. authenticate('calendar')
2. POST /calendar/v3/calendars/{calendarId}/events
3. Return created event

// Output
{
  success: true,
  eventId: "event_new456",
  htmlLink: "https://calendar.google.com/event?eid=...",
  created: "2026-01-17T14:30:52Z"
}
```

---

## OAuth Configuration

```json
{
  "google": {
    "clientId": "{{GOOGLE_CLIENT_ID}}",
    "clientSecret": "{{GOOGLE_CLIENT_SECRET}}",
    "scopes": {
      "calendar": "https://www.googleapis.com/auth/calendar",
      "calendar.readonly": "https://www.googleapis.com/auth/calendar.readonly",
      "drive": "https://www.googleapis.com/auth/drive",
      "drive.readonly": "https://www.googleapis.com/auth/drive.readonly"
    },
    "redirectUri": "http://localhost:3000/oauth/google/callback"
  }
}
```

---

## Error Handling

| Google Error | Action |
|--------------|--------|
| 401 Unauthorized | Re-authenticate (refresh token) |
| 403 Forbidden | Check scope, escalate if permission issue |
| 404 Not Found | Return not found error |
| 429 Rate Limited | Retry with exponential backoff |
| 500/503 | Retry, then escalate |

---

## Config

### module.config.json

```json
{
  "module": "GoogleIntegration",
  "version": "1.0.0",
  "description": "Interface with Google APIs",
  "type": "integration",

  "functions": [
    {
      "name": "authenticate",
      "description": "Get/refresh OAuth token",
      "input": ["scope"],
      "output": "token"
    },
    {
      "name": "listCalendars",
      "description": "List available calendars",
      "input": [],
      "output": "calendars"
    },
    {
      "name": "getCalendar",
      "description": "Get calendar details",
      "input": ["calendarId"],
      "output": "calendar"
    },
    {
      "name": "listEvents",
      "description": "List events in calendar",
      "input": ["calendarId", "options"],
      "output": "events"
    },
    {
      "name": "createEvent",
      "description": "Create calendar event",
      "input": ["calendarId", "event"],
      "output": "eventId"
    },
    {
      "name": "updateEvent",
      "description": "Update event",
      "input": ["calendarId", "eventId", "changes"],
      "output": "updated"
    },
    {
      "name": "deleteEvent",
      "description": "Delete event",
      "input": ["calendarId", "eventId"],
      "output": "deleted"
    }
  ],

  "steps": [
    {
      "name": "syncCalendar",
      "description": "Sync events with system",
      "sequence": ["authenticate", "listEvents"]
    },
    {
      "name": "scheduleBatch",
      "description": "Schedule multiple events",
      "sequence": ["authenticate", "createEvent"]
    }
  ],

  "config": {
    "apiBase": "https://www.googleapis.com",
    "defaultCalendar": "primary",
    "rateLimitPerMinute": 100
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| APIKeyManager | Getting Google credentials |
| AuthHandler | OAuth token management |
| Fetchers | Making HTTP requests |

---

## Used By

| Module | How |
|--------|-----|
| Scheduler | Creating scheduled events |
| Notification chains | Calendar-based reminders |

---

*Last updated: 2026-01-17*
