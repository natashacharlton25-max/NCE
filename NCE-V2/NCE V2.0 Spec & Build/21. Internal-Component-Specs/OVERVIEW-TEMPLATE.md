# {{Component Name}} â€” Overview

## Purpose

(2-3 sentences explaining why this component exists and what problem it solves)

---

## Roles & Responsibilities

### This Component DOES:

- {{Primary responsibility 1}}
- {{Primary responsibility 2}}
- {{Primary responsibility 3}}
- {{Primary responsibility 4}}
- {{Primary responsibility 5}}

### This Component does NOT:

- {{What adjacent component X handles}}
- {{What adjacent component Y handles}}
- {{What it explicitly won't do}}
- {{Common misconception to clarify}}

---

## Boundaries

### Scope

| Dimension | Boundary |
|-----------|----------|
| **Data Owned** | {{what data this component is source of truth for}} |
| **Decisions Made** | {{what decisions this component can make autonomously}} |
| **Actions Taken** | {{what side effects this component can cause}} |

### Interfaces

| Direction | What | Format |
|-----------|------|--------|
| **Receives** | {{input 1}} | {{format}} |
| **Receives** | {{input 2}} | {{format}} |
| **Produces** | {{output 1}} | {{format}} |
| **Produces** | {{output 2}} | {{format}} |

---

## Guarantees

What other components can rely on from this component:

| Guarantee | Description |
|-----------|-------------|
| **Data Integrity** | {{what data guarantees}} |
| **Availability** | {{what availability guarantees}} |
| **Consistency** | {{what consistency guarantees}} |
| **Performance** | {{what performance guarantees}} |

---

## Constraints

What this component must respect:

| Constraint | Description | Source |
|------------|-------------|--------|
| {{constraint}} | {{description}} | {{where this comes from}} |

---

## Lifecycle

| Stage | Description |
|-------|-------------|
| **Initialization** | {{what happens on startup}} |
| **Ready State** | {{what indicates component is ready}} |
| **Shutdown** | {{what happens on shutdown}} |
| **Recovery** | {{what happens after failure}} |

---

## Cross-Cutting Concerns

### Concern Ownership

| Concern | Owner | How This Component Uses It |
|---------|-------|---------------------------|
| **Validation** | {{owner}} | {{how}} |
| **Logging** | {{owner}} | {{how}} |
| **Error Handling** | {{owner}} | {{how}} |
| **Retry Logic** | {{owner}} | {{how}} |
| **Caching** | {{owner}} | {{how}} |
| **Authentication** | {{owner}} | {{how}} |

---

## Relationship to Adjacent Components

### Upstream (Components This Calls)

| Component | Relationship | Notes |
|-----------|--------------|-------|
| {{component}} | {{what it provides}} | {{notes}} |

### Downstream (Components That Call This)

| Component | Relationship | Notes |
|-----------|--------------|-------|
| {{component}} | {{what they use}} | {{notes}} |

### Siblings (Parallel Concerns)

| Component | Boundary | Notes |
|-----------|----------|-------|
| {{component}} | {{how responsibilities are split}} | {{notes}} |

---

## Context in System

(Where this component fits in the larger architecture)

```
{{ASCII diagram showing component in context}}

Example:
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”     â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚   Input     â”‚â”€â”€â”€â”€â–¶â”‚    THIS     â”‚â”€â”€â”€â”€â–¶â”‚   Output    â”‚
â”‚  Component  â”‚     â”‚  COMPONENT  â”‚     â”‚  Component  â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜     â””â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”˜     â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
                           â”‚
                           â–¼
                    â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
                    â”‚   Storage   â”‚
                    â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

---

## Non-Functional Requirements

| Requirement | Target | Notes |
|-------------|--------|-------|
| **Response Time** | {{target}} | {{conditions}} |
| **Throughput** | {{target}} | {{conditions}} |
| **Availability** | {{target}} | {{conditions}} |
| **Data Retention** | {{target}} | {{conditions}} |

---

## Security Considerations

| Consideration | How Addressed |
|---------------|---------------|
| **Authentication** | {{how}} |
| **Authorization** | {{how}} |
| **Data Sensitivity** | {{what data, how protected}} |
| **Audit** | {{what's logged}} |

---

## Notes

(Any additional context, assumptions, or clarifications)

---
Source: PRE-SPEC-NOTES.md sections 1, 2, 5, 11
Phase: 21 (Internal Component Specs)
---
