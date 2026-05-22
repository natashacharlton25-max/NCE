# 0f Implementation File Manifest

---
Section: 0f Implementation (Phases 43-53)
Purpose: Complete list of files available for audit review
---

## How to Use This Manifest

This lists all files created during Section 0f. When requesting files for audit:

1. Request specific files by name
2. Use **dependency bundles** to get related files together
3. Maximum requests: 10 files (Round 1) + 5 files (Round 2)

---

## Dependency Bundles

| Bundle Name | Request | You Get |
|-------------|---------|---------|
| **Backend Core** | "Backend core bundle" | Main entry, config, server setup |
| **API Routes** | "API routes bundle" | All route definitions |
| **Services** | "Services bundle" | All service files |
| **Models** | "Models bundle" | All database models |
| **Frontend Core** | "Frontend core bundle" | App.tsx, main entry, config |
| **Pages** | "Pages bundle" | All page components |
| **Components** | "Components bundle" | All UI components |
| **Shared Types** | "Shared types bundle" | All shared type definitions |
| **Database** | "Database bundle" | Migrations, seeds, schema |
| **Verification** | "Verification bundle" | All test results, reports |
| **Documentation** | "Documentation bundle" | IMPLEMENTATION-LOG, CHECKLIST, HANDOFF |

---

## Documentation Files

| File | Location | Purpose |
|------|----------|---------|
| IMPLEMENTATION-LOG.md | /docs/0f/ | Phase-by-phase progress |
| IMPLEMENTATION-CHECKLIST.md | /docs/0f/ | Final verification |
| BACKEND-VERIFICATION-REPORT.md | /docs/0f/ | Backend test results |
| DEVIATION-REPORT.md | /docs/0f/ | Spec deviations (if any) |
| 0f-to-0g-HANDOFF.md | /docs/0f/ | Handoff to testing |
| ARCHITECTURE-PATTERNS.md | /docs/0f/ | Implementation patterns used |

---

## Backend Files

### Entry & Configuration
| File | Location | Purpose |
|------|----------|---------|
| index.ts / main.ts | /src/backend/ | Application entry point |
| config.ts | /src/backend/config/ | Configuration |
| server.ts | /src/backend/ | Server setup |
| app.ts | /src/backend/ | Express/Fastify app |

### API Routes
| File | Location | Purpose |
|------|----------|---------|
| routes/index.ts | /src/backend/routes/ | Route aggregation |
| routes/{{resource}}.ts | /src/backend/routes/ | Resource routes |
| middleware/auth.ts | /src/backend/middleware/ | Auth middleware |
| middleware/validation.ts | /src/backend/middleware/ | Validation middleware |
| middleware/error.ts | /src/backend/middleware/ | Error handling |

### Services
| File | Location | Purpose |
|------|----------|---------|
| services/{{name}}.service.ts | /src/backend/services/ | Business logic |
| services/auth.service.ts | /src/backend/services/ | Authentication |

### Database
| File | Location | Purpose |
|------|----------|---------|
| models/{{name}}.model.ts | /src/backend/models/ | Database models |
| migrations/{{timestamp}}_*.ts | /migrations/ | Schema migrations |
| seeds/{{name}}.seed.ts | /seeds/ | Test data |
| db/index.ts | /src/backend/db/ | Database connection |

### Utilities
| File | Location | Purpose |
|------|----------|---------|
| utils/errors.ts | /src/backend/utils/ | Error classes |
| utils/validators.ts | /src/backend/utils/ | Validation helpers |
| utils/helpers.ts | /src/backend/utils/ | General utilities |

---

## Frontend Files

### Entry & Configuration
| File | Location | Purpose |
|------|----------|---------|
| main.tsx / index.tsx | /src/frontend/ | Entry point |
| App.tsx | /src/frontend/ | Root component |
| config.ts | /src/frontend/config/ | Frontend config |

### Pages
| File | Location | Purpose |
|------|----------|---------|
| pages/{{PageName}}.tsx | /src/frontend/pages/ | Page components |
| pages/index.ts | /src/frontend/pages/ | Page exports |

### Components
| File | Location | Purpose |
|------|----------|---------|
| components/{{Name}}.tsx | /src/frontend/components/ | UI components |
| components/index.ts | /src/frontend/components/ | Component exports |

### State Management
| File | Location | Purpose |
|------|----------|---------|
| store/index.ts | /src/frontend/store/ | Store setup |
| store/{{slice}}.ts | /src/frontend/store/ | State slices |
| hooks/{{name}}.ts | /src/frontend/hooks/ | Custom hooks |

### API Integration
| File | Location | Purpose |
|------|----------|---------|
| api/client.ts | /src/frontend/api/ | API client |
| api/{{resource}}.ts | /src/frontend/api/ | Resource API calls |

### Styles
| File | Location | Purpose |
|------|----------|---------|
| styles/globals.css | /src/frontend/styles/ | Global styles |
| styles/{{name}}.css | /src/frontend/styles/ | Component styles |

---

## Shared Files

| File | Location | Purpose |
|------|----------|---------|
| types/index.ts | /src/shared/types/ | Shared type definitions |
| types/{{domain}}.types.ts | /src/shared/types/ | Domain types |
| constants/index.ts | /src/shared/constants/ | Shared constants |
| errors/codes.ts | /src/shared/errors/ | Error code definitions |

---

## Test Files (Created in 0f)

| File | Location | Purpose |
|------|----------|---------|
| {{name}}.test.ts | /tests/unit/ | Unit tests |
| {{name}}.spec.ts | /tests/integration/ | Integration tests |
| fixtures/{{name}}.ts | /tests/fixtures/ | Test data |
| helpers/{{name}}.ts | /tests/helpers/ | Test utilities |

---

## Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| package.json | / | Dependencies |
| tsconfig.json | / | TypeScript config |
| .env.example | / | Environment template |
| docker-compose.yml | / | Local dev setup |

---

## Files from Previous Sections

These may be referenced but are NOT part of 0f:

| File | Section | Reference For |
|------|---------|---------------|
| DATABASE-SCHEMA.md | 0d | Schema reference |
| API-SURFACE.md | 0d | API contract |
| FUNCTIONS.md (per component) | 0c | Function specs |
| TYPES.md (per component) | 0c | Type specs |
| ERRORS.md (per component) | 0c | Error specs |
| ERROR-CODES.md | 0c | Error code registry |

---

## Priority Files for Audit

Start with these for the most insight:

1. **IMPLEMENTATION-LOG.md** - What was done, decisions made
2. **IMPLEMENTATION-CHECKLIST.md** - Is everything complete?
3. **BACKEND-VERIFICATION-REPORT.md** - Do tests pass?
4. **DEVIATION-REPORT.md** - Any spec changes?
5. **routes/index.ts** - API structure overview
6. **services/{{core}}.service.ts** - Core business logic
7. **middleware/auth.ts** - Security implementation
8. **shared/types/index.ts** - Type consistency

---

## File Count Summary

| Category | Count |
|----------|-------|
| Documentation | 6 |
| Backend entry/config | ~5 |
| Backend routes/middleware | ~10 |
| Backend services | ~{{n}} |
| Backend models | ~{{n}} |
| Backend migrations | ~{{n}} |
| Frontend entry/config | ~4 |
| Frontend pages | ~{{n}} |
| Frontend components | ~{{n}} |
| Shared types | ~5 |
| Tests (from 0f) | ~{{n}} |
| Config files | ~5 |
| **TOTAL** | **~{{total}}** |

---

*End of File Manifest*
