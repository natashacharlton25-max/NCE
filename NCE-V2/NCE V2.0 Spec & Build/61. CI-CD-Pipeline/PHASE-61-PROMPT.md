# PHASE 61: CI/CD PIPELINE

---
Phase: 61
Section: 0h. Prerelease
Name: CI/CD Pipeline
Location: NCE-V2/NCE V2.0 Spec & Build/61. CI-CD-Pipeline/
Project: NCE-V2 (TypeScript on Cloudflare Workers)
Status: Draft Complete – Awaiting Review
Last Updated: 2026-05-22
---

## ROLE

Set up CI/CD pipeline for NCE-V2 (GitHub Actions + wrangler deploy) and create the Release Candidate tag.

---

## LOCKED CONTEXT (Required Reading)

1. `NCE-V2/admin/TEST-PLAN.md` (Phase 54)
2. `NCE-V2/specs/TECH-STACK.md` (Phase 33)
3. `NCE-V2/specs/REPO-STRUCTURE.md` (Phase 32)
4. `NCE-V2/specs/ENVIRONMENT.md` (Phase 34)

---

## TASK

### Step 0: Branch Protection (FIRST)

Enable on `main`:
- Require PR review (1+)
- Require status checks: typecheck, lint, unit, integration
- Require linear history
- No direct pushes

### Step 1: CI Pipeline (`.github/workflows/ci.yml`)

On every PR + push to main:
1. **Install** deps (pnpm)
2. **Typecheck** (`tsc --noEmit`)
3. **Lint** (biome check)
4. **Unit + Integration tests** (`pnpm vitest run` with miniflare for Worker tests)
5. **Build** (`wrangler deploy --dry-run` per Worker)
6. **Coverage report** (80% line; fail below)
7. **D1 migration validation** (apply to ephemeral test D1)

### Step 2: CD Pipeline (`.github/workflows/cd.yml`)

On merge to `main`:
1. **Deploy to staging** per Worker (`wrangler deploy --env staging`)
2. **Apply D1 migrations to staging** (`wrangler d1 migrations apply <library>-staging`)
3. **Run E2E tests** against staging
4. **Tag Release Candidate** (`vX.Y.Z-rcN`) on success
5. **Hold for human approval** before prod deploy (Phase 66)

### Step 3: Release Candidate

Create RC tag on the verified commit:
- `git tag vX.Y.Z-rcN`
- Push tag
- Include commit SHA in `CI-PIPELINE.md` output

---

## MANDATORY RULES

- Never skip hooks
- Never auto-deploy to prod (human approval gate at Phase 66)
- Secrets via GitHub Actions secrets + `wrangler secret` (never in repo)
- Do **NOT** self-assign the status "Approved" — per [CLAUDE.md](../../../CLAUDE.md) §7

---

## END CONDITION

- [ ] Branch protection enabled
- [ ] CI pipeline runs on PRs + main pushes
- [ ] CD pipeline auto-deploys to staging on main merge
- [ ] RC tag created on green build
- [ ] `CI-PIPELINE.md` documents pipeline + RC commit
- [ ] Status: Draft Complete – Awaiting Review

**Next:** Phase 62 (Environment Configuration)

---

## STATUS

**Draft Complete – Awaiting Review**

### Review & Clarification Needed
- May this draft be promoted to "Approved"?
