# PHASE 12: PREPASS CHECK (Pre-Pass 0 Readiness)

---
Phase: 12
Name: PrePass Check
Location: NCE-V2/NCE V2.0 Spec & Build/12. PrePass Check/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

You verify that all Phase 1–11 build outputs are complete and approved before Pass 0 (coverage reviews) begins.

You are NOT designing, building, or fixing — only checking and reporting.

---

## LOCKED CONTEXT (Required Reading)

Per [CLAUDE.md](../../../CLAUDE.md) §10:

1. [Project-Intent.md](../../Project-Intent.md)
2. [CLAUDE.md](../../../CLAUDE.md) — status vocabulary
3. [FileTree-v2.md](../../FileTree-v2.md) — to verify count completeness

---

## TASK

Verify the following exist and are APPROVED:

### Foundation (in `NCE-V2/admin/`)
- [ ] `PROJECT-OVERVIEW.md` — exists, APPROVED (Phase 1)
- [ ] `PROJECT-INTENTION-CHECK.md` — exists, APPROVED (Phase 2)
- [ ] `SYSTEMS-CLARIFICATION.md` — exists, APPROVED (Phase 3) — covers all 27 systems
- [ ] `SYSTEMS-PREBUILD-CHECK.md` — exists, ALL 27 APPROVED (Phase 4)
- [ ] `EXTERNAL-INTEGRATIONS-REGISTER.md` — exists, APPROVED (Phase 3)
- [ ] `PASS-PROGRESS.md` — exists with Phases 1–11 marked COMPLETE
- [ ] `PASS-DECISION-NOTES.md` — exists; review for any blocking items

### Per System (in `NCE-V2/specs/<system>/`)
For each of the 27 systems:
- [ ] Folder exists
- [ ] `SYSTEM-NOTES.md` exists, APPROVED (Phase 5)
- [ ] `SUBSYSTEMS-CLARIFICATION.md` exists, APPROVED (Phase 6)
- [ ] `SUBSYSTEMS-PREBUILD-CHECK.md` exists, ALL APPROVED (Phase 7)

### Per Subsystem
For each subsystem in FileTree-v2.md:
- [ ] Folder exists at `NCE-V2/specs/<parent>/<subsystem>/`
- [ ] `SUBSYSTEM-NOTES.md` exists, APPROVED (Phase 8)

### Per Provider (in `NCE-V2/specs/integrations/<provider>/`)
For each provider from `EXTERNAL-INTEGRATIONS-REGISTER.md`:
- [ ] Folder exists
- [ ] `PROVIDER-NOTES.md` exists, APPROVED (Phase 9)
- [ ] `SERVICE-SCOPE.md` exists, APPROVED (Phase 10)

### Per Service
For each service in each `SERVICE-SCOPE.md`:
- [ ] Folder exists at `NCE-V2/specs/integrations/<provider>/<service>/`
- [ ] `SERVICE-NOTES.md` exists, APPROVED (Phase 11)

### NCE-V2-Specific Completeness Checks
- [ ] 27 systems represented (matches FileTree-v2.md count; excludes `lib/`)
- [ ] `library/` system has both SYSTEM-NOTES and the 5 subsystems (Librarian, FileManager, Writer, Archivist, CacheHandler) noted
- [ ] `assets/` system represented (R2-based binary store)
- [ ] Output Form declared for every system + subsystem
- [ ] Library ownership declared for library-owning systems
- [ ] Direction declared for every provider + service

> **Logging rule:** Any missing item logged in `NCE-V2/admin/PASS-DECISION-NOTES.md` with severity.

---

## RULES

1. **Check everything** — don't assume; verify by listing folders / reading files
2. **Report what's missing** — be specific
3. **Report what's unapproved** — status must be APPROVED
4. **Don't fix anything** — only report
5. Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## OUTPUT LOCATION

```
NCE-V2/admin/PREPASS-CHECKLIST.md
```

Plus, on completion, create the section handoff:

```
NCE-V2/admin/0a-to-0b-HANDOFF.md
```

---

## OUTPUT FORMAT

### If READY FOR PASS 0:
```
Pre-Pass 0 Checklist: READY

All Phase 1–11 outputs complete and approved.
- 27 systems built
- {{n}} subsystems built
- {{n}} providers built
- {{n}} services built

Reply APPROVE to proceed to Pass 0 (Phase 13).
```

### If NOT READY:
```
Pre-Pass 0 Checklist: NOT READY

Missing:
- {{item}}

Unapproved:
- {{item}} — Status: {{status}}

Please resolve before proceeding to Pass 0.
```

---

## END CONDITION

- [ ] `PREPASS-CHECKLIST.md` created
- [ ] Verdict: READY or NOT READY clearly stated
- [ ] `0a-to-0b-HANDOFF.md` created (if READY)
- [ ] `PASS-PROGRESS.md` updated: Phase 12 marked COMPLETE
- [ ] Status: Draft Complete – Awaiting Review

**Next (if READY):** Proceed to Phase 13 (Pass 0 Coverage)

---

## TEMPLATES (enriched for NCE-V2)

- [PREPASS-CHECKLIST-TEMPLATE.md](./PREPASS-CHECKLIST-TEMPLATE.md)

---

## INPUTS

- `NCE-V2/admin/` contents (foundation docs)
- `NCE-V2/specs/` contents (per-system + per-provider artefacts)
- `NCE-V2/FileTree-v2.md` (for count verification)

---

## STATUS

**Draft Complete – Awaiting Review**

---

### Review & Clarification Needed
- The NCE-V2-specific completeness checks (Output Form, Library ownership, Direction) are required gates here — confirm or drop?
- May this draft be promoted to "Approved"?
