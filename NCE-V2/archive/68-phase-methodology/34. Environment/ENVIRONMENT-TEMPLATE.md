# Environment Configuration

---
Project: {{Project Name}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Environments

| Environment | Purpose | URL Pattern |
|-------------|---------|-------------|
| Development | Local development | `localhost:{{port}}` |
| Staging | Pre-production | `staging.{{domain}}` |
| Production | Live system | `{{domain}}` |

---

## Environment Variables

### Application

| Variable | Description | Dev | Staging | Prod |
|----------|-------------|-----|---------|------|
| `NODE_ENV` | Runtime environment | `development` | `staging` | `production` |
| `PORT` | Application port | `3000` | `3000` | `443` |
| `HOST` | Application host | `localhost` | `0.0.0.0` | `0.0.0.0` |
| `BASE_URL` | Public URL | `http://localhost:3000` | `https://staging.{{domain}}` | `https://{{domain}}` |

### Database

| Variable | Description | Dev | Staging | Prod |
|----------|-------------|-----|---------|------|
| `DATABASE_URL` | Connection string | `{{local_url}}` | `{{staging_url}}` | `{{prod_url}}` |
| `DATABASE_POOL_SIZE` | Connection pool | `5` | `10` | `20` |

### Logging

| Variable | Description | Dev | Staging | Prod |
|----------|-------------|-----|---------|------|
| `LOG_LEVEL` | Minimum log level | `debug` | `info` | `warn` |
| `LOG_FORMAT` | Output format | `pretty` | `json` | `json` |

### External Services

| Variable | Description | Dev | Staging | Prod |
|----------|-------------|-----|---------|------|
| `{{SERVICE}}_URL` | Service endpoint | `{{dev_url}}` | `{{staging_url}}` | `{{prod_url}}` |
| `{{SERVICE}}_TIMEOUT` | Request timeout | `5000` | `5000` | `3000` |

---

## Feature Flags

| Flag | Description | Dev | Staging | Prod |
|------|-------------|-----|---------|------|
| `ENABLE_DEBUG_MODE` | Show debug info | `true` | `true` | `false` |
| `ENABLE_ANALYTICS` | Track analytics | `false` | `true` | `true` |
| `ENABLE_RATE_LIMIT` | Enable rate limiting | `false` | `true` | `true` |
| `{{FLAG}}` | {{description}} | {{dev}} | {{staging}} | {{prod}} |

---

## Secrets

**⚠️ Never commit actual secret values**

| Secret | Purpose | Dev Source | Staging/Prod Source |
|--------|---------|------------|---------------------|
| `DATABASE_PASSWORD` | Database authentication | `.env.local` | Secrets manager |
| `JWT_SECRET` | Token signing | `.env.local` | Secrets manager |
| `API_KEY_{{SERVICE}}` | External service auth | `.env.local` | Secrets manager |

### Secrets Management

| Environment | Method | Tool |
|-------------|--------|------|
| Development | Local file | `.env.local` (gitignored) |
| Staging | Secrets manager | {{Cloudflare / AWS / etc.}} |
| Production | Secrets manager | {{Cloudflare / AWS / etc.}} |

### Secret Rotation Strategy

| Secret | Rotation Frequency | Process | Owner |
|--------|-------------------|---------|-------|
| `DATABASE_PASSWORD` | 90 days | {{process}} | {{owner}} |
| `JWT_SECRET` | 180 days | {{process}} | {{owner}} |
| `API_KEY_{{SERVICE}}` | Per vendor policy | {{process}} | {{owner}} |

### Developer Access

| Environment | Who Has Access | How Granted | Revocation |
|-------------|----------------|-------------|------------|
| Development | All developers | Self-service `.env.local` | N/A |
| Staging | Dev team | {{method}} | On offboarding |
| Production | Ops only | {{method}} | Immediate on role change |

### Secret Leak Response

1. Immediately rotate affected secret
2. Audit access logs
3. Update all dependent systems
4. Document incident

---

## Configuration Files

### File Structure

```
config/
├── default.json      ← Base configuration
├── development.json  ← Dev overrides
├── staging.json      ← Staging overrides
├── production.json   ← Prod overrides
└── custom-environment-variables.json  ← Env var mapping
```

### Loading Order

1. `default.json` (base)
2. `{NODE_ENV}.json` (environment-specific)
3. Environment variables (highest priority)

---

## .env Files

### .env.example (committed)

```bash
# Application
NODE_ENV=development
PORT=3000
BASE_URL=http://localhost:3000

# Database
DATABASE_URL=

# Secrets (do not commit actual values)
JWT_SECRET=
API_KEY_{{SERVICE}}=

# Feature Flags
ENABLE_DEBUG_MODE=true
```

### .env.local (NOT committed)

```bash
# Local development secrets
DATABASE_URL=postgresql://localhost:5432/{{project}}_dev
JWT_SECRET=local-dev-secret-change-in-prod
API_KEY_{{SERVICE}}=dev-api-key
```

---

## Validation

Required variables that must be set:

| Variable | Required In | Validation |
|----------|-------------|------------|
| `DATABASE_URL` | All | Non-empty, valid URL |
| `JWT_SECRET` | All | Min 32 characters |
| `{{VAR}}` | {{environments}} | {{validation}} |

### Startup Validation

```typescript
// Application should validate config at startup
// and fail fast if required variables are missing
```

---

## Environment-Specific Behavior

| Behavior | Dev | Staging | Prod |
|----------|-----|---------|------|
| Error details in response | Full | Partial | Minimal |
| Request logging | Verbose | Standard | Standard |
| Performance monitoring | Off | On | On |
| Email sending | Mock/Console | Real (test addresses) | Real |
| Payment processing | Sandbox | Sandbox | Live |

---

## URLs and Endpoints

### Internal URLs

| Service | Dev | Staging | Prod |
|---------|-----|---------|------|
| API | `localhost:3000/api` | `staging.{{domain}}/api` | `{{domain}}/api` |
| Database | `localhost:5432` | `{{staging-db-host}}` | `{{prod-db-host}}` |

### External URLs

| Service | Dev | Staging | Prod |
|---------|-----|---------|------|
| {{Service}} | `sandbox.{{service}}` | `sandbox.{{service}}` | `api.{{service}}` |

---

## Resource Limits

| Resource | Dev | Staging | Prod |
|----------|-----|---------|------|
| Memory limit | 512MB | 1GB | 2GB |
| CPU | 1 | 2 | 4 |
| Connections | 5 | 10 | 50 |
| Rate limit | Off | 100/min | 100/min |

---

## Notes

{{Any additional environment configuration notes}}

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
