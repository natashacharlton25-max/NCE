# Phase 44: Database Implementation

---
Phase: 44
Section: 0f. Implementation
Name: Database Implementation
Purpose: Create database schema matching specification with validation
---

## Role

You are implementing the database schema. Your job is to create migrations, seeds, and validate that the physical database matches DATABASE-SCHEMA.md exactly.

---

## Inputs

Read these documents before starting:

| Document | Location | What to Extract |
|----------|----------|-----------------|
| DATABASE-SCHEMA.md | 0c/0d outputs | Tables, columns, relationships, indexes |
| TYPES.md | 0c outputs | Type alignment verification |
| TECH-STACK.md | 0d outputs | Database type, ORM choice |
| shared-types/ | From Phase 43 | Existing type definitions |

---

## Process

### Step 1: Review Database Schema

Extract from DATABASE-SCHEMA.md:

| Table | Columns | Primary Key | Foreign Keys | Indexes |
|-------|---------|-------------|--------------|---------|
| users | id, email, name, ... | id | — | email (unique) |
| projects | id, name, user_id, ... | id | user_id → users.id | user_id |
| ... | ... | ... | ... | ... |

### Step 2: Create Migration Files

Create one migration per logical unit (usually per table or related tables):

```bash
# Example with Prisma
npx prisma migrate dev --name create_users_table
npx prisma migrate dev --name create_projects_table

# Example with Knex
npx knex migrate:make create_users_table
npx knex migrate:make create_projects_table

# Example with raw SQL
touch migrations/001_create_users_table.sql
touch migrations/002_create_projects_table.sql
```

### Step 3: Define Tables

For each table in DATABASE-SCHEMA.md:

**Prisma Example:**
```prisma
// prisma/schema.prisma

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  password  String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  projects  Project[]
  
  @@map("users")
}

model Project {
  id          String   @id @default(cuid())
  name        String
  description String?
  status      String   @default("active")
  userId      String
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  user        User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@index([userId])
  @@map("projects")
}
```

**Raw SQL Example:**
```sql
-- migrations/001_create_users_table.sql

CREATE TABLE users (
  id VARCHAR(36) PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  password VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
```

**Verify:** Every column in DATABASE-SCHEMA.md exists with correct type.

### Step 4: Define Relationships

For each foreign key in DATABASE-SCHEMA.md:

| From Table | From Column | To Table | To Column | On Delete |
|------------|-------------|----------|-----------|-----------|
| projects | user_id | users | id | CASCADE |
| ... | ... | ... | ... | ... |

Implement in migrations:
```sql
ALTER TABLE projects
ADD CONSTRAINT fk_projects_user
FOREIGN KEY (user_id) REFERENCES users(id)
ON DELETE CASCADE;
```

### Step 5: Create Indexes

For each index in DATABASE-SCHEMA.md:

| Table | Index Name | Columns | Unique |
|-------|------------|---------|--------|
| users | idx_users_email | email | Yes |
| projects | idx_projects_user_id | user_id | No |

```sql
CREATE UNIQUE INDEX idx_users_email ON users(email);
CREATE INDEX idx_projects_user_id ON projects(user_id);
```

### Step 6: Create Seed Data

Create development seed data:

```typescript
// seeds/development.ts

import { db } from '../src/lib/db';

async function seed() {
  // Clear existing data
  await db.project.deleteMany();
  await db.user.deleteMany();
  
  // Create test users
  const user1 = await db.user.create({
    data: {
      id: 'user-1',
      email: 'test@example.com',
      name: 'Test User',
      password: await hash('password123'),
    },
  });
  
  // Create test projects
  await db.project.create({
    data: {
      id: 'project-1',
      name: 'Test Project',
      description: 'A test project',
      userId: user1.id,
    },
  });
  
  console.log('Seed complete');
}

seed();
```

### Step 7: Run Migrations

```bash
# Prisma
npx prisma migrate dev

# Knex
npx knex migrate:latest

# Raw SQL (example script)
node scripts/run-migrations.js
```

**Verify:** Migrations complete without errors.

### Step 8: Run Seeds

```bash
# Prisma
npx prisma db seed

# Knex
npx knex seed:run

# Custom
npm run seed
```

**Verify:** Seed data loads without errors.

### Step 9: Schema Validation

Run validation to prove physical database matches spec:

**Prisma:**
```bash
npx prisma validate
npx prisma db pull --force  # Compare pulled schema vs defined schema
```

**Manual Validation:**
```sql
-- Check tables exist
SHOW TABLES;

-- Check columns match
DESCRIBE users;
DESCRIBE projects;

-- Check indexes exist
SHOW INDEX FROM users;
SHOW INDEX FROM projects;

-- Check foreign keys
SELECT * FROM information_schema.KEY_COLUMN_USAGE
WHERE TABLE_SCHEMA = 'your_database';
```

**Create validation checklist:**

| Table | Exists | Columns Match | Indexes Match | FKs Match |
|-------|--------|---------------|---------------|-----------|
| users | ✅ | ✅ | ✅ | N/A |
| projects | ✅ | ✅ | ✅ | ✅ |

### Step 10: Update Shared Types (If Needed)

If your ORM generates types, ensure they align with shared-types/:

```typescript
// If Prisma generates types, re-export or align:

// src/shared/types/user.ts
import type { User as PrismaUser } from '@prisma/client';

// Either use Prisma types directly
export type User = PrismaUser;

// Or ensure manual types match
export interface User {
  id: string;
  email: string;
  name: string;
  password: string;
  createdAt: Date;
  updatedAt: Date;
}
```

**Verify:** No type mismatches between schema and shared-types/.

---

## Output

By the end of this phase:

| Output | Location | Status |
|--------|----------|--------|
| Migration files | migrations/ or prisma/ | ☐ Created |
| Seed files | seeds/ | ☐ Created |
| Working database | Local DB | ☐ Running |
| Schema validation | Documented | ☐ Passed |
| Updated types | shared-types/ (if needed) | ☐ Aligned |

---

## Checklist

Before moving to Phase 45:

- [ ] All tables from DATABASE-SCHEMA.md exist
- [ ] All columns match spec (name, type, nullable, default)
- [ ] All foreign keys configured correctly
- [ ] All indexes created
- [ ] Seed data loads successfully
- [ ] Schema validation passes
- [ ] Shared types align with schema
- [ ] Can connect from application code
- [ ] Migrations are safely re-runnable:
  - **ORM (Prisma, Drizzle, etc.):** Migration history clean, status shows no pending
  - **Raw SQL:** Scripts use `IF NOT EXISTS` / `IF EXISTS` guards
  - **Either:** Running migrations twice doesn't error or corrupt data

---

## Implementation Log Entry

Add to IMPLEMENTATION-LOG.md:

```markdown
## Phase 44: Database Implementation

**Started:** {{timestamp}}
**Completed:** {{timestamp}}
**Duration:** {{time}}

### Summary
Created database schema with migrations and seeds.

### Tables Created

| Table | Columns | Indexes | Foreign Keys |
|-------|---------|---------|--------------|
| users | 6 | 1 | 0 |
| projects | 7 | 1 | 1 |

### Schema Validation

**Command:** `npx prisma validate`
**Result:** ✅ Passed

### Files Created
- migrations/001_create_users.sql
- migrations/002_create_projects.sql
- seeds/development.ts
- (or Prisma schema changes)

### Deviations
- None

### Notes
{{Any observations or decisions}}
```

---

## Rules

1. **Match DATABASE-SCHEMA.md exactly** — Every table, column, index
2. **Validate schema** — Don't assume migrations worked correctly
3. **Idempotent migrations** — Must be safe to re-run
4. **Seed data for development** — Real-looking test data
5. **Type alignment** — shared-types/ must match schema

---

## When Issues Arise

| Issue | Action |
|-------|--------|
| Schema spec unclear | Check TYPES.md, escalate if still unclear |
| Migration fails | Debug, fix, document what was wrong |
| Type mismatch | Align shared-types/, don't create duplicates |
| Performance concern | Document, defer optimization to later |

---

## Next Phase

After completing Phase 44, proceed to:

**Phase 45: Backend Core**

---
