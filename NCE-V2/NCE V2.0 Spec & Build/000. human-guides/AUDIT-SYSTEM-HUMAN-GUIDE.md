# External Audit System: Human Guide

---
**What is this?** A plain-English guide to running external AI audits
**Who is this for?** Anyone running audits on Fullspecs projects
**No technical knowledge required**
---

## What Is an External Audit?

An external audit is when you get a **second AI** (like ChatGPT or Gemini) to review Claude's work. 

Think of it like getting a second opinion from a doctor. Claude did the work, but another AI checks it to catch anything that might have been missed.

---

## Why Do Audits?

| Without Audits | With Audits |
|----------------|-------------|
| One AI's blind spots go unchecked | Another AI catches what was missed |
| Mistakes found late (expensive to fix) | Mistakes found early (cheap to fix) |
| "It looked fine to me" | "Here's what I found" |
| Hope it works | Confidence it works |

---

## The Three Required Audits

You MUST run these three audits. They're not optional.

| Audit | When | What It Checks |
|-------|------|----------------|
| **0c Audit** | After specs are written | Are the blueprints complete and consistent? |
| **0f Audit** | After code is written | Does the code match the blueprints? |
| **0h Audit** | Before launch | Is everything ready for real users? |

**Why these three?**
- 0c: Catch spec errors before writing code (errors multiply later)
- 0f: Catch code errors before testing (security, correctness)
- 0h: Final safety check before real users are affected

---

## How an Audit Works (The Simple Version)

```
1. Claude finishes a section
        ↓
2. Claude prepares a summary (automatically)
        ↓
3. You open ChatGPT or Gemini
        ↓
4. You paste the audit instructions + summary
        ↓
5. The auditor asks for specific files
        ↓
6. You copy those files from your project
        ↓
7. The auditor reviews and gives you a report
        ↓
8. You give the report to Claude to fix any issues
        ↓
9. Continue to next section
```

**Time required:** About 30-60 minutes per audit.

---

## What's In Each Audit Folder?

Each section (0a, 0b, 0c, etc.) has a folder in `00. external-audit/` with these files:

| File | What It Does | When You Use It |
|------|--------------|-----------------|
| **HANDOFF-PROMPT.md** | Tells Claude how to prepare the summary | Claude uses this automatically |
| **AUDIT-PROMPT.md** | Instructions for the external AI | You paste this to ChatGPT/Gemini |
| **CHECKLIST.md** | List of things to verify | The auditor uses this |
| **FILE-MANIFEST.md** | List of all available files | Reference when auditor asks for files |
| **REPORT-TEMPLATE.md** | Format for the audit report | The auditor fills this out |

You mainly interact with **AUDIT-PROMPT.md** - the others work in the background.

---

## Step-by-Step: Running an Audit

### Step 1: Claude Prepares the Handoff

When Claude finishes section 0c, 0f, or 0h, it will say something like:

> "I've prepared the audit handoff package. This section requires a mandatory external audit before proceeding."

Claude creates a file called `0X-AUDIT-HANDOFF.md` with a summary of the work.

### Step 2: Open Your External AI

Open **ChatGPT** or **Google Gemini** in a new browser tab.

**Which one should I use?**
- **ChatGPT (GPT-4)**: Good all-around choice
- **Gemini**: Better for code review (0f audit)

### Step 3: Start the Audit

Copy and paste these two things to the external AI:

1. The **audit prompt** from `00. external-audit/0X-section/0X-AUDIT-PROMPT.md`
2. The **handoff** that Claude created

### Step 4: Provide Requested Files

The auditor will ask for specific files, like:

> "Please provide the following files:
> 1. DATABASE-SCHEMA.md
> 2. API-SURFACE.md
> 3. ..."

Go to your project, find those files, and copy their contents to the auditor.

**Tip:** Use the FILE-MANIFEST.md as a reference to find where files are located.

### Step 5: Answer Follow-up Questions

The auditor might ask for more files or ask clarifying questions. This is normal - just provide what's asked.

### Step 6: Get the Report

The auditor will produce a report with:
- **Scores** (1-5 for different categories)
- **Findings** (issues found, sorted by severity)
- **Verdict** (PASS, CONDITIONAL, or FAIL)
- **Recommendations** (what to fix)

### Step 7: Address the Findings

Take the report back to Claude. Say:

> "The external audit found these issues. Please fix them."

Then paste the findings.

### Step 8: Continue

Once issues are addressed:
- **PASS**: Continue to next section
- **CONDITIONAL**: Fix critical issues first, then continue
- **FAIL**: Fix issues, consider re-running the audit

---

## Understanding the Audit Report

### Scores

Each category is scored 1-5:
- **5** = Excellent
- **4** = Good
- **3** = Acceptable
- **2** = Needs work
- **1** = Major problems

### Severity Levels

| Icon | Level | What It Means | Action |
|------|-------|---------------|--------|
| 🔴 | Critical | Must fix before continuing | Fix immediately |
| 🟠 | Important | Should fix before continuing | Fix soon |
| 🟡 | Minor | Nice to fix | Fix if time allows |
| 🔵 | Suggestion | Optional improvement | Consider for future |

### Verdicts

| Verdict | Meaning | What To Do |
|---------|---------|------------|
| ✅ **PASS** | Good to go | Continue to next section |
| ⚠️ **CONDITIONAL** | Mostly good, some issues | Fix critical issues, then continue |
| ❌ **FAIL** | Significant problems | Fix issues, may need to re-audit |

---

## What If I Disagree With the Auditor?

You're the human. You make the final decision.

If you disagree with a finding:
1. Consider why the auditor flagged it
2. Decide if it's actually a problem
3. If you still disagree, mark it as "Disputed" in the report
4. Document your reasoning
5. Move forward with your decision

The audit is **advisory, not authoritative**. It's a second opinion, not a court order.

---

## Controlling the Auditor

If the external AI becomes over-pedantic, won't conclude, or goes off-topic, see `AUDITOR-CONTROL-GUIDE.md` in `00. external-audit/core/`.

It contains specific phrases and techniques for:
- Forcing a verdict
- Limiting scope
- Handling contradictions
- Demanding specificity

---

## Accepting Findings Without Fixing

If you choose to proceed despite an audit finding:
1. Document your decision in `RISK-ACCEPTANCE-TEMPLATE.md`
2. File it in `0. Admin/risk-acceptances/`
3. This creates an audit trail of informed decisions

---

## Troubleshooting

### "The auditor keeps asking for files that don't exist"
The auditor might be confused. Tell it: "That file doesn't exist in this project. Please continue with the files I've provided."

### "The auditor is taking too long"
If it's going in circles, say: "Please produce your final report now based on what you've reviewed."

### "The auditor found too many issues"
This is actually good - better to find them now! Prioritize:
1. Fix all 🔴 Critical issues
2. Fix 🟠 Important issues
3. Note 🟡 Minor for later
4. Consider 🔵 Suggestions

### "I don't understand a finding"
Ask the auditor: "Can you explain finding #X in simpler terms?"

### "Claude disagrees with the auditor"
Review both perspectives. Check the evidence. You decide who's right.

### "The audit is taking more than an hour"
You might be over-providing files. Stick to what's requested. Use "bundles" to provide related files together.

---

## Optional vs Required Audits

| Section | Required? | When to Run Optional Audits |
|---------|-----------|----------------------------|
| 0a Concept | Optional | Complex projects, unclear scope |
| 0b PassPhases | Recommended | Important architecture decisions |
| **0c Full Specs** | **REQUIRED** | Always |
| 0d PreCode | Optional | If 0c audit had issues |
| 0e Frontend | Optional | Complex UI projects |
| **0f Implementation** | **REQUIRED** | Always |
| 0g Testing | Recommended | Want confidence in tests |
| **0h Prerelease** | **REQUIRED** | Always |
| 0i Rollout | Optional | Post-launch review |

---

## Quick Reference Card

**To run an audit:**
1. Get Claude's handoff (automatic after 0c/0f/0h)
2. Open ChatGPT or Gemini
3. Paste: audit prompt + handoff
4. Provide files when asked
5. Get report
6. Give findings to Claude
7. Continue

**Report says PASS?** → Continue
**Report says CONDITIONAL?** → Fix critical issues, continue
**Report says FAIL?** → Fix issues, consider re-audit

---

## Files Quick Reference

| You Need | Location |
|----------|----------|
| Audit prompt | `00. external-audit/0X-section/0X-AUDIT-PROMPT.md` |
| Claude's handoff | Claude creates it (0X-AUDIT-HANDOFF.md) |
| File manifest | `00. external-audit/0X-section/0X-FILE-MANIFEST.md` |
| Report template | `00. external-audit/0X-section/0X-REPORT-TEMPLATE.md` |

---

*Audits are your safety net. They catch problems before they become expensive.*
