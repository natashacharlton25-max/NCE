# AuthHandler Module

> **Purpose:** Authentication management for future multi-user system
> **Type:** System Service Module
> **Status:** Planning (Future)

---

## Overview

AuthHandler manages **user authentication** for when the system expands to multi-user. Currently single-user, but AuthHandler provides the foundation for future user management.

```
Request → AuthHandler.authenticate() → Token validated → User context provided
```

**AuthHandler will handle:**
- User login/logout
- Session management
- Token validation
- User context injection

---

## Roles & Rules

### AuthHandler DOES:
- Authenticate users
- Manage sessions
- Validate tokens
- Provide user context
- Handle password/token refresh
- Track login history

### AuthHandler does NOT:
- Authorize actions (PolicyEngine does that)
- Store user data (UserManager would do that)
- Handle API keys for providers (APIKeyManager does that)

### Boundaries:
- Authentication only, not authorization
- Cannot bypass login requirements
- Tokens expire according to policy
- All auth events logged

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `authenticate(credentials)` | Authenticate user | credentials | session |
| `validateToken(token)` | Validate session token | token | valid |
| `refreshToken(token)` | Refresh expiring token | token | newToken |
| `logout(sessionId)` | End session | sessionId | loggedOut |
| `getCurrentUser(token)` | Get current user context | token | user |
| `getActiveSessions(userId)` | Get user's active sessions | userId | sessions |

---

## Detailed Function Specs

### authenticate(credentials)

Authenticate user and create session.

```javascript
// Input
{
  type: "password",  // "password" | "token" | "oauth"
  username: "user@example.com",
  password: "********"
}

// Output - Success
{
  authenticated: true,
  user: {
    userId: "user_123",
    email: "user@example.com",
    name: "John Doe",
    role: "admin"
  },
  session: {
    sessionId: "sess_abc123",
    token: "eyJhbGc...",
    expiresAt: "2026-01-18T14:30:00Z",
    refreshToken: "refresh_xyz789"
  }
}

// Output - Failure
{
  authenticated: false,
  reason: "invalid_credentials",
  attempts: 2,
  lockoutWarning: true
}
```

### validateToken(token)

Validate a session token.

```javascript
// Input
{
  token: "eyJhbGc..."
}

// Output - Valid
{
  valid: true,
  userId: "user_123",
  sessionId: "sess_abc123",
  expiresIn: 3600000,  // 1 hour remaining
  needsRefresh: false
}

// Output - Invalid
{
  valid: false,
  reason: "expired",  // or "invalid", "revoked"
  action: "re-authenticate"
}
```

### getCurrentUser(token)

Get current user context for request.

```javascript
// Input
{
  token: "eyJhbGc..."
}

// Output
{
  userId: "user_123",
  email: "user@example.com",
  name: "John Doe",
  role: "admin",
  permissions: ["brand.create", "brand.edit", "chain.execute"],
  brands: ["acme", "globex"],  // Brands user can access
  preferences: {
    theme: "dark",
    notifications: true
  }
}
```

---

## Session Management

```json
// Active session
{
  "sessionId": "sess_abc123",
  "userId": "user_123",
  "createdAt": "2026-01-17T10:00:00Z",
  "expiresAt": "2026-01-18T10:00:00Z",
  "lastActivity": "2026-01-17T14:30:00Z",
  "device": "Chrome on Windows",
  "ip": "192.168.1.100"
}
```

---

## Current State: Single-User

For now, AuthHandler provides a **pass-through**:

```javascript
// Single-user mode
{
  singleUserMode: true,
  defaultUser: {
    userId: "owner",
    role: "admin",
    permissions: ["*"]  // All permissions
  }
}

// authenticate() always returns default user
// validateToken() always returns valid
// No actual login required
```

---

## Config

### module.config.json

```json
{
  "module": "AuthHandler",
  "version": "1.0.0",
  "description": "Authentication management",
  "type": "system-service",

  "functions": [
    {
      "name": "authenticate",
      "description": "Authenticate user",
      "input": ["credentials"],
      "output": "session"
    },
    {
      "name": "validateToken",
      "description": "Validate session token",
      "input": ["token"],
      "output": "valid"
    },
    {
      "name": "refreshToken",
      "description": "Refresh expiring token",
      "input": ["token"],
      "output": "newToken"
    },
    {
      "name": "logout",
      "description": "End session",
      "input": ["sessionId"],
      "output": "loggedOut"
    },
    {
      "name": "getCurrentUser",
      "description": "Get current user context",
      "input": ["token"],
      "output": "user"
    },
    {
      "name": "getActiveSessions",
      "description": "Get user's active sessions",
      "input": ["userId"],
      "output": "sessions"
    }
  ],

  "config": {
    "singleUserMode": true,
    "sessionDuration": 86400000,
    "refreshWindow": 3600000,
    "maxSessions": 5,
    "lockoutAttempts": 5
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| Logger | Logging auth events |
| FileManager | Session storage |

---

## Used By

| Component | How |
|-----------|-----|
| PolicyEngine | User context for permissions |
| All requests | Authentication validation |

---

*Last updated: 2026-01-17*
