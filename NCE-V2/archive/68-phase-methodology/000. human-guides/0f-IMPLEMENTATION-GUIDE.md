# Section 0f: Implementation

## Human Guide

---
**What this section does:** Actually writes the code
**Phases:** 43-53
**Time:** Days to weeks (depends on project size)
**Audit:** ⭐ MANDATORY
---

## What Happens in This Section?

This is where code gets written. Claude:

1. **Plans the build order** - What to build first
2. **Writes backend code** - The server-side logic
3. **Writes frontend code** - The user interface
4. **Connects everything** - Integration
5. **Documents deviations** - Anything that differs from specs

**Think of it like:** Actually building the house from the blueprints.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Approve build order | Does the sequence make sense? |
| Review code | Spot-check what's being built |
| Approve deviations | If specs need to change, approve it |
| Run the audit | **Required** before moving to testing |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 43 | Implementation Planning | Define build order |
| 44-47 | Backend Implementation | Build server-side code |
| 48-50 | Frontend Implementation | Build user interface |
| 51 | Integration | Connect frontend to backend |
| 52 | Implementation Review | Check the work |
| 53 | Implementation Complete | Final verification |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| IMPLEMENTATION-PLAN.md | Build order and approach |
| IMPLEMENTATION-LOG.md | Record of what was built |
| DEVIATION-REPORT.md | Where code differs from specs |
| IMPLEMENTATION-CHECKLIST.md | Final verification |

---

## The Golden Rule: Specs Are Truth

If Claude needs to do something differently than the spec says:

1. **Stop**
2. **Document** the deviation
3. **Get approval** from you
4. **Update the spec** in 0c (go back if needed)
5. **Then continue**

**Never** silently deviate from specs. That creates confusion later.

---

## Understanding Deviations

A "deviation" is when the code differs from the spec. Examples:

| Type | Example | Required Action |
|------|---------|-----------------|
| **Documented** | "Spec said X, but Y works better because..." | Approve, update spec |
| **Undocumented** | Code just does something different | 🚨 Critical issue! |

Documented deviations are fine (with approval). Undocumented deviations are problems.

---

## Common Questions

### "Do I need to review all the code?"
Not line-by-line. Focus on:
- Does it seem to do what the spec says?
- Are there any deviations documented?
- Does it run without errors?

The audit will do the detailed review.

### "Claude made a deviation. Is that bad?"
Not necessarily. Sometimes specs don't account for real-world constraints. What matters:
- Is it documented?
- Does the deviation make sense?
- Did you approve it?

### "The code doesn't work"
Ask Claude to debug. Say: "I'm seeing [error]. Can you investigate?"

### "This is taking a long time"
Implementation is the longest phase. Check progress in IMPLEMENTATION-LOG.md.

---

## Troubleshooting

### "There are undocumented deviations"
This is a critical issue. Ask Claude: "I found code that differs from the spec in [location]. Was this documented? If not, why does it differ?"

### "The code seems over-complicated"
Ask: "Can you simplify [component]? It seems more complex than the spec requires."

### "Something wasn't implemented"
Check the spec. If it's in the spec, tell Claude: "[Feature] from the spec isn't implemented. Please add it."

### "I want to add a new feature"
**Stop!** Don't add features during implementation. Go back to 0c, update the spec, then return.

---

## Code Quality Rules

Claude follows these rules (and the audit checks them):

| Rule | Why |
|------|-----|
| ~300 lines per file | Keeps files manageable |
| ~1,500 lines per component | Prevents complexity |
| Clear naming | Easy to understand |
| Comments for "why" | Explain reasoning |
| Match the spec | Specs are truth |

---

## When You're Done

You'll have:
- ✅ All backend code written
- ✅ All frontend code written
- ✅ Everything integrated
- ✅ All deviations documented and approved
- ✅ IMPLEMENTATION-CHECKLIST.md complete
- ✅ **External audit passed** ⭐

**Next:** Move to Section 0g (Testing)

---

## Audit (MANDATORY) ⭐

**You must run the 0f audit before proceeding.**

This audit checks:
- Does code match specs?
- Are deviations documented?
- Is code secure?
- Is code quality acceptable?

**Audit files:** `00. external-audit/0f-implementation/`

**What the audit specifically looks for:**
- Security vulnerabilities
- Spec compliance
- Code quality
- Missing functionality

---

*This section turns plans into reality. The audit ensures it was done right.*
