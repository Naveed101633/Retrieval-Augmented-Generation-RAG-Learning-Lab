# 🔎 Retriever Architecture

*In a Retrieval-Augmented Generation (RAG) system, the **retriever** is responsible for finding the most relevant documents for a given user prompt.*

When a prompt is received, it is first sent to the retriever, which searches a **knowledge base** (a collection of documents 📚) and returns the best matches to be passed to the **LLM** 🤖.

![Retriever Architecture](Images/1.png)

---

## 📑 Content Index

* [Keyword Search](#-keyword-search)
* [Semantic Search](#-semantic-search)
* [Metadata Filtering](#-metadata-filtering)
* [Hybrid Search](#-hybrid-search)

---

## 🔑 Keyword Search

**Keyword search** retrieves documents containing the **exact words** from the user’s prompt.
It is the **classic retrieval technique**, used in search engines and databases for decades.

**Characteristics**

* Precise and fast ⚡
* Sensitive to exact wording
* Weak at understanding meaning

---

### 📌 How Keyword Search Works

1. **Bag of Words (BoW)**

   * Text is converted into vectors of word counts
   * Word order is ignored
   * Results in sparse vectors

2. **Inverted Index**

   * Maps each word to the documents that contain it
   * Enables fast keyword lookups

3. **Document Scoring**

   * Counts keyword matches
   * Normalizes for document length
   * Weights important words using **TF-IDF**

---

### 📑 TF-IDF (Term Frequency–Inverse Document Frequency)

* Rare words → higher weight
* Common words → lower weight
* Highlights discriminative terms

![TF-IDF](Images/3.png)

---

### 🧩 Example

Prompt:
`"making pizza without a pizza oven"`

* Keywords: `pizza`, `making`, `oven`
* Documents containing rare terms like **pizza** score higher
* Common words like *“a”* contribute little

![TF-IDF](Images/4.png)
![TF-IDF](Images/5.png)

---

### 🏆 BM25 (Best Matching 25)

BM25 is the **industry-standard keyword ranking algorithm** used in modern retrievers.

![BM25](Images/6.png)

**Why BM25 improves on TF-IDF**

* **Term frequency saturation**
  Repeating a word many times yields diminishing returns
  (“pizza” ×20 ≠ twice as relevant as “pizza” ×10)

* **Smarter length normalization**
  Long documents are penalized gently, not aggressively

* **Tunable behavior**

  * `k₁` → controls term frequency saturation
  * `b` → controls length normalization

![BM25 Params](Images/7.png)
![BM25 Params](Images/8.png)

---

### ✅ Strengths

* Fast and interpretable
* Ensures keyword presence
* Ideal for exact or technical queries

### ⚠️ Limitations

* Cannot understand synonyms or intent
* Misses semantic relevance
* Needs semantic search for completeness

---

# 🧩 Semantic Search

**Semantic search retrieves documents based on meaning, not exact words.**

Instead of asking:

> *“Which documents contain these words?”*

It asks:

> *“Which documents express the same idea?”*

This makes it essential for natural language queries in RAG systems.

---

### ❌ Why Keyword Search Is Not Enough

Keyword search fails when:

* Different words share the same meaning
  *happy ≠ glad*
* One word has multiple meanings
  *Python (language) ≠ python (snake)*

Semantic search resolves this by modeling **context and intent**.

---

### 🧠 Core Idea: Everything Becomes a Vector

Semantic search converts text into vectors using an **embedding model**:

1. Documents → vectors
2. Prompt → vector
3. Similar meaning → similar vectors

![Embedding Models](Images/9.png)

---

### 📍 What Is an Embedding?

An **embedding** is a numerical representation of meaning.

* Similar concepts → vectors close together
* Unrelated concepts → vectors far apart

Think of it as a **map of meaning** in high-dimensional space:

* *food* ↔ *cuisine* → close
* *trombone* ↔ *cat* → far

![Embeddings](Images/10.png)

---

### 📐 Measuring Similarity

The retriever compares vectors using **cosine similarity**:

* Measures **direction**, not magnitude
* Focuses on semantic orientation
* Stable in high-dimensional spaces

Higher cosine similarity → higher relevance

![Cosine Similarity](Images/12.png)

---

### 🧩 Example

Prompt:
`"He whispered quietly during class"`

* Document A: *“He spoke softly in class”* → high similarity
* Document B: *“Her daughter brightened the gloomy day”* → low similarity

![Semantic Example](Images/11.png)

---

### 🔍 Semantic Search in a RAG Pipeline

1. Embed all documents
2. Embed the user prompt
3. Compute similarity scores
4. Rank documents
5. Send top results to the LLM

![Semantic_Serarch](Images/13.png)

---

### ✅ Strengths

* Understands synonyms and paraphrases
* Matches intent, not wording
* Essential for conversational queries

### ⚠️ Limitations

* Does not guarantee keyword presence
* May surface semantically close but factually weak results
* Best combined with keyword search

---
## ⚙️ How Embeddings Are Trained
**1. Collect Examples**
- Positive pairs: text with similar meaning
- Negative pairs: text with different meaning

Millions of pairs are used to capture subtle relationships across concepts.

**2. Initialize Vectors**

- Start randomly: vectors have no meaning at the beginning
- Untrained vectors → retrieval would be meaningless
![Semantic_Serarch](Images/16.png)

**3. Contrastive Learning**

_For each pair:_

1. Pull positive pairs closer
2. Push negative pairs farther apart
3. Repeat iteratively over all examples

The model updates its internal parameters at each step

**4. High-Dimensional Space**

- Embeddings often have hundreds or thousands of dimensions
- High dimensions give flexibility to place millions of texts meaningfully
- Each text is influenced by many positives and negatives at once
![Semantic_Serarch](Images/17.png)

---
# 🏷️ Metadata Filtering

**Metadata filtering** narrows documents using **structured attributes**, not text content.

![Metadata Filtering](Images/2.png)

---

### 📌 What Is Metadata?

Metadata includes:

* Author
* Date
* Section
* Access level
* Region or department

It acts as **strict pre-conditions** before ranking.

---

### ⚙️ How It Works

1. Each document has metadata fields
2. Filters exclude documents that fail conditions
3. Remaining documents are passed to search

> Comparable to SQL `WHERE` clauses or spreadsheet filters

---

### 🧩 Example

Newspaper retriever:

* User is a free subscriber
* Region: Europe

Query:

> “Opinion articles by Alice in June 2024”

System:

* Excludes paid articles
* Filters to Europe region
* Ignores full text at this stage

---

### ✅ Advantages

* Fast and deterministic
* Enforces access control
* Easy to debug

### ⚠️ Limitations

* Does not rank relevance
* Ignores document meaning
* Cannot work alone

---

## 🔀 Hybrid Search

Production retrievers combine all techniques:

1. Keyword search 🔑
2. Semantic search 🧩
3. Metadata filtering 🏷️
4. Merge and re-rank 📊
5. Return best documents 🎯

> **Hybrid search balances precision, meaning, and control — the foundation of modern RAG systems.**

---
