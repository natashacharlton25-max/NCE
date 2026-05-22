# 0e → 0f Handoff Contract

---
Status: TEMPLATE
Version: v1.0.0
Purpose: Binding contract between Frontend Specs (0e) and Implementation (0f)
---

## What 0e Delivers

By the end of 0e Frontend, these artifacts exist and are APPROVED:

| Artifact | Contains | Status |
|----------|----------|--------|
| FRONTEND-BACKEND-CONTRACT.md | API endpoints, types, errors, mock data | ☐ APPROVED |
| DESIGN-SYSTEM.md | Tokens, components, patterns, performance constraints | ☐ APPROVED |
| PAGES.md | Routes, guards, data requirements, states | ☐ APPROVED |
| COMPONENTS.md | Props, events, states, accessibility, utilities | ☐ APPROVED |
| STATE-MANAGEMENT.md | State categories, ownership, flow, anti-patterns | ☐ APPROVED |
| API-MAPPING.md | Component → endpoint mapping, coverage verification | ☐ APPROVED |

**Mandatory Artifacts within STATE-MANAGEMENT.md:**
- State Ownership Table (required)
- State Flow Diagram (required)
| FRONTEND-CHECKLIST.md | All verifications passed | ☐ APPROVED |

---

## What 0e Does NOT Deliver

These are explicitly deferred to 0f Implementation:

| Item | Why Deferred |
|------|--------------|
| Actual CSS/styling code | Implementation detail |
| Framework boilerplate | Implementation detail |
| Build configuration | Tooling setup |
| Test code | 0g Testing |
| Deployment configuration | 0h Prerelease |
| Performance optimization code | Implementation detail |

---

## What 0f Must NOT Change

These are LOCKED from 0e:

| Locked Item | May Not Change | Escalation If Needed |
|-------------|----------------|----------------------|
| Component names | No renaming | Return to Phase 40 |
| Prop signatures | No adding required props | Return to Phase 40 |
| API endpoint mapping | No new endpoints | Return to Phase 37 |
| Route structure | No new routes | Return to Phase 39 |
| State ownership | No moving state between categories | Return to Phase 41 |

**Performance Constraints Authority:** Performance targets in DESIGN-SYSTEM.md (bundle boundaries, hydration strategy, critical rendering path, Core Web Vitals targets) are considered **locked behaviour**. Implementation may vary in approach, but must achieve the specified targets.

**If 0f discovers a need to change these:** STOP. Document. Escalate to 0e. Do not "fix forward."

---

## What 0f MAY Add

Within the boundaries of 0e specs:

| Allowed Addition | Example | Constraint |
|------------------|---------|------------|
| Implementation details | CSS classes, hooks | Must match spec behavior |
| Utility functions | formatDate, debounce | Document in COMPONENTS.md utilities |
| Internal component state | Hover states, animation states | Must not change external API |
| Error boundary wrappers | React error boundaries | Must match ERROR patterns |
| Performance optimizations | Memoization, lazy loading | Must not change behavior |

---

## Handoff Checklist

Before starting 0f Implementation, verify:

### Documents Ready

- [ ] All 0e documents downloaded/accessible
- [ ] FRONTEND-CHECKLIST.md shows all APPROVED
- [ ] No unresolved escalations in FRONTEND-DECISION-NOTES.md
- [ ] API-MAPPING.md shows no missing endpoints
- [ ] All frontend data calls have corresponding contract endpoints

### Environment Ready

- [ ] TECH-STACK.md choices are installable
- [ ] REPO-STRUCTURE.md locations exist
- [ ] ENVIRONMENT.md variables are documented

### Team Ready

- [ ] Implementer has read all 0e documents
- [ ] Implementer understands "Different AI" test implications (i.e., all context must be in documents — no assumed knowledge from conversations, meetings, or tribal knowledge)
- [ ] Escalation path is clear (who to ask when stuck)

---

## Implementation Order (Recommended)

1. **Setup** — Create folder structure from REPO-STRUCTURE.md
2. **Design System** — Implement tokens and base components
3. **Layout Components** — Header, Sidebar, Page layouts
4. **Atomic Components** — Button, Input, Badge, etc.
5. **Composite Components** — Card, Modal, Forms
6. **State Setup** — Configure state management per STATE-MANAGEMENT.md
7. **Pages** — Implement pages using components
8. **API Integration** — Wire up data fetching
9. **Route Guards** — Implement auth checks
10. **Polish** — Loading states, error handling, accessibility

---

## Success Criteria

0f Frontend Implementation is complete when:

- [ ] Every component in COMPONENTS.md is implemented
- [ ] Every page in PAGES.md is implemented
- [ ] Every route guard works as specified
- [ ] Every API call maps to FRONTEND-BACKEND-CONTRACT.md
- [ ] State management matches STATE-MANAGEMENT.md
- [ ] Performance constraints from DESIGN-SYSTEM.md are respected
- [ ] Accessibility requirements are met
- [ ] No deviations from spec without documented approval

---

## When Things Go Wrong

| Situation | Action |
|-----------|--------|
| Spec is ambiguous | Ask for clarification, document answer |
| Spec seems wrong | Do NOT fix. File DEVIATION-REPORT |
| Missing information | Check FRONTEND-CHECKLIST.md "Missing Items" first |
| Need new component | File DEVIATION-REPORT |
| Performance issue | Implement within spec first, then optimize |
| Accessibility gap | Flag, implement best practice, document |

**For any deviation from spec:** Use `DEVIATION-REPORT-TEMPLATE.md`

---

## Deviation Process

1. **Discover** — Implementer finds issue with spec
2. **Document** — Fill out DEVIATION-REPORT-TEMPLATE.md
3. **Submit** — Send to 0e Owner
4. **Review** — 0e Owner assesses impact
5. **Decide** — Approve, reject, or escalate
6. **Update** — If approved, update spec documents
7. **Continue** — Resume implementation

**Do NOT implement deviations without approval.**

---

## Sign-Off

| Role | Name | Date | Signature |
|------|------|------|-----------|
| 0e Owner | | | ☐ Specs Complete |
| 0f Implementer | | | ☐ Ready to Begin |

---
Generated: {{timestamp}}
Handoff: 0e Frontend → 0f Implementation
---
