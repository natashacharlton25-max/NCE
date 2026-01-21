# Librarian Module

> **Purpose:** Search, discover, and recommend entries from repos
> **Type:** Service Module
> **Status:** Planning

---

## Overview

Librarian is the search and discovery layer for all repos. When you don't know the exact ID or need alternatives, ask Librarian. It maintains cached indexes for fast searching and provides recommendations when entries are missing.

```
Search query → Librarian → Index lookup → Matching entries
Recommend request → Librarian → Scoring logic → Best alternatives
```

**Two access patterns:**
- **FileManager**: Know exact ID → direct fetch
- **Librarian**: Need to search/discover → index-based lookup

---

## Roles & Rules

### Librarian DOES:
- Search repos by criteria (tags, domain, status, etc.)
- Recommend alternatives when entries are missing
- Maintain cached indexes for fast lookups
- Find similar entries for substitution
- Score entries against requirements
- Rebuild indexes when repos change

### Librarian does NOT:
- Create or modify entries (Archivist does that)
- Make decisions about which alternative to use (returns options, caller decides)
- Access external APIs (only reads from local repos)
- Cache actual file contents (only indexes)

### Boundaries:
- Read-only access to repos
- Cannot create new entries (only find existing)
- Cannot modify index entries (rebuild from source)
- Cannot access repos outside configured list

---

## Functions (Bricks)

| Function | Purpose | Input | Output |
|----------|---------|-------|--------|
| `search(repo, query)` | Search repo by criteria | repo, query | matchingEntries |
| `recommend(repo, requirements)` | Recommend best match for needs | repo, requirements | recommendations |
| `getIndex(repo)` | Get cached index for repo | repo | index |
| `rebuildIndex(repo)` | Rebuild index from repo files | repo | newIndex |
| `scoreEntry(entry, criteria)` | Score entry against criteria | entry, criteria | score |
| `findSimilar(repo, entryId)` | Find entries similar to given | repo, entryId | similarEntries |
| `validateIndex(repo)` | Check index matches actual files | repo | validationResult |

---

## Steps (Sequences)

| Step | Description | Functions Used |
|------|-------------|----------------|
| `Librarian.searchWithFallback` | Search with auto-rebuild if stale | validateIndex → search (or rebuildIndex → search) |
| `Librarian.recommendAlternative` | Find alternative when primary missing | search → scoreEntry → recommend |

---

## Detailed Function Specs

### search(repo, query)

Searches repo index for matching entries.

```javascript
// Input
{
  repo: "tones",
  query: {
    domain: "business",
    tags: ["professional"],
    status: "active"
  }
}

// Process
1. getIndex(repo)
2. Filter entries by query criteria
3. Sort by relevance
4. Return matches

// Output
{
  matches: [
    { id: "formal-corporate", name: "Formal Corporate", score: 0.95 },
    { id: "business-casual", name: "Business Casual", score: 0.82 }
  ],
  total: 2,
  query: { ... }
}
```

### recommend(repo, requirements)

Recommends best entries for given requirements.

```javascript
// Input
{
  repo: "tones",
  requirements: {
    similar_to: "formal-corporate",  // Find similar to this
    domain: "business",
    exclude: ["formal-corporate"],   // Don't recommend the one that failed
    maxResults: 3
  }
}

// Process
1. If similar_to provided → findSimilar()
2. Search by domain/tags
3. Score each against requirements
4. Exclude any in exclude list
5. Return top matches

// Output
{
  recommendations: [
    { id: "business-formal", score: 0.92, reason: "Same domain, similar tags" },
    { id: "corporate-neutral", score: 0.85, reason: "Professional tone family" }
  ]
}
```

### rebuildIndex(repo)

Rebuilds the `_index.json` from actual repo files.

```javascript
// Input
"tones"

// Process
1. List all .json files in /repos/tones/ (except _index.json)
2. For each file:
   - Read file
   - Extract indexable fields (id, name, tags, domain, status)
3. Build new index
4. Write to _index.json

// Output
{
  repo: "tones",
  updated: "2026-01-17T14:30:00Z",
  count: 42,
  entries: [...]
}
```

### findSimilar(repo, entryId)

Finds entries similar to a given entry.

```javascript
// Input
{
  repo: "tones",
  entryId: "formal-corporate"
}

// Process
1. FileManager.get('tones', 'formal-corporate')
2. Extract tags, domain, type
3. Search index for entries with overlapping tags/domain
4. Score by similarity
5. Return sorted by similarity

// Output
{
  similar: [
    { id: "business-formal", similarity: 0.89 },
    { id: "corporate-neutral", similarity: 0.76 }
  ]
}
```

---

## Index Structure

Each repo has an `_index.json`:

```json
{
  "repo": "tones",
  "updated": "2026-01-17T14:30:00Z",
  "count": 42,
  "entries": [
    {
      "id": "formal-corporate",
      "name": "Formal Corporate",
      "tags": ["professional", "business", "formal"],
      "domain": "business",
      "type": "text",
      "status": "active"
    },
    {
      "id": "friendly-casual",
      "name": "Friendly Casual",
      "tags": ["casual", "friendly", "approachable"],
      "domain": "consumer",
      "type": "text",
      "status": "active"
    }
  ]
}
```

**Index is auto-generated** - never edit manually. Archivist triggers rebuild on repo changes.

---

## Cache Management

```javascript
// Cache structure (in memory)
cache = {
  "tones": { index: {...}, loadedAt: timestamp },
  "models": { index: {...}, loadedAt: timestamp }
}

// Cache invalidation (called by Archivist after writes)
Librarian.cache.invalidate('tones');

// Cache refresh (if stale or missing)
if (!cache[repo] || isStale(cache[repo])) {
  cache[repo] = await rebuildIndex(repo);
}
```

---

## Config

### module.config.json

```json
{
  "module": "Librarian",
  "version": "1.0.0",
  "description": "Search, discover, and recommend from repos",

  "functions": [
    {
      "name": "search",
      "description": "Search repo by criteria",
      "input": ["repo", "query"],
      "output": "matchingEntries"
    },
    {
      "name": "recommend",
      "description": "Recommend best match for needs",
      "input": ["repo", "requirements"],
      "output": "recommendations"
    },
    {
      "name": "getIndex",
      "description": "Get cached index for repo",
      "input": ["repo"],
      "output": "index"
    },
    {
      "name": "rebuildIndex",
      "description": "Rebuild index from repo files",
      "input": ["repo"],
      "output": "newIndex"
    },
    {
      "name": "scoreEntry",
      "description": "Score entry against criteria",
      "input": ["entry", "criteria"],
      "output": "score"
    },
    {
      "name": "findSimilar",
      "description": "Find entries similar to given",
      "input": ["repo", "entryId"],
      "output": "similarEntries"
    },
    {
      "name": "validateIndex",
      "description": "Check index matches actual files",
      "input": ["repo"],
      "output": "validationResult"
    }
  ],

  "steps": [
    {
      "name": "searchWithFallback",
      "description": "Search with auto-rebuild if stale",
      "sequence": ["validateIndex", "search"]
    },
    {
      "name": "recommendAlternative",
      "description": "Find alternative when primary missing",
      "sequence": ["search", "scoreEntry", "recommend"]
    }
  ],

  "config": {
    "cacheMaxAge": 300000,
    "defaultMaxResults": 10,
    "similarityThreshold": 0.6
  }
}
```

---

## Dependencies

| Depends On | For |
|------------|-----|
| FileManager | Reading repo files for indexing |
| Writer | Writing _index.json files |

---

## Used By

| Module | How |
|--------|-----|
| PromptBuilder | Finding alternative tones/prompts |
| FailureHandler | Getting recommendations on failure |
| ModelManager | Finding alternative models |
| Self-healing | Finding templates for new entries |

---

*Last updated: 2026-01-17*
