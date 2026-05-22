# Section 0d: PreCode

## Human Guide

---
**What this section does:** Consolidates specs and prepares for coding
**Phases:** 29-36
**Time:** Half a day to a day
**Audit:** Optional
---

## What Happens in This Section?

You have detailed specs for each component. Now this section:

1. **Consolidates the database** - Combines all storage needs into one schema
2. **Consolidates the API** - Combines all functions into one API surface
3. **Sets up structure** - Defines folder organization and libraries
4. **Chooses technology** - Documents what tech stack you're using
5. **Establishes standards** - Coding style and conventions

**Think of it like:** Taking all the room blueprints and creating the master construction plan.

---

## Your Job in This Section

| Task | What You Do |
|------|-------------|
| Review consolidations | Does the combined schema make sense? |
| Confirm tech stack | Are you happy with the technology choices? |
| Approve standards | Do the coding conventions work for your team? |
| Watch for conflicts | If Claude finds conflicts, decide how to resolve |

---

## The Phases

| Phase | Name | What Happens |
|-------|------|--------------|
| 29 | Database Schema Consolidation | Combine all STORAGE.md into one schema |
| 30 | API Surface Consolidation | Combine all FUNCTIONS.md into one API |
| 31 | Library Structure | Set up shared code libraries |
| 32 | Repo Setup | Define folder structure |
| 33 | Tech Stack | Document technology choices |
| 34 | Environment | Define dev/staging/production configs |
| 35 | Coding Standards | Style guides and conventions |
| 36 | PreCode Ready Check | Final verification |

---

## Key Documents Created

| Document | What It Is |
|----------|------------|
| DATABASE-SCHEMA.md | Complete database structure |
| MIGRATION-STRATEGY.md | How to update the database over time |
| API-SURFACE.md | All API endpoints in one place |
| LIBRARY-STRUCTURE.md | Shared code organization |
| REPO-STRUCTURE.md | Folder organization |
| TECH-STACK.md | Technologies you're using |
| ENVIRONMENT.md | Environment configurations |
| CODING-STANDARDS.md | How code should be written |
| PRECODE-CHECKLIST.md | Final verification |

---

## Important Rule: No Fix Forward

If Claude finds conflicts during consolidation (Phase 29 or 30), **you must go back to 0c** and fix the specs there.

**Why?** The specs are the source of truth. If you "fix" things here without updating specs, the specs and reality will diverge. That causes problems later.

**Example conflict:**
- System A says users have "email" field
- System B says users have "emailAddress" field

**Wrong:** Just pick one in the database schema
**Right:** Go back to 0c, fix both specs to be consistent, then return

---

## Common Questions

### "What is a database schema?"
It's the structure of your data storage - what tables exist, what columns they have, how they relate to each other.

### "What is an API surface?"
It's all the ways your system can be talked to - the endpoints, what data they accept, what they return.

### "Do I need to understand the technical details?"
Not deeply. Focus on:
- Does the schema have all the data you need?
- Does the API have all the operations you need?
- Does the tech stack match your requirements?

### "We haven't decided on a tech stack yet"
That's fine! Phase 33 is where you document the decision. If you're unsure, Claude can recommend options.

---

## Troubleshooting

### "Claude found a conflict"
This is actually good - it caught an inconsistency before coding. Go back to 0c, fix the conflicting specs, then return.

### "The database schema looks wrong"
Ask Claude: "Why did you structure [table] this way? I expected [something else]."

### "I want to use a different technology"
Just tell Claude. The tech stack is your choice.

### "The coding standards are too strict/loose"
Adjust them! These are guidelines for your project. Say: "Let's relax/tighten the rule about [X]."

---

## When You're Done

You'll have:
- ✅ Consolidated DATABASE-SCHEMA.md
- ✅ Consolidated API-SURFACE.md
- ✅ REPO-STRUCTURE.md defined
- ✅ TECH-STACK.md documented
- ✅ CODING-STANDARDS.md established
- ✅ PRECODE-CHECKLIST.md complete

**Next:** Move to Section 0e (Frontend)

---

## Audit (Optional)

Consider running the 0d audit if:
- The 0c audit found issues
- You want extra confidence in the consolidation
- The database schema is complex

**Audit files:** `00. external-audit/0d-precode/`

---

*This section bridges specs and code. Conflicts found here save time later.*
