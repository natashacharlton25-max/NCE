# Project Resumption Checklist

---
**Purpose:** Safely resume a project after a pause
**Use when:** Project has been inactive for 2+ weeks, or Claude context has been lost
---

## Why This Matters

When projects pause:
- Claude loses context (new conversation = blank slate)
- Specs may have drifted from your memory
- External dependencies may have changed
- Decisions made weeks ago may no longer make sense

This checklist prevents "zombie specs" — documentation that no longer reflects reality.

---

## Quick Assessment

### How Long Was the Pause?

| Pause Duration | Risk Level | Required Actions |
|---------------|------------|------------------|
| < 2 weeks | Low | Sections 1-2 only |
| 2-4 weeks | Medium | Sections 1-4 |
| 1-3 months | High | Sections 1-5 |
| 3+ months | Critical | Full review (all sections) |

---

## Section 1: Context Recovery (Always Do)

### 1.1 Find Your Place

☐ Open your project's `0. Admin/` folder
☐ Find the PROGRESS file for your current section
☐ Identify: Last completed phase, current phase, any blockers noted

**I am at:** Section ____ Phase ____

### 1.2 Re-Read Key Documents

☐ Read the relevant `Claude-*.md` file for your current section
☐ Read the last 2-3 outputs you created
☐ Read any DECISION-NOTES entries from your last session

### 1.3 Start Fresh Claude Conversation

☐ Start a new Claude conversation
☐ Paste the `Claude-*.md` file for your section
☐ Give Claude a summary: "I'm resuming this project at Phase X. Here's where we left off: [brief summary]"
☐ Paste the most recent relevant outputs for context

---

## Section 2: Drift Detection (Always Do)

### 2.1 Check Your Assumptions

Ask yourself:
☐ Has my understanding of the project changed?
☐ Have requirements changed (even informally)?
☐ Have I learned something that affects earlier decisions?

If YES to any → Document in DECISION-NOTES, consider rollback

### 2.2 Check External Dependencies

☐ Have any external APIs you're integrating changed?
☐ Have any libraries you planned to use been updated/deprecated?
☐ Have any third-party services changed pricing/terms?

If YES → Update relevant specs before continuing

---

## Section 3: Integrity Check (2+ Week Pause)

### 3.1 Spec Consistency Review

For each component you've speced:
☐ Does FUNCTIONS.md still make sense?
☐ Does STORAGE.md still match your data model understanding?
☐ Are there any "TODO" or "TBD" markers you forgot about?

### 3.2 Cross-Reference Check

☐ Do contracts between components still align?
☐ Does DEPENDENCY-MAP.md still reflect reality?
☐ Are there any orphaned specs (components you've mentally removed)?

---

## Section 4: Re-Audit Consideration (1+ Month Pause)

### 4.1 Determine If Re-Audit Needed

**Re-audit is MANDATORY if:**
- You made spec changes during this resumption
- You're past a mandatory audit point (0c/0f/0h) and haven't audited
- External dependencies changed significantly

**Re-audit is RECOMMENDED if:**
- Pause exceeded 2 months
- You feel uncertain about spec quality
- Project scope shifted during pause

### 4.2 Perform Re-Audit

If re-auditing:
☐ Use the audit files for your current section
☐ Focus auditor on "drift and consistency" concerns
☐ Document any findings in RISK-ACCEPTANCE if proceeding anyway

---

## Section 5: Full Review (3+ Month Pause)

### 5.1 Start-to-Current Walkthrough

Walk through each completed section:

| Section | Review Focus | ☐ |
|---------|--------------|---|
| 0a | Are systems/subsystems still correct? | ☐ |
| 0b | Are contracts and dependencies still valid? | ☐ |
| 0c | Do specs still represent what you want to build? | ☐ |
| 0d | Is tech stack still appropriate? | ☐ |
| 0e | Do UI specs match current vision? | ☐ |
| 0f | Does code match specs? | ☐ |
| 0g | Are tests still relevant? | ☐ |
| 0h | Is deployment config still valid? | ☐ |

### 5.2 Consider Scope Reset

For very long pauses, ask:
☐ Should I restart from 0a with fresh perspective?
☐ Is this project still worth completing?
☐ Should I descope to ship something smaller?

**There's no shame in restarting.** A fresh start with updated understanding often beats fixing stale work.

---

## Section 6: Resume Protocol

Once checks are complete:

### 6.1 Update Documentation

☐ Add a "Project Resumed" entry to DECISION-NOTES:
```markdown
## Project Resumed: [Date]

**Pause duration:** X weeks/months
**Checks completed:** [list sections from this checklist]
**Changes made:** [list any updates]
**Re-audit performed:** Yes/No
**Resuming at:** Section X, Phase Y
```

### 6.2 Set Realistic Expectations

☐ Block dedicated time for project (avoid stop/start)
☐ If another pause is likely, document stopping point thoroughly
☐ Consider smaller milestones to maintain momentum

### 6.3 Begin Work

☐ Open the prompt for your current phase
☐ Resume normal workflow
☐ Proceed more carefully for first few outputs until Claude has context

---

## Quick Reference: Minimum Actions by Pause Length

| Pause | Minimum Actions |
|-------|-----------------|
| < 2 weeks | Re-read last outputs, restart Claude with context |
| 2-4 weeks | Above + drift check + assumption review |
| 1-3 months | Above + integrity check + consider re-audit |
| 3+ months | Full review, seriously consider restart |

---

## Warning Signs of Zombie Specs

If you notice any of these, consider deeper review:

- Specs describe features you no longer want
- You keep saying "that's not quite right" when reading old work
- Claude's outputs based on old specs feel "off"
- You're mentally planning features not in any spec
- External auditor finds major gaps you didn't notice

---

*Resuming well is harder than starting fresh. Take the time to do it right.*
