# PHASE 24: REPO SPECS

---
Phase: 24
Name: Repo Specs
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/24.Repo-Specs/
---

## ROLE

You are documenting the repository structure, conventions, and development setup — everything a new developer needs to understand the codebase.

---

## PURPOSE

Repo Specs answer:
- How is this codebase organized?
- What conventions do we follow?
- How do I set up my environment?
- How do I build, test, and deploy?
- Where do things go?

---

## TASK

Create a single comprehensive REPO-SPEC.md that documents:

1. **Repository Structure** — Folder layout, naming conventions
2. **Development Setup** — Environment, tools, configuration
3. **Build & Run** — Commands, scripts, pipelines
4. **Testing** — Test structure, running tests, coverage
5. **Conventions** — Code style, naming, patterns
6. **Workflows** — Git flow, PR process, deployment

---

## OUTPUT

**Create `REPO-SPEC.md` using `REPO-SPEC-TEMPLATE.md`**

Location: Repository root
```
project/
├── REPO-SPEC.md      ← Create using REPO-SPEC-TEMPLATE.md
├── README.md         ← Quick start (references REPO-SPEC.md)
├── systems/
├── lib/
└── ...
```

---

## WHEN TO CREATE

Create REPO-SPEC.md when:
- All system/subsystem specs are complete (Phases 21-23)
- Repository structure is finalized
- Development conventions are decided

---

## INPUTS

| Source | Information |
|--------|-------------|
| System specs | Folder structure for systems |
| Subsystem specs | Folder structure for subsystems |
| Library specs | Folder structure for libraries |
| Project decisions | Tech stack, conventions |

---

## TEMPLATE SECTIONS

### 1. Repository Overview
- Purpose of the repository
- Tech stack summary
- Key directories

### 2. Directory Structure
- Full tree view
- Explanation of each folder
- Where new code goes

### 3. Naming Conventions
- Files and folders
- Functions and variables
- Types and interfaces
- Error codes

### 4. Development Setup
- Prerequisites
- Installation steps
- Environment variables
- IDE setup

### 5. Build & Run
- Build commands
- Run locally
- Environment modes

### 6. Testing
- Test location
- Test naming
- Running tests
- Coverage requirements

### 7. Code Style
- Formatting rules
- Linting configuration
- Auto-formatting

### 8. Git Workflow
- Branch naming
- Commit messages
- PR process
- Merge strategy

### 9. Deployment
- Environments
- Deployment process
- Rollback procedure

### 10. Troubleshooting
- Common issues
- Debug tips
- Getting help

---

## RULES

### DO:
- Be specific and concrete
- Include actual commands
- Show real examples
- Keep it updated

### DO NOT:
- Duplicate content from system specs
- Include sensitive credentials
- Assume knowledge — explain everything
- Let it get stale

---

## MAINTENANCE

| Trigger | Action |
|---------|--------|
| New system added | Update directory structure |
| Convention changed | Update relevant section |
| New dependency | Update setup section |
| Process changed | Update workflow section |

---

## APPROVAL GATE

- [ ] All sections completed
- [ ] Commands tested and working
- [ ] Directory structure matches reality
- [ ] No sensitive information included
- [ ] Human approves

---

## END CONDITION

Phase 24 is COMPLETE when:
- [ ] REPO-SPEC.md is comprehensive
- [ ] All sections are accurate
- [ ] A new developer could onboard using it
- [ ] Human approves

**Next:** Phase 25 (Post-Spec Analysis)

---
Generated: {{timestamp}}
---
