# EXTERNAL INTEGRATION SERVICE BUILD — PROMPT

## ROLE
You are materialising approved services into a real folder structure inside existing provider folders.
Your job is to create service folders and populate each with a completed SERVICE-NOTES.md file.

You are NOT designing implementation, writing code, or adding new services.

---

## TASK

For each approved service from the Service Scope:
1. Create a folder inside the provider folder using the service name
2. Create a SERVICE-NOTES.md file inside that folder
3. Complete all sections based on the approved information and provider documentation

**Process one provider at a time.**

---

## FOLDER STRUCTURE

```
{{project-name}}/
├── integration-{{provider-name}}/
│   ├── PROVIDER-NOTES.md (already exists)
│   ├── {{service-name}}/              ← Created in this phase
│   │   └── SERVICE-NOTES.md           ← Created in this phase
│   ├── {{service-name}}/
│   │   └── SERVICE-NOTES.md
│   └── ...
```

**Naming rules:**
- Service folders: lowercase, hyphens (e.g., `drive-api`, `oauth`, `payments-api`)
- Use the common name for the service
- No prefix with provider name (just `drive-api`, not `google-drive-api`)
- Include API version if significant (e.g., `drive-api-v3` if v2 and v3 coexist)

---

## COMPLETING SERVICE-NOTES.md

For each section, derive content from:
- The approved Service Scope (purpose, consuming systems)
- The provider's API documentation (auth, limits, endpoints)
- The parent PROVIDER-NOTES.md (overall context)

### Service Identity
- Provider name, service name, API version
- Link to specific API documentation

### Purpose & Intent
- Pull from Service Scope "Purpose" and "Why Needed"
- Focus on what this service enables for YOUR project

### Consuming Systems
- Pull from Service Scope
- Reference actual system names

### In Scope / Out of Scope / Non-Goals
- What parts of the API will you use?
- What parts will you NOT use?
- Be explicit about boundaries

### Data Boundary
- What data will be sent to this service?
- What data will be received?
- Assess sensitivity

### Authentication
- Look up the specific auth requirements for this service
- Note required scopes
- Reference OAuth service if applicable

### Service Constraints
- Look up rate limits, quotas, pricing
- Be specific to THIS service, not the provider overall

### Failure Contract
- What failures are possible?
- Don't design handling — just document what to expect

---

## RULES

1. **Use ONLY the approved services** from Service Scope — no new services
2. **Do NOT change service names** — use as approved
3. **Do NOT design implementation** — no code, no error handling logic
4. **Do NOT store credentials** — only reference where they'll be stored
5. **Mark unknowns explicitly** — if you can't find rate limits, write "Unknown"
6. **Respect the ~1,500 LOC constraint** — each service wrapper should be focused

---

## PROCESS

1. Read the approved Service Scope for the target provider
2. For each approved service:
   - Create the service folder
   - Create SERVICE-NOTES.md
   - Complete all sections (research API docs as needed)
3. Update the parent PROVIDER-NOTES.md "Services Used" table
4. Present the full structure and file contents
5. Ask for approval
6. Repeat for next provider (if any)

---

## OUTPUT FORMAT

Return a single Markdown document showing:

1. The folder structure created
2. The complete contents of each SERVICE-NOTES.md file

```markdown
## Provider

{{project-name}}/integration-{{provider-name}}/

## Created Service Folders

{{project-name}}/integration-{{provider-name}}/
├── {{service-name}}/
│   └── SERVICE-NOTES.md
├── {{service-name}}/
│   └── SERVICE-NOTES.md
└── ...

---

## {{service-name}}/SERVICE-NOTES.md

(complete file contents)

---

## {{service-name}}/SERVICE-NOTES.md

(complete file contents)

---

(repeat for each service)
```

After presenting, ask:

```
Please review the created service folders and SERVICE-NOTES.md files
for {{Provider Name}}.
- Reply APPROVE to accept and proceed to the next provider (or Pass 0 if done)
- Reply REVISE with specific changes needed
```

---

## ON APPROVAL

When approved:
- Update each SERVICE-NOTES.md Status to: **APPROVED**
- Update Last Updated to: **{{current_timestamp}}**
- Ensure PROVIDER-NOTES.md "Services Used" table is updated
- If more providers have services → process next provider
- If all services done → proceed to: **Pass 0**

---

## INPUTS

Project Name:
{{project_name}}

Provider Name:
{{provider_name}}

Provider PROVIDER-NOTES.md:
{{provider_notes}}

Approved Service Scope:
{{service_scope}}

All System NOTES (for reference on consuming systems):
{{all_system_notes}}

Timestamp:
{{current_timestamp}}