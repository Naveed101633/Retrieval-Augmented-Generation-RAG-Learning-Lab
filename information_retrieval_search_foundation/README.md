
# 🔎 Retriever Architecture 

*In a Retrieval-Augmented Generation (RAG) system, the **retriever** is responsible for finding the most relevant documents for a given user prompt.*

When a prompt is received, it is first sent to the retriever, which searches a **knowledge base** (a collection of documents 📚) and returns the best matches to be passed to the **LLM** 🤖.

![Retriever Architecture](1.png)

---

## 🧠 Core Retrieval Techniques

Modern retrievers typically use **three complementary techniques**.

---

### 🔑 Keyword Search

Keyword search retrieves documents that contain the **exact words** from the user’s prompt.

* Precise and fast ⚡
* Sensitive to exact wording
* Limited when documents use different phrasing

---

### 🧩 Semantic Search

Semantic search retrieves documents based on **meaning**, not exact wording.

* Uses embeddings and vector similarity
* Finds relevant documents even when wording differs
* More flexible than keyword search

---

### 🏷️ Metadata Filtering

Metadata filtering removes documents that do not meet **strict criteria**, such as:

* department
* access level
* document type

This step ensures only **allowed and relevant** documents are considered ✅.

---

## 🔀 Hybrid Search

In practice, retrievers combine these techniques:

1. Run keyword search 🔑
2. Run semantic search 🧩
3. Apply metadata filters 🏷️
4. Merge and re-rank results 📊
5. Return the top relevant documents 🎯

This approach is called **hybrid search**.
