# API Surface

---
Project: {{Project Name}}
API Style: REST / GraphQL / RPC
Base URL: {{base_url}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Overview

| Metric | Value |
|--------|-------|
| Total Endpoints | {{n}} |
| Components with APIs | {{n}} |
| Authentication Methods | {{methods}} |

---

## Authentication

| Method | Header/Mechanism | Used By |
|--------|------------------|---------|
| Bearer Token | `Authorization: Bearer {token}` | All protected endpoints |
| API Key | `X-API-Key: {key}` | {{endpoints}} |
| None | — | Public endpoints |

---

## Base Paths

| Path Prefix | Component | Description |
|-------------|-----------|-------------|
| `/api/auth` | Auth System | Authentication & authorization |
| `/api/users` | Users System | User management |
| `/api/content` | Content System | Content operations |
| `/api/{{resource}}` | {{Component}} | {{description}} |

---

## Endpoints by Component

### Auth System

Source: `auth/spec/FUNCTIONS.md`

#### POST /api/auth/login

**Purpose:** Authenticate user and return tokens

**Authentication:** None (public)

**Request:**
```json
{
  "email": "string (required)",
  "password": "string (required)"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "accessToken": "string",
    "refreshToken": "string",
    "expiresIn": "number (seconds)"
  }
}
```

**Errors:**

| Code | HTTP | Condition |
|------|------|-----------|
| AUTH_001 | 400 | Missing email or password |
| AUTH_002 | 401 | Invalid credentials |
| AUTH_003 | 403 | Account locked |

---

#### POST /api/auth/logout

**Purpose:** Invalidate current session

**Authentication:** Bearer Token

**Request:** None

**Response (200):**
```json
{
  "success": true
}
```

**Errors:**

| Code | HTTP | Condition |
|------|------|-----------|
| AUTH_010 | 401 | Invalid or expired token |

---

### {{Next Component}}

Source: `{{component}}/spec/FUNCTIONS.md`

#### {{METHOD}} {{path}}

**Purpose:** {{description}}

**Authentication:** {{None / Bearer Token / API Key}}

**Request:**
```json
{
  {{request_schema}}
}
```

**Response ({{status}}):**
```json
{
  {{response_schema}}
}
```

**Errors:**

| Code | HTTP | Condition |
|------|------|-----------|
| {{code}} | {{http}} | {{condition}} |

---

## Common Response Formats

### Success Response

```json
{
  "success": true,
  "data": { ... }
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "code": "{{PREFIX}}_{{NUMBER}}",
    "message": "Human-readable message",
    "details": { ... }
  }
}
```

### Paginated Response

```json
{
  "success": true,
  "data": {
    "items": [ ... ],
    "total": 100,
    "limit": 20,
    "offset": 0,
    "hasMore": true
  }
}
```

---

## Shared Types

From `standards/SCHEMAS.md`:

### {{TypeName}}

```typescript
interface {{TypeName}} {
  {{field}}: {{type}};
}
```

Used by: {{endpoints}}

---

## Error Code Registry

From `standards/ERROR-CODES.md`:

| Range | Component | Category |
|-------|-----------|----------|
| AUTH_001-099 | Auth | Validation |
| AUTH_100-199 | Auth | Business rules |
| {{PREFIX}}_XXX | {{Component}} | {{Category}} |

Full error definitions: See ERROR-CODES.md

---

## Rate Limits

| Endpoint Pattern | Limit | Window |
|------------------|-------|--------|
| `/api/auth/*` | 10 requests | per minute |
| `/api/*` | 100 requests | per minute |
| {{pattern}} | {{limit}} | {{window}} |

---

## Versioning

| Aspect | Approach |
|--------|----------|
| Version Location | URL path (`/api/v1/...`) / Header |
| Current Version | v1 |
| Deprecation Policy | {{policy}} |

---

## Endpoint Summary Table

| Method | Path | Auth | Component | Purpose |
|--------|------|------|-----------|---------|
| POST | /api/auth/login | None | Auth | User login |
| POST | /api/auth/logout | Bearer | Auth | User logout |
| GET | /api/users/me | Bearer | Users | Get current user |
| {{method}} | {{path}} | {{auth}} | {{component}} | {{purpose}} |

---

## Gaps & Unclear Items

Items that could not be fully consolidated due to unclear specs:

| Item | Source | Issue | Resolution Needed |
|------|--------|-------|-------------------|
| {{item or "None"}} | {{FUNCTIONS.md}} | {{what's unclear}} | {{what needs clarification}} |

**Note:** These gaps were NOT filled by inference. They require spec clarification in 0c.

---

## Conflict Check

| Check | Result |
|-------|--------|
| No path collisions | ✅ / ❌ |
| No method collisions | ✅ / ❌ |
| No type mismatches | ✅ / ❌ |
| No error code collisions | ✅ / ❌ |

**Conflicts Found:** None / See PRECODE-DECISION-NOTES.md

---

## Change Log

| Version | Date | Change | Author |
|---------|------|--------|--------|
| v1.0.0 | {{date}} | Initial API surface | Claude |

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
