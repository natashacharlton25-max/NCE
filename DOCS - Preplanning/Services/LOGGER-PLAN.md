# Logger Service Plan

> **Purpose:** Central logging for all services - temp file for recent, SQLite for history.
> **Level:** System Service
> **Status:** Planning
> **Created:** 2026-01-16

---

## Overview

Logger handles all log entries across the system. Uses a two-tier approach:
- **Temp file** for fast writes (last 24 hours)
- **SQLite database** for queryable history

**Key Principle:** All services log through Logger. Fast writes to temp, background sync to database.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              LOGGER SERVICE                                  │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         LOG WRITER                                   │    │
│  │                                                                      │    │
│  │  • Receives log entries from all services                           │    │
│  │  • Writes immediately to temp file (fast)                           │    │
│  │  • Buffers for batch database inserts                               │    │
│  └──────────────────────────────┬──────────────────────────────────────┘    │
│                                 │                                            │
│           ┌─────────────────────┴─────────────────────┐                     │
│           │                                           │                     │
│           ▼                                           ▼                     │
│  ┌─────────────────────┐                 ┌─────────────────────┐           │
│  │    TEMP FILE        │                 │    SQLITE DB        │           │
│  │    (24 hours)       │────────────────▶│    (persistent)     │           │
│  │                     │   background    │                     │           │
│  │ • Fast writes       │     sync        │ • Queryable         │           │
│  │ • Recent access     │                 │ • Pattern detection │           │
│  │ • JSONL format      │                 │ • Long-term storage │           │
│  └─────────────────────┘                 └─────────────────────┘           │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Data Flow

```
1. Service calls Logger.log({ ... })
2. Logger writes to temp file immediately (sync or async)
3. Logger buffers entry for database
4. Every N seconds (or N entries), batch insert to SQLite
5. Every 24 hours, rotate temp file (clear old entries)
```

---

## Responsibility

| Does | Does NOT |
|------|----------|
| Write logs to temp file | Decide what to log (services do) |
| Sync logs to SQLite database | Handle retries (FailSys does) |
| Provide query interface | Make decisions based on logs |
| Rotate temp files | Alert on errors (FailSys does) |
| Track log levels (info, warn, error) | Read log files directly (use query) |

---

## Log Entry Structure

```typescript
interface LogEntry {
  id: string;              // UUID
  timestamp: string;       // ISO 8601
  level: 'debug' | 'info' | 'warn' | 'error' | 'fatal';
  service: string;         // 'librarian', 'archivist', 'parser', etc.
  operation: string;       // 'fetch', 'validate', 'write', etc.
  message: string;         // Human-readable message
  data?: Record<string, unknown>;  // Additional context
  brandId?: string;        // If related to a brand
  sessionId?: string;      // If related to a session
  duration?: number;       // Operation duration in ms
  error?: {
    code: string;
    message: string;
    stack?: string;
  };
}
```

---

## API Design

```typescript
// lib/server/services/logger.ts

// Log levels
type LogLevel = 'debug' | 'info' | 'warn' | 'error' | 'fatal';

interface LogOptions {
  service: string;
  operation: string;
  message: string;
  level?: LogLevel;        // Default: 'info'
  data?: Record<string, unknown>;
  brandId?: string;
  sessionId?: string;
  duration?: number;
  error?: Error | string;
}

interface LogQueryOptions {
  level?: LogLevel | LogLevel[];
  service?: string | string[];
  operation?: string;
  brandId?: string;
  sessionId?: string;
  from?: Date;
  to?: Date;
  limit?: number;
  offset?: number;
}

interface LogQueryResult {
  entries: LogEntry[];
  total: number;
  hasMore: boolean;
}

// Core functions
function log(options: LogOptions): void;
function debug(service: string, operation: string, message: string, data?: object): void;
function info(service: string, operation: string, message: string, data?: object): void;
function warn(service: string, operation: string, message: string, data?: object): void;
function error(service: string, operation: string, message: string, error?: Error): void;
function fatal(service: string, operation: string, message: string, error?: Error): void;

// Query functions (from SQLite)
function query(options: LogQueryOptions): Promise<LogQueryResult>;
function getRecent(limit?: number): Promise<LogEntry[]>;
function getByService(service: string, limit?: number): Promise<LogEntry[]>;
function getErrors(from?: Date, to?: Date): Promise<LogEntry[]>;

// Maintenance
function rotate(): Promise<void>;           // Manual rotation
function flush(): Promise<void>;            // Force write buffer to DB
function getStats(): Promise<LogStats>;     // Log statistics
```

---

## Usage

```typescript
import { Logger } from '@/lib/server/services/logger';

// Simple logging
Logger.info('librarian', 'fetch', 'Fetched brand profile', { brandId: 'mind-the-box' });

Logger.error('parser', 'validate', 'Schema validation failed', validationError);

// With full options
Logger.log({
  service: 'archivist',
  operation: 'store',
  message: 'Stored draft section',
  level: 'info',
  brandId: 'mind-the-box',
  data: { section: 'voice', size: 1234 },
  duration: 45
});

// Querying logs
const recentErrors = await Logger.query({
  level: 'error',
  from: new Date(Date.now() - 3600000),  // Last hour
  limit: 50
});

const brandLogs = await Logger.query({
  brandId: 'mind-the-box',
  service: ['librarian', 'archivist'],
  limit: 100
});
```

---

## Temp File Format

JSONL (JSON Lines) for fast append-only writes:

```jsonl
{"id":"abc123","timestamp":"2026-01-16T10:30:00.000Z","level":"info","service":"librarian","operation":"fetch","message":"Fetched brand profile","brandId":"mind-the-box"}
{"id":"abc124","timestamp":"2026-01-16T10:30:01.000Z","level":"error","service":"parser","operation":"validate","message":"Schema validation failed","error":{"code":"VALIDATION_ERROR","message":"Missing required field: name"}}
```

**Location:** `/files/logs/current.jsonl`

---

## SQLite Schema

```sql
-- logs.db

CREATE TABLE logs (
  id TEXT PRIMARY KEY,
  timestamp TEXT NOT NULL,
  level TEXT NOT NULL,
  service TEXT NOT NULL,
  operation TEXT NOT NULL,
  message TEXT NOT NULL,
  data TEXT,              -- JSON string
  brand_id TEXT,
  session_id TEXT,
  duration INTEGER,
  error_code TEXT,
  error_message TEXT,
  error_stack TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);

-- Indexes for common queries
CREATE INDEX idx_logs_timestamp ON logs(timestamp);
CREATE INDEX idx_logs_level ON logs(level);
CREATE INDEX idx_logs_service ON logs(service);
CREATE INDEX idx_logs_brand_id ON logs(brand_id);
CREATE INDEX idx_logs_session_id ON logs(session_id);

-- Composite index for filtered queries
CREATE INDEX idx_logs_service_level ON logs(service, level);
CREATE INDEX idx_logs_timestamp_level ON logs(timestamp, level);
```

**Location:** `/files/logs/logs.db`

---

## Sync Strategy

```typescript
interface SyncConfig {
  batchSize: number;      // Entries before batch insert (default: 100)
  syncInterval: number;   // Seconds between syncs (default: 30)
  rotationAge: number;    // Hours before temp rotation (default: 24)
}

// Sync process
class LogSync {
  private buffer: LogEntry[] = [];
  private timer: NodeJS.Timer;

  // Called on each log
  addToBuffer(entry: LogEntry): void {
    this.buffer.push(entry);

    if (this.buffer.length >= this.config.batchSize) {
      this.flush();
    }
  }

  // Batch insert to SQLite
  async flush(): Promise<void> {
    if (this.buffer.length === 0) return;

    const entries = this.buffer.splice(0);
    await this.db.insertMany(entries);
  }

  // Rotate temp file (clear entries older than 24h)
  async rotate(): Promise<void> {
    const cutoff = Date.now() - (24 * 60 * 60 * 1000);
    // Read temp file, filter out old entries, write back
    // Or simply truncate if all synced to DB
  }
}
```

---

## Integration with FailSys

Logger and FailSys work together:

```typescript
// FailSys uses Logger for pattern detection
class FailSys {
  async detectPatterns(): Promise<FailurePattern[]> {
    // Query recent errors from Logger
    const errors = await Logger.query({
      level: ['error', 'fatal'],
      from: new Date(Date.now() - 3600000),  // Last hour
    });

    // Analyze for patterns
    return this.analyzePatterns(errors.entries);
  }
}

// Logger reports to FailSys on fatal
Logger.fatal('service', 'op', 'Critical failure', error);
// Internally: await FailSys.report({ ... })
```

---

## Configuration

```json
// services/logger/config.json
{
  "tempFile": "/files/logs/current.jsonl",
  "database": "/files/logs/logs.db",
  "batchSize": 100,
  "syncInterval": 30,
  "rotationAge": 24,
  "levels": {
    "debug": { "enabled": false, "console": false },
    "info": { "enabled": true, "console": false },
    "warn": { "enabled": true, "console": true },
    "error": { "enabled": true, "console": true },
    "fatal": { "enabled": true, "console": true }
  },
  "retention": {
    "days": 30,
    "maxSize": "100MB"
  }
}
```

---

## Implementation Notes

### File Locations

```
/files/logs/
  current.jsonl          # Temp file (last 24 hours)
  logs.db                # SQLite database
  archive/               # Rotated temp files (optional)

lib/server/services/
  logger.ts              # Main logger service
  log-sync.ts            # Sync temp to database
  log-query.ts           # Query interface
```

### Dependencies

| Component | Dependencies |
|-----------|--------------|
| Logger | Node.js fs |
| LogSync | better-sqlite3 or sqlite3 |
| LogQuery | better-sqlite3 or sqlite3 |

### Size Estimate

- logger.ts: ~150 lines
- log-sync.ts: ~100 lines
- log-query.ts: ~120 lines
- Total: ~370 lines

---

## Build Phases

### L1: Core Logger
- Log entry structure
- Write to temp file (JSONL)
- Basic log functions (info, warn, error)

### L2: SQLite Integration
- Database schema
- Batch sync from temp to DB
- Query interface

### L3: Rotation & Maintenance
- Temp file rotation (24h)
- Retention policy
- Stats and monitoring

### L4: FailSys Integration
- Fatal → FailSys.report()
- Pattern detection queries

---

## Testing

| Test | Description |
|------|-------------|
| Log to temp file | Entry appears in JSONL |
| Batch sync | Buffer flushes to SQLite |
| Query by level | Returns filtered entries |
| Query by service | Returns filtered entries |
| Query by date range | Returns entries in range |
| Rotation | Old entries removed from temp |
| Fatal triggers FailSys | FailSys receives report |

---

## Related Documents

- [SERVICE-MAP.md](./SERVICE-MAP.md) - Overview of all services
- [FAILSYS-PLAN.md](./FAILSYS-PLAN.md) - Uses Logger for pattern detection
- [WRITER-PLAN.md](./WRITER-PLAN.md) - Logger uses Writer for temp file

---

*Logger is the system's memory - fast writes, queryable history.*
