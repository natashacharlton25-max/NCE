# 0e Frontend Audit Checklist

---
Section: 0e Frontend (Phases 37-42)
Purpose: Verification checklist for external auditor
---

## Phase 37: Frontend Design Input

- [ ] DESIGN-INPUT.md exists
- [ ] Mockups/wireframes referenced
- [ ] Design tokens defined
- [ ] UI patterns documented

---

## Phase 38: Page Specs

### PAGES.md
- [ ] File exists
- [ ] All pages listed
- [ ] Routes defined

### Per-Page Specs
For each page:
- [ ] PAGE-{{name}}.md exists
- [ ] Purpose defined
- [ ] Components listed
- [ ] Data requirements identified
- [ ] User interactions documented

---

## Phase 39: Component Specs

### COMPONENTS.md
- [ ] File exists
- [ ] All components listed
- [ ] Component hierarchy clear

### Per-Component Specs
For each component:
- [ ] COMPONENT-{{name}}.md exists
- [ ] Props defined with types
- [ ] Events documented
- [ ] States identified
- [ ] Accessibility attributes noted

---

## Phase 40: State Management

- [ ] STATE-MANAGEMENT.md exists
- [ ] State approach defined (Redux, Zustand, Context, etc.)
- [ ] Global state identified
- [ ] Local state identified
- [ ] State shape documented
- [ ] Actions/mutations defined

---

## Phase 41: API Integration Mapping

### API-MAPPING.md
- [ ] File exists
- [ ] All API endpoints mapped
- [ ] Component → Endpoint mapping clear
- [ ] Request triggers documented
- [ ] Response handling defined
- [ ] Error handling specified

### Loading States
- [ ] Loading states defined for API calls
- [ ] Skeleton/spinner patterns identified

### Error States
- [ ] Error states defined
- [ ] Error display approach clear
- [ ] Retry logic specified (if applicable)

---

## Phase 42: Frontend Ready Check

### FRONTEND-BACKEND-CONTRACT.md
- [ ] File exists
- [ ] All API endpoints from API-SURFACE.md included
- [ ] Request types match
- [ ] Response types match
- [ ] Error codes included
- [ ] Authentication requirements noted

### FRONTEND-CHECKLIST.md
- [ ] File exists
- [ ] All items checked
- [ ] Ready for implementation

### 0e-to-0f-HANDOFF.md
- [ ] File exists
- [ ] All deliverables listed
- [ ] Implementation priorities noted

---

## Accessibility (A11Y)

### WCAG 2.1 Level A
- [ ] Text alternatives for images
- [ ] Keyboard accessible
- [ ] No keyboard traps
- [ ] Focus order logical
- [ ] Link purpose clear

### WCAG 2.1 Level AA
- [ ] Color contrast sufficient (4.5:1)
- [ ] Text resizable to 200%
- [ ] Focus visible
- [ ] Error identification
- [ ] Labels for inputs

### A11Y-CHECKLIST.md
- [ ] File exists
- [ ] Requirements documented
- [ ] Testing approach noted

---

## Consistency Checks

### With API-SURFACE.md (0d)
- [ ] All endpoints in contract exist in API-SURFACE.md
- [ ] No endpoints used that aren't documented
- [ ] Types match

### With TYPES.md (0c)
- [ ] Frontend types match backend types
- [ ] No type mismatches

### With ERROR-CODES.md (0c)
- [ ] Error codes handled match ERROR-CODES.md
- [ ] Error messages appropriate

---

## Red Flags (Automatic Critical)

- [ ] API endpoint used but not in contract
- [ ] Type mismatch frontend ↔ backend
- [ ] WCAG Level A failure
- [ ] No error handling for API calls

---

## Consistency Delta (vs Section 0d)

- [ ] Contract matches API-SURFACE.md
- [ ] Types match consolidated types
- [ ] No missing endpoints

---

*End of 0e Checklist*
