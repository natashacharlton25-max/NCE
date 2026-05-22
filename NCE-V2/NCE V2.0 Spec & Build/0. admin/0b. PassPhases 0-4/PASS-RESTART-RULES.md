# Pass Restart Rules

This file defines which pass must be restarted when an issue is discovered.

---

## Restart Matrix

| Issue Discovered | Restart At |
|------------------|------------|
| Problem misunderstood | Restart pipeline (Phase 1) |
| Missing system | Phase 3 (Systems Identification) |
| Missing subsystem | Phase 6 (Subsystem Identification) |
| Missing external integration | Phase 3 (Systems Identification) |
| Conflicting responsibilities | Phase 13a (Pass 0a Coverage) |
| Missing or invalid requirement | Phase 14 (Pass 1 Grounding) |
| Requirement exceeds system scope | System/Subsystem NOTES.md |
| Architecture invalid | Phase 15 (Pass 2 Contracts) |
| Implementation cannot follow architecture | Phase 15 (Pass 2 Contracts) |
| Circular dependency found | Phase 16 (Pass 3 Dependencies) |
| Size limit exceeded (~1,500 LOC) | Phase 6 (Subsystem Identification) |
| Contract violation | Phase 15 (Pass 2 Contracts) |
| Implementation bug | Phase 17 (Pass 4 Implementation Planning) |
| Missing tests | Phase 17 (Pass 4 Implementation Planning) |

---

## Rules

1. **Restart only as far back as needed** — don't restart from scratch if you don't have to
2. **Do not patch later passes to hide earlier mistakes** — fix the root cause
3. **NOTES.md changes require explicit re-approval** — if you change a SYSTEM-NOTES.md, it must be approved again
4. **Log all restarts in PASS-DECISION-NOTES.md** — create an audit trail
5. **Update PASS-PROGRESS.md** — mark the restarted phase as ACTIVE

---

## How to Handle a Restart

1. **Identify the issue** — what went wrong?
2. **Consult the matrix** — where should we restart?
3. **Log the decision** — add entry to PASS-DECISION-NOTES.md
4. **Update progress** — mark current phase as BLOCKED, target phase as ACTIVE
5. **Re-run from restart point** — don't skip steps
6. **Re-approve affected docs** — anything that changed needs approval

---

## Notes

This file exists to prevent:
- Scope drift
- Shortcut fixes that cause bigger problems later
- Losing track of why decisions were made
