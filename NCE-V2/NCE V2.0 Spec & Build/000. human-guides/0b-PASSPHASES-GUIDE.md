# Section 0b: PassPhases 0-4

## Human Guide

---
**What this section does:** Refines your structure and defines how pieces connect
**Phases:** 13-19
**Time:** Half a day to a day
**Audit:** Recommended
---

## What Happens in This Section?

You've got your systems and subsystems from 0a. Now this section:

1. **Reviews coverage** - Makes sure nothing was missed
2. **Defines contracts** - How do the pieces talk to each other?
3. **Maps dependencies** - What depends on what?
4. **Plans implementation** - How big is each piece?

**Think of it like:** Drawing the wiring diagram after sketching the rooms.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Review contracts | Do the interfaces make sense? |
| Check dependencies | Does the build order make sense? |
| Approve estimates | Do the size estimates seem reasonable? |
| Flag concerns | If something feels wrong, speak up |

---

## The Passes (This Section Works Differently)

Instead of linear phases, this section does "passes" over your components:

| Pass | Name | What Happens |
|------|------|--------------|
| Pass 0 | Coverage | Review all systems/subsystems - anything missing? |
| Pass 1 | Grounding | Create SYSTEM.md and ADMIN.md for each component |
| Pass 2 | Contracts | Define how components talk to each other |
| Pass 3 | Dependencies | Map what depends on what, set build order |
| Pass 4 | Implementation Planning | Estimate files, lines of code, tasks |

Then two final phases:
| Phase | What Happens |
|-------|--------------|
| 18 | Post-Pass Checklist - verify everything's complete |
| 19 | Pass Notes Distribution - send context to each component |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| SYSTEM.md | Definition for each system |
| ADMIN.md | Administrative info for each component |
| CONTRACT.md | Interface definition (how components connect) |
| DEPENDENCY-MAP.md | What depends on what |
| PASS-NOTES.md | Context summary for each component |
| POST-PASS-CHECKLIST.md | Verification checklist |

---

## Understanding Contracts

A "contract" defines how two pieces of your system communicate. For example:

> "The Payment System will send the User System a 'payment completed' message containing: user ID, amount, and timestamp."

Without clear contracts, pieces might not fit together later.

---

## Understanding Dependencies

The dependency map shows what needs to be built first. For example:

```
User System (build first)
    ↓
Authentication System (needs User System)
    ↓
Payment System (needs Authentication)
```

**Circular dependencies are bad!** If A depends on B and B depends on A, there's a problem. Claude will flag these.

---

## Common Questions

### "What's the point of contracts?"
They prevent misunderstandings. When you get to implementation, everyone knows exactly how pieces connect.

### "The dependency map is confusing"
Ask Claude: "Explain the build order in plain English."

### "These estimates seem too high/low"
Tell Claude. Estimates are rough guides at this stage, but significant errors should be addressed.

### "What are PASS-NOTES?"
Summaries of key decisions that get distributed to each component. They help Claude remember context when working on individual pieces later.

---

## Troubleshooting

### "A circular dependency was found"
This means two things depend on each other. One of them needs to be restructured. Ask Claude for options.

### "A contract seems vague"
Ask: "Can you make the contract for [component] more specific? What exactly gets sent and received?"

### "Too many dependencies"
The system might be too tightly coupled. Ask: "Can we reduce dependencies by [restructuring X]?"

### "I don't understand what ADMIN.md is for"
It tracks who owns the component, its status, and administrative details. You can mostly ignore it - it's for project management.

---

## When You're Done

You'll have:
- ✅ All components reviewed (Pass 0)
- ✅ Each component grounded with SYSTEM.md and ADMIN.md (Pass 1)
- ✅ Contracts defined for all connections (Pass 2)
- ✅ Dependency map with build order (Pass 3)
- ✅ Implementation estimates (Pass 4)
- ✅ PASS-NOTES.md distributed to each component
- ✅ POST-PASS-CHECKLIST.md complete

**Next:** Move to Section 0c (Full Specs)

---

## Audit (Recommended)

Architecture decisions made here are hard to change later. Consider running the 0b audit if:
- You want confidence in the architecture
- The dependency map is complex
- Contracts feel uncertain

**Audit files:** `00. external-audit/0b-passphases/`

---

*This section locks in your architecture. Review carefully.*
