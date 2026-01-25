# 🔎 Retriever Architecture

*In a Retrieval-Augmented Generation (RAG) system, the **retriever** is responsible for finding the most relevant documents for a given user prompt.*

When a prompt is received, it is first sent to the retriever, which searches a **knowledge base** (a collection of documents 📚) and returns the best matches to be passed to the **LLM** 🤖.

![Retriever Architecture](1.png)

---

## 📑 Content Index

* [Keyword Search](#-keyword-search)
* [Semantic Search](#-semantic-search)
* [Metadata Filtering](#-metadata-filtering)
* [Hybrid Search](#-hybrid-search)

---

## 🔑 Keyword Search

Keyword search retrieves documents that contain the **exact words** from the user’s prompt.

* Precise and fast ⚡
* Sensitive to exact wording
* Limited when documents use different phrasing

---

## 🧩 Semantic Search

Semantic search retrieves documents based on **meaning**, not exact wording.

* Uses embeddings and vector similarity
* Finds relevant documents even when wording differs
* More flexible than keyword search

---

## 🏷️ Metadata Filtering

*Metadata filtering* narrows down documents based on **structured attributes**, not content. It works alongside keyword or semantic search to refine results.

### Concept Index

* [What is Metadata Filtering](#what-is-metadata-filtering)
* [How It Works](#how-it-works)
* [Example](#example)
* [Use in RAG](#use-in-rag)
* [Advantages](#advantages)
* [Limitations](#limitations)

### What is Metadata Filtering

Metadata filtering selects documents using **metadata** rather than full text. Common metadata includes:

* Title
* Author
* Creation date
* Access level (e.g., free or paid)
* Region or department

Think of it as **strict rules that filter a large dataset** before further retrieval.
![Metadata Filtering](2.png)
### How It Works

1. Each document in the knowledge base has metadata attached.
2. Filters exclude documents that do not meet criteria.
3. Only documents satisfying **all conditions** are returned.

> Conceptually, it’s like filtering rows in a spreadsheet or a SQL query.

Filters can be dynamic depending on the user:

* Paid vs free subscriber
* User location / region
* Department-specific access

### Example

Suppose you build a retriever for a newspaper:

* Knowledge base: thousands of articles
* Metadata per article: author, date, section, paid/free, region

**Queries:**

* “All opinion articles by Alice between June and July 2024”
* System detects the user is a free subscriber → excludes paid articles
* System detects the reader is in Europe → returns only Europe articles

> Only metadata is used in this step — the full text is ignored.

### Use in RAG Systems

* Metadata filtering **does not retrieve documents alone**.
* It refines results from **keyword or semantic search**.
* Filters are based on **user attributes**, not query text.

### Advantages ✅

* Conceptually simple — easy to understand and debug
* Fast and well-optimized
* Only method to strictly enforce inclusion/exclusion rules

### Limitations ⚠️

* Not a true search technique — does **not rank by relevance**
* Overly rigid — ignores document content
* Cannot determine if a document is truly relevant

> Metadata filtering is effective **only when combined with other retrieval techniques** like keyword or semantic search.

---

## 🔀 Hybrid Search

In practice, retrievers combine these techniques:

1. Run keyword search 🔑
2. Run semantic search 🧩
3. Apply metadata filters 🏷️
4. Merge and re-rank results 📊
5. Return the top relevant documents 🎯

This approach is called **hybrid search**.

---

### ✅ Notes on Navigation

* Clicking the links in the **Content Index** will jump to the respective section.
* Metadata Filtering section contains a **full concept with examples and pros/cons**, while other sections remain concise.

---

If you want, I can **also expand Keyword Search, Semantic Search, and Hybrid Search** sections in the same “book-style” format with **examples, pros/cons, and clear explanations** so the README becomes **one full beginner-to-advanced guide for RAG retrievers**.

Do you want me to do that next?
