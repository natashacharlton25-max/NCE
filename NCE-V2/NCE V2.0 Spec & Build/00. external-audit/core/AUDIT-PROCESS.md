# External Audit Process

---
Version: 1.0.0
Purpose: Step-by-step guide for running external AI audits
Audience: Humans running audits
---

## Overview

External audits use another AI (ChatGPT, Gemini, etc.) to review Claude's work. This provides a second opinion and catches issues Claude might have missed.

**Time Required:** 30-60 minutes per audit

---

## When to Audit

### Mandatory Audits (Must Do)

| Section | Phase | Why |
|---------|-------|-----|
| **0c Full Specs** | 28 | Last chance before code - errors multiply |
| **0f Implementation** | 53 | Code must match specs exactly |
| **0h Prerelease** | 64 | Last gate before production |

### Recommended Audits (Should Do)

| Section | Phase | Why |
|---------|-------|-----|
| 0b PassPhases | 19 | Architecture decisions lock here |
| 0g Testing | 60 | Test quality affects confidence |

### Optional Audits (Can Skip)

| Section | Phase | When to Skip |
|---------|-------|--------------|
| 0a Concept | 12 | Small projects, internal tools |
| 0d PreCode | 36 | If 0c audit was thorough |
| 0e Frontend | 42 | If frontend is simple |
| 0i Rollout | 68 | Post-deploy review only |

---

## The 6-Step Process

```
┌─────────────────────────────────────────────────────────────────┐
│                     EXTERNAL AUDIT PROCESS                       │
└─────────────────────────────────────────────────────────────────┘

Step 1          Step 2          Step 3          Step 4
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ Claude  │───►│  Human  │───►│ Auditor │───►│ Auditor │
│Prepares │    │ Gathers │    │Requests │    │ Reviews │
│ Handoff │    │Materials│    │ Files   │    │  Files  │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
                                                  │
                    ┌─────────────────────────────┘
                    ▼
Step 5          Step 6
┌─────────┐    ┌─────────┐
│ Auditor │───►│ Human   │
│ Reports │    │ Decides │
│Findings │    │& Closes │
└─────────┘    └─────────┘
```

---

## Step 1: Claude Prepares Handoff

**Who:** Claude
**Time:** 5-10 minutes

Tell Claude:
> "Prepare the audit handoff package for section 0X"

Claude will create a handoff document containing:
- Summary of work done
- Self-assessment scores
- Concerns and uncertainties
- Key decisions made
- File manifest
- Dependency bundles
- Recommended files for review

**What to check:**
- Is the summary accurate?
- Are concerns listed honestly?
- Is the file manifest complete?

---

## Step 2: Human Gathers Materials

**Who:** You
**Time:** 5 minutes

Gather these items:
1. Claude's handoff document
2. The audit prompt for this section (0X-AUDIT-PROMPT.md)
3. The checklist for this section (0X-CHECKLIST.md)
4. Access to the project files

**Tip:** Have the project open in VS Code so you can quickly copy files.

---

## Step 3: Auditor Requests Files (Round 1)

**Who:** External AI (ChatGPT, Gemini, etc.)
**Time:** 5-10 minutes

1. Open the external AI
2. Paste the audit prompt
3. Paste Claude's handoff document
4. The auditor will request files to review

**File Limits:**
| Section | Round 1 | Round 2 |
|---------|---------|---------|
| 0a, 0b, 0d, 0h, 0i | 5 | 3 |
| 0e | 6 | 3 |
| 0c, 0g | 7 | 4 |
| 0f | 10 | 5 |

**Bundles:** When the auditor requests a bundle (e.g., "Component spec bundle"), provide all related files together.

---

## Step 4: Auditor Reviews & Follow-up (Round 2)

**Who:** External AI + You
**Time:** 15-30 minutes

1. Copy the requested files from your project
2. Paste them to the auditor
3. The auditor may:
   - Request additional files (Round 2)
   - Ask clarifying questions
   - Ask you to check something with Claude

**If auditor asks questions:**
- You can answer directly if you know
- You can ask Claude and relay the answer
- Keep the auditor focused on review, not implementation

**If auditor requests too many rounds:**
Say: "Please produce your final report now."

---

## Step 5: Auditor Produces Report

**Who:** External AI
**Time:** 10-15 minutes

The auditor produces a report with:
- Scores (1-5) for each category
- Findings by severity (🔴🟠🟡🔵)
- Verdict (PASS / CONDITIONAL / FAIL)
- Recommendations

**Save the report** in your project:
```
/audits/0X-audit-report-YYYY-MM-DD.md
```

---

## Step 6: Human Decides & Closes

**Who:** You
**Time:** 10-20 minutes

### Review Findings

For each finding, decide:

| Resolution | When to Use |
|------------|-------------|
| **Fixed** | You'll fix the issue |
| **Accepted** | Known risk, consciously not fixing |
| **Deferred** | Will fix in future release |
| **Disputed** | You disagree it's an issue |

### Address Critical Findings

For 🔴 Critical findings:
1. Give the finding to Claude
2. Ask Claude to fix it
3. Verify the fix
4. Update resolution status

### Make Final Decision

| Verdict | Your Options |
|---------|--------------|
| ✅ PASS | Proceed to next section |
| ⚠️ CONDITIONAL | Fix critical issues, then proceed |
| ❌ FAIL | Fix issues, consider re-audit |

### Close the Audit

Update the report with:
- Resolution status for each finding
- Your decision
- Your sign-off

---

## Giving Findings to Claude

When Claude needs to fix something:

> "The external audit found this issue:
> 
> **Finding:** [paste finding]
> **File:** [file name]
> **Severity:** [level]
> **Recommendation:** [paste recommendation]
> 
> Please fix this issue."

After Claude fixes:
1. Verify the fix addresses the finding
2. Update resolution status in audit report
3. If fix creates new issues, note them

---

## Choosing an External AI

| AI | Good For | Notes |
|----|----------|-------|
| **ChatGPT (GPT-4)** | Logic, consistency, security | Good all-around |
| **Gemini 1.5 Pro** | Large files, code review | Best for 0f, 0d |
| **Claude** | N/A | Don't audit Claude with Claude |
| **Copilot** | Code-specific | Good for 0f only |

**Recommendation:**
- 0a-0c: ChatGPT
- 0d-0f: Gemini (handles large context)
- 0g-0i: ChatGPT

---

## Handling Disagreements

### Claude Disagrees with Auditor

1. Review both positions
2. Check the evidence
3. You decide who's right
4. Document your reasoning

### Two AIs Disagree

If you run the same audit with two AIs:
1. Take the stricter interpretation
2. Or investigate the specific point of disagreement
3. You make the final call

### You Disagree with Auditor

You always have final authority. If you disagree:
1. Document why
2. Mark finding as "Disputed"
3. Note the risk you're accepting

---

## Controlling Difficult Auditors

If the external AI is over-pedantic, won't conclude, or goes off-topic, see `AUDITOR-CONTROL-GUIDE.md` in this folder.

---

## Common Problems

| Problem | Solution |
|---------|----------|
| Auditor hallucinates files | Tell them the file doesn't exist, ask to continue |
| Auditor requests too many files | Remind them of the limit |
| Auditor won't give verdict | Ask explicitly: "Based on your review, what is your verdict?" |
| Audit taking too long | Switch to Fast mode (see AUDIT-MODES.md) |
| Auditor flags style preferences | Ignore unless it affects function |

---

## Skipping an Audit

You can skip optional audits. If skipping:

1. Fill out AUDIT-SKIP-TEMPLATE.md
2. Document why you're skipping
3. Acknowledge the risks
4. Sign it

**Never skip:** 0c, 0f, 0h (mandatory audits)

---

## After the Audit

1. **Save the report** to `/audits/`
2. **Update the section checklist** with "External Audit: COMPLETE"
3. **Proceed to next section** (if PASS)
4. **Track any deferred items** for future

---

## Quick Reference

| Step | Who | Time | Output |
|------|-----|------|--------|
| 1. Prepare | Claude | 5-10 min | Handoff doc |
| 2. Gather | Human | 5 min | Materials ready |
| 3. Request | Auditor | 5-10 min | File requests |
| 4. Review | Auditor | 15-30 min | Follow-up |
| 5. Report | Auditor | 10-15 min | Audit report |
| 6. Decide | Human | 10-20 min | Resolution |
| **Total** | | **50-90 min** | |

---

*End of Audit Process*
