# External Audit Prompt: Section 0d - PreCode

---
Section: 0d PreCode (Phases 29-36)
Audit Type: OPTIONAL (Light Audit)
Priority: Low-Medium
Estimated Time: 30 minutes
---

## Your Role

You are an independent auditor reviewing code preparation documents.

**Your job is to verify** that specs have been correctly consolidated into implementation-ready artifacts.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0d. This section consolidates specifications into unified artifacts.

**0d produces:**
- DATABASE-SCHEMA.md (from all STORAGE.md specs)
- API-SURFACE.md (from all FUNCTIONS.md specs)
- LIBRARY-STRUCTURE.md
- REPO-STRUCTURE.md
- TECH-STACK.md
- ENVIRONMENT.md
- CODING-STANDARDS.md

**Why 0d matters:**
- Consolidation catches spec conflicts
- Schema must support all component needs
- API surface is the contract for frontend
- Wrong tech stack = expensive rewrite

**Key Rule:** If conflicts are found, 0d must roll back to 0c (no "fix forward").

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package
2. Check for consolidation issues
3. Request **up to 5 files**

**Priority Files:**
- DATABASE-SCHEMA.md
- API-SURFACE.md
- PRECODE-CHECKLIST.md
- TECH-STACK.md

### Round 2: Follow-up

Request **up to 3 additional files** if needed.

---

## What to Look For

### Critical Issues (🔴)

- [ ] Schema doesn't support a component's STORAGE spec
- [ ] API endpoint missing from a component's FUNCTIONS spec
- [ ] Unresolved conflict (should have rolled back to 0c)
- [ ] Tech stack incompatible with requirements

### Important Issues (🟠)

- [ ] Schema could be more normalized
- [ ] API naming inconsistent
- [ ] Environment config incomplete
- [ ] Coding standards unclear

### Minor Issues (🟡)

- [ ] Documentation could be clearer
- [ ] Minor naming inconsistencies

---

## Specific Checks for 0d

### Database Schema (Phase 29)
- Does schema support ALL STORAGE.md requirements?
- Are relationships correct?
- Are indexes appropriate?
- Is migration strategy sound?

### API Surface (Phase 30)
- Does API include ALL FUNCTIONS.md endpoints?
- Are request/response types consistent?
- Do error codes match ERROR-CODES.md?
- Is API RESTful/consistent?

### Tech Stack (Phase 33)
- Is stack appropriate for requirements?
- Are all dependencies identified?
- Version compatibility considered?

### Conflict Resolution
- Were any conflicts found?
- If yes, how were they resolved?
- Did resolution require 0c rollback?

---

## 0d CHECKLIST

```
## Consolidation
- [ ] DATABASE-SCHEMA.md complete
- [ ] API-SURFACE.md complete
- [ ] No unresolved conflicts

## Schema Verification
- [ ] All STORAGE.md specs covered
- [ ] Relationships correct
- [ ] Indexes defined

## API Verification
- [ ] All FUNCTIONS.md specs covered
- [ ] Types consistent
- [ ] Error codes match

## Infrastructure
- [ ] REPO-STRUCTURE.md complete
- [ ] TECH-STACK.md complete
- [ ] ENVIRONMENT.md complete
- [ ] CODING-STANDARDS.md complete

## Gate
- [ ] PRECODE-CHECKLIST.md complete
- [ ] Ready for 0e/0f
```

---

## HANDOFF PACKAGE

[Human: Paste Claude's 0d-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
