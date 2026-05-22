# External Audit Prompt: Section 0e - Frontend

---
Section: 0e Frontend (Phases 37-42)
Audit Type: OPTIONAL (Light Audit)
Priority: Medium
Estimated Time: 30 minutes
---

## Your Role

You are an independent auditor reviewing frontend specifications.

**Your job is to verify** that frontend specs correctly map to the backend API contract.

---

## What You're Auditing

An AI assistant (Claude) has completed Section 0e. This section creates frontend specifications from mockups and maps them to the API.

**0e produces:**
- FRONTEND-BACKEND-CONTRACT.md (binding document)
- Page specifications
- Component specifications
- State management approach
- API-to-frontend mapping
- Accessibility checklist

**Why 0e matters:**
- Frontend must align with API contract
- Missing API mappings = broken features
- Accessibility failures = legal/usability issues
- State management affects performance

---

## Your Process

### Round 1: Review & Request Files

1. Read the handoff package
2. Check contract completeness
3. Request **up to 6 files**

**Priority Files:**
- FRONTEND-BACKEND-CONTRACT.md
- API-MAPPING.md
- PAGES.md
- A11Y-CHECKLIST.md
- FRONTEND-CHECKLIST.md

### Round 2: Follow-up

Request **up to 3 additional files** if needed.

---

## What to Look For

### Critical Issues (🔴)

- [ ] API endpoint used but not in contract
- [ ] Type mismatch between frontend and API
- [ ] Critical accessibility missing (WCAG A)
- [ ] Page has no API mapping

### Important Issues (🟠)

- [ ] Error states not handled
- [ ] Loading states not specified
- [ ] Accessibility gaps (WCAG AA)
- [ ] Component props unclear

### Minor Issues (🟡)

- [ ] Documentation could be clearer
- [ ] Component naming inconsistent
- [ ] Minor accessibility improvements

---

## Specific Checks for 0e

### Frontend-Backend Contract
- Does it include all API endpoints from 0d?
- Are request/response types defined?
- Are error codes mapped?
- Is authentication handled?

### Page Specs
- Does each page have clear purpose?
- Are components on each page identified?
- Is routing defined?

### Component Specs
- Are props clearly defined?
- Are events documented?
- Is state identified?

### API Mapping
- Is every API call mapped to a component/page?
- Are all frontend actions that need API covered?
- Is error handling specified?

### Accessibility
- WCAG 2.1 Level A requirements met?
- Level AA requirements identified?
- Keyboard navigation considered?
- Screen reader support considered?

---

## 0e CHECKLIST

```
## Contract
- [ ] FRONTEND-BACKEND-CONTRACT.md exists
- [ ] All API endpoints included
- [ ] Types match API-SURFACE.md
- [ ] Error codes included

## Pages
- [ ] All pages specified
- [ ] Page routes defined
- [ ] Component usage documented

## Components
- [ ] All components specified
- [ ] Props defined
- [ ] Events documented

## State
- [ ] STATE-MANAGEMENT.md exists
- [ ] State approach defined
- [ ] Global vs local state clear

## API Mapping
- [ ] API-MAPPING.md exists
- [ ] All API calls mapped
- [ ] Error handling specified

## Accessibility
- [ ] A11Y-CHECKLIST.md exists
- [ ] WCAG A requirements covered
- [ ] Keyboard navigation addressed

## Gate
- [ ] FRONTEND-CHECKLIST.md complete
- [ ] Ready for implementation
```

---

## HANDOFF PACKAGE

[Human: Paste Claude's 0e-AUDIT-HANDOFF.md here]

---

*End of Audit Prompt*
