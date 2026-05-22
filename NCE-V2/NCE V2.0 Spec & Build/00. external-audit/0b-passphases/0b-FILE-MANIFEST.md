# 0b PassPhases 0-4 File Manifest

---
Section: 0b PassPhases 0-4 (Phases 13-19)
Purpose: Complete list of files available for audit review
---

## Dependency Bundles

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **Pass Progress** | "Pass progress bundle" | PASS-PROGRESS.md, PASS-DECISION-NOTES.md |
| **Contracts** | "Contracts bundle" | All CONTRACT.md files |
| **Dependencies** | "Dependencies bundle" | DEPENDENCY-MAP.md, build order |
| **Component {{name}}** | "{{name}} bundle" | SYSTEM.md, ADMIN.md, CONTRACT.md, PASS-NOTES.md |
| **Gate** | "Gate bundle" | POST-PASS-CHECKLIST.md |

---

## Pass 0: Coverage (Phase 13)

| File | Location | Purpose |
|------|----------|---------|
| PASS-0-COVERAGE.md | /13. Pass 0/ | Coverage review results |

---

## Pass 1: Grounding (Phase 14)

Per component:
| File | Location | Purpose |
|------|----------|---------|
| SYSTEM.md | /{{component}}/ | Component definition |
| ADMIN.md | /{{component}}/ | Administrative info |

---

## Pass 2: Contracts (Phase 15)

Per component:
| File | Location | Purpose |
|------|----------|---------|
| CONTRACT.md | /{{component}}/ | Interface definition |

**Contracts in this project:**
| Component | Contract With |
|-----------|---------------|
| {{name}} | {{list}} |

---

## Pass 3: Dependencies (Phase 16)

| File | Location | Purpose |
|------|----------|---------|
| DEPENDENCY-MAP.md | /16. Pass 3/ | Dependency graph |
| BUILD-ORDER.md | /16. Pass 3/ | Implementation order |

---

## Pass 4: Implementation Planning (Phase 17)

Per component:
| File | Location | Purpose |
|------|----------|---------|
| IMPLEMENTATION-PLAN.md | /{{component}}/ | Files, LOC, tasks |

---

## Post-Pass (Phase 18-19)

| File | Location | Purpose |
|------|----------|---------|
| POST-PASS-CHECKLIST.md | /18. Post-Pass Checklist/ | Final verification |
| PASS-PROGRESS.md | /0. Admin/0b. PassPhases 0-4/ | Progress tracking |
| PASS-DECISION-NOTES.md | /0. Admin/0b. PassPhases 0-4/ | Decision log |

Per component:
| File | Location | Purpose |
|------|----------|---------|
| PASS-NOTES.md | /{{component}}/ | Context for specs |

---

## Priority Files for Audit

1. **DEPENDENCY-MAP.md** - Architecture overview
2. **POST-PASS-CHECKLIST.md** - Completion status
3. **PASS-DECISION-NOTES.md** - Key decisions
4. **{{core component}}/CONTRACT.md** - Critical interface
5. **BUILD-ORDER.md** - Implementation sequence

---

## File Count Summary

| Category | Count |
|----------|-------|
| Pass 0 files | 1 |
| Pass 1 files | {{components}} × 2 |
| Pass 2 files | {{components}} × 1 |
| Pass 3 files | 2 |
| Pass 4 files | {{components}} × 1 |
| Post-pass files | 2 + {{components}} |
| Admin files | 2 |
| **TOTAL** | **~{{total}}** |

---

*End of File Manifest*
