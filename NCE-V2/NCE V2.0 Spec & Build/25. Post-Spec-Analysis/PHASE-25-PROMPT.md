# PHASE 25: POST-SPEC ANALYSIS

---
Phase: 25
Name: Post-Spec Analysis
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/25.Post-Spec-Analysis/
---

## ROLE

You are analyzing all completed specs to identify patterns, gaps, inconsistencies, and opportunities for improvement BEFORE implementation begins.

This is the "grep phase" — systematically scanning all specs to surface issues.

---

## PURPOSE

Post-Spec Analysis answers:
- Are all error codes unique and consistent?
- Are all types defined and used consistently?
- Are there duplicate patterns that should be libraries?
- Are all dependencies documented and valid?
- Are there gaps or contradictions between specs?

---

## TASK

Run 5 analysis passes across all completed specs:

| Pass | Focus | Output | Template |
|------|-------|--------|----------|
| 1 | Error Codes | ERROR-CODE-ANALYSIS.md | **Create using ERROR-CODE-ANALYSIS-TEMPLATE.md** |
| 2 | Types & Schemas | TYPE-ANALYSIS.md | **Create using TYPE-ANALYSIS-TEMPLATE.md** |
| 3 | Dependencies | DEPENDENCY-ANALYSIS.md | **Create using DEPENDENCY-ANALYSIS-TEMPLATE.md** |
| 4 | Patterns & Duplication | PATTERN-ANALYSIS.md | **Create using PATTERN-ANALYSIS-TEMPLATE.md** |
| 5 | Gaps & Inconsistencies | GAP-ANALYSIS.md | **Create using GAP-ANALYSIS-TEMPLATE.md** |

---

## PROCESS

### Pass 1: Error Code Analysis

Scan all ERRORS.md files:

- [ ] Collect all error codes
- [ ] Check for duplicates
- [ ] Verify prefix conventions
- [ ] Check code range allocation
- [ ] Identify missing error handling

**Output:** ERROR-CODE-ANALYSIS.md

---

### Pass 2: Type Analysis

Scan all TYPES.md files:

- [ ] Collect all type definitions
- [ ] Find duplicate/similar types
- [ ] Identify shared types to extract
- [ ] Check naming consistency
- [ ] Verify type references are valid

**Output:** TYPE-ANALYSIS.md

---

### Pass 3: Dependency Analysis

Scan all specs for dependencies:

- [ ] Build dependency graph
- [ ] Check for circular dependencies
- [ ] Verify all dependencies exist
- [ ] Check dependency direction rules
- [ ] Identify missing dependencies

**Output:** DEPENDENCY-ANALYSIS.md

---

### Pass 4: Pattern Analysis

Look for repeated patterns:

- [ ] Find duplicate code patterns
- [ ] Identify library candidates
- [ ] Find repeated validation logic
- [ ] Find repeated error handling
- [ ] Recommend extractions

**Output:** PATTERN-ANALYSIS.md

---

### Pass 5: Gap Analysis

Check for completeness:

- [ ] Missing spec files
- [ ] Incomplete sections
- [ ] Contradictions between specs
- [ ] Unresolved questions
- [ ] Missing edge cases

**Output:** GAP-ANALYSIS.md

---

## ANALYSIS TECHNIQUES

### Grep Patterns

```bash
# Find all error codes
grep -r "{{PREFIX}}_[0-9]" */spec/ERRORS.md

# Find all type definitions
grep -r "^### " */spec/TYPES.md

# Find all dependencies
grep -r "Depends On" */spec/OVERVIEW.md

# Find all functions
grep -r "^### .*()$" */spec/FUNCTIONS.md
```

### Cross-Reference Checks

| Check | Method |
|-------|--------|
| Error code uniqueness | Collect all, find duplicates |
| Type consistency | Compare definitions across files |
| Dependency validity | Verify referenced components exist |
| Function signatures | Compare similar functions |

---

## RULES

### DO:
- Be systematic — check everything
- Document every finding
- Propose specific fixes
- Prioritize by impact
- Reference specific files/lines

### DO NOT:
- Fix issues during analysis (just document)
- Skip components
- Make assumptions
- Ignore "minor" issues

---

## OUTPUT LOCATION

```
0. Admin/0c. Full Specs/25.Post-Spec-Analysis/
├── PHASE-25-PROMPT.md
├── ERROR-CODE-ANALYSIS.md
├── TYPE-ANALYSIS.md
├── DEPENDENCY-ANALYSIS.md
├── PATTERN-ANALYSIS.md
└── GAP-ANALYSIS.md
```

---

## FINDINGS FORMAT

Each analysis file should use this format for findings:

```markdown
### Finding: {{Title}}

**Severity:** Critical / High / Medium / Low
**Location:** {{file path}}
**Issue:** {{description}}
**Impact:** {{what breaks if not fixed}}
**Recommendation:** {{specific fix}}
```

---

## SUMMARY REQUIREMENTS

Each analysis file must end with:

1. **Summary Statistics**
   - Total items analyzed
   - Issues found by severity
   - Components affected

2. **Action Items**
   - Prioritized list of fixes
   - Estimated effort
   - Dependencies between fixes

3. **Sign-off Checklist**
   - [ ] Analysis complete
   - [ ] All findings documented
   - [ ] Recommendations provided

---

## APPROVAL GATE

Phase 25 is COMPLETE when:

- [ ] All 5 analysis passes complete
- [ ] All findings documented
- [ ] Critical/High issues have recommendations
- [ ] Human reviews and approves
- [ ] Decision made on each finding (fix now / defer / accept)

---

## WHAT HAPPENS NEXT

After Phase 25:
1. **Critical findings** → Fix immediately, update specs
2. **Library candidates** → Create PRE-SPEC-NOTES, go to Phase 23
3. **Shared types** → Document in Phase 26 (Project-Wide Specs)
4. **Minor issues** → Track in Phase 28 checklist

**Next Phase:** Phase 26 (Project-Wide Specs)

---
Generated: {{timestamp}}
---
