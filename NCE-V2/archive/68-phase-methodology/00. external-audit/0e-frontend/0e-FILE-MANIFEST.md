# 0e Frontend File Manifest

---
Section: 0e Frontend (Phases 37-42)
Purpose: Complete list of files available for audit review
---

## Dependency Bundles

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **Contract** | "Contract bundle" | FRONTEND-BACKEND-CONTRACT.md |
| **Pages** | "Pages bundle" | PAGES.md, all PAGE-*.md files |
| **Components** | "Components bundle" | COMPONENTS.md, all COMPONENT-*.md files |
| **State** | "State bundle" | STATE-MANAGEMENT.md |
| **API Mapping** | "API mapping bundle" | API-MAPPING.md |
| **Accessibility** | "Accessibility bundle" | A11Y-CHECKLIST.md |
| **Gate** | "Gate bundle" | FRONTEND-CHECKLIST.md, 0e-to-0f-HANDOFF.md |

---

## Files by Phase

### Phase 37: Frontend Design Input
| File | Location | Purpose |
|------|----------|---------|
| DESIGN-INPUT.md | /37. Frontend Design Input/ | Mockups and design tokens |

### Phase 38: Page Specs
| File | Location | Purpose |
|------|----------|---------|
| PAGES.md | /38. Page Specs/ | Page overview |
| PAGE-{{name}}.md | /38. Page Specs/ | Per-page specification |

**Pages in this project:**
| Page | Route | File |
|------|-------|------|
| {{name}} | {{route}} | PAGE-{{name}}.md |

### Phase 39: Component Specs
| File | Location | Purpose |
|------|----------|---------|
| COMPONENTS.md | /39. Component Specs/ | Component overview |
| COMPONENT-{{name}}.md | /39. Component Specs/ | Per-component specification |

**Components in this project:**
| Component | Used In | File |
|-----------|---------|------|
| {{name}} | {{pages}} | COMPONENT-{{name}}.md |

### Phase 40: State Management
| File | Location | Purpose |
|------|----------|---------|
| STATE-MANAGEMENT.md | /40. State Management/ | State approach |

### Phase 41: API Integration Mapping
| File | Location | Purpose |
|------|----------|---------|
| API-MAPPING.md | /41. API Integration Mapping/ | Frontend-API connections |
| FRONTEND-BACKEND-CONTRACT.md | /41. API Integration Mapping/ | Binding contract |

### Phase 42: Frontend Ready Check
| File | Location | Purpose |
|------|----------|---------|
| A11Y-CHECKLIST.md | /42. Frontend Ready Check/ | Accessibility requirements |
| FRONTEND-CHECKLIST.md | /42. Frontend Ready Check/ | Final verification |
| 0e-to-0f-HANDOFF.md | /42. Frontend Ready Check/ | Handoff to implementation |

---

## Source Files (from previous sections)

| File | Section | Purpose |
|------|---------|---------|
| API-SURFACE.md | 0d | API contract source |
| TYPES.md | 0c | Type definitions |
| ERROR-CODES.md | 0c | Error codes |

---

## Priority Files for Audit

1. **FRONTEND-BACKEND-CONTRACT.md** - API binding
2. **API-MAPPING.md** - Frontend-API connection
3. **PAGES.md** - Page overview
4. **STATE-MANAGEMENT.md** - State approach
5. **A11Y-CHECKLIST.md** - Accessibility
6. **FRONTEND-CHECKLIST.md** - Gate status

---

## File Count Summary

| Category | Count |
|----------|-------|
| Design input | 1 |
| Page specs | 1 + {{n}} pages |
| Component specs | 1 + {{n}} components |
| State management | 1 |
| API mapping | 2 |
| Accessibility | 1 |
| Gate files | 2 |
| **TOTAL** | **~10-30** |

---

*End of File Manifest*
