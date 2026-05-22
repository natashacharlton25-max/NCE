# PRE-PASS 0 CHECKLIST — PROMPT

## ROLE
You are verifying that all build phases are complete before starting Pass 0 coverage reviews.

You are NOT designing, building, or fixing anything — only checking and reporting.

---

## TASK

Review the project folder structure and verify:
1. All foundation docs exist and are approved
2. All systems are built and approved
3. All subsystems are built and approved (or confirmed not needed)
4. All external integration providers are built and approved (or confirmed none needed)
5. All services are built and approved (or confirmed none needed)

**Output a completed Pre-Pass 0 Checklist.**

---

## WHY THIS CHECK EXISTS

Pass 0 reviews coverage of systems, subsystems, and integrations. If any are missing or unapproved, Pass 0 will be incomplete or incorrect.

This checklist:
- Catches missing docs before we start
- Confirms everything is approved
- Provides a clear inventory of what exists
- Identifies blockers

---

## WHAT TO CHECK

### Foundation (in `/admin/` or project root)
- [ ] PROJECT-OVERVIEW.md — exists, APPROVED
- [ ] PROJECT-INTENTION.md — exists, APPROVED
- [ ] SYSTEMS-CLARIFICATION.md — exists, APPROVED
- [ ] SYSTEMS-PREBUILD-CHECK.md — exists, ALL APPROVED
- [ ] EXTERNAL-INTEGRATIONS-REGISTER.md — exists, APPROVED (with providers specified)

### For Each System
- [ ] Folder exists: `{{project}}/{{system-name}}/`
- [ ] SYSTEM-NOTES.md exists inside folder
- [ ] Status is APPROVED

### For Each System with Subsystems
- [ ] SUBSYSTEMS-CLARIFICATION.md exists (in system folder or admin)
- [ ] SUBSYSTEM-PREBUILD-CHECK.md exists
- [ ] Each subsystem folder exists
- [ ] Each SUBSYSTEM-NOTES.md exists and is APPROVED

### For Each Provider (if any external integrations)
- [ ] Folder exists: `{{project}}/integration-{{provider}}/`
- [ ] PROVIDER-NOTES.md exists and is APPROVED
- [ ] SERVICE-SCOPE.md exists and is APPROVED

### For Each Service
- [ ] Folder exists: `{{project}}/integration-{{provider}}/{{service}}/`
- [ ] SERVICE-NOTES.md exists and is APPROVED

---

## RULES

1. **Check everything** — don't assume, verify
2. **Report what's missing** — be specific
3. **Report what's unapproved** — status must be APPROVED
4. **Don't fix anything** — only report
5. **Be thorough** — check every folder, every doc

---

## PROCESS

1. List all expected systems from SYSTEMS-CLARIFICATION.md
2. Check each system folder and SYSTEM-NOTES.md
3. For each system, check if subsystems exist
4. Check each subsystem folder and SUBSYSTEM-NOTES.md
5. Check EXTERNAL-INTEGRATIONS-REGISTER.md for providers
6. Check each provider folder and PROVIDER-NOTES.md
7. Check each service folder and SERVICE-NOTES.md
8. Compile results into the checklist
9. Determine verdict: READY or NOT READY

---

## OUTPUT FORMAT

Return a completed Pre-Pass 0 Checklist using the template.

**If READY FOR PASS 0:**
```
✅ Pre-Pass 0 Checklist: READY

All build phases are complete and approved.
- {{n}} systems
- {{n}} subsystems
- {{n}} providers
- {{n}} services

Reply APPROVE to proceed to Pass 0 (Coverage Reviews).
```

**If NOT READY:**
```
❌ Pre-Pass 0 Checklist: NOT READY

The following items are missing or unapproved:

Missing:
- {{item}}
- {{item}}

Unapproved:
- {{item}} — Status: {{status}}

Please resolve these issues before proceeding to Pass 0.
```

---

## ON APPROVAL

When the checklist shows READY and user approves:
- Update Status to: **READY FOR PASS 0**
- Update Last Updated to: **{{current_timestamp}}**
- Store checklist in: `{{project}}/admin/PRE-PASS-0-CHECKLIST.md`
- **Create section handoff:** `0a-to-0b-HANDOFF.md` (see below)
- Confirm next step: **Pass 0 (Coverage Reviews)**

---

## SECTION HANDOFF

**Create `0a-to-0b-HANDOFF.md`** in the project admin folder with:

```markdown
# 0a → 0b Section Handoff

---
Created: {{current_timestamp}}
Section Completed: 0a PreBuild (Phases 1-12)
Next Section: 0b Pass Phases (Phases 13-19)
---

## Summary

0a PreBuild is COMPLETE. All foundation documents, systems, subsystems, and external integrations have been built and approved.

## Key Artifacts Created

| Artifact | Location | Status |
|----------|----------|--------|
| PROJECT-OVERVIEW.md | /admin/ | APPROVED |
| PROJECT-INTENTION.md | /admin/ | APPROVED |
| SYSTEMS-CLARIFICATION.md | /admin/ | APPROVED |
| SYSTEMS-PREBUILD-CHECK.md | /admin/ | APPROVED |
| EXTERNAL-INTEGRATIONS-REGISTER.md | /admin/ | APPROVED |
| {{n}} SYSTEM-NOTES.md files | /{{system}}/ | APPROVED |
| {{n}} SUBSYSTEM-NOTES.md files | /{{system}}/{{subsystem}}/ | APPROVED |
| {{n}} PROVIDER-NOTES.md files | /integration-{{provider}}/ | APPROVED |
| {{n}} SERVICE-NOTES.md files | /integration-{{provider}}/{{service}}/ | APPROVED |

## Component Inventory

- Systems: {{list}}
- Subsystems: {{list}}
- External Providers: {{list}}
- External Services: {{list}}

## Known Issues or Deferred Items

{{Any blockers, deferred decisions, or items flagged for 0b attention}}

## Handoff Verification

- [ ] All foundation docs approved
- [ ] All systems approved
- [ ] All subsystems approved (or none needed)
- [ ] All providers approved (or none needed)
- [ ] All services approved (or none needed)
- [ ] PRE-PASS-0-CHECKLIST.md shows READY

**Ready to begin 0b Pass Phases.**
```

---

## INPUTS

Project folder structure:
{{folder_structure}}

Or: Access to browse the project folders

Timestamp:
{{current_timestamp}}