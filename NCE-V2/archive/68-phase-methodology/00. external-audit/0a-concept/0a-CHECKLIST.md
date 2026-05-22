# 0a Concept to PassPhase Audit Checklist

---
Section: 0a Concept to PassPhase (Phases 1-12)
Purpose: Verification checklist for external auditor
---

## Phase 1-2: Project Definition

### PROJECT-OVERVIEW.md
- [ ] File exists
- [ ] Project name defined
- [ ] Project type defined (web app, API, tool, etc.)
- [ ] High-level description provided
- [ ] Target users identified
- [ ] Key features listed

### PROJECT-INTENTION.md
- [ ] File exists
- [ ] Success criteria defined
- [ ] Success criteria are measurable
- [ ] Scope boundaries defined
- [ ] Out-of-scope items listed
- [ ] Constraints documented

---

## Phase 3-5: Systems

### System Identification
- [ ] SYSTEM-NOTES.md exists
- [ ] All necessary systems identified
- [ ] No obvious systems missing
- [ ] System count is reasonable (not too many, not too few)

### System Documentation
For each system:
- [ ] SYSTEM-{{name}}.md exists
- [ ] Purpose is clear
- [ ] Responsibilities defined
- [ ] Boundaries clear (what it does NOT do)
- [ ] No overlap with other systems

### System Quality
- [ ] Systems are at the right level of abstraction
- [ ] Each system has a single responsibility area
- [ ] Systems can be developed independently
- [ ] System names are clear and descriptive

---

## Phase 6-8: Subsystems

### Subsystem Identification
- [ ] SUBSYSTEM-NOTES.md exists
- [ ] Subsystems identified for each system
- [ ] Subsystem count is reasonable per system

### Subsystem Documentation
For each subsystem:
- [ ] SUBSYSTEM-{{name}}.md exists
- [ ] Parent system identified
- [ ] Purpose is clear
- [ ] Responsibilities defined

### Subsystem Quality
- [ ] Subsystems logically belong to parent system
- [ ] Granularity is appropriate
- [ ] No subsystem should be its own system
- [ ] No system should be a subsystem

---

## Phase 9-11: External Integrations

### Provider Identification
- [ ] PROVIDER-NOTES.md exists
- [ ] All external providers identified
- [ ] Provider purposes documented

### Service Scope
- [ ] SERVICE-SCOPE.md exists
- [ ] Services per provider listed
- [ ] Scope of each service defined

### Service Documentation
- [ ] SERVICE-NOTES.md exists
- [ ] Each service documented
- [ ] Integration approach noted

### Integration Quality
- [ ] All external dependencies identified
- [ ] No hidden integrations
- [ ] Scope is clear for each integration
- [ ] Fallback considerations noted (if applicable)

---

## Phase 12: Pre-Pass Gate

### PREPASS-CHECKLIST.md
- [ ] File exists
- [ ] All items checked
- [ ] No blocking issues
- [ ] Human approval recorded

### Readiness
- [ ] All required files exist
- [ ] Documentation is complete
- [ ] Ready to proceed to 0b

---

## Cross-Cutting Checks

### Naming Consistency
- [ ] System names are consistent throughout
- [ ] Subsystem names are consistent
- [ ] Integration names are consistent

### Completeness
- [ ] No TODO placeholders remaining
- [ ] No TBD items remaining
- [ ] All sections filled out

### Logical Structure
- [ ] Project → Systems → Subsystems hierarchy makes sense
- [ ] Integrations are at the right level
- [ ] No circular dependencies implied

---

## Red Flags (Automatic Critical)

- [ ] Project scope is contradictory
- [ ] Major system obviously missing
- [ ] System boundaries overlap significantly
- [ ] Critical external dependency not identified
- [ ] PREPASS-CHECKLIST has unchecked required items

---

## Audit Exit Gate

This audit is complete when:

- [ ] All sections reviewed
- [ ] All findings documented with severity
- [ ] Scores assigned
- [ ] Verdict provided
- [ ] Recommendations listed

---

*End of 0a Checklist*
