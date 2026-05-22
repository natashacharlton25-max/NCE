# EXTERNAL INTEGRATION PROVIDER BUILD — PROMPT

## ROLE
You are materialising approved external integration providers into a real folder structure.
Your job is to create provider folders and populate each with a completed PROVIDER-NOTES.md file.

You are NOT designing implementation, writing code, or selecting services yet.

---

## TASK

For each approved provider from the External Integrations Register:
1. Create a folder at the project root using the provider name (prefixed with `integration-`)
2. Create a PROVIDER-NOTES.md file inside that folder
3. Complete all sections based on the approved information

**This phase runs AFTER Subsystem Build and BEFORE Service Scope.**

---

## FOLDER STRUCTURE

```
{{project-name}}/
├── {{system-name}}/
│   └── SYSTEM-NOTES.md
├── {{system-name}}/
│   └── ...
├── integration-{{provider-name}}/        ← Created in this phase
│   └── PROVIDER-NOTES.md                 ← Created in this phase
├── integration-{{provider-name}}/
│   └── PROVIDER-NOTES.md
└── admin/
    └── EXTERNAL-INTEGRATIONS-REGISTER.md
```

**Naming rules:**
- Provider folders: `integration-` prefix + provider name, lowercase, hyphens
- Examples: `integration-google`, `integration-stripe`, `integration-canva`
- Use the provider's common name, not the parent company (e.g., `integration-stripe` not `integration-stripe-inc`)

---

## COMPLETING PROVIDER-NOTES.md

For each section, derive content from:
- The External Integrations Register (capability, why needed, consuming systems)
- Provider's public website and documentation
- Your knowledge of the provider

### Provider Identity
- Official provider name
- Links to website, developer portal, API docs

### Purpose & Intent
- Pull from the "Capability Needed" and "Why Needed" in the Register
- Focus on what the provider enables for YOUR project

### Services Used
- Leave as placeholder table — will be filled in during Service Scope phase
- Write "TBD — to be identified in Service Scope"

### Services NOT Used
- If already known, list explicit exclusions
- Otherwise write "TBD"

### Consuming Systems
- Pull from the Register
- Reference the actual system names from your project

### Account & Access
- Note account type if known
- **Never store credentials** — only reference where they'll be stored

### Provider Constraints
- Look up pricing, rate limits from provider's public docs
- If unknown, write "Unknown — verify before implementation"

### Compliance & Risk
- Note any known compliance certifications
- Assess vendor lock-in risk

---

## RULES

1. **Use ONLY the approved providers** from the Register — no new providers
2. **Use the provider name specified by the user** — don't rename
3. **Do NOT select services yet** — that's the next phase (Service Scope)
4. **Do NOT design implementation** — no code, no architecture
5. **Do NOT store credentials** — only reference where they'll be stored
6. **Mark unknowns explicitly** — if you can't find info, write "Unknown"

---

## WHEN TO SKIP THIS PHASE

If the External Integrations Register shows "None identified":
- Skip this phase entirely
- Skip Service Scope and Service Build
- Proceed directly to Pass 0

---

## PROCESS

1. Read the approved External Integrations Register
2. For each provider with confirmed selection:
   - Create the provider folder (`integration-{{provider}}`)
   - Create PROVIDER-NOTES.md
   - Complete all sections (research provider docs as needed)
3. Present the full structure and file contents
4. Ask for approval

---

## OUTPUT FORMAT

Return a single Markdown document showing:

1. The folder structure created
2. The complete contents of each PROVIDER-NOTES.md file

```markdown
## External Integration Provider Folders Created

{{project-name}}/
├── integration-{{provider-name}}/
│   └── PROVIDER-NOTES.md
├── integration-{{provider-name}}/
│   └── PROVIDER-NOTES.md
└── ...

---

## integration-{{provider-name}}/PROVIDER-NOTES.md

(complete file contents)

---

## integration-{{provider-name}}/PROVIDER-NOTES.md

(complete file contents)

---

(repeat for each provider)
```

After presenting, ask:

```
Please review the created provider folders and PROVIDER-NOTES.md files.
- Reply APPROVE to accept and proceed to Service Scope
- Reply REVISE with specific changes needed
```

---

## ON APPROVAL

When approved:
- Update each PROVIDER-NOTES.md Status to: **APPROVED**
- Update Last Updated to: **{{current_timestamp}}**
- Confirm next step: **External Integration Service Scope**

---

## INPUTS

Project Name:
{{project_name}}

Approved External Integrations Register:
{{external_integrations_register}}

All System NOTES (for reference on consuming systems):
{{all_system_notes}}

Timestamp:
{{current_timestamp}}