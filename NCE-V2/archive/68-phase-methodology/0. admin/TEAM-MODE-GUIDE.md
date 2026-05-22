# Team Mode Guide

---
**Purpose:** Adapt Fullspecs for teams (2+ humans)
**Default:** Fullspecs assumes single human operator
**This guide:** Extends methodology for collaborative use
---

## Core Principle: Single Source of Truth

No matter how many people work on a project:
- **One decision owner** per component/phase
- **One canonical version** of each document
- **One approval** required to proceed

Teams don't vote. Teams have owners who decide.

---

## Team Roles

### Decision Owner (Required)
- One person, typically project lead/founder
- Final authority on all decisions
- Signs off on phase completion
- Resolves all disputes
- Can delegate but retains accountability

### Contributors (Optional)
- Can draft outputs
- Can suggest changes
- Can run their own Claude conversations
- Cannot approve phase completion
- Cannot override Decision Owner

### Reviewers (Optional)
- Review outputs before approval
- Flag concerns
- Cannot block progress (only Decision Owner can)
- Provide advisory input only

---

## Workflow: Who Does What

### Phase Execution

| Task | Who | Notes |
|------|-----|-------|
| Run Claude prompts | Contributor or Owner | Anyone can draft |
| Create outputs | Contributor or Owner | First draft |
| Review outputs | Reviewer or Owner | Advisory feedback |
| Request changes | Anyone | Via comments/notes |
| Approve output | **Decision Owner only** | Gate to next phase |
| Update PROGRESS | Decision Owner | Official record |

### Decision Points

| Decision Type | Authority |
|---------------|-----------|
| Phase complete | Decision Owner |
| Rollback required | Decision Owner |
| Audit finding accepted/rejected | Decision Owner |
| Risk acceptance | Decision Owner (must sign) |
| Scope changes | Decision Owner |
| Proceed despite concerns | Decision Owner |

---

## Preventing Fork Hell

### The Problem
Multiple people running Claude = multiple versions of truth.

**Bad pattern:**
- Alice runs Phase 21 for ComponentA
- Bob runs Phase 21 for ComponentA differently
- Now there are two conflicting specs
- Nobody knows which is canonical

### The Solution: Claim Before Work

Before starting any phase on any component:

1. **Check the PROGRESS file** — Is someone already working on it?
2. **Claim it** — Add your name and timestamp
3. **Complete it** — Do the work
4. **Submit for approval** — Decision Owner reviews
5. **Release claim** — On approval or rejection

### Claim Format (add to PROGRESS file)

```markdown
## Active Claims

| Component | Phase | Claimed By | Since | Status |
|-----------|-------|------------|-------|--------|
| UserSystem | 21 | Alice | 2025-01-15 | In Progress |
| PaymentSystem | 21 | Bob | 2025-01-15 | Awaiting Review |
```

### Rule: One Claim Per Component-Phase
No two people work on the same component-phase simultaneously.

---

## Communication Protocol

### Async-First
Fullspecs is document-heavy by design. Use this:

| Need | Method |
|------|--------|
| Propose change | Comment in DECISION-NOTES |
| Report blocker | Update PROGRESS file |
| Request review | Note in PROGRESS, notify owner |
| Disagree with decision | Document in DECISION-NOTES, owner decides |

### Sync Meetings (If Needed)
Keep them short and decision-focused:

- **Phase kickoff:** 15 min to align on approach
- **Review meeting:** 30 min to walk through outputs
- **Dispute resolution:** Decision Owner + parties, time-boxed

### What Not to Do
- Don't discuss spec details in Slack/chat (it gets lost)
- Don't make decisions verbally without documenting
- Don't assume others read your Claude conversation

---

## Claude Context Across Team Members

### The Problem
Each person has their own Claude conversation. Context doesn't transfer.

### Solutions

**Option A: Shared Context Document**
Maintain a `CLAUDE-CONTEXT.md` that anyone starting work must paste:
```markdown
# Claude Context for [Project]

## Current State
- Completed through: Phase X
- Active work: ComponentY Phase Z

## Key Decisions (Paste These)
[Paste most important decisions from DECISION-NOTES]

## Active Specs (Reference)
[List of approved spec files Claude should know about]
```

**Option B: Section Handoff**
When person A hands off to person B:
1. A exports relevant Claude conversation
2. A writes handoff summary
3. B starts fresh conversation with summary + Claude-*.md

**Option C: Primary Operator Model**
One person runs all Claude interactions; others contribute via review only.

### Recommendation
For teams < 5: Use Primary Operator Model
For teams 5+: Use Shared Context Document

---

## Handling Disagreements

### Disagreement Types

| Type | Resolution |
|------|------------|
| Technical approach | Decision Owner decides |
| Spec interpretation | Decision Owner decides |
| Priority/scope | Decision Owner decides |
| Claude vs team member | Document both views, Owner decides |
| External audit vs team | Owner decides, uses RISK-ACCEPTANCE |

### Escalation Path

```
Contributor concern
    ↓
Discuss in DECISION-NOTES
    ↓
Decision Owner rules
    ↓
Document decision with rationale
    ↓
Move on (no re-litigation)
```

### Rule: Decide and Document, Don't Debate Endlessly
Once Decision Owner rules, it's final unless new information emerges.

---

## File Locking (Optional)

For teams with shared file systems:

### Soft Locks
Use naming convention:
- `STORAGE.md` — Available
- `STORAGE.md.locked-alice-20250115` — Alice is editing

### Hard Locks
Use git branches:
- Main branch = approved outputs only
- Feature branches = work in progress
- PRs = request for approval
- Merge = Decision Owner approval

---

## Parallel Work Patterns

### Safe Parallelism
Different people CAN work simultaneously on:
- Different components in same phase
- Different phases (if dependencies allow)
- Reviews while new work proceeds

### Unsafe Parallelism
Different people should NOT work simultaneously on:
- Same component, same phase
- Phases with dependencies (e.g., Phase 25 needs all Phase 21 outputs)
- Post-spec analysis while specs still changing

### Sync Points
Everyone must wait at:
- End of section (before handoff)
- Mandatory audit points (0c, 0f, 0h)
- Rollback decisions

---

## Recommended Team Structures

### 2-Person Team
- **Person A:** Decision Owner + Backend
- **Person B:** Contributor + Frontend
- Both review each other's work
- A approves everything

### 3-5 Person Team
- **Lead:** Decision Owner, runs 0a-0b, reviews all
- **Backend Dev:** Components, specs, implementation
- **Frontend Dev:** UI specs, frontend implementation
- **QA (optional):** Testing phases, audit coordination

### 5+ Person Team
- Add Component Owners (delegated decision authority per component)
- Decision Owner becomes "Project Owner" (scope/architecture only)
- Component Owners approve their component phases
- Project Owner approves section completion

---

## Checklist: Team Setup

Before starting with a team:

☐ Designate Decision Owner
☐ Define roles (Contributor/Reviewer)
☐ Choose context sharing method
☐ Set up file storage (shared folder/repo)
☐ Create CLAUDE-CONTEXT.md template
☐ Add "Active Claims" section to PROGRESS template
☐ Agree on communication protocol
☐ Brief team on this guide

---

## Quick Reference

| Question | Answer |
|----------|--------|
| Who approves phases? | Decision Owner only |
| Can two people work on same thing? | No, claim first |
| Where do disagreements go? | DECISION-NOTES → Owner decides |
| How does Claude context transfer? | It doesn't. Use handoff docs |
| Can we skip the single-owner rule? | No. Committees kill projects |

---

*One throat to choke, one hand to shake. Clarity over democracy.*
