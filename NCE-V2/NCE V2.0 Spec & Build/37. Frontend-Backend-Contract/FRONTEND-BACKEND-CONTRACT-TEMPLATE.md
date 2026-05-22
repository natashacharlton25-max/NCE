# Frontend-Backend Contract — {{Project Name}}

---
Status: DRAFT | IN REVIEW | APPROVED
Version: v1.0.0
Last Updated: {{timestamp}}
Owner: {{owner}}
---

## Purpose

This is the **single binding artifact** between frontend and backend. Frontend developers should reference ONLY this document for API communication.

**Source Documents:**
- API-SURFACE.md (v{{version}})
- ERROR-CODES.md (v{{version}})
- TYPES (v{{version}})

**Contract Rule:** This document is a projection of the sources above. It does NOT introduce new endpoints, parameters, or behaviours.

---

## Base Configuration

| Setting | Value |
|---------|-------|
| Base URL (Development) | `{{dev_url}}` |
| Base URL (Staging) | `{{staging_url}}` |
| Base URL (Production) | `{{prod_url}}` |
| Authentication | {{Bearer token / Cookie / API Key}} |
| Content-Type | `application/json` |

---

## Authentication

### Auth Header Format

```
Authorization: Bearer {{token}}
```

### Auth Flow

1. {{Step 1 - e.g., User logs in with credentials}}
2. {{Step 2 - e.g., Server returns access token}}
3. {{Step 3 - e.g., Frontend stores token}}
4. {{Step 4 - e.g., Frontend includes token in requests}}

### Token Refresh

{{Describe refresh flow or "N/A"}}

---

## Endpoints

### {{Domain/Resource}} Endpoints

#### GET {{/api/resource}}

**Purpose:** {{What this endpoint does}}

**Authentication:** Required / Optional / None

**Request:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| {{param}} | {{type}} | Yes/No | {{description}} |

**Response (Success - 200):**

```typescript
interface {{ResourceName}}ListResponse {
  data: {{ResourceName}}[];
  pagination: {
    total: number;
    page: number;
    pageSize: number;
    hasMore: boolean;
  };
}
```

**Mock Response:**

```json
{
  "data": [
    {
      "id": "{{prefix}}_abc123",
      "name": "Example Item",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ],
  "pagination": {
    "total": 42,
    "page": 1,
    "pageSize": 20,
    "hasMore": true
  }
}
```

**Errors:**

| Code | HTTP Status | When | UI Action |
|------|-------------|------|-----------|
| {{ERROR_001}} | 401 | Not authenticated | Redirect to login |
| {{ERROR_002}} | 403 | Not authorized | Show permission error |
| {{ERROR_003}} | 500 | Server error | Show retry option |

---

#### GET {{/api/resource/:id}}

**Purpose:** {{What this endpoint does}}

**Authentication:** Required / Optional / None

**Request:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Resource identifier |

**Response (Success - 200):**

```typescript
interface {{ResourceName}}Response {
  data: {{ResourceName}};
}
```

**Mock Response:**

```json
{
  "data": {
    "id": "{{prefix}}_abc123",
    "name": "Example Item",
    "description": "A detailed description",
    "status": "active",
    "createdAt": "2024-01-15T10:30:00Z",
    "updatedAt": "2024-01-15T14:20:00Z"
  }
}
```

**Errors:**

| Code | HTTP Status | When | UI Action |
|------|-------------|------|-----------|
| {{ERROR_001}} | 404 | Resource not found | Show not found page |
| {{ERROR_002}} | 401 | Not authenticated | Redirect to login |

---

#### POST {{/api/resource}}

**Purpose:** {{What this endpoint does}}

**Authentication:** Required

**Request Body:**

```typescript
interface Create{{ResourceName}}Request {
  name: string;
  description?: string;
  // ... other fields
}
```

**Mock Request:**

```json
{
  "name": "New Item",
  "description": "Optional description"
}
```

**Response (Success - 201):**

```typescript
interface Create{{ResourceName}}Response {
  data: {{ResourceName}};
}
```

**Mock Response:**

```json
{
  "data": {
    "id": "{{prefix}}_new123",
    "name": "New Item",
    "description": "Optional description",
    "status": "draft",
    "createdAt": "2024-01-20T09:00:00Z"
  }
}
```

**Errors:**

| Code | HTTP Status | When | UI Action |
|------|-------------|------|-----------|
| {{VALIDATION_001}} | 400 | Name missing | Highlight field |
| {{VALIDATION_002}} | 400 | Name too long | Show max length |
| {{ERROR_001}} | 409 | Duplicate name | Show conflict message |

---

#### PUT {{/api/resource/:id}}

**Purpose:** {{What this endpoint does}}

**Authentication:** Required

**Request Body:**

```typescript
interface Update{{ResourceName}}Request {
  name?: string;
  description?: string;
  // ... other fields (all optional for partial update)
}
```

**Response (Success - 200):**

```typescript
interface Update{{ResourceName}}Response {
  data: {{ResourceName}};
}
```

**Errors:**

| Code | HTTP Status | When | UI Action |
|------|-------------|------|-----------|
| {{ERROR_001}} | 404 | Not found | Show not found |
| {{ERROR_002}} | 409 | Concurrent edit | Prompt refresh |

---

#### DELETE {{/api/resource/:id}}

**Purpose:** {{What this endpoint does}}

**Authentication:** Required

**Request:** None (ID in path)

**Response (Success - 200):**

```json
{
  "success": true,
  "deleted": "{{prefix}}_abc123"
}
```

**Errors:**

| Code | HTTP Status | When | UI Action |
|------|-------------|------|-----------|
| {{ERROR_001}} | 404 | Not found | Show not found |
| {{ERROR_002}} | 409 | Has dependents | Show cannot delete |

---

### {{Next Domain}} Endpoints

(Repeat endpoint pattern for each domain)

---

## Shared Types

### {{TypeName}}

```typescript
interface {{TypeName}} {
  id: string;
  name: string;
  status: {{TypeName}}Status;
  createdAt: string;  // ISO 8601
  updatedAt: string;  // ISO 8601
}

type {{TypeName}}Status = "draft" | "active" | "archived";
```

---

### Pagination

```typescript
interface PaginationParams {
  page?: number;      // Default: 1
  pageSize?: number;  // Default: 20, Max: 100
  sortBy?: string;
  sortOrder?: "asc" | "desc";
}

interface PaginationResponse {
  total: number;
  page: number;
  pageSize: number;
  hasMore: boolean;
}
```

---

### Error Response

```typescript
interface ErrorResponse {
  error: {
    code: string;
    message: string;
    field?: string;      // For validation errors
    details?: unknown;   // Additional context
  };
}
```

---

## Error Codes Reference

### Validation Errors (400)

| Code | Message | UI Handling |
|------|---------|-------------|
| {{VAL_001}} | {{message}} | Highlight field |
| {{VAL_002}} | {{message}} | Show format hint |

### Authentication Errors (401)

| Code | Message | UI Handling |
|------|---------|-------------|
| {{AUTH_001}} | {{message}} | Redirect to login |
| {{AUTH_002}} | {{message}} | Prompt re-auth |

### Authorization Errors (403)

| Code | Message | UI Handling |
|------|---------|-------------|
| {{AUTHZ_001}} | {{message}} | Show permission denied |

### Not Found Errors (404)

| Code | Message | UI Handling |
|------|---------|-------------|
| {{NF_001}} | {{message}} | Show not found page |

### Conflict Errors (409)

| Code | Message | UI Handling |
|------|---------|-------------|
| {{CONF_001}} | {{message}} | Prompt user action |

### Server Errors (500)

| Code | Message | UI Handling |
|------|---------|-------------|
| {{SRV_001}} | {{message}} | Show retry option |

---

## Rate Limits

| Endpoint Pattern | Limit | Window | On Exceed |
|------------------|-------|--------|-----------|
| `*` | {{n}} requests | {{duration}} | 429 + Retry-After header |

---

## Gaps Identified

Any gaps found during contract creation.

| Gap | Should Be In | Impact | Status |
|-----|--------------|--------|--------|
| {{gap description}} | {{source file}} | High/Medium/Low | Escalated / Resolved |

**Note:** Gaps are NOT filled in this contract. They are escalated to source documents.

---

## Versioning

| Contract Version | API Version | Changes |
|------------------|-------------|---------|
| v1.0.0 | v1 | Initial contract |

---

## Notes

{{Any additional notes about API behaviour, quirks, or frontend considerations}}

---
Generated: {{timestamp}}
Phase: 37 (Frontend-Backend Contract)
Source: API-SURFACE.md, ERROR-CODES.md, TYPES
---
