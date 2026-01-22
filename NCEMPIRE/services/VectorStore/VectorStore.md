# VectorStore Module

> **Purpose:** Store and retrieve vector embeddings
> **Type:** Services Module
> **Status:** Placeholder
> **Version:** 0.0.0

---

## Overview

VectorStore provides storage, retrieval, and similarity search capabilities for vector embeddings. It acts as the semantic memory layer, allowing the system to find content based on meaning rather than just keywords.

---

## Notes

### Role
- Store audience psychographics, brand voice samples, theme concepts
- Enable semantic comparison for ContentAlignment
- Support RAG-based validation

### Owns
- Vector storage
- Similarity search
- Embedding indexing

### Architecture Strategy

Uses SQLite-VSS (Vector Similarity Search extension) or pure-JS cosine similarity fallback for maximum portability with existing DatabaseHandler.

### Key Responsibilities
- **Store Vectors:** Save embeddings linked to their source entity (Theme, Audience, Brand Truth).
- **Similarity Search:** `findSimilar(vector, threshold, limit)` returns items sorted by semantic closeness.
- **Metadata Filtering:** Allow hybrid search (e.g., "Find semantically similar truths" AND "where brandId = X").
- **Namespace Management:** Keep "Brand Voice" vectors separate from "Audience" vectors to prevent pollution.

### Core Methods

| Method | Input | Output | Description |
|--------|-------|--------|-------------|
| upsert | { id, text, vector, metadata } | boolean | Stores or updates a vector entry. |
| search | { vector, topK, filter } | Result[] | Returns top K nearest neighbors. |
| delete | { id } | boolean | Removes a vector. |
| purge | { namespace } | boolean | Clears an entire category of vectors. |

### Data Schema (SQLite)

```sql
CREATE TABLE vector_store (
  id TEXT PRIMARY KEY,
  namespace TEXT NOT NULL,  -- e.g., "audience_psychographics"
  content TEXT,             -- Original text content
  metadata JSON,            -- Context (brandId, source, etc.)
  vector BLOB,              -- Binary vector data
  created_at INTEGER
);
```

### Use Cases

**ContentAlignment:**
- Query: "Does this generated paragraph align with the brand's core truths?"
- Action: Vectorize paragraph → Search brand_truths namespace → Calc similarity score.

**Librarian:**
- Query: "Find templates related to 'empathy'."
- Action: Vectorize "empathy" → Search template_descriptions namespace.

**TransformationCheck:**
- Query: "Does this content facilitate the 'Belief Shift'?"
- Action: Compare vector of Content vs. vector of Desired Outcome.

### Dependencies (Systems)
- **EmbeddingService:** Generates vectors for storage.
- **DatabaseHandler:** Underlying SQLite storage.

### Feeds Into
- **ContentAlignment:** Semantic similarity scoring.
- **Librarian:** Template and resource discovery.
- **TransformationCheck:** Outcome validation.

### Pre-Build Concerns
- **Indexing:** Plain brute-force cosine similarity is fine for <10k items. For scaling beyond, implement HNSW index (via sqlite-vss or external vector DB).
- **Re-indexing:** If embedding model changes (in EmbeddingService), all stored vectors become invalid and must be regenerated.
- **Latency:** Vector search is CPU intensive. Ensure VectorStore runs in a worker thread if the dataset grows large.

---

*Spec version: 0.0.0*
*Created: 2026-01-21*
*Status: Placeholder*
