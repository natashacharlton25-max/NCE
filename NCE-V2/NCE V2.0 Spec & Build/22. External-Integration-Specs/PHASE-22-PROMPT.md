# PHASE 22: EXTERNAL INTEGRATION SPECS

---
Phase: 22
Name: External Integration Specs
Section: 0c. Full Specs
Location: 0. Admin/0c. Full Specs/22.External-Integration-Specs/
---

## ROLE

You are writing implementation-ready specifications for external integrations (third-party APIs and services).

This phase takes PRE-SPEC-NOTES.md (external template) and expands it into spec files.

**Key difference from internal specs:** We don't control external APIs. We document THEIR surface and OUR wrapper around it.

---

## PROVIDER/SERVICE HIERARCHY

External integrations use a **two-level hierarchy**:

```
integrations/
├── stripe/                    # PROVIDER (system-level)
│   ├── PROVIDER.md            # Shared config: auth, base URL, rate limits
│   ├── payments/              # SERVICE (subsystem-level)
│   │   └── spec/
│   │       ├── INDEX.md
│   │       └── ... (9 files)
│   ├── billing/               # Another service
│   │   └── spec/
│   └── identity/              # Another service
│       └── spec/
│
├── supabase/                  # Another provider
│   ├── PROVIDER.md
│   ├── auth/
│   ├── database/
│   └── storage/
```

### Provider = System-Level
- Owns shared configuration (API keys, base URL, auth method)
- Contains PROVIDER.md with authentication, rate limits, webhooks
- Groups related services together

### Service = Subsystem-Level
- Specific API surface (endpoints, operations)
- Own field mappings, errors, constraints
- Full 9-file spec structure

---

## TASK

**For EACH provider:**

1. Create PROVIDER.md using PROVIDER-TEMPLATE.md
2. For each service under the provider:
   - Read service's PRE-SPEC-NOTES.md
   - Create `spec/` folder
   - Create all 9 spec files
3. Get approval before moving to next provider

---

## PROCESS ORDER

```
Provider: stripe/
├── Create PROVIDER.md (shared config)
├── Service: payments/
│   ├── Read PRE-SPEC-NOTES.md
│   ├── Create spec/INDEX.md
│   ├── Create spec/OVERVIEW.md
│   ├── Create spec/API-SURFACE.md      ← Their API
│   ├── Create spec/OUR-WRAPPER.md      ← Our functions
│   ├── Create spec/FIELD-MAPPING.md    ← Translation
│   ├── Create spec/ERRORS.md           ← Their errors → ours
│   ├── Create spec/CONSTRAINTS.md      ← Rate limits, quotas
│   ├── Create spec/FAILURE-MODES.md    ← What can fail
│   ├── Create spec/EXAMPLES.md
│   └── Get approval
├── Service: billing/
│   └── ... same pattern
└── Service: identity/
    └── ... same pattern

Provider: supabase/
├── Create PROVIDER.md
├── Service: auth/
├── Service: database/
└── Service: storage/
```

**Order:**
1. By provider (alphabetically)
2. PROVIDER.md first (shared config)
3. Then services within provider (alphabetically)

---

## SPEC FILES

### Provider Level (1 file)

| File | Purpose | Target LOC |
|------|---------|------------|
| PROVIDER.md | Shared auth, URLs, rate limits, webhooks | ~150 |

### Service Level (9 files)

| File | Purpose | Source (PRE-SPEC Section) | Target LOC |
|------|---------|---------------------------|------------|
| INDEX.md | Quick reference, status | 1, 4, 12 | ~80 |
| OVERVIEW.md | Purpose, what we use it for | 1, 2, 4 | ~100 |
| API-SURFACE.md | Their API (endpoints we call) | 5 | ~200 |
| OUR-WRAPPER.md | Our functions wrapping their API | 3, 4 | ~250 |
| FIELD-MAPPING.md | Their fields ↔ our fields | 6 | ~150 |
| ERRORS.md | Their error codes → our error codes | 7 | ~150 |
| CONSTRAINTS.md | Service-specific limits, quotas | 8 | ~100 |
| FAILURE-MODES.md | What can fail, degradation | 9 | ~100 |
| EXAMPLES.md | Full request/response examples | New | ~200 |

---

## KEY DIFFERENCES FROM INTERNAL SPECS

| Aspect | Internal Component | External Integration |
|--------|-------------------|---------------------|
| **Who controls API** | We do | They do |
| **API spec** | FUNCTIONS.md (we define) | API-SURFACE.md (they define) + OUR-WRAPPER.md (we define) |
| **Types** | TYPES.md (we define) | FIELD-MAPPING.md (theirs → ours) |
| **Errors** | ERRORS.md (our codes) | ERRORS.md (their codes → our codes) |
| **Config** | CONFIG.md (our settings) | PROVIDER.md (shared) + CONSTRAINTS.md (service-specific) |
| **Behaviour** | BEHAVIOUR.md (our flows) | FAILURE-MODES.md (their failures) |
| **Storage** | STORAGE.md | N/A (they store it) |

---

## MANDATORY RULES

- Do NOT invent API endpoints — document what they actually provide
- Do NOT invent functionality not in PRE-SPEC-NOTES.md
- Do NOT skip files (mark N/A if not applicable)
- If information is missing from provider docs, mark as "Unknown — verify"
- Reference PRE-SPEC-NOTES.md section numbers
- Never include actual credentials or API keys
- Provider-level config goes in PROVIDER.md, not repeated per service

---

## OUTPUT LOCATION

```
{{project}}/
└── integrations/
    └── {{provider}}/
        ├── PROVIDER.md              ← Provider-level config
        │
        └── {{service}}/
            └── spec/
                ├── INDEX.md
                ├── OVERVIEW.md
                ├── API-SURFACE.md
                ├── OUR-WRAPPER.md
                ├── FIELD-MAPPING.md
                ├── ERRORS.md
                ├── CONSTRAINTS.md
                ├── FAILURE-MODES.md
                └── EXAMPLES.md
```

---

## APPROVAL GATE

Per provider:
- [ ] PROVIDER.md created
- [ ] All services have 9 spec files (or marked N/A)
- [ ] INDEX.md shows all files status per service
- [ ] No critical gaps
- [ ] Human approves

---

## END CONDITION

Phase 22 is COMPLETE only when:
- [ ] Every provider has PROVIDER.md
- [ ] Every service has spec/ folder with all files
- [ ] All approved by human
- [ ] All issues logged in INDEX.md or resolved

**Next:** Phase 23 (Library Specs)

---

## TEMPLATES

**Provider Level:**
- PROVIDER-TEMPLATE.md (new)

**Service Level (9 files):**
- INDEX-TEMPLATE.md (reuse internal)
- OVERVIEW-TEMPLATE.md (reuse internal)
- API-SURFACE-TEMPLATE.md
- OUR-WRAPPER-TEMPLATE.md
- FIELD-MAPPING-TEMPLATE.md
- ERRORS-TEMPLATE.md (modified for mapping)
- CONSTRAINTS-TEMPLATE.md
- FAILURE-MODES-TEMPLATE.md
- EXAMPLES-TEMPLATE.md (reuse internal)

---

## INPUTS

From Phase 20:
- PRE-SPEC-NOTES.md (external template)

From earlier phases:
- EXTERNAL-INTEGRATION-NOTES.md
- Provider documentation (external)

---
Generated: {{timestamp}}
---
