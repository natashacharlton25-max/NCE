# NCE-V2 CI/CD Pipeline

---
Status: DRAFT | APPROVED | OPERATIONAL
Version: v0.1.0
Last Updated: {{timestamp}}
Methodology Stage: 5a
---

## Branch Protection (on `main`)

- [ ] Require PR before merge
- [ ] Require ≥1 approving review
- [ ] Require status checks: typecheck, lint, test, build-dry-run, coverage
- [ ] Require linear history
- [ ] No force pushes
- [ ] Branch up-to-date before merge

## CI Workflow (`.github/workflows/ci.yml`)

Triggered on: PR + push to main.

| Step | Command | Fail On |
|---|---|---|
| Install | `pnpm install --frozen-lockfile` | Lockfile mismatch |
| Typecheck | `pnpm tsc --noEmit` | Any TS error |
| Lint | `pnpm biome check .` | Lint errors |
| Unit + Integration tests | `pnpm vitest run` | Any failing test |
| Coverage | (vitest reporter) | <80% line coverage |
| Build dry-run | `wrangler deploy --dry-run` per Worker | Build error |
| D1 migration validation | `wrangler d1 migrations apply --local` (ephemeral D1) | SQL error |

## CD Workflow (`.github/workflows/cd.yml`)

Triggered on: merge to `main`.

| Step | Command |
|---|---|
| Deploy to staging (per Worker) | `wrangler deploy --env staging` |
| Apply D1 migrations to staging | `wrangler d1 migrations apply <library>-staging` per library |
| Run E2E tests against staging | `pnpm playwright test --base-url=https://staging.nce-v2.example` |
| Tag RC | `git tag v${VERSION}-rc${N} && git push --tags` |
| Notify | Slack/email — RC tagged, hold for prod approval |

## Release Candidate

| Field | Value |
|---|---|
| **RC tag** | `v{{X.Y.Z}}-rc{{N}}` |
| **Commit SHA** | `{{...}}` |
| **Staging deployed** | {{timestamp}} |
| **E2E pass** | ☐ |
| **Approved for prod** | ☐ (human signs) |

---

## Sign-off

| Role | Name | Date |
|---|---|---|
| DevOps | | |
| Tech lead | | |

---
