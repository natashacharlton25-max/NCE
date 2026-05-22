# Start Here

---
**For:** First-time Fullspecs users
**Time to read:** 5 minutes
**Goal:** Get you building without overwhelm
---

## The Only 3 Things You Need to Read First

| Order | Document | What It Tells You |
|-------|----------|-------------------|
| 1 | `FULLSPECS-HUMAN-GUIDE.md` | What Fullspecs is, how it works, your role |
| 2 | `QUICK-REFERENCE.md` | Cheat sheet for phases, folders, files |
| 3 | `sections/0a-CONCEPT-GUIDE.md` | How to start your first section |

**That's it.** Ignore everything else until you need it.

---

## Your First Session

### Step 1: Set Up Your Project Folder

Create a folder for your project. Copy the Fullspecs methodology folders into it.

### Step 2: Open Claude

Start a conversation with Claude. Give it the contents of:
```
0. Admin/0a. Concept-to-PassPhases/Claude-prebuild.md
```

### Step 3: Start Phase 1

Open `1. Project Overview/PROJECT-OVERVIEW-PROMPT.md` and give it to Claude.

Answer Claude's questions about your project.

### Step 4: Keep Going

Work through phases 1-12 in order. Each phase has:
- A PROMPT file (give to Claude)
- A TEMPLATE file (Claude fills this in)

**You review and approve each output before moving on.**

---

## What to Ignore (For Now)

| Don't Read Yet | Why |
|----------------|-----|
| Section guides for 0b-0i | You're not there yet |
| External Audit docs | Only needed at specific checkpoints |
| MASTER-DOCUMENT-INDEX | Reference doc, not a starting point |
| TEAM-MODE-GUIDE | Only if working with others |

---

## When You Get Stuck

| Problem | Solution |
|---------|----------|
| Claude seems confused | Start a fresh conversation, re-paste Claude-prebuild.md |
| Not sure what phase you're on | Check your PROGRESS file in the Admin folder |
| Output doesn't look right | Compare against the TEMPLATE, ask Claude to revise |
| Need to change something from earlier | See `FAILURE-RECOVERY-RULES.md` |

---

## The 3 Mandatory Checkpoints

As you work through Fullspecs, there are 3 points where you **must** run an external audit:

| After | Section | What It Checks |
|-------|---------|----------------|
| Phase 28 | 0c Full Specs | Your specifications are complete |
| Phase 53 | 0f Implementation | Your code matches specs |
| Phase 64 | 0h Prerelease | You're ready to deploy |

Don't skip these. They catch problems before they become expensive.

---

## Summary

1. Read 3 docs: Human Guide → Quick Reference → 0a Guide
2. Start Phase 1 with Claude
3. Work through phases in order
4. Stop at mandatory audit points
5. Reference other docs only when needed

**You don't need to understand everything upfront. The system guides you.**

---

*Now go read `FULLSPECS-HUMAN-GUIDE.md` and get started.*
