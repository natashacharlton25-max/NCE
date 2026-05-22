# Phase 47: Backend Verification

---
Phase: 47
Section: 0f. Implementation
Name: Backend Verification
Purpose: Verify backend is complete and correct before frontend
MANDATORY: This phase cannot be skipped or collapsed
---

## Role

You are verifying the backend implementation. Your job is to test every endpoint, verify type contracts, and document the results before any frontend work begins.

**This phase is a MANDATORY checkpoint. Do not proceed to frontend until verification passes.**

---

## Inputs

| Document | Location | What to Verify Against |
|----------|----------|------------------------|
| API-SURFACE.md | 0c/0d outputs | Every endpoint exists |
| ERROR-CODES.md | 0c outputs | Error format correct |
| shared-types/ | From Phase 43 | Response types match |
| Backend code | From Phase 46 | Implementation |

---

## Process

### Step 1: Create Test Plan

For each endpoint in API-SURFACE.md, plan tests:

| Endpoint | Happy Path | Auth Test | Validation Test | Error Test |
|----------|------------|-----------|-----------------|------------|
| POST /auth/login | Valid login | N/A | Invalid email | Wrong password |
| GET /projects | List projects | No token | N/A | Empty list |
| POST /projects | Create project | No token | Missing name | N/A |
| GET /projects/:id | Get project | No token | N/A | Not found |
| PUT /projects/:id | Update project | No token | Invalid data | Not owner |
| DELETE /projects/:id | Delete project | No token | N/A | Not owner |

### Step 2: Test Each Endpoint

#### Testing Tools

Use one or more:
- **curl** — Command line
- **Postman** — GUI tool
- **REST Client** — VS Code extension
- **Automated tests** — Jest/Vitest (optional at this phase)

#### Test Pattern: Happy Path

```bash
# Login first to get token
TOKEN=$(curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}' \
  | jq -r '.token')

# Test endpoint
curl -X GET http://localhost:3000/api/projects \
  -H "Authorization: Bearer $TOKEN" \
  | jq .
```

**Verify:**
- Status code is correct (200, 201, 204)
- Response shape matches shared-types/
- Data is correct

#### Test Pattern: Auth Path

```bash
# Test without token
curl -X GET http://localhost:3000/api/projects \
  | jq .

# Expected:
# {
#   "error_code": "UNAUTHORIZED",
#   "message": "Missing or invalid token"
# }
```

**Verify:**
- Status code is 401
- Error format matches ERROR-CODES.md

#### Test Pattern: Validation Path

```bash
# Test with invalid data
curl -X POST http://localhost:3000/api/projects \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":""}' \
  | jq .

# Expected:
# {
#   "error_code": "VALIDATION_ERROR",
#   "message": "Validation failed",
#   "details": [...]
# }
```

**Verify:**
- Status code is 400
- Error includes validation details

#### Test Pattern: Error Path

```bash
# Test not found
curl -X GET http://localhost:3000/api/projects/nonexistent-id \
  -H "Authorization: Bearer $TOKEN" \
  | jq .

# Expected:
# {
#   "error_code": "NOT_FOUND",
#   "message": "Project not found"
# }
```

**Verify:**
- Status code is 404
- Error code is correct

### Step 3: Verify Type Contracts

For each endpoint, verify response matches shared-types/:

```typescript
// Expected type from shared-types/
interface Project {
  id: string;
  name: string;
  description: string | null;
  status: string;
  userId: string;
  createdAt: string;
  updatedAt: string;
}

// Actual response
{
  "id": "clx123...",
  "name": "Test Project",
  "description": null,
  "status": "active",
  "userId": "user-1",
  "createdAt": "2024-01-15T10:30:00.000Z",
  "updatedAt": "2024-01-15T10:30:00.000Z"
}

// ✅ Matches - all fields present with correct types
```

**Check for:**
- All fields present
- Correct types (string, number, boolean, null)
- No extra fields (unless expected)
- Date format consistent

### Step 4: Verify Auth Flows

Test complete authentication flows:

#### Login Flow
```bash
# 1. Login
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'

# ✅ Returns token and user

# 2. Use token
curl -X GET http://localhost:3000/api/users/me \
  -H "Authorization: Bearer $TOKEN"

# ✅ Returns current user
```

#### Register Flow
```bash
# 1. Register
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"new@example.com","password":"password123","name":"New User"}'

# ✅ Returns token and user

# 2. Can login with new account
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"new@example.com","password":"password123"}'

# ✅ Returns token
```

#### Token Expiry (if applicable)
```bash
# Use expired token
curl -X GET http://localhost:3000/api/users/me \
  -H "Authorization: Bearer EXPIRED_TOKEN"

# ✅ Returns 401 with TOKEN_EXPIRED
```

### Step 5: Document Results

Create BACKEND-VERIFICATION-REPORT.md:

```markdown
# Backend Verification Report

**Date:** {{date}}
**Tester:** {{name}}
**Backend Version:** {{commit hash}}

## Summary

| Category | Total | Passed | Failed |
|----------|-------|--------|--------|
| Happy paths | 10 | 10 | 0 |
| Auth tests | 8 | 8 | 0 |
| Validation tests | 6 | 6 | 0 |
| Error tests | 5 | 5 | 0 |
| **Total** | 29 | 29 | 0 |

## Endpoint Results

### Auth Endpoints

| Endpoint | Happy | Auth | Validation | Errors | Status |
|----------|-------|------|------------|--------|--------|
| POST /auth/login | ✅ | N/A | ✅ | ✅ | PASS |
| POST /auth/register | ✅ | N/A | ✅ | ✅ | PASS |
| POST /auth/logout | ✅ | ✅ | N/A | N/A | PASS |

### Project Endpoints

| Endpoint | Happy | Auth | Validation | Errors | Status |
|----------|-------|------|------------|--------|--------|
| GET /projects | ✅ | ✅ | N/A | ✅ | PASS |
| POST /projects | ✅ | ✅ | ✅ | N/A | PASS |
| GET /projects/:id | ✅ | ✅ | N/A | ✅ | PASS |
| PUT /projects/:id | ✅ | ✅ | ✅ | ✅ | PASS |
| DELETE /projects/:id | ✅ | ✅ | N/A | ✅ | PASS |

## Type Contract Verification

| Endpoint | Response Type | Matches | Notes |
|----------|---------------|---------|-------|
| GET /projects | PaginatedResponse<Project> | ✅ | |
| POST /projects | Project | ✅ | |
| GET /projects/:id | Project | ✅ | |

## Auth Flow Verification

| Flow | Status | Notes |
|------|--------|-------|
| Login | ✅ | |
| Register | ✅ | |
| Token usage | ✅ | |
| Token expiry | ✅ | |

## Issues Found

| # | Endpoint | Issue | Severity | Status |
|---|----------|-------|----------|--------|
| 1 | PUT /projects/:id | Was returning 200 instead of 200 with body | Low | Fixed |

## Conclusion

Backend verification: **PASSED**

Ready for frontend integration.
```

### Step 6: Fix Issues

If issues are found:

1. **Document the issue** in the report
2. **Fix the implementation** (Phase 46 code)
3. **Re-test** the affected endpoint
4. **Update the report** to show fixed

### Step 7: Create Summary

Update IMPLEMENTATION-LOG.md with verification results.

---

## Output

| Output | Location | Status |
|--------|----------|--------|
| BACKEND-VERIFICATION-REPORT.md | docs/ or project root | ☐ Created |
| All issues fixed | Backend code | ☐ Complete |
| Implementation log updated | IMPLEMENTATION-LOG.md | ☐ Updated |

---

## Checklist

Before moving to Phase 48 (Frontend):

- [ ] Every endpoint in API-SURFACE.md tested
- [ ] All happy paths work
- [ ] All auth tests pass (401 when expected)
- [ ] All validation tests pass (400 when expected)
- [ ] All error tests pass (404, 403, etc.)
- [ ] Response types match shared-types/
- [ ] Error format matches ERROR-CODES.md
- [ ] Auth flows work end-to-end
- [ ] No blocking issues
- [ ] BACKEND-VERIFICATION-REPORT.md created

---

## BACKEND-VERIFICATION-TEMPLATE.md

Use this template for the report:

```markdown
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
| Happy paths | | | |
| Auth tests | | | |
| Validation tests | | | |
| Error tests | | | |
| **Total** | | | |

## Endpoint Results

### {{Resource}} Endpoints

| Endpoint | Happy | Auth | Validation | Errors | Status |
|----------|-------|------|------------|--------|--------|
| {{method}} {{path}} | | | | | |

## Type Contract Verification

| Endpoint | Expected Type | Matches | Notes |
|----------|---------------|---------|-------|
| {{endpoint}} | {{type}} | ✅/❌ | |

## Auth Flow Verification

| Flow | Status | Notes |
|------|--------|-------|
| Login | | |
| Register | | |
| Token usage | | |
| Token expiry | | |

## Issues Found

| # | Endpoint | Issue | Severity | Fixed |
|---|----------|-------|----------|-------|
| | | | | |

## Conclusion

Backend verification: **PASS / FAIL**

{{If FAIL, list blocking issues}}

---
```

---

## Implementation Log Entry

Add to IMPLEMENTATION-LOG.md:

```markdown
## Phase 47: Backend Verification

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Backend verification completed. All endpoints tested and working.

### Test Results

| Category | Tested | Passed | Failed |
|----------|--------|--------|--------|
| Happy paths | 10 | 10 | 0 |
| Auth paths | 8 | 8 | 0 |
| Validation paths | 6 | 6 | 0 |
| Error paths | 5 | 5 | 0 |
| **Total** | 29 | 29 | 0 |

### Issues Found and Fixed

| Issue | Endpoint | Resolution |
|-------|----------|------------|
| (none) | | |

### Files Created
- BACKEND-VERIFICATION-REPORT.md

### Deviations
- None

### Notes
Backend is ready for frontend integration.
```

---

## Rules

1. **Test EVERY endpoint** — No exceptions
2. **Document EVERYTHING** — Report must be complete
3. **Fix before proceeding** — No known failures allowed
4. **Type contracts matter** — Response shapes must match
5. **This phase is MANDATORY** — Cannot skip or collapse
6. **No partial passes** — Phase 47 may only pass if ALL endpoints required by API-MAPPING.md pass. Partial backend readiness is not acceptable for frontend integration. If an endpoint is blocked by external dependency, document and resolve before proceeding.

---

## Next Phase

After completing Phase 47 with all tests passing:

**Phase 48: Frontend Setup**

**DO NOT proceed if:**
- Any endpoint is untested
- Any test is failing
- Any blocking issues exist

---
