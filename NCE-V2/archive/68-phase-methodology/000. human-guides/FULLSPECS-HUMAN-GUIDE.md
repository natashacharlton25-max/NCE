# Fullspecs: Human Guide

---
**What is this?** A plain-English guide to the Fullspecs methodology
**Who is this for?** Project owners, managers, and anyone working with Claude on a Fullspecs project
**No technical knowledge required**
---

## First Time Here?

If this is your first time using Fullspecs, start with `START-HERE.md` instead of this document. It will guide you through exactly what to read and in what order.

## Additional Resources

| Document | Use When |
|----------|----------|
| `START-HERE.md` | First time using Fullspecs |
| `FAILURE-RECOVERY-RULES.md` | Something went wrong, need to roll back |
| `RISK-ACCEPTANCE-TEMPLATE.md` | Accepting audit findings without fixing |
| `PROJECT-RESUMPTION-CHECKLIST.md` | Resuming after 2+ week pause |
| `TEAM-MODE-GUIDE.md` | Working with multiple people |

---

## What Is Fullspecs?

Fullspecs is a step-by-step system for building software projects from start to finish. Think of it like a recipe book for software development.

**The problem it solves:** When you ask AI to help build software, it can lose track of earlier decisions in long projects. Fullspecs breaks the work into manageable chunks so nothing gets lost.

**How it works:** You work through sections in order (0a → 0b → 0c... etc.). Each section has specific jobs to do. Claude does most of the work, but you make the decisions.

---

## The Journey at a Glance

| Section | Name | What Happens | Your Job |
|---------|------|--------------|----------|
| **0a** | Concept | Capture your idea, identify the pieces | Describe what you want |
| **0b** | PassPhases | Refine the structure, define how pieces connect | Review and approve |
| **0c** | Full Specs | Write detailed blueprints for everything | Review specs |
| **0f** | Implementation | Actually write the code | Review code |
| **0g** | Testing | Make sure it works | Review test results |
| **0h** | Prerelease | Prepare for launch | Final approval |
| **0i** | Rollout | Launch it! | Watch it go live |

There are also sections 0d (PreCode) and 0e (Frontend) between 0c and 0f.

---

## Your Role vs Claude's Role

### Claude Does:
- Generate documents from templates
- Follow the methodology rules
- Flag issues and concerns
- Prepare work for your review

### You Do:
- Make decisions
- Approve or reject Claude's work
- Run external audits (at key checkpoints)
- Give the final go-ahead

**Remember:** Claude advises, you decide. You're always in charge.

---

## The Three Mandatory Checkpoints

At three points, you MUST get a second opinion from another AI before continuing:

| Checkpoint | After Section | Why It Matters |
|------------|---------------|----------------|
| **Spec Review** | 0c (Full Specs) | Last chance to catch blueprint errors |
| **Code Review** | 0f (Implementation) | Make sure code matches blueprints |
| **Launch Review** | 0h (Prerelease) | Final safety check before real users |

These audits typically take 30-60 minutes each. They're your safety net.

---

## How a Typical Section Works

### Step 1: Start the Section
Tell Claude which section you're working on. Claude will know what to do.

### Step 2: Claude Works Through Phases
Each section has numbered phases. Claude works through them in order, creating documents as it goes.

### Step 3: You Review
Claude shows you the work. You can:
- **Approve** - Move to the next phase
- **Request changes** - Claude revises
- **Ask questions** - Get clarification

### Step 4: Complete the Section
When all phases are done, Claude creates a checklist and handoff document.

### Step 5: Audit (if required)
For sections 0c, 0f, and 0h, you run an external audit before moving on.

---

## Understanding the Folder Structure

Your project will have folders like this:

```
Your Project/
├── 0. Admin/           ← Instructions and tracking
├── 00. external-audit/ ← Audit system files
├── 1. Project Overview/
├── 2. Project Intention/
├── 3. System Identification/
... and so on
```

**The numbered folders (1, 2, 3...)** contain the actual work for each phase.

**The Admin folder** contains instructions for Claude and tracking documents.

**The external-audit folder** contains everything for running audits.

---

## Common Questions

### "How long does this take?"
Depends on project size. A simple project might take a few days. A complex one could take weeks. The methodology doesn't add time—it prevents wasted time from mistakes.

### "Can I skip sections?"
Not recommended. Each section builds on the previous one. Skipping creates gaps.

### "What if Claude makes a mistake?"
That's what the review steps and audits are for. You catch mistakes before they become expensive.

### "Do I need to understand the technical details?"
Not always. You need to understand what's being built (the "what"), not necessarily how (the "how"). Claude handles the technical details.

### "What if I disagree with Claude?"
You're in charge. Tell Claude what you want changed. Claude will adjust.

---

## Troubleshooting

### "Claude seems confused about where we are"
Tell Claude: "Let's check our progress. What section and phase are we on?"

### "The project feels too complex"
This might mean systems need to be split into smaller pieces. Ask Claude to review the system breakdown.

### "Claude keeps making the same mistake"
The issue might be in an earlier document. Ask Claude to trace back where the wrong information came from.

### "I don't understand what Claude produced"
Ask Claude to explain it in simpler terms. Say: "Explain this to me like I'm not a developer."

### "The audit found problems"
This is good! That's what audits are for. Give the findings to Claude and ask it to fix them.

---

## Key Terms (Plain English)

| Term | What It Means |
|------|---------------|
| **System** | A big chunk of your project (like "User Management" or "Payments") |
| **Subsystem** | A smaller piece within a system |
| **Spec** | A detailed blueprint for how something should work |
| **Phase** | One step in the process |
| **Section** | A group of related phases (0a, 0b, etc.) |
| **Handoff** | A summary document passed to the next section |
| **Audit** | Getting a second AI to review the work |
| **RC (Release Candidate)** | The final version ready for launch |

---

## Getting Help

If you're stuck:

1. **Ask Claude** - Claude knows the methodology
2. **Check the Admin folder** - There are guides and instructions there
3. **Read the relevant Human Guide** - Each section has one
4. **Run a self-check** - Ask Claude to verify the current state

---

## Quick Start Checklist

- [ ] Understand what Fullspecs is (you're doing that now!)
- [ ] Have your project idea ready
- [ ] Start with Section 0a
- [ ] Work through sections in order
- [ ] Review Claude's work at each step
- [ ] Run audits at 0c, 0f, and 0h
- [ ] Launch your project!

---

*You've got this. The methodology is here to help, not to complicate things.*
