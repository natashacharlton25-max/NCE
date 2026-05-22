# Section 0a: Concept to PassPhase

## Human Guide

---
**What this section does:** Captures your project idea and breaks it into pieces
**Phases:** 1-12
**Time:** A few hours to a day
**Audit:** Optional
---

## What Happens in This Section?

This is where you start. You have an idea for a project, and this section helps you:

1. **Describe your project** - What is it? What should it do?
2. **Identify the systems** - The big chunks of functionality
3. **Break down subsystems** - Smaller pieces within each system
4. **List external services** - Things you'll connect to (payments, email, etc.)

**Think of it like:** Sketching the rooms of a house before building it.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Describe your idea | Tell Claude what you want to build |
| Answer questions | Claude will ask clarifying questions |
| Review the breakdown | Make sure systems/subsystems make sense |
| Approve | Give the go-ahead to continue |

---

## The Phases (What Happens When)

| Phase | Name | What Happens |
|-------|------|--------------|
| 1 | Project Overview | You describe your project, Claude documents it |
| 2 | Project Intention | Define what success looks like |
| 3 | System Identification | Claude identifies the main systems |
| 4 | Systems Pre-Build Check | Review and refine the systems list |
| 5 | Systems Build | Create detailed docs for each system |
| 6 | Subsystems Identification | Break systems into smaller pieces |
| 7 | Subsystems Pre-Build Check | Review the breakdown |
| 8 | Subsystem Build | Document each subsystem |
| 9 | External Integration Provider | Identify external services (Stripe, AWS, etc.) |
| 10 | External Integration Service Scope | Define what you need from each |
| 11 | External Integration Service Build | Document each integration |
| 12 | PrePass Check | Final review before moving on |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| PROJECT-OVERVIEW.md | Description of your project |
| PROJECT-INTENTION.md | What success looks like |
| SYSTEM-*.md | One doc per system |
| SUBSYSTEM-*.md | One doc per subsystem |
| PROVIDER-NOTES.md | External services you'll use |
| PREPASS-CHECKLIST.md | Verification that everything's ready |

---

## Common Questions

### "How detailed should my description be?"
As detailed as you can. The more Claude knows, the better the breakdown. Include:
- What problem does this solve?
- Who will use it?
- What are the main features?

### "What if I'm not sure about something?"
That's fine! Tell Claude what you know and what you're unsure about. Claude will ask questions.

### "How many systems should there be?"
Depends on your project. A simple app might have 3-5 systems. A complex one might have 10+. Trust Claude's recommendations, but push back if it feels wrong.

### "What's the difference between a system and subsystem?"
- **System**: A major area (like "User Management")
- **Subsystem**: A piece within that (like "Password Reset" within User Management)

---

## Troubleshooting

### "The systems don't feel right"
Ask Claude to re-evaluate. Say: "I think these systems might need to be restructured because [your reason]."

### "There are too many systems"
Some might be combined. Ask: "Could systems X and Y be combined?"

### "Something's missing"
Just tell Claude: "I think we're missing a system for [feature]."

### "I don't understand the breakdown"
Ask Claude to explain each system in one sentence.

---

## When You're Done

You'll have:
- ✅ A clear project overview
- ✅ Systems identified and documented
- ✅ Subsystems identified and documented
- ✅ External integrations listed
- ✅ PREPASS-CHECKLIST.md complete

**Next:** Move to Section 0b (PassPhases)

---

## Audit (Optional)

Consider running the 0a audit if:
- Your project is complex
- You want a second opinion on the structure
- Multiple stakeholders need to agree

**Audit files:** `00. external-audit/0a-concept/`

---

*This section sets the foundation. Take your time to get it right.*
