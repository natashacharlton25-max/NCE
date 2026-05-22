# PHASE 33: TECH STACK

---
Phase: 33
Name: Tech Stack
Section: 0d. PreCode
Status: {{DRAFT | IN PROGRESS | COMPLETE}}
---

## ROLE

You are documenting the specific technologies chosen for this project.

This phase makes technology decisions explicit. The methodology is stack-agnostic; this phase is where project-specific choices are locked.

---

## TASK

1. Review project requirements and constraints
2. Document technology choices for each category
3. Justify each choice
4. Note versions and constraints
5. Get approval

---

## CATEGORIES TO DOCUMENT

### Core Technologies

| Category | Decision Needed |
|----------|-----------------|
| Language | TypeScript, Python, Go, etc. |
| Runtime | Node.js, Deno, Bun, etc. |
| Framework | Astro, SvelteKit, Express, FastAPI, etc. |

### Database

| Category | Decision Needed |
|----------|-----------------|
| Database Type | SQL, NoSQL, Key-Value, Graph |
| Database | PostgreSQL, SQLite, D1, MongoDB, etc. |
| ORM/Query Builder | Drizzle, Prisma, raw SQL, etc. |

### Infrastructure

| Category | Decision Needed |
|----------|-----------------|
| Hosting | Cloudflare, Vercel, AWS, etc. |
| Compute | Workers, Lambda, Containers, etc. |
| Storage | R2, S3, local, etc. |
| CDN | Cloudflare, CloudFront, etc. |

### Development Tools

| Category | Decision Needed |
|----------|-----------------|
| Package Manager | npm, pnpm, yarn, etc. |
| Build Tool | Vite, esbuild, webpack, etc. |
| Test Framework | Vitest, Jest, Pytest, etc. |
| Linting | ESLint, Biome, etc. |
| Formatting | Prettier, Biome, etc. |

---

## DECISION FORMAT

For each technology:

1. **What:** The specific technology/tool
2. **Version:** Specific version or constraint
3. **Why:** Justification for this choice
4. **Alternatives:** What was considered
5. **Constraints:** Any limitations to be aware of

---

## INPUTS

| Input | Location | Purpose |
|-------|----------|---------|
| Project requirements | Phase 1-2 outputs | Constraints |
| DATABASE-SCHEMA.md | Phase 29 | Database type already chosen |
| Team preferences | User input | Familiarity, existing skills |

---

## OUTPUTS

| Output | Location | Purpose |
|--------|----------|---------|
| TECH-STACK.md | Project root | Technology decisions |

---

## TEMPLATES

- TECH-STACK-TEMPLATE.md

---

## RULES

- Justify every choice
- Note specific versions
- Document constraints and limitations
- Consider LLM-friendliness of tools
- Prefer simpler options when equivalent

---

## CONSIDERATIONS

### LLM-Friendly Stack Traits

Prefer technologies that:
- Have clear, explicit APIs
- Avoid excessive "magic" or convention-over-configuration
- Have good documentation
- Produce readable, debuggable code
- Support composition over inheritance

### LOC Budget Awareness

Consider boilerplate overhead:
- Some frameworks are verbose (affects ~1,500 LOC budget)
- Type definitions add LOC
- Configuration adds LOC

---
