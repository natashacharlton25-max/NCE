# Tech Stack

---
Project: {{Project Name}}
Version: v1.0.0
Generated: {{timestamp}}
Status: DRAFT | APPROVED
---

## Overview

| Layer | Technology |
|-------|------------|
| Language | {{language}} |
| Framework | {{framework}} |
| Database | {{database}} |
| Hosting | {{hosting}} |

---

## Core Technologies

### Language

| Attribute | Value |
|-----------|-------|
| **Choice** | {{TypeScript / Python / Go / etc.}} |
| **Version** | {{version}} |
| **Runtime** | {{Node.js / Deno / Bun / Python / etc.}} |

**Justification:**
{{Why this language was chosen}}

**Alternatives Considered:**

| Alternative | Pros | Cons | Why Not |
|-------------|------|------|---------|
| {{lang}} | {{pros}} | {{cons}} | {{reason}} |

---

### Framework

| Attribute | Value |
|-----------|-------|
| **Choice** | {{Astro / SvelteKit / Express / FastAPI / etc.}} |
| **Version** | {{version}} |

**Justification:**
{{Why this framework was chosen}}

**Alternatives Considered:**

| Alternative | Pros | Cons | Why Not |
|-------------|------|------|---------|
| {{framework}} | {{pros}} | {{cons}} | {{reason}} |

**LLM-Friendliness:**
- Boilerplate level: Low / Medium / High
- Magic/convention level: Low / Medium / High
- Explicit APIs: Yes / No

---

## Database

### Primary Database

| Attribute | Value |
|-----------|-------|
| **Type** | {{SQL / Document / Key-Value / Graph}} |
| **Choice** | {{PostgreSQL / SQLite / D1 / MongoDB / etc.}} |
| **Version** | {{version}} |

**Justification:**
{{Why this database was chosen}}

**Alternatives Considered:**

| Alternative | Pros | Cons | Why Not |
|-------------|------|------|---------|
| {{database}} | {{pros}} | {{cons}} | {{reason}} |

---

### ORM / Query Builder

| Attribute | Value |
|-----------|-------|
| **Choice** | {{Drizzle / Prisma / Raw SQL / etc.}} |
| **Version** | {{version}} |

**Justification:**
{{Why this approach was chosen}}

---

### Caching (if applicable)

| Attribute | Value |
|-----------|-------|
| **Choice** | {{Redis / Memcached / In-memory / None}} |
| **Purpose** | {{what's cached}} |

---

## Infrastructure

### Hosting Platform

| Attribute | Value |
|-----------|-------|
| **Choice** | {{Cloudflare / Vercel / AWS / GCP / etc.}} |
| **Compute** | {{Workers / Lambda / Containers / VMs}} |

**Justification:**
{{Why this platform was chosen}}

---

### Storage

| Type | Service | Purpose |
|------|---------|---------|
| Object Storage | {{R2 / S3 / etc.}} | {{purpose}} |
| Static Assets | {{CDN / etc.}} | {{purpose}} |
| File Storage | {{service}} | {{purpose}} |

---

### CDN / Edge

| Attribute | Value |
|-----------|-------|
| **CDN** | {{Cloudflare / CloudFront / etc.}} |
| **Edge Compute** | {{Yes / No}} |

---

## Development Tools

### Package Manager

| Attribute | Value |
|-----------|-------|
| **Choice** | {{npm / pnpm / yarn / etc.}} |
| **Version** | {{version}} |
| **Lock File** | {{package-lock.json / pnpm-lock.yaml / etc.}} |

---

### Build Tool

| Attribute | Value |
|-----------|-------|
| **Choice** | {{Vite / esbuild / webpack / etc.}} |
| **Version** | {{version}} |

---

### Testing

| Type | Tool | Version |
|------|------|---------|
| Unit Tests | {{Vitest / Jest / Pytest / etc.}} | {{version}} |
| Integration Tests | {{tool}} | {{version}} |
| E2E Tests | {{Playwright / Cypress / etc.}} | {{version}} |

---

### Code Quality

| Tool | Purpose | Version |
|------|---------|---------|
| {{ESLint / Biome}} | Linting | {{version}} |
| {{Prettier / Biome}} | Formatting | {{version}} |
| {{TypeScript}} | Type checking | {{version}} |

---

## External Services

| Service | Provider | Purpose |
|---------|----------|---------|
| Authentication | {{Auth0 / Clerk / Custom}} | User auth |
| Email | {{SendGrid / Resend / etc.}} | Transactional email |
| Monitoring | {{Sentry / DataDog / etc.}} | Error tracking |
| Analytics | {{service}} | {{purpose}} |

---

## Version Constraints

| Technology | Constraint | Reason |
|------------|------------|--------|
| Node.js | >= 20.x | LTS support |
| TypeScript | >= 5.x | Latest features |
| {{tech}} | {{constraint}} | {{reason}} |

---

## Known Limitations

| Technology | Limitation | Workaround |
|------------|------------|------------|
| {{tech}} | {{limitation}} | {{workaround}} |

---

## LOC Budget Impact

| Factor | Impact | Notes |
|--------|--------|-------|
| Framework boilerplate | ~{{n}} LOC | Per component |
| Type definitions | ~{{n}} LOC | Per component |
| Configuration | ~{{n}} LOC | Project-wide |
| **Net available for logic** | ~{{n}} LOC | Per component |

---

## Security Considerations

| Aspect | Approach |
|--------|----------|
| Secrets management | {{approach}} |
| Authentication | {{approach}} |
| Authorization | {{approach}} |
| Data encryption | {{approach}} |

---

## Notes

{{Any additional tech stack notes}}

---

## Approval

| Role | Name | Date | Status |
|------|------|------|--------|
| Human | | | APPROVED / PENDING |

---
