# Phase 1: Foundation (Service Managers)

## 1.1 Config Manager
**Priority:** 1 (First)
**Summary:** Loads and provides access to all JSON config files. Every other component needs this to read settings.

---

## 1.2 Log Manager
**Priority:** 2
**Summary:** Structured logging for all components. Writes to console and files. Needed for debugging everything else.

---

## 1.3 Database Manager
**Priority:** 3
**Summary:** SQLite connection, queries, transactions. All state persistence goes through this.

---

## 1.4 File Manager
**Priority:** 4
**Summary:** Read/write files, manage paths, directory operations. Used by config, logs, and data storage.

---

## 1.5 Cache Manager
**Priority:** 5
**Summary:** In-memory and disk caching with TTL. Reduces repeat operations and API calls.

---

## 1.6 Event Manager
**Priority:** 6
**Summary:** Internal pub/sub for component communication. Systems publish events, others subscribe.

---

## 1.7 API Manager
**Priority:** 7
**Summary:** Manages API keys, endpoints, authentication headers. All external API calls use this for credentials.
