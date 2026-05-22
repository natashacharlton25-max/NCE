# Auditor Control Guide

---
**Purpose:** Control external AI auditor behaviour when it goes off-track
**For:** Human operators running external audits
**Remember:** You are in charge. The auditor works for you.
---

## Common Auditor Problems

| Problem | What It Looks Like | Solution Section |
|---------|-------------------|------------------|
| Over-pedantic | Flags every minor style issue | §1 |
| Scope creep | Audits things you didn't ask about | §2 |
| Won't conclude | Keeps finding more issues endlessly | §3 |
| Contradicts itself | Says X then later says not-X | §4 |
| Contradicts Claude | Disagrees with primary AI's work | §5 |
| Too vague | "This could be improved" without specifics | §6 |
| Off-topic | Discusses unrelated concerns | §7 |

---

## §1: Handling Over-Pedantic Auditors

### Symptoms
- Every file has 10+ "suggestions"
- Flags formatting, naming preferences
- Treats guidelines as rules
- Creates noise that hides real issues

### Control Phrases

**Severity filter:**
```
Focus only on Critical and Important severity findings. 
Do not report Minor issues or Suggestions unless I specifically ask.
```

**Pragmatic filter:**
```
Only report issues that would cause:
- Runtime failures
- Security vulnerabilities  
- Data corruption
- User-facing bugs
- Inability to maintain code

Skip style preferences and theoretical concerns.
```

**Quantity limit:**
```
Give me your top 5 most important findings only.
Rank by severity and impact.
```

---

## §2: Handling Scope Creep

### Symptoms
- Audits files you didn't provide
- Comments on business decisions
- Suggests architectural changes
- Reviews things outside the checklist

### Control Phrases

**Hard scope:**
```
Your audit scope is LIMITED to:
- [List specific files]
- [List specific concerns]

Do not comment on anything outside this scope.
```

**Redirect:**
```
That feedback is outside the scope of this audit.
Return to the checklist I provided and complete only those items.
```

**Boundary:**
```
I'm not asking you to redesign the system.
I'm asking you to verify the current design against the checklist.
```

---

## §3: Forcing Conclusion

### Symptoms
- "I'd need to see more files to be sure"
- "There might be other issues..."
- Endless "one more thing" additions
- Refuses to give pass/fail verdict

### Control Phrases

**Forced verdict:**
```
Based ONLY on what you have seen, give me:
1. A verdict: PASS, CONDITIONAL PASS, or FAIL
2. Your top 3 findings ranked by severity
3. Stop. Do not add caveats.
```

**Hard stop:**
```
The audit is now complete.
Summarize your findings in the report format I provided.
Do not continue reviewing.
```

**Completeness acceptance:**
```
Assume the files I provided are the complete relevant set.
Do not speculate about files you haven't seen.
Audit only what's in front of you.
```

---

## §4: Handling Self-Contradiction

### Symptoms
- "X is good" then later "X is bad"
- Conflicting recommendations
- Flip-flops when challenged

### Control Phrases

**Clarification:**
```
You said [X] earlier but now you're saying [Y].
Which is your actual finding? Pick one and explain why.
```

**Pin down:**
```
I need a definitive answer on [topic].
State your position clearly and do not change it.
```

**Record:**
```
Your final position on [topic] is: ___________
Confirm this is correct before we continue.
```

---

## §5: Handling Claude Disagreements

### Symptoms
- External auditor contradicts Claude's design
- Different interpretation of requirements
- Conflicting technical recommendations

### Resolution Process

**Step 1: Isolate the disagreement**
```
Claude said: [X]
You're saying: [Y]
Explain specifically why you disagree with Claude's approach.
```

**Step 2: Get Claude's defense**
(In your Claude conversation)
```
The external auditor disagrees with your approach on [topic].
Their argument: [paste auditor's reasoning]
Defend your position or acknowledge if they're correct.
```

**Step 3: Make your decision**
- If Claude concedes → Accept auditor's finding
- If Claude defends well → Consider rejecting finding
- If unclear → Default to safer/more conservative option
- Document in RISK-ACCEPTANCE either way

**Step 4: Inform auditor (if continuing)**
```
I've reviewed this disagreement with my primary AI.
My decision: [state decision]
This is final. Continue with remaining items.
```

---

## §6: Demanding Specificity

### Symptoms
- "This could be better"
- "Consider improving X"
- "There may be issues with Y"
- No actionable feedback

### Control Phrases

**Demand specifics:**
```
That's too vague. For each finding, provide:
1. Exact file and line/section
2. What is wrong (specific, not general)
3. What the fix would look like
4. Why it matters (severity)

Restate your finding with these details.
```

**Actionability test:**
```
Would a developer know exactly what to change based on this finding?
If no, make it specific enough that they would.
```

**Example demand:**
```
Show me an example of the problem and an example of the fix.
```

---

## §7: Redirecting Off-Topic

### Symptoms
- Discusses tangential concerns
- Philosophizes about best practices
- Provides unsolicited advice
- Goes on tangents

### Control Phrases

**Redirect:**
```
That's not relevant to this audit.
Return to item [X] on the checklist.
```

**Time box:**
```
We have 10 minutes to complete this audit.
Focus only on the checklist items.
```

**Explicit ignore:**
```
I'm ignoring that comment.
Next checklist item please.
```

---

## Emergency Phrases

When auditor is completely off the rails:

**Full reset:**
```
Stop. Let's restart.
Your ONLY task is to complete the checklist I provided.
Do not add commentary beyond what the checklist asks.
Begin with item 1.
```

**Abort:**
```
This audit is terminated.
Provide a summary of findings so far in the report format.
Do not continue.
```

**Authority assertion:**
```
I am the decision-maker on this project.
Your role is advisory only.
Complete the checklist and provide your report.
I will decide what to act on.
```

---

## Best Practices for Smooth Audits

### Before the Audit
- Use the AUDIT-PROMPT from the relevant section (it's pre-tuned)
- Don't ad-lib instructions — use what's provided
- Have files ready to paste quickly

### During the Audit
- Don't argue with every finding
- Let auditor complete the checklist before pushing back
- Take notes on disagreements for later

### After the Audit
- Review findings calmly
- Use RISK-ACCEPTANCE for findings you won't address
- Don't feel obligated to fix everything

---

## Remember

- The auditor is a tool, not an authority
- You can stop the audit at any time
- "Advisory, not authoritative" — you make final calls
- A difficult auditor doesn't mean bad findings
- Document your decisions either way

---

*You hired the auditor. You can fire the auditor. Act accordingly.*
