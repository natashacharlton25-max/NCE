# Spec Approval Checklist

> **Purpose:** Index of updates required when approving a system spec
> **Type:** Reference Document
> **Status:** Active
> **Version:** 1.0.0

---

## Overview

When moving a system from placeholder (`_ModuleName`) to approved (`ModuleName`), use this checklist to ensure all cross-references and dependencies are updated.

---

## Pre-Approval Checklist

### 1. Spec Completeness
- [ ] All functions documented with Input/Output/Process
- [ ] Error codes defined
- [ ] Dependencies listed
- [ ] Version number assigned (start at 1.0.0)
- [ ] Remove underscore prefix from folder name

### 2. Move Files
```
FROM: systems/_ModuleName/ModuleName.md
TO:   systems/ModuleName/ModuleName.md
```

---

## Global Updates Required

### 3. Version Compatibility Matrix
**Update in:** Each spec that depends on the new module

Add to their `## Build Notes` section:
```markdown
### Version Compatibility
| Dependency | Min Version | Notes |
|------------|-------------|-------|
| NewModule | 1.0.0+ | [purpose] |
```

**Check these approved specs:**
| Spec | Likely Needs Update If... |
|------|---------------------------|
| ThemeGenerator | New module handles themes, outcomes, brands |
| OutlineGenerator | New module handles outlines, templates, themes |
| TemplateEngine | New module handles rendering, content, sections |
| FileManager | New module does file operations |
| Writer | New module does write operations |

---

### 4. Library Reference Integrity
**Update in:** Libraries that the new module reads/writes

Add to their `## Reference Integrity` section:
```markdown
**Dependents:**
| Referenced By | Field | On This Archive |
|---------------|-------|-----------------|
| NewModule | fieldName | action |
```

**Check these libraries:**
| Library | Update If Module... |
|---------|---------------------|
| _themes.library | Reads/writes themes |
| _outlines.library | Reads/writes outlines |
| _templates.library | Reads/writes templates |
| _brands.library | Reads brand data |
| _audiences.library | Reads audience data |
| _outcomes.library | Reads outcomes |
| _frameworks.library | Uses evidence-based or proprietary frameworks |
| _content-type-frameworks.library | Uses workbook type structures |
| _paragraph-structures.library | Uses paragraph structures (PEARL, SAFE, etc.) |
| _cognitive-types.library | Uses cognitive frameworks |
| _tones.library | Uses tone data |
| _voices.library | Uses voice data |

---

### 5. Constants Reference
**Update in:** `reference/_constants.md`

Add if the new module introduces:
- [ ] New status enums
- [ ] New rate limits
- [ ] New size limits
- [ ] New TTLs
- [ ] New thresholds

---

### 6. Librarian Cascade Rules
**Update in:** `systems/_Librarian/Librarian.md`

If module manages library data, add to cascade rules table:
```markdown
| Library | On Archive | Dependents Affected |
|---------|------------|---------------------|
| new-library | action | affected modules |
```

---

### 7. Error Codes
**Update in:** `reference/ERROR-CODES.md` (if exists)

Add module-specific error codes:
```markdown
| Code | Module | Description | Recovery |
|------|--------|-------------|----------|
| NEWMODULE_* | NewModule | ... | ... |
```

---

### 8. Cross-Module Integration Points

**Check if new module needs to be called by:**

| Caller | Integration Point | Update Location |
|--------|-------------------|-----------------|
| ThemeGenerator | After theme generation | ThemeGenerator.md |
| OutlineGenerator | After outline generation | OutlineGenerator.md |
| TemplateEngine | During/after rendering | TemplateEngine.md |
| FailureHandler | On errors from new module | FailureHandler.md |
| CacheHandler | If module results cacheable | CacheHandler.md |

**Check if new module needs to call:**

| Callee | When | Update Location |
|--------|------|-----------------|
| FileManager | Reading library data | New module spec |
| Writer | Writing data | New module spec |
| DatabaseHandler | Database operations | New module spec |
| PromptBuilder | Building AI prompts | New module spec |
| AICaller | Making AI requests | New module spec |
| Parsers | Parsing AI responses | New module spec |
| CacheHandler | Caching results | New module spec |
| FailureHandler | Error recovery | New module spec |

---

## Module-Specific Checklists

### If Module Handles AI Generation
- [ ] Add to PromptBuilder integration notes
- [ ] Add to AICaller integration notes
- [ ] Add to Parsers integration notes
- [ ] Document token budgets in _constants.md
- [ ] Document cache TTL in _constants.md

### If Module Handles Library Data
- [ ] Add Reference Integrity section to relevant library
- [ ] Add cascade rules to Librarian
- [ ] Document in DatabaseHandler notes
- [ ] Add to IngestionEngine if handles promotions

### If Module Validates Content
- [ ] Add to ContentValidator integration notes
- [ ] Document thresholds in _constants.md
- [ ] Add quality metrics if applicable

### If Module Handles Errors
- [ ] Add to FailureHandler integration notes
- [ ] Document retry policies in _constants.md
- [ ] Add error codes to ERROR-CODES.md

---

## Post-Approval Verification

### 9. Final Checks
- [ ] Spec file moved (no underscore)
- [ ] Version set to 1.0.0
- [ ] Status changed to "Approved"
- [ ] All dependent specs updated
- [ ] All relevant libraries updated
- [ ] Constants documented
- [ ] Cascade rules added (if applicable)
- [ ] Dev team gap notes removed or converted to Build Notes

### 10. Update Tracking
Add entry to approval log:
```markdown
| Date | Module | Version | Approved By | Notes |
|------|--------|---------|-------------|-------|
| YYYY-MM-DD | ModuleName | 1.0.0 | [name] | [notes] |
```

---

## Quick Reference: Placeholder Modules

Current placeholders awaiting approval:

| Module | Purpose | Key Dependencies |
|--------|---------|------------------|
| _PromptBuilder | Construct AI prompts | All generators, PromptCondenser |
| _PromptCondenser | Validate/condense prompts | PromptBuilder |
| _AICaller | LLM API calls | PromptBuilder, Parsers |
| _Parsers | Validate AI output | AICaller, all generators |
| _DatabaseHandler | SQLite operations | All modules with DB |
| _Librarian | Cross-library search | All libraries |
| _CacheHandler | Caching layer | Generators, queries |
| _FailureHandler | Error recovery | All modules |
| _ImageFieldRenderer | Image processing | TemplateEngine |
| _IngestionEngine | Draft promotion | All libraries |
| _PostCreationCheckManager | Orchestrate quality checks | All checkers |
| _BrandCheck | Brand voice/terminology | PostCreationCheckManager |
| _ValuesCheck | Values/principles alignment | PostCreationCheckManager |
| _ContentAlignmentCheck | Audience/theme/tone/structure | PostCreationCheckManager |
| _TransformationCheck | Belief journey/promises | PostCreationCheckManager |
| _SpellingCheck | Spelling validation (Python) | PostCreationCheckManager |
| _GrammarCheck | Grammar validation (Python) | PostCreationCheckManager |
| _LayoutCheck | Structure/formatting | PostCreationCheckManager |
| _VisualCheck | Images/colors/graphics | PostCreationCheckManager |

---

*Created: 2026-01-20*
*Status: Active*
