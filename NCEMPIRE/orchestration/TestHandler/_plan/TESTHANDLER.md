# TestHandler Module

> **Purpose:** Automated testing for integration and unit tests
> **Type:** System Service Module
> **Status:** Planning

---

## Overview

TestHandler provides **automated testing capabilities** for the system. It runs integration tests, validates module functions, and ensures system reliability.

```
Test request → TestHandler.runTests() → Tests executed → Results reported
```

**TestHandler supports:**
- Unit tests (individual functions)
- Integration tests (module interactions)
- End-to-end tests (full chains)
- Regression tests (after changes)

---

## Roles & Rules

### TestHandler DOES:
- Run automated tests
- Validate module functions
- Test chain execution
- Report test results
- Track test history
- Mock external services

### TestHandler does NOT:
- Modify production data
- Run in production mode
- Skip failing tests
- Execute without isolation

### Boundaries:
- Test environment only
- Uses mock/sandbox data
- Cannot affect production
- Results logged separately

---

## Test Types

| Type | Scope | Purpose |
|------|-------|---------|
| `unit` | Single function | Verify function behavior |
| `integration` | Module interaction | Verify modules work together |
| `e2e` | Full chain | Verify complete workflows |
| `regression` | After changes | Ensure no breakage |
| `performance` | Speed/load | Verify performance |

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `runTests(suite)` | Run test suite | suite | results |
| `runSingleTest(testId)` | Run one test | testId | result |
| `getTestSuites()` | List available suites | - | suites |
| `getTestHistory(suite)` | Get test history | suite | history |
| `validateModule(moduleName)` | Validate module | moduleName | valid |
| `mockService(service, responses)` | Set up mock | service, responses | mocked |

---

## Detailed Function Specs

### runTests(suite)

Run a test suite.

```javascript
// Input
{
  suite: "core-modules",
  options: {
    parallel: true,
    stopOnFailure: false,
    verbose: true
  }
}

// Output
{
  suite: "core-modules",
  status: "passed",  // "passed" | "failed" | "partial"
  summary: {
    total: 25,
    passed: 24,
    failed: 1,
    skipped: 0,
    duration: 45000
  },
  tests: [
    {
      id: "orchestrator-job-create",
      name: "Orchestrator can create job",
      status: "passed",
      duration: 150
    },
    {
      id: "aicaller-timeout",
      name: "AICaller handles timeout",
      status: "failed",
      duration: 5000,
      error: "Expected timeout after 3000ms, got 5000ms"
    }
  ],
  timestamp: "2026-01-17T14:30:00Z"
}
```

### validateModule(moduleName)

Validate a module's functions work correctly.

```javascript
// Input
{
  moduleName: "PromptBuilder"
}

// Output
{
  module: "PromptBuilder",
  valid: true,
  functions: [
    { name: "loadTemplate", status: "valid", tests: 3 },
    { name: "resolveVariables", status: "valid", tests: 5 },
    { name: "compile", status: "valid", tests: 4 }
  ],
  coverage: 0.85
}
```

### mockService(service, responses)

Set up mock for external service.

```javascript
// Input
{
  service: "openai",
  responses: [
    {
      endpoint: "chat/completions",
      response: {
        id: "mock-123",
        choices: [{ message: { content: "Mock response" } }],
        usage: { total_tokens: 100 }
      },
      delay: 500
    }
  ]
}

// Output
{
  mocked: true,
  service: "openai",
  endpoints: ["chat/completions"],
  active: true
}
```

---

## Test Definition

```javascript
// /repos/tests/core-modules/orchestrator.test.js
{
  suite: "core-modules",
  module: "Orchestrator",
  tests: [
    {
      id: "orchestrator-job-create",
      name: "Orchestrator can create job",
      type: "unit",
      async run() {
        const job = await Orchestrator.createJob({
          chainName: "test-chain",
          brandId: "test-brand"
        });
        expect(job.jobId).toBeDefined();
        expect(job.status).toBe("queued");
      }
    },
    {
      id: "orchestrator-chain-execute",
      name: "Orchestrator executes chain",
      type: "integration",
      setup: async () => {
        await TestHandler.mockService("openai", mockResponses);
      },
      async run() {
        const result = await Orchestrator.runJob("test-chain", {});
        expect(result.success).toBe(true);
      },
      teardown: async () => {
        await TestHandler.clearMocks();
      }
    }
  ]
}
```

---

## Test Environment

```javascript
{
  environment: "test",
  isolation: {
    useTestRepos: true,
    mockExternalCalls: true,
    sandboxedFileSystem: true
  },
  fixtures: {
    brands: "/repos/test-fixtures/brands/",
    templates: "/repos/test-fixtures/templates/"
  }
}
```

---

## Config

### module.config.json

```json
{
  "module": "TestHandler",
  "version": "1.0.0",
  "description": "Automated testing",
  "type": "system-service",

  "functions": [
    {
      "name": "runTests",
      "description": "Run test suite",
      "input": ["suite"],
      "output": "results"
    },
    {
      "name": "runSingleTest",
      "description": "Run one test",
      "input": ["testId"],
      "output": "result"
    },
    {
      "name": "getTestSuites",
      "description": "List available suites",
      "input": [],
      "output": "suites"
    },
    {
      "name": "getTestHistory",
      "description": "Get test history",
      "input": ["suite"],
      "output": "history"
    },
    {
      "name": "validateModule",
      "description": "Validate module",
      "input": ["moduleName"],
      "output": "valid"
    },
    {
      "name": "mockService",
      "description": "Set up mock",
      "input": ["service", "responses"],
      "output": "mocked"
    }
  ],

  "config": {
    "testsPath": "/repos/tests/",
    "fixturesPath": "/repos/test-fixtures/",
    "historyPath": "/repos/test-results/",
    "defaultTimeout": 30000,
    "parallelLimit": 4
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading test files |
| Logger | Logging results |

---

## Used By

| Component | How |
|-----------|-----|
| CI/CD | Automated testing |
| Development | Manual test runs |

---

*Last updated: 2026-01-17*
