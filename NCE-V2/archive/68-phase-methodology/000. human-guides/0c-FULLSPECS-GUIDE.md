# Section 0c: Full Specs

## Human Guide

---
**What this section does:** Creates detailed blueprints for every component
**Phases:** 20-28
**Time:** Several days (depends on project size)
**Audit:** ⭐ MANDATORY
---

## What Happens in This Section?

This is where the detailed work happens. For every system, subsystem, and integration, Claude creates comprehensive specifications covering:

- What functions it has
- What data it stores
- How it handles errors
- Security rules
- And much more

**Think of it like:** Writing the detailed blueprints for every room, including where every outlet and pipe goes.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Review specs | Do they capture what you want? |
| Clarify requirements | Answer questions about how things should work |
| Approve specs | Sign off before moving on |
| Run the audit | **Required** before proceeding to 0d |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 20 | Pre-Spec Notes | Prepare context for spec writing |
| 21 | Internal Component Specs | Write specs for systems/subsystems |
| 22 | External Integration Specs | Write specs for external services |
| 23 | Library Specs | Spec shared code libraries (if any) |
| 24 | Repo Specs | Define repository structure |
| 25 | Post-Spec Analysis | Analyze for gaps and patterns |
| 26 | Project-Wide Specs | Standards that apply everywhere |
| 27 | Hardening | Stress-test the specs for problems |
| 28 | Full-Spec Checklist | Final verification |

---

## The 10 Spec Files Per Component

Each internal component (system or subsystem) gets 10 spec files:

| File | What It Covers |
|------|----------------|
| INDEX.md | Overview and quick reference |
| OVERVIEW.md | Purpose and responsibilities |
| FUNCTIONS.md | What operations it performs |
| TYPES.md | Data structures it uses |
| ERRORS.md | Error conditions and codes |
| STORAGE.md | Data it stores |
| VALIDATION.md | Rules for valid data |
| SECURITY.md | Access control and permissions |
| EVENTS.md | Events it emits or listens to |
| BOUNDARIES.md | What it does and doesn't do |

---

## Key Documents Created (Project-Wide)

| Document | What It Is |
|----------|------------|
| TYPES.md | All data types used in the project |
| ERROR-CODES.md | All error codes used |
| NAMING-CONVENTIONS.md | How things should be named |
| DATABASE-SCHEMA.md (partial) | Database structure hints |
| HARDENING-SUMMARY.md | Results of stress-testing specs |
| FULL-SPEC-CHECKLIST.md | Final verification |

---

## What Is "Hardening"?

Phase 27 "hardens" the specs by stress-testing them:
- Are there contradictions?
- Are there gaps?
- Will these specs actually work when implemented?

Think of it as shaking the blueprints to see if anything falls off.

---

## Common Questions

### "These specs are very detailed. Do I need to read every line?"
Not necessarily. Focus on:
- FUNCTIONS.md - What does it do?
- ERRORS.md - What can go wrong?
- BOUNDARIES.md - What are the limits?

Ask Claude to summarize if needed.

### "I found an error in a spec"
Tell Claude! Say: "In [component] FUNCTIONS.md, I think [X] should be [Y] because [reason]."

### "This feels like too much documentation"
It is a lot, but it's insurance. These specs prevent expensive mistakes during implementation. Better to spend time here than rewrite code later.

### "What if I want to change something later?"
You can, but changes become harder after this point. The audit helps catch issues now.

---

## Troubleshooting

### "The specs seem inconsistent"
This is exactly what Phase 25 (Post-Spec Analysis) and Phase 27 (Hardening) are for. They should catch inconsistencies. If they persist, point them out.

### "I don't understand a spec"
Ask Claude: "Explain [component] FUNCTIONS.md in plain English. What does it actually do?"

### "Two components seem to overlap"
This might be a boundary issue. Ask: "Can you clarify the boundary between [A] and [B]? They seem to overlap on [feature]."

### "The hardening found problems"
Good! That's the point. Review the HARDENING-SUMMARY.md and work with Claude to address each issue.

---

## When You're Done

You'll have:
- ✅ 10 spec files for each internal component
- ✅ 10+ spec files for each external integration
- ✅ Project-wide standards and types
- ✅ Hardening review complete
- ✅ FULL-SPEC-CHECKLIST.md complete
- ✅ **External audit passed** ⭐

**Next:** Move to Section 0d (PreCode)

---

## Audit (MANDATORY) ⭐

**You must run the 0c audit before proceeding.**

This is the last checkpoint before code is written. Spec errors found now are cheap to fix. Spec errors found during implementation are expensive.

**Audit files:** `00. external-audit/0c-fullspecs/`

**What the audit checks:**
- Are all components fully specified?
- Are specs internally consistent?
- Do specs align with each other?
- Are there gaps or contradictions?

---

*This section is the foundation for all the code that follows. Get it right.*
