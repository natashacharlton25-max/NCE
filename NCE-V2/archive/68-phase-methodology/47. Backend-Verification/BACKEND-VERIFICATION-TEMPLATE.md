# Backend Verification Report

---
Project: {{project_name}}
Date: {{date}}
Tester: {{name}}
Backend Version: {{git_commit}}
Status: PASS | FAIL
---

## Summary

| Category | Total | Passed | Failed |
|----------|-------|--------|--------|
| Happy paths | {{count}} | {{count}} | {{count}} |
| Auth tests | {{count}} | {{count}} | {{count}} |
| Validation tests | {{count}} | {{count}} | {{count}} |
| Error tests | {{count}} | {{count}} | {{count}} |
| **Total** | {{count}} | {{count}} | {{count}} |

**Pass Rate:** {{percentage}}%

---

## Endpoint Results

### Auth Endpoints

| Endpoint | Method | Happy | Auth | Validation | Errors | Status |
|----------|--------|-------|------|------------|--------|--------|
| /api/auth/login | POST | ✅/❌ | N/A | ✅/❌ | ✅/❌ | PASS/FAIL |
| /api/auth/register | POST | ✅/❌ | N/A | ✅/❌ | ✅/❌ | PASS/FAIL |
| /api/auth/logout | POST | ✅/❌ | ✅/❌ | N/A | N/A | PASS/FAIL |
| /api/auth/refresh | POST | ✅/❌ | ✅/❌ | N/A | ✅/❌ | PASS/FAIL |

### User Endpoints

| Endpoint | Method | Happy | Auth | Validation | Errors | Status |
|----------|--------|-------|------|------------|--------|--------|
| /api/users/me | GET | ✅/❌ | ✅/❌ | N/A | N/A | PASS/FAIL |
| /api/users/me | PUT | ✅/❌ | ✅/❌ | ✅/❌ | N/A | PASS/FAIL |

### {{Resource}} Endpoints

| Endpoint | Method | Happy | Auth | Validation | Errors | Status |
|----------|--------|-------|------|------------|--------|--------|
| /api/{{resource}} | GET | ✅/❌ | ✅/❌ | N/A | ✅/❌ | PASS/FAIL |
| /api/{{resource}} | POST | ✅/❌ | ✅/❌ | ✅/❌ | N/A | PASS/FAIL |
| /api/{{resource}}/:id | GET | ✅/❌ | ✅/❌ | N/A | ✅/❌ | PASS/FAIL |
| /api/{{resource}}/:id | PUT | ✅/❌ | ✅/❌ | ✅/❌ | ✅/❌ | PASS/FAIL |
| /api/{{resource}}/:id | DELETE | ✅/❌ | ✅/❌ | N/A | ✅/❌ | PASS/FAIL |

---

## Type Contract Verification

### Response Types

| Endpoint | Expected Type | Actual | Match | Notes |
|----------|---------------|--------|-------|-------|
| GET /api/{{resource}} | `PaginatedResponse<{{Type}}>` | ✅/❌ | ✅/❌ | |
| POST /api/{{resource}} | `{{Type}}` | ✅/❌ | ✅/❌ | |
| GET /api/{{resource}}/:id | `{{Type}}` | ✅/❌ | ✅/❌ | |
| PUT /api/{{resource}}/:id | `{{Type}}` | ✅/❌ | ✅/❌ | |

### Error Types

| Error Code | HTTP Status | Format Correct | Notes |
|------------|-------------|----------------|-------|
| UNAUTHORIZED | 401 | ✅/❌ | |
| FORBIDDEN | 403 | ✅/❌ | |
| NOT_FOUND | 404 | ✅/❌ | |
| VALIDATION_ERROR | 400 | ✅/❌ | |
| INTERNAL_ERROR | 500 | ✅/❌ | |

---

## Auth Flow Verification

### Login Flow

| Step | Action | Expected | Actual | Status |
|------|--------|----------|--------|--------|
| 1 | POST /auth/login with valid credentials | 200 + token | | ✅/❌ |
| 2 | Use token on protected endpoint | 200 + data | | ✅/❌ |
| 3 | POST /auth/login with wrong password | 401 + INVALID_CREDENTIALS | | ✅/❌ |
| 4 | POST /auth/login with nonexistent email | 401 + INVALID_CREDENTIALS | | ✅/❌ |

### Register Flow

| Step | Action | Expected | Actual | Status |
|------|--------|----------|--------|--------|
| 1 | POST /auth/register with valid data | 201 + token + user | | ✅/❌ |
| 2 | POST /auth/register with existing email | 400 + EMAIL_EXISTS | | ✅/❌ |
| 3 | Login with new account | 200 + token | | ✅/❌ |

### Token Handling

| Test | Expected | Actual | Status |
|------|----------|--------|--------|
| No token on protected route | 401 + UNAUTHORIZED | | ✅/❌ |
| Invalid token | 401 + UNAUTHORIZED | | ✅/❌ |
| Expired token | 401 + TOKEN_EXPIRED | | ✅/❌ |
| Malformed token | 401 + UNAUTHORIZED | | ✅/❌ |

---

## Detailed Test Results

### Happy Path Tests

| # | Endpoint | Test Description | Expected | Actual | Status |
|---|----------|------------------|----------|--------|--------|
| 1 | POST /auth/login | Login with valid credentials | 200 + token | | ✅/❌ |
| 2 | GET /api/projects | List user's projects | 200 + paginated list | | ✅/❌ |
| 3 | POST /api/projects | Create new project | 201 + project | | ✅/❌ |
| 4 | GET /api/projects/:id | Get single project | 200 + project | | ✅/❌ |
| 5 | PUT /api/projects/:id | Update project | 200 + updated project | | ✅/❌ |
| 6 | DELETE /api/projects/:id | Delete project | 204 | | ✅/❌ |

### Error Path Tests

| # | Endpoint | Test Description | Expected | Actual | Status |
|---|----------|------------------|----------|--------|--------|
| 1 | GET /api/projects/:id | Nonexistent ID | 404 + NOT_FOUND | | ✅/❌ |
| 2 | PUT /api/projects/:id | Not owner | 403 + FORBIDDEN | | ✅/❌ |
| 3 | DELETE /api/projects/:id | Not owner | 403 + FORBIDDEN | | ✅/❌ |
| 4 | POST /api/projects | Missing required field | 400 + VALIDATION_ERROR | | ✅/❌ |

---

## Issues Found

| # | Severity | Endpoint | Issue Description | Resolution | Status |
|---|----------|----------|-------------------|------------|--------|
| 1 | {{High/Med/Low}} | {{endpoint}} | {{description}} | {{how fixed}} | Fixed/Open |

### Issue Details

#### Issue #1: {{Title}}

**Endpoint:** {{method}} {{path}}
**Severity:** High / Medium / Low
**Found:** {{timestamp}}

**Description:**
{{Detailed description of the issue}}

**Expected Behavior:**
{{What should have happened}}

**Actual Behavior:**
{{What actually happened}}

**Resolution:**
{{How it was fixed}}

**Verified:** ✅ Fixed and re-tested / ❌ Still open

---

## Performance Notes

| Endpoint | Avg Response Time | Notes |
|----------|-------------------|-------|
| GET /api/projects | {{ms}} | |
| POST /api/projects | {{ms}} | |
| GET /api/projects/:id | {{ms}} | |

---

## Recommendations

### For Phase 48+ (Frontend)

- {{Any recommendations for frontend integration}}
- {{Known quirks or edge cases}}
- {{Suggested error handling approaches}}

### For 0g Testing

- {{Areas needing more thorough testing}}
- {{Edge cases to cover}}
- {{Performance concerns}}

---

## Conclusion

### Overall Status: **PASS / FAIL**

### Summary

{{Summary paragraph describing the verification outcome}}

### Blocking Issues

{{If FAIL, list what must be fixed before proceeding}}

- [ ] {{Issue 1}}
- [ ] {{Issue 2}}

### Non-Blocking Issues

{{Issues that can be addressed later}}

- [ ] {{Issue 1}}

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Tester | {{name}} | {{date}} | ☐ Verified |
| Reviewer | {{name}} | {{date}} | ☐ Approved |

---
Template: BACKEND-VERIFICATION-TEMPLATE.md
Phase: 47
Section: 0f. Implementation
---
