# Security Standards

---
Project: {{project_name}}
Version: {{version}}
Last Updated: {{timestamp}}
Status: DRAFT | IN REVIEW | APPROVED
---

## Purpose

Defines security requirements and standards that apply to all components. Every component spec should reference these standards.

---

## Security Principles

1. **Defense in Depth** — Multiple layers of security
2. **Least Privilege** — Minimum necessary access
3. **Fail Secure** — Errors default to deny
4. **Zero Trust** — Verify everything, trust nothing
5. **Secure by Default** — Safe defaults, opt-in for risky features

---

## Authentication

### Requirements

| Requirement | Standard |
|-------------|----------|
| Password hashing | bcrypt with cost factor ≥ 12 |
| Token format | JWT with RS256 or EdDSA |
| Token lifetime | Access: 15 min, Refresh: 7 days |
| Session storage | Server-side, not just JWT |
| Multi-factor | Required for admin, optional for users |

### Password Policy

| Rule | Requirement |
|------|-------------|
| Minimum length | 12 characters |
| Complexity | No specific requirements (length > complexity) |
| Common passwords | Block top 10,000 common passwords |
| Breach check | Check against known breaches (optional) |
| History | Cannot reuse last 5 passwords |

### Token Security

```typescript
// Token payload — minimal data
{
  sub: "usr_123",        // Subject (user ID)
  iat: 1705312800,       // Issued at
  exp: 1705313700,       // Expiration (15 min)
  jti: "tok_abc123",     // Token ID (for revocation)
  scope: ["read", "write"]
}
```

**Rules:**
- Never store sensitive data in JWT payload
- Always validate signature
- Always check expiration
- Support token revocation

---

## Authorization

### Model

Use Role-Based Access Control (RBAC) with optional Attribute-Based Access Control (ABAC).

### Standard Roles

| Role | Description | Typical Permissions |
|------|-------------|---------------------|
| `admin` | Full system access | All operations |
| `user` | Standard user | Own resources |
| `viewer` | Read-only | Read own resources |
| `service` | System-to-system | Specific operations |

### Permission Format

```
{resource}:{action}
```

| Permission | Description |
|------------|-------------|
| `user:read` | Read user data |
| `user:write` | Create/update users |
| `user:delete` | Delete users |
| `user:*` | All user operations |
| `*:read` | Read all resources |

### Authorization Check Pattern

```typescript
// Always check authorization
async function updateUser(id: string, changes: UserChanges): Promise<Result<User>> {
  // 1. Authenticate
  const auth = await authenticate(context);
  if (!auth.success) return auth;
  
  // 2. Authorize
  const canUpdate = await authorize(auth.user, 'user:write', { userId: id });
  if (!canUpdate) {
    return { success: false, error: { code: 'AUTH_201', message: 'Not authorized' } };
  }
  
  // 3. Proceed with operation
  // ...
}
```

---

## Input Validation

### Requirements

| Rule | Implementation |
|------|----------------|
| Validate all input | Never trust user input |
| Validate at boundary | Validate when data enters system |
| Whitelist approach | Accept known good, reject all else |
| Type checking | Enforce expected types |
| Length limits | Enforce maximum lengths |
| Format validation | Validate formats (email, URL, etc.) |

### Validation Order

1. Type check (is it a string, number, etc.?)
2. Required check (is it present if required?)
3. Format check (does it match expected pattern?)
4. Length check (is it within limits?)
5. Business rules (is it valid in context?)

### Common Validations

| Input | Validation |
|-------|------------|
| Email | RFC 5322 format, max 254 chars |
| URL | Valid URL with allowed protocols |
| ID | Match `^[a-z]{3}_[a-zA-Z0-9]+$` |
| Phone | E.164 format |
| Date | ISO 8601 format |
| HTML | Strip or escape, never render raw |

---

## Data Protection

### Sensitive Data Classification

| Classification | Examples | Handling |
|----------------|----------|----------|
| **Public** | Product names, public profiles | No restrictions |
| **Internal** | User IDs, project names | Access control |
| **Confidential** | Email addresses, names | Encryption, access control |
| **Secret** | Passwords, API keys | Encryption, audit, need-to-know |
| **PII** | Personal data under GDPR/CCPA | Special handling, consent |

### Data at Rest

| Requirement | Standard |
|-------------|----------|
| Database encryption | AES-256 |
| File encryption | AES-256-GCM |
| Key management | External KMS (AWS KMS, Vault) |
| Backups | Encrypted with separate key |

### Data in Transit

| Requirement | Standard |
|-------------|----------|
| Protocol | TLS 1.2+ (prefer 1.3) |
| Certificates | Valid, from trusted CA |
| Internal traffic | TLS required (no plain HTTP) |
| Certificate validation | Always validate, no skip |

### Data Retention

| Data Type | Retention | After Expiry |
|-----------|-----------|--------------|
| Active data | Indefinite | N/A |
| Soft-deleted | 30 days | Hard delete |
| Audit logs | 90 days | Archive or delete |
| Sessions | 7 days after last use | Delete |
| Backups | 30 days | Delete |

---

## Secret Management

### Requirements

| Rule | Implementation |
|------|----------------|
| Never hardcode | Use environment or secrets manager |
| Never log | Secrets must never appear in logs |
| Never commit | Use .gitignore, pre-commit hooks |
| Rotate regularly | API keys every 90 days |
| Limit access | Need-to-know basis |

### Secret Storage

| Environment | Storage |
|-------------|---------|
| Development | `.env` file (gitignored) |
| CI/CD | CI secrets (GitHub, GitLab) |
| Production | Secrets manager (AWS Secrets Manager, Vault) |

### Secret Types

| Secret | Rotation Period | Notes |
|--------|-----------------|-------|
| API keys | 90 days | Automate if possible |
| Database credentials | 90 days | Use IAM if available |
| JWT signing keys | 1 year | Overlap during rotation |
| Service account keys | 90 days | Automate rotation |

---

## Logging and Audit

### What to Log

| Event | Log Level | Data to Include |
|-------|-----------|-----------------|
| Login success | INFO | userId, timestamp, IP |
| Login failure | WARN | attempted email, IP, reason |
| Authorization failure | WARN | userId, resource, action |
| Data access | INFO | userId, resource, action |
| Data modification | INFO | userId, resource, action, changes |
| Error | ERROR | error code, context (no PII) |

### What NOT to Log

| Data | Reason |
|------|--------|
| Passwords | Security |
| API keys | Security |
| Full credit card | PCI compliance |
| Personal data | Privacy (unless required) |
| Session tokens | Security |
| Stack traces (production) | Information disclosure |

### Audit Trail

Critical operations require immutable audit trail:

```typescript
interface AuditEntry {
  id: string;
  timestamp: string;
  actor: string;         // User or system ID
  action: string;        // What was done
  resource: string;      // What was affected
  resourceId: string;    // ID of affected entity
  changes?: object;      // Before/after for updates
  ip?: string;           // Client IP
  userAgent?: string;    // Client info
}
```

---

## Error Handling

### Security Rules

| Rule | Implementation |
|------|----------------|
| Don't leak details | Generic errors to users, details in logs |
| Don't confirm existence | "Invalid credentials" not "User not found" |
| Log security errors | All auth failures, all permission denials |
| Rate limit errors | Prevent enumeration attacks |

### Error Messages

| Scenario | Bad | Good |
|----------|-----|------|
| Login fail | "User not found" | "Invalid email or password" |
| Permission | "You don't own this resource" | "Not authorized" |
| Internal error | Full stack trace | "An error occurred" + traceId |

---

## Rate Limiting

### Requirements

| Endpoint Type | Limit | Window |
|---------------|-------|--------|
| Login | 5 attempts | 15 minutes |
| API (authenticated) | 100 requests | 1 minute |
| API (unauthenticated) | 20 requests | 1 minute |
| Password reset | 3 attempts | 1 hour |

### Response

```typescript
// Rate limit headers
{
  'X-RateLimit-Limit': '100',
  'X-RateLimit-Remaining': '45',
  'X-RateLimit-Reset': '1705312800'
}

// When limited
{
  success: false,
  error: {
    code: 'SHARED_005',
    message: 'Too many requests',
    details: { retryAfter: 60 }
  }
}
```

---

## Dependency Security

### Requirements

| Rule | Implementation |
|------|----------------|
| Audit dependencies | Regular security audits |
| Pin versions | Use exact versions in lock file |
| Update regularly | Security updates within 7 days |
| Minimize dependencies | Fewer deps = smaller attack surface |
| Verify integrity | Use lock files, verify checksums |

### Tools

| Tool | Purpose |
|------|---------|
| `npm audit` | Node.js vulnerability check |
| `pip-audit` | Python vulnerability check |
| Dependabot | Automated update PRs |
| Snyk | Comprehensive security scanning |

---

## Secure Defaults

### Configuration

| Setting | Secure Default |
|---------|----------------|
| CORS | Disabled (explicit allowlist) |
| HTTPS | Required |
| Cookies | `Secure; HttpOnly; SameSite=Strict` |
| Headers | Security headers enabled |
| Debug mode | Disabled in production |
| Verbose errors | Disabled in production |

### Security Headers

```
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
Referrer-Policy: strict-origin-when-cross-origin
```

---

## Security Testing

### Requirements

| Test Type | Frequency | Scope |
|-----------|-----------|-------|
| Static analysis | Every commit | All code |
| Dependency audit | Every build | All dependencies |
| Unit tests (security) | Every commit | Auth, authz, validation |
| Penetration testing | Quarterly | External surface |
| Security review | Major releases | New features |

### Security Test Cases

- [ ] Authentication bypass attempts
- [ ] Authorization bypass (horizontal/vertical)
- [ ] Input validation bypass
- [ ] Injection attacks (SQL, XSS, etc.)
- [ ] Rate limiting effectiveness
- [ ] Session security
- [ ] Error information leakage

---

## Incident Response

### Security Incident Levels

| Level | Description | Response Time |
|-------|-------------|---------------|
| Critical | Active breach, data exfiltration | Immediate |
| High | Vulnerability being exploited | 4 hours |
| Medium | Vulnerability discovered | 24 hours |
| Low | Security improvement needed | 1 week |

### Response Steps

1. **Detect** — Identify the incident
2. **Contain** — Stop the bleeding
3. **Investigate** — Understand scope
4. **Remediate** — Fix the issue
5. **Recover** — Restore normal operation
6. **Review** — Post-incident analysis

---

## Compliance Considerations

### GDPR (if applicable)

- [ ] Data inventory documented
- [ ] Consent mechanisms in place
- [ ] Right to access implemented
- [ ] Right to deletion implemented
- [ ] Data portability supported
- [ ] DPA with processors

### SOC 2 (if applicable)

- [ ] Access controls documented
- [ ] Encryption implemented
- [ ] Logging and monitoring
- [ ] Incident response plan
- [ ] Change management process

---

## Security Checklist for Specs

Every component spec should address:

- [ ] What sensitive data does it handle?
- [ ] How is authentication enforced?
- [ ] How is authorization enforced?
- [ ] What inputs need validation?
- [ ] What security errors can occur?
- [ ] What needs to be audited?
- [ ] What rate limits apply?

---
Phase: 26 (Project-Wide Specs)
Generated: {{timestamp}}
---
