# 0a Audit Handoff Prompt

---
Section: 0a Concept to PassPhase (Phases 1-12)
Purpose: Instructions for Claude to prepare audit package
Use When: After completing Phase 12, before external audit (OPTIONAL)
---

## Instructions for Claude

You have completed Section 0a (Concept to PassPhase). Prepare a handoff package for external audit review.

**Note:** This audit is OPTIONAL but recommended for complex projects.

---

## Create: 0a-AUDIT-HANDOFF.md

### 1. Executive Summary (2-3 paragraphs)

Describe:
- What project concept was captured
- How many systems, subsystems, and integrations identified
- Overall confidence in the project structure

### 2. Self-Assessment Scores

Score your own work honestly (1-5 scale):

| Category | Score | Notes |
|----------|-------|-------|
| Completeness | /5 | Are all components identified? |
| Consistency | /5 | Do definitions align? |
| Accuracy | /5 | Is the structure correct for this project? |
| Methodology | /5 | Did you follow templates? |
| Quality | /5 | Is documentation clear? |
| Readiness | /5 | Ready for Pass phases? |

### 3. Concerns & Uncertainties

List anything you're not confident about:
- Scope boundaries that are unclear
- Systems that might need splitting
- Integrations that might be missing
- Areas where requirements were vague

### 4. Project Structure Summary

**Systems Identified:**
| System | Purpose | Subsystems |
|--------|---------|------------|
| | | |

**External Integrations:**
| Provider | Services | Purpose |
|----------|----------|---------|
| | | |

### 5. Key Decisions Made

| Decision | Rationale | Impact |
|----------|-----------|--------|
| | | |

### 6. File Manifest

**Phase 1-2 (Project Definition):**
| File | Status |
|------|--------|
| PROJECT-OVERVIEW.md | |
| PROJECT-INTENTION.md | |

**Phase 3-5 (Systems):**
| File | Status |
|------|--------|
| SYSTEM-NOTES.md | |
| SYSTEMS-CLARIFICATION.md | |
| SYSTEM-*.md (per system) | |

**Phase 6-8 (Subsystems):**
| File | Status |
|------|--------|
| SUBSYSTEM-NOTES.md | |
| SUBSYSTEMS-CLARIFICATION.md | |
| SUBSYSTEM-*.md (per subsystem) | |

**Phase 9-11 (Integrations):**
| File | Status |
|------|--------|
| PROVIDER-NOTES.md | |
| SERVICE-SCOPE.md | |
| SERVICE-NOTES.md | |

**Phase 12 (Gate):**
| File | Status |
|------|--------|
| PREPASS-CHECKLIST.md | |

### 7. Dependency Bundles

| Bundle Name | Primary File | Related Files |
|-------------|--------------|---------------|
| Project Foundation | PROJECT-OVERVIEW.md | PROJECT-INTENTION.md |
| Systems | SYSTEM-NOTES.md | All SYSTEM-*.md files |
| Subsystems | SUBSYSTEM-NOTES.md | All SUBSYSTEM-*.md files |
| Integrations | PROVIDER-NOTES.md | SERVICE-SCOPE.md, SERVICE-NOTES.md |
| Gate | PREPASS-CHECKLIST.md | All above |

### 8. Condensed Context

```
PROJECT: {{name}}
PURPOSE: {{one sentence}}
TYPE: {{web app / API / tool / etc.}}

SYSTEMS ({{count}}):
1. {{system}} - {{purpose}}
2. {{system}} - {{purpose}}

SUBSYSTEMS ({{count}}):
- {{system}}: {{subsystem list}}

INTEGRATIONS ({{count}}):
- {{provider}}: {{services}}

KEY CONSTRAINTS:
- {{constraint}}
```

### 9. Recommended Files for Review

1. **PROJECT-OVERVIEW.md** - Project scope
2. **PROJECT-INTENTION.md** - Success criteria
3. **SYSTEM-NOTES.md** - System breakdown
4. **SUBSYSTEM-NOTES.md** - Subsystem breakdown
5. **PREPASS-CHECKLIST.md** - Gate status

---

## Pre-Handoff Verification

Before creating this handoff, verify:

- [ ] PROJECT-OVERVIEW.md complete
- [ ] PROJECT-INTENTION.md complete
- [ ] All systems documented
- [ ] All subsystems documented
- [ ] All integrations documented
- [ ] PREPASS-CHECKLIST.md complete

---

## Output Location

Save as: `0a-AUDIT-HANDOFF.md` in `00. external-audit/0a-concept/`

---

*End of Handoff Prompt*
