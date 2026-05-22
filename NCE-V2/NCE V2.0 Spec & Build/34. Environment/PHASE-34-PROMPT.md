# PHASE 34: ENVIRONMENT

---
Phase: 34
Name: Environment
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are defining environment configuration for all deployment stages.

This ensures consistent configuration across development, staging, and production.

---

## TASK

1. Define environments (dev, staging, prod)
2. Document environment variables
3. Document configuration values per environment
4. Define secrets management approach
5. Get approval

---

## ENVIRONMENTS

Standard environments:

| Environment | Purpose | Typical Usage |
|-------------|---------|---------------|
| Development | Local development | Individual developer machines |
| Staging | Pre-production testing | QA, integration testing |
| Production | Live system | End users |

Additional environments as needed:
- Preview (PR deployments)
- Testing (automated tests)

---

## CONFIGURATION CATEGORIES

### Environment Variables

Variables that differ per environment:

| Variable | Dev | Staging | Prod |
|----------|-----|---------|------|
| `NODE_ENV` | development | staging | production |
| `DATABASE_URL` | local | staging-db | prod-db |
| `LOG_LEVEL` | debug | info | warn |

### Feature Flags

Features that can be toggled:

| Flag | Dev | Staging | Prod |
|------|-----|---------|------|
| `ENABLE_DEBUG` | true | true | false |
| `ENABLE_ANALYTICS` | false | true | true |

### Secrets

Sensitive values (document reference, not value):

| Secret | Purpose | Storage |
|--------|---------|---------|
| `DATABASE_PASSWORD` | DB auth | Secrets manager |
| `API_KEY` | External API | Secrets manager |

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| TECH-STACK.md | Phase 33 | Platform determines config approach |
| Security requirements | Project docs | Secrets handling |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| ENVIRONMENT.md | Project root | Environment configuration |

---

## TEMPLATES

- ENVIRONMENT-TEMPLATE.md

---

## RULES

- Never include actual secret values
- Document all environment differences
- Provide sensible defaults where possible
- Consider security for each setting

---
