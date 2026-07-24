# Week 2 - Day 1: Embeddings & Vector Databases

> **Learning Goal:** Understand how embeddings represent text as vectors, why semantic search beats keyword search, what vector databases do, and how this powers enterprise AI systems through Retrieval-Augmented Generation (RAG).

> **How to use this guide:**
> 1. Read sections 1–6 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain what an embedding is and why it captures semantic meaning.
- Describe why semantic search outperforms keyword search for enterprise AI.
- Explain what a vector database is and what problem it solves.
- List common vector database options (Pinecone, Weaviate, ChromaDB, Milvus, pgvector).
- Describe the RAG (Retrieval-Augmented Generation) pattern and why enterprises use it.
- Walk through how embeddings, vector search, and LLMs combine to answer a real user question.

---

# 1. Why LLMs Need More Than Prompting

An LLM is trained on general knowledge, but it does not know your company's latest support cases, internal policies, customer records, or product documentation. You cannot simply ask it:

    "Show me the support case where the customer complained about duplicate invoices."

Three fundamental constraints:

1. **Context window limits** — even long-context models (100K+ tokens) cannot hold every enterprise document in every conversation.
2. **Constantly changing data** — company data changes daily; retraining the model for every update is impractical and slow.
3. **Hallucination risk** — without grounding in real data, the LLM may confidently invent an answer.

The solution is **Retrieval-Augmented Generation (RAG)**:

```text
Convert documents → Store as vectors → Search for similar meanings
                → Retrieve relevant docs → Send to LLM with the prompt
```

RAG lets the LLM answer from trusted, up-to-date enterprise data without retraining.

> 🧠 **Memory hook:** LLMs are smart but have no live memory. RAG = **Retrieve**, then generate. Grounding beats guessing.

---

# 2. What is an Embedding?

An **embedding** is a list of numbers (a vector) that represents the **semantic meaning** of text. Words or sentences with similar meanings get vectors that are close together in vector space.

**Example:**

```text
"I love Salesforce"
        ↓
[0.23, -0.71, 1.02, 0.44, ...]

"I enjoy Salesforce"
        ↓
[0.20, -0.68, 1.01, 0.41, ...]  (close to the first vector)

"I need pizza"
        ↓
[-0.82, 2.01, -0.43, ...]  (far away — different meaning)
```

**How embeddings are created:**
- An **embedding model** (e.g., OpenAI `text-embedding-ada-002`, Cohere, Hugging Face Sentence-Transformers) reads text and outputs a vector.
- The model is trained to place semantically similar sentences near each other in high-dimensional space (typically 384–1536 dimensions).

> 🧠 **Memory hook:** **Closer vectors = closer meaning.** Embeddings turn "meaning" into math you can search.

**Key idea:** embeddings capture **semantic similarity**, not just word overlap. This is what lets AI understand that "refund" and "money back" and "return my payment" all mean the same thing — something keyword search cannot do.

---

# 3. Why Keywords Fail (Semantic Search vs. Keyword Search)

Traditional search (SQL `LIKE`, Elasticsearch, basic full-text index) matches **exact words or word stems**.

**Example scenario:**

Your support article title is:
```
Refund Policy
```

A customer asks:
```
How do I get my money back?
```

- **Keyword search:** ❌ No match — the words "money back" do not appear in "Refund Policy".
- **Semantic search (embedding-based):** ✅ Finds the article — the embedding model knows "get my money back" and "refund" are semantically equivalent.

**Another example:**

Document:
```
Customer received multiple invoices for the same order.
```

Query:
```
Show me the case about duplicate invoices.
```

- **Keyword search:** might miss it — "duplicate" ≠ "multiple".
- **Semantic search:** matches it — the meanings align.

**When keywords work fine:**
- Exact product SKUs, IDs, email addresses, structured fields (customer name, order number).

**When embeddings win:**
- Natural-language questions, synonyms, paraphrases, conceptual overlap (e.g., "billing issue" ↔ "invoice problem").

> 🧠 **Memory hook:** Keywords = exact match. Embeddings = meaning match. Enterprise users ask in natural language → embeddings win.

---

# 4. Vector Databases

A **vector database** is a specialized data store optimized for:
1. **Storing embeddings** (high-dimensional vectors).
2. **Fast similarity search** — finding the nearest vectors to a query vector (approximate nearest neighbors / ANN).

**Why not use a regular SQL database?**
- Traditional databases are built for equality and range queries (`WHERE id = 123`, `WHERE date > X`).
- Vector similarity (cosine similarity, Euclidean distance) over 1000+ dimensions requires specialized indexing (HNSW, IVF) that vector databases provide.

**Popular vector database options:**

| Database | Notes |
|---|---|
| **Pinecone** | Fully managed cloud service; simple API; scales automatically. |
| **Weaviate** | Open-source; built-in hybrid search (vector + keyword); GraphQL API. |
| **ChromaDB** | Open-source; lightweight; embeddable; great for prototypes / local dev. |
| **Milvus** | Open-source; designed for massive scale; cloud or self-hosted. |
| **pgvector** | PostgreSQL extension; store vectors in an existing Postgres DB; good for adding vector search to a relational app. |

They all answer the same question:
```
Find the top K most similar documents to this query vector.
```

instead of:
```sql
SELECT * FROM documents WHERE title LIKE '%refund%'
```

**What gets stored in the vector DB:**
- The **embedding** (the vector).
- **Metadata** — the document ID, title, timestamp, source system, etc.
- Optionally, the **raw text** (or a pointer to it).

When a match is found, you retrieve the metadata + text so the LLM can read the actual content.

---

# 5. How RAG Works (The Full Flow)

**RAG = Retrieval-Augmented Generation** — retrieve relevant context, then let the LLM generate an answer grounded in that context.

**Flow diagram:**

```text
    Customer Question
          │
          ▼
    Embedding Model (convert question → vector)
          │
          ▼
    Vector Database (similarity search)
          │
          ▼
    Top K Matching Documents (retrieve text)
          │
          ▼
    LLM (prompt = question + retrieved docs)
          │
          ▼
    Final Answer (grounded in retrieved context)
```

**Step-by-step example:**

**User asks:**
```
Why did Opportunity ABC fail?
```

**What the system does:**

1. **Embedding:** convert the question into a vector using an embedding model.
2. **Search:** query the vector database for the 5 most similar documents (e.g., emails, notes, CRM records mentioning Opportunity ABC).
3. **Retrieve:** fetch the text of those 5 documents.
4. **Prompt construction:** build a prompt like this:
   ```
   Context:
   [Retrieved email 1]
   [Retrieved note 2]
   [Retrieved case 3]
   
   Question: Why did Opportunity ABC fail?
   
   Answer based only on the context above.
   ```
5. **LLM generation:** the LLM reads the retrieved context and produces an answer grounded in real data.

**Key benefit:** the LLM is **not guessing** — it answers from documents that actually exist in the company's systems. This reduces hallucination and keeps answers current (as long as the vector DB is kept up to date).

> 🧠 **Memory hook:** RAG = **Retrieve the facts, then generate the answer.** Context grounds the LLM.

---

# 6. Enterprise Examples

**Salesforce Agentforce:**
- An agent retrieves from:
  - **Knowledge articles** (public or internal help docs).
  - **Case history** (past support tickets).
  - **CRM records** (accounts, opportunities, contacts).
  - **Data Cloud** (unified customer profiles from multiple sources).
- Before answering, it performs a vector search to find relevant content, then the LLM reasons over that grounded context.

**Microsoft 365 Copilot:**
- Searches:
  - **Outlook emails**, **Teams chats**, **SharePoint documents**, **OneDrive files**.
- When you ask "What did Sarah say about the Q4 budget?", Copilot retrieves relevant emails/chats, then generates a summary.

**Internal Company Assistant:**
- An employee asks:
  ```
  How do I request production access?
  ```
- The assistant retrieves:
  - Internal wiki pages.
  - HR policy documents.
  - Security documentation.
- Then the LLM generates step-by-step instructions grounded in those sources.

**Common architecture pattern you'll see in interviews:**

```text
        Enterprise Application
               │
        User Question (natural language)
               │
               ▼
        Embedding Model (e.g., OpenAI, Cohere)
               │
               ▼
        Vector Database (Pinecone, Weaviate, etc.)
               │
               ▼
        Top K Relevant Documents (retrieved)
               │
               ▼
        LLM (e.g., GPT-4, Claude) + prompt with context
               │
               ▼
        Response (grounded in retrieved docs)
```

This pattern (embed → search → retrieve → prompt LLM) is the **foundation** of nearly every enterprise AI assistant.

---

# Interview Questions & Answers

## Q1. What is an embedding, and why does it matter for enterprise AI?

An embedding is a vector (list of numbers) that represents the semantic meaning of text. Words or sentences with similar meanings get vectors that are close together. It matters because embeddings let you search by meaning (semantic search) instead of exact keywords — so an AI can understand that "refund" and "get my money back" are the same thing, even if the words don't match. This is the foundation of RAG and enterprise search.

## Q2. Why can't you just put all your company documents into the LLM's prompt?

Three reasons: (1) context window limits — even long-context models can't hold every document in every conversation; (2) constantly changing data — company data updates daily, and retraining or reprompting for every change is impractical; (3) cost and latency — sending huge prompts is expensive and slow. Instead, you retrieve only the relevant subset via RAG, which keeps answers grounded and current.

## Q3. How does semantic search differ from keyword search?

Keyword search matches exact words or stems (e.g., SQL `LIKE`, full-text index). Semantic search uses embeddings to match by meaning — so "duplicate invoices" and "multiple invoices for the same order" are understood as similar, even if the words differ. Enterprise users ask questions in natural language with synonyms and paraphrases; embeddings handle that, keywords often don't.

## Q4. What is a vector database, and why do you need one?

A vector database stores high-dimensional embeddings and performs fast similarity search (finding the nearest vectors to a query vector). You need one because traditional SQL databases aren't optimized for vector similarity — vector DBs use specialized indexes (HNSW, IVF) to handle thousands of dimensions efficiently. Examples: Pinecone, Weaviate, ChromaDB, Milvus, pgvector.

## Q5. Walk through the RAG flow: user asks a question, how does the system answer?

(1) Convert the question into an embedding using an embedding model. (2) Query the vector database for the top K most similar documents. (3) Retrieve the text of those documents. (4) Build a prompt with the question + retrieved context. (5) Send the prompt to the LLM. (6) The LLM generates an answer grounded in the retrieved docs. This reduces hallucination and keeps answers current.

## Q6. Name three vector database options and when you might choose each.

**Pinecone** — fully managed cloud service; great for production with minimal ops overhead. **Weaviate** — open-source with built-in hybrid search (vector + keyword); good for complex queries. **pgvector** — PostgreSQL extension; ideal if you already use Postgres and want to add vector search without a separate DB. Others: ChromaDB (lightweight, local dev), Milvus (massive scale).

## Q7. In a Salesforce Agentforce context, what data sources would you typically index in a vector database for RAG?

Knowledge articles, case history, CRM records (accounts, opportunities, contacts), Data Cloud (unified customer profiles), email/chat transcripts, and internal documentation. The agent retrieves from these sources before answering, so its responses are grounded in actual company data.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats re-reading.

<details>
<summary>1. What is an embedding in one sentence?</summary>

A vector (list of numbers) that represents the semantic meaning of text, where similar meanings produce vectors close together in vector space.
</details>

<details>
<summary>2. Why can't you rely solely on keyword search for enterprise AI assistants?</summary>

Because users ask questions in natural language with synonyms and paraphrases (e.g., "money back" vs. "refund"), and keyword search only matches exact words — it misses semantic similarity.
</details>

<details>
<summary>3. Complete the phrase: "Closer vectors = ___"</summary>

Closer **meaning**. Embeddings map semantic similarity to geometric proximity.
</details>

<details>
<summary>4. What does RAG stand for, and what problem does it solve?</summary>

**Retrieval-Augmented Generation**. It solves the problem of grounding LLM answers in up-to-date, trusted enterprise data without retraining the model. Retrieve relevant docs, then generate the answer from them.
</details>

<details>
<summary>5. List the five steps of the RAG flow from question to answer.</summary>

(1) Embed the question. (2) Search the vector DB for similar docs. (3) Retrieve the top K matches. (4) Build a prompt with question + context. (5) LLM generates an answer grounded in the context.
</details>

<details>
<summary>6. Why can't a standard SQL database efficiently handle similarity search over embeddings?</summary>

SQL databases are optimized for equality/range queries, not high-dimensional vector similarity. Vector databases use specialized indexes (HNSW, IVF) for fast approximate nearest neighbor search.
</details>

<details>
<summary>7. Name four vector database options.</summary>

**Pinecone, Weaviate, ChromaDB, Milvus** (or pgvector).
</details>

<details>
<summary>8. What is stored in a vector database (three things)?</summary>

(1) The **embedding** (vector). (2) **Metadata** (doc ID, title, timestamp, source). (3) Optionally, the **raw text** or a pointer to it.
</details>

<details>
<summary>9. Why doesn't putting all company docs in the LLM context window work?</summary>

Context limits (even 100K+ tokens can't hold everything), constantly changing data (retraining/reprompting impractical), and cost/latency (huge prompts are expensive and slow).
</details>

<details>
<summary>10. Give an example where semantic search beats keyword search.</summary>

User asks: "How do I get my money back?" Document title: "Refund Policy." Keyword search: no match (different words). Semantic search: match (same meaning).
</details>

<details>
<summary>11. In Salesforce Agentforce, what are three data sources an agent might retrieve from for RAG?</summary>

Knowledge articles, case history, CRM records, Data Cloud unified profiles, email/chat transcripts, internal docs (any three).
</details>

<details>
<summary>12. How does RAG reduce hallucination?</summary>

By grounding the LLM's answer in retrieved, real documents from enterprise systems — the model answers from actual context, not from guessing or general training data.
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ Embedding → vector of numbers representing semantic meaning; closer vectors = closer meaning
- ✅ Semantic search → matches by meaning, not just exact words; beats keywords for natural language
- ✅ Vector database → stores embeddings + fast similarity search (Pinecone, Weaviate, ChromaDB, Milvus, pgvector)
- ✅ RAG (Retrieval-Augmented Generation) → embed question → search vector DB → retrieve docs → prompt LLM with context → grounded answer
- ✅ Why RAG → LLMs need current, trusted data; context limits + changing data + hallucination risk → retrieve, don't guess
- ✅ Enterprise examples → Agentforce (Knowledge, Cases, CRM, Data Cloud), Copilot (Outlook, Teams, SharePoint), internal assistants (wiki, policy, docs)

---

# Next Topic

**Week 2 – Day 2: Chunking, Indexing & Retrieval**

- How to split large documents into chunks for embedding.
- Chunking strategies (fixed-size, semantic, recursive).
- Metadata filtering and hybrid search (vector + keyword).
- Retrieval quality — precision, recall, and why both matter.
- Indexing pipelines: ingest → chunk → embed → store.
