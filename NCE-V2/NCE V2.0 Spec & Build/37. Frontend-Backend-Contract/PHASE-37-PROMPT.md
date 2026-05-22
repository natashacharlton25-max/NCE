# PHASE 37: FRONTEND-BACKEND CONTRACT

---
Phase: 37
Name: Frontend-Backend Contract
Section: 0e. Frontend
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are creating the single binding artifact between frontend and backend. This contract is the ONLY document frontend should reference for API communication.

---

## CRITICAL RULE: PROJECTION ONLY

**The Frontend-Backend Contract may NOT introduce:**
- New endpoints
- New parameters
- New request/response fields
- New behaviours
- New error codes

It is a **projection** of API-SURFACE.md, not a modification.

**If you find a gap:**
1. Document it in FRONTEND-DECISION-NOTES.md
2. Note which source file should have it
3. Escalate to user
4. Do NOT fill the gap yourself

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| API-SURFACE.md | `precode/` or `standards/` | Source of endpoint definitions |
| ERROR-CODES.md | `standards/` | Error codes frontend must handle |
| Shared TYPES | `standards/` | Data types for requests/responses |

---

## TASK

### Step 1: Gather Sources

Read:
- API-SURFACE.md
- ERROR-CODES.md
- All relevant TYPES files

### Step 2: Filter for Frontend

Extract only what frontend needs:
- Public API endpoints (exclude internal/admin if separate apps)
- Types used in public API requests/responses
- Error codes that can reach the frontend

**Ask user:** "Are there endpoints in API-SURFACE.md that the frontend should NOT access?"

### Step 3: Structure the Contract

For each endpoint:
1. Copy definition from API-SURFACE.md exactly
2. Add request/response type definitions
3. List error codes that endpoint can return
4. Add mock data example (golden sample)

### Step 4: Create Mock Data Examples

For each endpoint, provide a realistic example:

```json
// GET /api/users/123
// Mock Response (Success)
{
  "id": "user_123",
  "name": "Jane Smith",
  "email": "jane@example.com",
  "createdAt": "2024-01-15T10:30:00Z"
}

// Mock Response (Error)
{
  "error": {
    "code": "USER_001",
    "message": "User not found"
  }
}
```

### Step 5: Verify Completeness

Check:
- [ ] Every public endpoint is in contract
- [ ] Every endpoint has request/response types
- [ ] Every endpoint has error codes listed
- [ ] Every endpoint has mock data example
- [ ] No new information was invented

### Step 6: Gap Check

List any gaps found:
- Missing endpoints needed by frontend
- Missing error codes
- Unclear behaviours

**Do NOT fill gaps. Document and escalate.**

---

## RULES

### DO:
- Copy exactly from source documents
- Include mock data for every endpoint
- Note all error codes per endpoint
- Flag gaps explicitly

### DO NOT:
- Add new endpoints
- Add new parameters
- Add new error codes
- Infer behaviour not documented
- "Fix" inconsistencies (escalate instead)

---

## OUTPUT

Create: `frontend/FRONTEND-BACKEND-CONTRACT.md`

Use template: `FRONTEND-BACKEND-CONTRACT-TEMPLATE.md`

---

## APPROVAL

Present contract to user.

**If user says APPROVE:**
- Mark Phase 37 complete
- Proceed to Phase 38

**If user says REVISE:**
- Make requested changes
- Re-present for approval

**If gaps require rollback:**
- Document in FRONTEND-DECISION-NOTES.md
- Return to 0c/0d to fix source
- Resume Phase 37 after fix

---
Generated: {{timestamp}}
Phase: 37 (Frontend-Backend Contract)
---
