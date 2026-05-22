# Project Error Codes — Master Registry

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

Master registry of ALL error codes in the project. This is the single source of truth — no error code should exist that isn't registered here.

---

## Error Code Format

```
{PREFIX}_{NUMBER}
```

| Part | Description | Example |
|------|-------------|---------|
| PREFIX | Component identifier | `USR`, `AUTH`, `INT_STRIPE` |
| NUMBER | 3-digit code | `001`, `101`, `901` |

**Full example:** `USR_101` — User not found

---

## Prefix Registry

### Systems

| System | Prefix | Range |
|--------|--------|-------|
| user-system | `USR` | USR_001 — USR_999 |
| auth-system | `AUTH` | AUTH_001 — AUTH_999 |
| project-system | `PRJ` | PRJ_001 — PRJ_999 |
| {{system}} | `{{PREFIX}}` | {{range}} |

### Integrations

| Integration | Prefix | Range |
|-------------|--------|-------|
| Stripe | `INT_STRIPE` | INT_STRIPE_001 — INT_STRIPE_999 |
| Email | `INT_EMAIL` | INT_EMAIL_001 — INT_EMAIL_999 |
| {{integration}} | `INT_{{NAME}}` | {{range}} |

### Libraries

| Library | Prefix | Range |
|---------|--------|-------|
| lib-validation | `LIB_VAL` | LIB_VAL_001 — LIB_VAL_099 |
| lib-errors | `LIB_ERR` | LIB_ERR_001 — LIB_ERR_099 |
| {{library}} | `LIB_{{NAME}}` | {{range}} |

### Shared

| Category | Prefix | Range |
|----------|--------|-------|
| Shared/Global | `SHARED` | SHARED_001 — SHARED_099 |

---

## Category Ranges

Within each prefix, codes are organized by category:

| Category | Range | Description |
|----------|-------|-------------|
| Validation | 001-099 | Input validation errors |
| Business | 100-199 | Business rule violations |
| Authorization | 200-299 | Permission errors |
| Dependency | 300-399 | Internal dependency failures |
| Storage | 400-499 | Database/storage errors |
| External | 500-599 | External service errors |
| State | 600-699 | Invalid state errors |
| Resource | 700-799 | Resource limit errors |
| Reserved | 800-899 | Reserved for future |
| System | 900-999 | Internal system errors |

---

## Complete Error Code Registry

### SHARED — Global Errors

| Code | Name | Message | HTTP | Retryable |
|------|------|---------|------|-----------|
| SHARED_001 | INTERNAL_ERROR | Internal server error | 500 | No |
| SHARED_002 | NOT_IMPLEMENTED | Feature not implemented | 501 | No |
| SHARED_003 | SERVICE_UNAVAILABLE | Service temporarily unavailable | 503 | Yes |
| SHARED_004 | TIMEOUT | Request timed out | 504 | Yes |
| SHARED_005 | RATE_LIMITED | Too many requests | 429 | Yes |

---

### USR — User System

#### Validation (USR_001-099)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| USR_001 | INVALID_EMAIL | Invalid email format | 400 |
| USR_002 | EMAIL_REQUIRED | Email is required | 400 |
| USR_003 | NAME_REQUIRED | Name is required | 400 |
| USR_004 | NAME_TOO_LONG | Name exceeds maximum length | 400 |
| USR_005 | INVALID_PHONE | Invalid phone format | 400 |

#### Business (USR_100-199)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| USR_101 | NOT_FOUND | User not found | 404 |
| USR_102 | EMAIL_EXISTS | Email already registered | 409 |
| USR_103 | CANNOT_DELETE | Cannot delete user with active resources | 409 |
| USR_104 | ALREADY_ARCHIVED | User is already archived | 409 |

#### Authorization (USR_200-299)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| USR_201 | NOT_AUTHORIZED | Not authorized to access this user | 403 |
| USR_202 | CANNOT_MODIFY_SELF | Cannot perform this action on yourself | 403 |

#### Storage (USR_400-499)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| USR_401 | SAVE_FAILED | Failed to save user | 500 |
| USR_402 | QUERY_FAILED | Failed to query users | 500 |

---

### AUTH — Auth System

#### Validation (AUTH_001-099)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| AUTH_001 | CREDENTIALS_REQUIRED | Email and password required | 400 |
| AUTH_002 | INVALID_TOKEN_FORMAT | Invalid token format | 400 |
| AUTH_003 | PASSWORD_TOO_WEAK | Password does not meet requirements | 400 |

#### Business (AUTH_100-199)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| AUTH_101 | INVALID_CREDENTIALS | Invalid email or password | 401 |
| AUTH_102 | TOKEN_EXPIRED | Token has expired | 401 |
| AUTH_103 | TOKEN_REVOKED | Token has been revoked | 401 |
| AUTH_104 | SESSION_NOT_FOUND | Session not found | 404 |
| AUTH_105 | ACCOUNT_LOCKED | Account is locked | 403 |
| AUTH_106 | ACCOUNT_DISABLED | Account is disabled | 403 |

#### Authorization (AUTH_200-299)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| AUTH_201 | INSUFFICIENT_PERMISSIONS | Insufficient permissions | 403 |
| AUTH_202 | SCOPE_REQUIRED | Required scope not granted | 403 |

#### Resource (AUTH_700-799)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| AUTH_701 | TOO_MANY_ATTEMPTS | Too many login attempts | 429 |
| AUTH_702 | SESSION_LIMIT | Maximum sessions reached | 429 |

---

### PRJ — Project System

#### Validation (PRJ_001-099)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| PRJ_001 | NAME_REQUIRED | Project name is required | 400 |
| PRJ_002 | NAME_TOO_LONG | Project name too long | 400 |
| PRJ_003 | INVALID_STATUS | Invalid project status | 400 |

#### Business (PRJ_100-199)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| PRJ_101 | NOT_FOUND | Project not found | 404 |
| PRJ_102 | NAME_EXISTS | Project name already exists | 409 |
| PRJ_103 | HAS_TASKS | Cannot delete project with tasks | 409 |
| PRJ_104 | INVALID_TRANSITION | Invalid status transition | 400 |

---

### INT_STRIPE — Stripe Integration

#### Validation (INT_STRIPE_001-099)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| INT_STRIPE_001 | INVALID_AMOUNT | Invalid payment amount | 400 |
| INT_STRIPE_002 | INVALID_CURRENCY | Invalid currency | 400 |

#### External (INT_STRIPE_500-599)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| INT_STRIPE_501 | API_ERROR | Stripe API error | 502 |
| INT_STRIPE_502 | RATE_LIMITED | Stripe rate limit exceeded | 429 |
| INT_STRIPE_503 | UNAVAILABLE | Stripe service unavailable | 503 |

#### Business (INT_STRIPE_100-199)

| Code | Name | Message | HTTP |
|------|------|---------|------|
| INT_STRIPE_101 | CARD_DECLINED | Card was declined | 402 |
| INT_STRIPE_102 | INSUFFICIENT_FUNDS | Insufficient funds | 402 |
| INT_STRIPE_103 | EXPIRED_CARD | Card has expired | 402 |

---

### LIB_VAL — Validation Library

| Code | Name | Message | HTTP |
|------|------|---------|------|
| LIB_VAL_001 | REQUIRED | Field is required | 400 |
| LIB_VAL_002 | TYPE_MISMATCH | Invalid type | 400 |
| LIB_VAL_003 | MIN_LENGTH | Below minimum length | 400 |
| LIB_VAL_004 | MAX_LENGTH | Exceeds maximum length | 400 |
| LIB_VAL_005 | PATTERN_MISMATCH | Does not match pattern | 400 |
| LIB_VAL_006 | OUT_OF_RANGE | Value out of range | 400 |
| LIB_VAL_007 | INVALID_ENUM | Not a valid enum value | 400 |

---

## Error Code Lookup

### By HTTP Status

| HTTP | Codes |
|------|-------|
| 400 | All validation errors (*_001-099), some business errors |
| 401 | AUTH_101, AUTH_102, AUTH_103 |
| 402 | INT_STRIPE_101, INT_STRIPE_102, INT_STRIPE_103 |
| 403 | All authorization errors (*_200-299) |
| 404 | *_101 (NOT_FOUND pattern) |
| 409 | Conflict errors (EMAIL_EXISTS, HAS_TASKS, etc.) |
| 429 | Rate limit errors |
| 500 | Storage errors, SHARED_001 |
| 502 | External API errors |
| 503 | Service unavailable |
| 504 | Timeout |

### By Retryability

| Retryable | Codes |
|-----------|-------|
| Yes | SHARED_003, SHARED_004, SHARED_005, *_RATE_LIMITED, *_UNAVAILABLE, *_TIMEOUT |
| No | All validation, authorization, business logic errors |
| Maybe | AUTH_101 (after credential fix), Storage errors (after transient fix) |

---

## Adding New Error Codes

### Checklist

1. [ ] Check this registry for existing similar code
2. [ ] Use correct prefix for component
3. [ ] Use correct range for category
4. [ ] Ensure code is unique
5. [ ] Add to component's ERRORS.md
6. [ ] Add to this master registry
7. [ ] Update version

### Template

```markdown
| {{PREFIX}}_{{NNN}} | {{NAME}} | {{Message}} | {{HTTP}} |
```

---

## Deprecation

### Deprecated Codes

| Code | Deprecated | Replaced By | Remove By |
|------|------------|-------------|-----------|
| _None_ | | | |

### Deprecation Process

1. Mark as deprecated in this registry
2. Add `@deprecated` in code
3. Log warning when used
4. Remove after 2 major versions

---

## Statistics

| Category | Count |
|----------|-------|
| Total codes | {{n}} |
| Systems | {{n}} |
| Integrations | {{n}} |
| Libraries | {{n}} |
| Shared | {{n}} |

---

## Notes

- All error codes MUST be registered here
- Never reuse a code for different meanings
- Deprecated codes cannot be reassigned
- Review quarterly for cleanup

---
Phase: 26 (Project-Wide Specs)
Generated: {{timestamp}}
---
