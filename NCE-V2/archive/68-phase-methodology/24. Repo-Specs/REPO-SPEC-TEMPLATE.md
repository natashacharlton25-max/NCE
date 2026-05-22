# {{Project Name}} — Repository Specification

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## 1. Repository Overview

### Purpose

{{2-3 sentences describing what this repository contains and its purpose}}

### Tech Stack

| Layer | Technology | Version |
|-------|------------|---------|
| Language | {{language}} | {{version}} |
| Runtime | {{runtime}} | {{version}} |
| Framework | {{framework}} | {{version}} |
| Database | {{database}} | {{version}} |
| Testing | {{test framework}} | {{version}} |

### Key Characteristics

| Characteristic | Value |
|----------------|-------|
| Architecture | {{monolith / microservices / modular monolith}} |
| Primary language | {{language}} |
| Package manager | {{npm / yarn / pip / cargo / etc.}} |
| Build system | {{build tool}} |

---

## 2. Directory Structure

```
{{project_name}}/
│
├── README.md                   # Quick start guide
├── REPO-SPEC.md               # This file
├── package.json               # Dependencies (or equivalent)
├── tsconfig.json              # TypeScript config (if applicable)
├── .env.example               # Environment template
├── .gitignore                 # Git ignore rules
│
├── 0. Admin/                  # Project documentation
│   ├── 0a. Project Overview/  # High-level project docs
│   ├── 0b. Pass Phases/       # Pass phase documentation
│   └── 0c. Full Specs/        # Specification documents
│
├── src/                       # Source code
│   │
│   ├── systems/               # System implementations
│   │   ├── {{system-a}}/
│   │   │   ├── spec/          # System specifications
│   │   │   ├── src/           # System source code
│   │   │   ├── tests/         # System tests
│   │   │   └── index.ts       # Public exports
│   │   │
│   │   └── {{system-b}}/
│   │       └── ...
│   │
│   ├── lib/                   # Shared libraries
│   │   ├── lib-{{name}}/
│   │   │   ├── spec/
│   │   │   ├── src/
│   │   │   ├── tests/
│   │   │   └── index.ts
│   │   │
│   │   └── lib-{{name}}/
│   │       └── ...
│   │
│   ├── integrations/          # External integrations
│   │   ├── integration-{{provider}}-{{service}}/
│   │   │   ├── spec/
│   │   │   ├── src/
│   │   │   ├── tests/
│   │   │   └── index.ts
│   │   │
│   │   └── ...
│   │
│   ├── shared/                # Shared types, constants
│   │   ├── types/
│   │   ├── constants/
│   │   └── errors/
│   │
│   └── main.ts                # Entry point
│
├── tests/                     # Integration/E2E tests
│   ├── integration/
│   └── e2e/
│
├── scripts/                   # Build/deploy scripts
│   ├── build.sh
│   ├── deploy.sh
│   └── setup.sh
│
├── config/                    # Configuration files
│   ├── default.json
│   ├── development.json
│   ├── production.json
│   └── test.json
│
└── docs/                      # Additional documentation
    ├── architecture/
    ├── api/
    └── guides/
```

### Directory Explanations

| Directory | Purpose | Contents |
|-----------|---------|----------|
| `0. Admin/` | Project documentation | Specs, decisions, guides |
| `src/systems/` | System implementations | One folder per system |
| `src/lib/` | Shared libraries | Reusable utilities |
| `src/integrations/` | External integrations | Third-party API wrappers |
| `src/shared/` | Shared code | Types, constants, errors |
| `tests/` | Cross-system tests | Integration and E2E |
| `scripts/` | Automation | Build, deploy, setup |
| `config/` | Configuration | Environment configs |

### Where New Code Goes

| Type of Code | Location |
|--------------|----------|
| New system | `src/systems/{{system-name}}/` |
| New subsystem | `src/systems/{{parent}}/{{subsystem}}/` |
| New library | `src/lib/lib-{{name}}/` |
| New integration | `src/integrations/integration-{{provider}}-{{service}}/` |
| Shared types | `src/shared/types/` |
| Shared errors | `src/shared/errors/` |
| Integration tests | `tests/integration/` |
| E2E tests | `tests/e2e/` |

---

## 3. Naming Conventions

### Files and Folders

| Type | Convention | Example |
|------|------------|---------|
| System folder | `kebab-case` | `user-management/` |
| Subsystem folder | `kebab-case` | `authentication/` |
| Library folder | `lib-kebab-case` | `lib-validation/` |
| Source files | `kebab-case.ts` | `user-service.ts` |
| Test files | `{{name}}.test.ts` | `user-service.test.ts` |
| Type files | `{{name}}.types.ts` | `user.types.ts` |
| Index files | `index.ts` | `index.ts` |

### Code Identifiers

| Type | Convention | Example |
|------|------------|---------|
| Functions | `camelCase` | `createUser()` |
| Variables | `camelCase` | `userData` |
| Constants | `SCREAMING_SNAKE_CASE` | `MAX_RETRY_COUNT` |
| Classes | `PascalCase` | `UserService` |
| Interfaces | `PascalCase` | `UserData` |
| Types | `PascalCase` | `UserId` |
| Enums | `PascalCase` | `UserStatus` |
| Enum values | `PascalCase` | `UserStatus.Active` |

### Error Codes

| Component Type | Prefix | Example |
|----------------|--------|---------|
| System | `{{SYS}}` | `USR_001` (User system) |
| Integration | `{{INT}}_{{PROVIDER}}` | `INT_STRIPE_001` |
| Library | `LIB_{{NAME}}` | `LIB_VAL_001` |
| Shared | `SHARED` | `SHARED_001` |

### Git Branches

| Type | Pattern | Example |
|------|---------|---------|
| Feature | `feature/{{ticket}}-{{description}}` | `feature/ABC-123-add-auth` |
| Bug fix | `fix/{{ticket}}-{{description}}` | `fix/ABC-456-login-error` |
| Hotfix | `hotfix/{{description}}` | `hotfix/critical-security` |
| Release | `release/{{version}}` | `release/1.2.0` |

---

## 4. Development Setup

### Prerequisites

| Requirement | Version | Installation |
|-------------|---------|--------------|
| {{runtime}} | {{version}}+ | {{install command or link}} |
| {{package manager}} | {{version}}+ | {{install command}} |
| {{database}} | {{version}}+ | {{install command}} |
| {{other tool}} | {{version}}+ | {{install command}} |

### Installation

```bash
# 1. Clone the repository
git clone {{repo_url}}
cd {{project_name}}

# 2. Install dependencies
{{package_manager}} install

# 3. Copy environment template
cp .env.example .env

# 4. Configure environment variables
# Edit .env with your values (see Environment Variables section)

# 5. Setup database (if applicable)
{{database_setup_command}}

# 6. Run setup script
./scripts/setup.sh

# 7. Verify installation
{{package_manager}} run verify
```

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `NODE_ENV` | Yes | `development` | Environment mode |
| `PORT` | No | `3000` | Server port |
| `DATABASE_URL` | Yes | — | Database connection string |
| `LOG_LEVEL` | No | `info` | Logging level |
| `{{VAR}}` | Yes/No | {{default}} | {{description}} |

**.env.example:**
```bash
# Environment
NODE_ENV=development
PORT=3000

# Database
DATABASE_URL=postgresql://localhost:5432/{{project}}

# Logging
LOG_LEVEL=debug

# External Services
{{SERVICE}}_API_KEY=your_key_here
```

### IDE Setup

#### VS Code (Recommended)

Install extensions:
- {{extension 1}}
- {{extension 2}}
- {{extension 3}}

Recommended settings (`.vscode/settings.json`):
```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "{{formatter}}",
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

---

## 5. Build & Run

### Commands

| Command | Description |
|---------|-------------|
| `{{pm}} run dev` | Run in development mode (hot reload) |
| `{{pm}} run build` | Build for production |
| `{{pm}} run start` | Run production build |
| `{{pm}} run clean` | Clean build artifacts |

### Development Mode

```bash
# Start development server with hot reload
{{pm}} run dev

# Server runs at http://localhost:{{port}}
```

### Production Build

```bash
# Build
{{pm}} run build

# Run production
{{pm}} run start
```

### Environment Modes

| Mode | Config File | Use Case |
|------|-------------|----------|
| `development` | `config/development.json` | Local development |
| `test` | `config/test.json` | Running tests |
| `staging` | `config/staging.json` | Pre-production |
| `production` | `config/production.json` | Live environment |

---

## 6. Testing

### Test Structure

```
tests/
├── unit/                      # Unit tests (co-located with source)
├── integration/               # Integration tests
│   ├── systems/
│   └── integrations/
└── e2e/                       # End-to-end tests
    ├── scenarios/
    └── fixtures/

src/systems/{{system}}/
├── src/
│   └── {{file}}.ts
└── tests/
    └── {{file}}.test.ts       # Unit tests next to source
```

### Running Tests

| Command | Description |
|---------|-------------|
| `{{pm}} test` | Run all tests |
| `{{pm}} test:unit` | Run unit tests only |
| `{{pm}} test:integration` | Run integration tests |
| `{{pm}} test:e2e` | Run E2E tests |
| `{{pm}} test:coverage` | Run with coverage report |
| `{{pm}} test:watch` | Run in watch mode |

### Test Naming

| Test Type | Pattern | Example |
|-----------|---------|---------|
| Unit test | `{{function}} should {{expected behaviour}}` | `createUser should return user with id` |
| Integration | `{{system}} {{action}} {{outcome}}` | `UserSystem creates user in database` |
| E2E | `{{scenario}} {{flow}}` | `User registration flow completes` |

### Coverage Requirements

| Metric | Minimum | Target |
|--------|---------|--------|
| Line coverage | 80% | 90% |
| Branch coverage | 75% | 85% |
| Function coverage | 80% | 90% |

---

## 7. Code Style

### Formatting

| Tool | Config File | Command |
|------|-------------|---------|
| {{formatter}} | `.{{formatter}}rc` | `{{pm}} run format` |
| {{linter}} | `.{{linter}}rc.js` | `{{pm}} run lint` |

### Auto-format on Save

Enabled by default in VS Code settings.

### Pre-commit Hooks

```bash
# Installed automatically via husky
# Runs on every commit:
# 1. Lint staged files
# 2. Run affected tests
# 3. Check types
```

### Style Rules

| Rule | Setting |
|------|---------|
| Indent | {{spaces/tabs}} ({{n}}) |
| Quotes | {{single/double}} |
| Semicolons | {{yes/no}} |
| Trailing commas | {{es5/all/none}} |
| Max line length | {{n}} |

---

## 8. Git Workflow

### Branch Strategy

```
main (production)
  │
  ├── develop (integration)
  │     │
  │     ├── feature/ABC-123-feature-name
  │     ├── feature/ABC-456-another-feature
  │     └── fix/ABC-789-bug-fix
  │
  └── hotfix/critical-fix (emergency only)
```

### Commit Messages

Format: `{{type}}({{scope}}): {{description}}`

| Type | Use For |
|------|---------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation |
| `style` | Formatting (no code change) |
| `refactor` | Code change (no feature/fix) |
| `test` | Adding tests |
| `chore` | Maintenance |

Examples:
```
feat(user-system): add password reset
fix(auth): correct token expiry check
docs(readme): update setup instructions
```

### Pull Request Process

1. Create branch from `develop`
2. Make changes, commit with conventional messages
3. Push branch, open PR
4. Fill PR template
5. Request review
6. Address feedback
7. Squash and merge when approved

### PR Template

```markdown
## Description
{{What does this PR do?}}

## Type
- [ ] Feature
- [ ] Bug fix
- [ ] Refactor
- [ ] Documentation

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guide
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No breaking changes (or documented)
```

---

## 9. Deployment

### Environments

| Environment | Branch | URL | Auto-deploy |
|-------------|--------|-----|-------------|
| Development | `develop` | {{dev_url}} | Yes |
| Staging | `release/*` | {{staging_url}} | Yes |
| Production | `main` | {{prod_url}} | Manual |

### Deployment Commands

```bash
# Deploy to staging
./scripts/deploy.sh staging

# Deploy to production
./scripts/deploy.sh production
```

### Deployment Checklist

- [ ] All tests passing
- [ ] Code reviewed and approved
- [ ] Changelog updated
- [ ] Database migrations ready
- [ ] Feature flags configured
- [ ] Monitoring alerts set

### Rollback

```bash
# Rollback to previous version
./scripts/rollback.sh production

# Rollback to specific version
./scripts/rollback.sh production v1.2.3
```

---

## 10. Troubleshooting

### Common Issues

#### Issue: `{{error message}}`

**Cause:** {{explanation}}

**Solution:**
```bash
{{fix command}}
```

---

#### Issue: Tests failing locally

**Cause:** Database not running or not seeded

**Solution:**
```bash
# Start database
{{start_db_command}}

# Seed test data
{{pm}} run db:seed:test
```

---

#### Issue: Type errors after pulling

**Cause:** Dependencies updated

**Solution:**
```bash
# Clean install
rm -rf node_modules
{{pm}} install
```

---

### Debug Tips

| Scenario | Tip |
|----------|-----|
| API issues | Check `LOG_LEVEL=debug` |
| Database issues | Check connection string |
| Test failures | Run single test with `--verbose` |
| Build issues | Run `{{pm}} run clean` first |

### Getting Help

| Resource | Location |
|----------|----------|
| Documentation | `docs/` folder |
| System specs | `src/systems/{{system}}/spec/` |
| Team channel | {{slack/teams channel}} |
| Issue tracker | {{issue tracker url}} |

---

## Appendix

### Useful Commands Reference

```bash
# Development
{{pm}} run dev              # Start dev server
{{pm}} run build            # Build
{{pm}} test                 # Run tests

# Code quality
{{pm}} run lint             # Lint code
{{pm}} run format           # Format code
{{pm}} run typecheck        # Check types

# Database
{{pm}} run db:migrate       # Run migrations
{{pm}} run db:seed          # Seed data
{{pm}} run db:reset         # Reset database

# Utilities
{{pm}} run clean            # Clean build
{{pm}} run verify           # Verify setup
```

---
Phase: 24 (Repo Specs)
Generated: {{timestamp}}
---
