# 🗄️ Retrieval with Vector Databases

> Production-grade retrieval: scaling vector search, chunking, query parsing, and re-ranking.

---

## 🧩 Why Vector Databases?

Standard relational databases can handle basic retrieval, but **vector operations (semantic search) degrade sharply at scale**. Vector databases are purpose-built to store high-dimensional vectors and run ANN algorithms efficiently — making them the backbone of production RAG systems.

---

## ⚡ Scaling Vector Search: KNN → ANN

### The Problem with K-Nearest Neighbors (KNN)
- Computes distance between the query vector and **every** document vector.
- Runtime scales **linearly** — 1K docs = 1K calculations; 1B docs = 1B calculations.
- Completely impractical at production scale.

### Approximate Nearest Neighbors (ANN)
- Uses clever data structures (proximity graphs) to **skip most comparisons**.
- Trade-off: not guaranteed to find the absolute best match, but finds **very close** ones — fast.

### Navigable Small World (NSW)
1. Pre-build a **proximity graph**: each document node connects to its closest neighbors.
2. On query arrival: pick a random entry point → hop to whichever neighbor is closest to the query vector → repeat until no neighbor is closer.
3. Result: fast traversal that converges near the best match without checking everything.

### HNSW (Hierarchical NSW) — The Industry Standard
Adds **multiple layers** to NSW:

| Layer | Vectors | Purpose |
|-------|---------|---------|
| Top (L3) | 10 | Fast coarse jump |
| Mid (L2) | 100 | Refinement |
| Bottom (L1) | All 1000 | Precise local search |

- Search starts at the top → big jumps to the right neighborhood → drill down to the full index.
- Runtime: **O(log n)** vs KNN's O(n) — enables billions of vectors with milliseconds latency.
- Proximity graph is **pre-computed** once; no rebuild per query.

---

## 🏛️ Vector Databases (Weaviate)

Key operations in any vector DB workflow:

```
Configure DB → Load documents → Index (sparse + dense vectors + ANN index) → Query
```

**Search types supported:**
- **Semantic (vector) search** — dense embedding similarity
- **Keyword (BM25) search** — sparse inverted index
- **Hybrid search** — weighted blend of both via `alpha` parameter  
  *(e.g., `alpha=0.25` → 25% vector, 75% keyword)*
- **Filtered search** — apply metadata filters on top of any search type

> 💡 **Hybrid search is the production default** — balances semantic similarity with exact term matching.

---

## ✂️ Chunking

Splitting documents into smaller pieces before indexing. Three reasons:
1. Embedding models have **token limits**.
2. Smaller chunks → **sharper, more precise vectors**.
3. Retrieve only the relevant excerpt → **less LLM context waste**.

### Fixed-Size Chunking
- Chunk every N characters (e.g., 500 chars).
- Add **overlap** (e.g., 10% = 50 chars) to avoid cutting context at boundaries.
- Simple, fast, good default. ✅ Start here.

### Recursive Character Splitting
- Split on meaningful characters (e.g., `\n` between paragraphs, `</p>` for HTML, function definitions for code).
- Produces **variable-size chunks** that respect document structure.

### Semantic Chunking
- Vectorize each sentence; compare it to the growing chunk.
- If similarity drops below threshold → start a new chunk.
- Chunks follow the **author's train of thought**, not arbitrary character counts.
- More expensive (repeated vectorization), but higher retrieval quality.

### LLM-Based Chunking
- Give the full document + chunking instructions to an LLM.
- Produces semantically coherent, context-aware chunks.
- Expensive but high-performing; increasingly viable as LLM costs drop.

### Context-Aware Chunking (Add-On to Any Strategy)
- LLM annotates each chunk with a brief summary of its role in the broader document.
- Improves both **vectorization quality** and **LLM understanding** at retrieval time.
- Often the **best first upgrade** beyond fixed-size chunking.

> 🎯 **Rule of thumb:** Start with fixed-size (500 chars, 50–100 char overlap). Upgrade only after measuring retrieval quality.

---

## 🔍 Query Parsing

User prompts are conversational — not optimized for retrieval. Parse them before sending to the vector DB.

### LLM Query Rewriting (Default — Always Do This)
Rewrite the user's messy prompt into a clean, retrieval-optimized query:
- Remove irrelevant narrative
- Clarify ambiguous phrasing
- Add domain terminology and synonyms

**Example:**
```
Raw:    "I was walking my dog and she yanked the leash. My shoulder is numb 3 days later."
Rewritten: "Sudden forceful shoulder pull → persistent shoulder and finger numbness 3 days. 
            Possible causes: neuropathy, nerve impingement?"
```

### Named Entity Recognition (NER) — Advanced
- Identify entities in the query (people, dates, locations, etc.)
- Use extracted entities to **inform filters or steer vector search**
- Efficient enough to run per-query (e.g., GLiNER model)

### HyDE — Hypothetical Document Embeddings — Advanced
- LLM generates a *hypothetical ideal answer document* for the query.
- Embed that hypothetical doc → use its vector as the search query.
- Converts "prompt vs. document" mismatch (apples-to-oranges) into "document vs. document" (apples-to-apples).
- Improves recall at the cost of added latency.

---

## 🏆 Search Architectures

### Bi-Encoder (Standard)
- Documents and prompts embedded **separately**.
- All document vectors pre-computed → only prompt needs embedding at query time.
- Fast ✅ | Good quality ✅ | Default choice ✅

### Cross-Encoder (Gold Standard Quality)
- Concatenate `[prompt + document]` → pass through model → outputs relevance score (0–1).
- Captures **deep contextual interactions** between prompt and document.
- Cannot pre-compute → must score every doc at query time → **scales terribly**.
- ❌ Not viable as a standalone search technique.
- ✅ Perfect for **re-ranking** a small shortlist.

### ColBERT (Best of Both)
- Embed each **token** separately (not whole document).
- At query time: each prompt token finds its most similar document token → sum max scores (**MaxSim**).
- Nearly cross-encoder quality at near bi-encoder speed.
- Cost: stores **N vectors per document** (one per token) → large storage footprint.
- Best for high-precision domains (legal, medical).

---

## 🔁 Re-Ranking

**Problem:** ANN vector search returns semantically related docs, not necessarily the most *relevant* ones.

**Solution:** Over-fetch → re-rank with a better model → return only the top subset.

```
Hybrid Search (retrieve 20–100 docs)
        ↓
   Re-Ranker (cross-encoder or LLM)
        ↓
Final Top 5–10 (high precision)
```

- Re-ranker rescores each `(prompt, doc)` pair with a cross-encoder or LLM.
- Only applied to the shortlist → expensive model is now **affordable**.
- Adds small latency — almost always worth it.
- In most vector DBs: **one line of code** to enable.

> 🎯 Re-ranking is often the **highest ROI improvement** you can make to a retriever.

---

## 🗺️ Full Production Retrieval Pipeline

```
User Prompt
    ↓
Query Parser (LLM rewrite / NER / HyDE)
    ↓
Vector DB — Hybrid Search (ANN + BM25) + Metadata Filters
    ↓
Re-Ranker (Cross-Encoder / LLM)
    ↓
Top-K Chunks → LLM Context Window
```

---

## 📓 Labs

| Lab | Concepts Covered |
|-----|-----------------|
| `vector_db_weaviate.ipynb` | Setup, indexing, hybrid search, filters |
| `chunking_strategies.ipynb` | Fixed-size, recursive, semantic, LLM-based chunking |
| `query_parsing.ipynb` | LLM rewriting, NER, HyDE |
| `reranking.ipynb` | Cross-encoder re-ranking pipeline |
| `end_to_end_retriever.ipynb` | Full production retrieval pipeline |
