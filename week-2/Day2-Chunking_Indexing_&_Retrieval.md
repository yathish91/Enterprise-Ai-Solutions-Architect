# Week 2 - Day 2: Chunking, Indexing & Retrieval

> **Learning Goal:** Understand why RAG systems split documents into chunks, how indexing works, and how retrieval quality is improved through chunking strategies, overlap, metadata filtering, and hybrid search.

> **How to use this guide:**
> 1. Read sections 1–7 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain why documents must be chunked instead of sent whole to an LLM.
- Describe what a chunk is and how it preserves semantic meaning.
- Explain the trade-offs of chunk size (too large vs. too small).
- Explain why chunk overlap matters and how it preserves context.
- Describe the indexing process (document → chunks → embeddings → vector database).
- Explain similarity search and how it retrieves relevant chunks.
- Describe metadata filtering and its benefits for retrieval speed and accuracy.
- Compare similarity search vs. keyword search vs. hybrid search.

---

# 1. Why Can't We Store Entire Documents?

Imagine your company has a 150-page employee handbook.

An employee asks: "How many maternity leave days are available?"

If you send all 150 pages to the LLM:
- ❌ Too much context
- ❌ Expensive (more tokens = higher cost)
- ❌ Slow (longer processing time)
- ❌ Many irrelevant pages dilute the answer

Instead, **split the handbook into small pieces (chunks)**, find the relevant ones, and send **only those** to the LLM. This is the core idea behind chunking.

**Key line:** Chunking solves the problem of context overload by breaking documents into semantically meaningful units that can be retrieved selectively.

---

# 2. What is Chunking?

A **chunk** is a small section of a larger document. Chunking breaks a document into manageable pieces so each can be embedded and retrieved independently.

**Example document:**

```text
Employee Handbook
├─ Section 1: Company Vision
├─ Section 2: Working Hours
├─ Section 3: Leave Policy
├─ Section 4: Medical Insurance
└─ Section 5: Resignation Process
```

**After chunking:**

```text
Chunk 1: Company Vision
Chunk 2: Working Hours
Chunk 3: Leave Policy
Chunk 4: Medical Insurance
Chunk 5: Resignation Process
```

Each chunk gets its own **embedding** (a vector representation of its meaning) and is stored separately in the vector database.

---

# 3. Chunk Size Trade-offs

## Why Not Make Huge Chunks?

Suppose one chunk contains:

```text
Vacation Policy
Expense Reimbursement
Promotion Policy
Cyber Security
Parking Rules
```

A user asks: "What is the promotion cycle?"

The chunk contains the answer, but also **too much unrelated information**. Problems:
- The LLM may become **distracted** by irrelevant content.
- **Higher cost** (more tokens sent to the model).
- **Lower answer quality** (noise drowns out the signal).

## Why Not Tiny Chunks?

Example:

```text
Chunk 1: Annual
Chunk 2: Leave
Chunk 3: Policy
```

These chunks **lose context**. The phrase "Annual Leave Policy" becomes meaningless when split apart.

> 🧠 **Memory hook:** Goldilocks chunking — not too big (too much noise), not too small (loses context), just right (semantic unit).

**Typical chunk sizes:**
- **200–500 tokens** for dense technical content.
- **500–1,000 tokens** for narrative documents (articles, handbooks).
- Adjust based on document structure and retrieval quality testing.

---

# 4. Chunk Overlap

Suppose the document is:

```text
Salesforce provides Agentforce.
Agentforce uses Retrieval Augmented Generation.
It can access Data Cloud.
It can summarize customer history.
```

**Without overlap:**

```text
Chunk 1:
Salesforce provides Agentforce.
Agentforce uses Retrieval...

Chunk 2:
Generation.
It can access Data Cloud...
```

The phrase **"Retrieval Augmented Generation" gets split**. The meaning is lost across the boundary.

**With overlap:**

```text
Chunk 1:
Salesforce provides Agentforce.
Agentforce uses Retrieval Augmented Generation.

Chunk 2:
Agentforce uses Retrieval Augmented Generation.
It can access Data Cloud.
```

The overlap **preserves meaning** across chunk boundaries.

**Why it matters:** without overlap, key phrases or concepts that span the boundary between two chunks can be lost, leading to retrieval failures or incomplete answers.

**Typical overlap:** 10–20% of the chunk size (e.g., 50–100 tokens for a 500-token chunk).

---

# 5. What is Indexing?

**Indexing** is the process of storing chunks in a searchable form. The pipeline:

```text
Document
   ↓
Chunks (splitting)
   ↓
Embeddings (embedding model converts text → vector)
   ↓
Vector Database (stores chunk text + embedding + metadata)
```

Think of it like **creating a searchable catalog** for your AI. Once indexed, the system can perform fast similarity searches to find relevant chunks for a query.

**Example:** a 100-page technical manual becomes 500 indexed chunks, each with its own embedding, stored in a vector database like Pinecone, Weaviate, or Salesforce Data Cloud.

---

# 6. Similarity Search

**Similarity search** retrieves chunks whose embeddings are closest (in vector space) to the query's embedding.

**Flow:**

```text
User query: "How can I recover my account?"
   ↓
Convert query to embedding
   ↓
Search vector database for nearest chunk embeddings
   ↓
Return top-k chunks (e.g., top 3)
```

**Example stored chunks:**
- Reset Password
- Refund Policy
- Holiday Calendar

The embedding model identifies **"Reset Password"** as the closest semantic match, even though the wording differs from "recover my account."

**Why it works:** embeddings capture **semantic meaning**, not just keywords. "Recover account" and "reset password" are different words but similar concepts.

---

# 7. Metadata Filtering

Each chunk can include **metadata** — structured fields that describe the chunk.

| Chunk               | Metadata                      |
|---------------------|-------------------------------|
| Leave Policy        | Department: HR, Type: Policy  |
| Sales Guide         | Department: Sales, Type: Guide|
| Security Manual     | Department: IT, Type: Manual  |

**User asks:** "How do I apply for leave?"

Instead of searching **every** document, the system can:
1. **Filter** by `Department = HR`.
2. Then perform **similarity search** only within HR documents.

**Benefits:**
- **Faster retrieval** (smaller search space).
- **Lower cost** (fewer chunks to process).
- **Higher accuracy** (less noise from irrelevant departments).

**Example metadata fields:**
- Department, document type, author, date, region, product, access level.

> 🧠 **Memory hook:** Metadata filtering = pre-filter before search. Narrow the haystack before finding the needle.

---

# 8. Similarity Search vs. Keyword Search vs. Hybrid Search

## Similarity Search (Vector Search)

Uses **embeddings** to find semantically similar content.

**Example:**
- **Query:** "Money back"
- **Returns:** "Refund Policy"

Why? Because the **meanings** are similar, even though the words differ.

## Keyword Search

Looks for **exact words** (full-text search, BM25).

**Example:**
- **Query:** "Refund"
- **Returns:** documents containing the exact word "Refund".

## Hybrid Search

**Combines both approaches:** embedding search + keyword search.

```text
Semantic understanding (from embeddings)
  +
Exact matching (from keywords)
  =
Hybrid Search
```

**Why enterprises use it:**
- **Semantic understanding** for natural questions ("How do I get my money back?").
- **Exact matching** for IDs, product codes, error codes, legal terms ("INC-23451", "Regulation 508(c)").

**Example:**

User asks: "INC-23451"

A **keyword search** is better than embeddings because ticket IDs require exact matches. The embedding of "INC-23451" may be close to other ticket IDs, but the user wants **this specific ticket**.

**When to use:**
- **Similarity-only:** natural language questions, exploratory search.
- **Keyword-only:** exact IDs, codes, or terms (rare in isolation).
- **Hybrid (recommended for enterprise):** best of both — semantic + exact.

---

# Enterprise Example: Salesforce Knowledge RAG

Imagine Salesforce Knowledge contains:
- Password Reset
- MFA Setup
- Data Cloud
- Opportunity Management
- Agentforce

**User asks:** "I forgot my password."

**Flow:**

```text
1. Convert the query to an embedding
       ↓
2. Filter metadata (e.g., IT Knowledge Base only)
       ↓
3. Retrieve the top matching chunks (similarity search)
       ↓
4. Send those chunks to the LLM as context
       ↓
5. LLM generates an accurate response grounded in the retrieved content
```

This is how many enterprise AI assistants operate: **retrieve first, then generate**.

---

# Interview Questions & Answers

## Q1. Why do we chunk documents instead of storing whole PDFs?

Large documents exceed an LLM's context window, increase cost, and often include irrelevant information. Chunking breaks documents into meaningful sections, allowing the retrieval system to fetch only the most relevant content. This improves retrieval accuracy, reduces latency and token usage, and provides better context for the LLM to generate precise answers.

## Q2. Why don't we simply send the entire PDF to the LLM instead of chunking it?

We don't send the entire PDF because it is inefficient and often exceeds the model's context window. Even if the document fits, most of it is usually irrelevant to the user's question, which increases token usage, latency, and cost. Instead, we split the document into meaningful chunks and create embeddings for each chunk. When a user asks a question, we convert the query into an embedding and perform a similarity search in the vector database to retrieve only the most relevant chunks. Those retrieved chunks are then provided to the LLM as context, enabling it to generate a more accurate, grounded, and cost-effective response. Chunk overlap further improves retrieval quality by preserving context across chunk boundaries.

## Q3. What are the trade-offs of chunk size? What happens if chunks are too large or too small?

**Too large:** chunks contain too much irrelevant information (noise), which distracts the LLM, increases cost (more tokens), and reduces answer quality. **Too small:** chunks lose semantic context — for example, splitting "Annual Leave Policy" into separate words makes them meaningless. The goal is to chunk at a **semantic unit** — large enough to preserve meaning, small enough to avoid noise. Typical sizes are 200–1,000 tokens depending on document type.

## Q4. Why is chunk overlap important? What problem does it solve?

Chunk overlap preserves **context and meaning across chunk boundaries**. Without overlap, a key phrase or concept that spans two chunks can be split, causing the retrieval system to miss it. For example, "Retrieval Augmented Generation" split across two chunks becomes "Retrieval..." and "Generation...", losing the full concept. With overlap (typically 10–20% of chunk size), the phrase appears complete in at least one chunk, improving retrieval accuracy.

## Q5. What is the indexing process, from raw document to searchable chunks?

The indexing pipeline is: **Document → Chunks (splitting) → Embeddings (embedding model converts text to vectors) → Vector Database (stores chunk text + embedding + metadata)**. Once indexed, the system can perform fast similarity searches to find relevant chunks for a query. For example, a 100-page manual becomes 500 indexed chunks, each with its own embedding, stored in a vector database.

## Q6. How does metadata filtering improve retrieval?

Metadata filtering **pre-filters** the search space before running similarity search. Instead of searching all chunks, the system filters by structured fields (e.g., Department = HR, Document Type = Policy, Region = US), then performs similarity search only within that subset. This makes retrieval **faster** (smaller search space), **cheaper** (fewer chunks to process), and **more accurate** (less noise from irrelevant documents).

## Q7. What is hybrid search, and why do enterprises use it?

Hybrid search **combines similarity search (embeddings) with keyword search (exact matching)**. Similarity search handles natural questions ("How do I get my money back?"), while keyword search handles exact IDs, codes, or legal terms ("INC-23451", "Regulation 508(c)"). Enterprises use hybrid because it provides the best of both: semantic understanding plus exact retrieval. Similarity-only can miss exact terms; keyword-only can miss semantic matches.

## Q8. A customer says: "I asked the AI assistant, 'How do I reset my password?' but it returned a leave policy instead." As an Enterprise AI Solutions Architect, how would you troubleshoot this?

I would troubleshoot the RAG pipeline step by step:
1. **Indexing:** Verify that the Password Reset document was indexed correctly.
2. **Chunking:** Review the chunking strategy — are chunks meaningful? Is there appropriate overlap?
3. **Embeddings & Retrieval:** Validate that the embedding model and vector search are retrieving the correct chunks for the query. Test the query embedding against stored chunk embeddings.
4. **Metadata Filters:** Check if metadata filters are incorrectly restricting the search (e.g., searching only HR docs when IT docs contain the answer).
5. **LLM Prompt:** Inspect the prompt to confirm the LLM is instructed to answer **only from the retrieved context**. If the wrong chunk is retrieved, the LLM can't fix it.

By isolating each stage — indexing, chunking, retrieval, and generation — I can identify where the failure occurs. Most retrieval failures happen at the chunking or retrieval stage, not the LLM generation stage.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats re-reading.

<details>
<summary>1. Why can't we send a 150-page document to the LLM in one shot?</summary>

It exceeds the context window, is expensive (high token cost), slow, and contains mostly irrelevant content. Chunking retrieves only the relevant sections.
</details>

<details>
<summary>2. What is a chunk?</summary>

A small, semantically meaningful section of a larger document, each with its own embedding, stored separately in a vector database.
</details>

<details>
<summary>3. What happens if chunks are too large?</summary>

Too much irrelevant information (noise), which distracts the LLM, increases cost, and reduces answer quality.
</details>

<details>
<summary>4. What happens if chunks are too small?</summary>

They lose semantic context — for example, "Annual Leave Policy" split into separate words becomes meaningless.
</details>

<details>
<summary>5. What is chunk overlap and why does it matter?</summary>

Chunk overlap means repeating some content between adjacent chunks (typically 10–20% of chunk size). It preserves meaning across chunk boundaries — without it, key phrases spanning two chunks get split and lost.
</details>

<details>
<summary>6. What are the steps of the indexing pipeline?</summary>

Document → Chunks (splitting) → Embeddings (text → vector) → Vector Database (store chunk + embedding + metadata).
</details>

<details>
<summary>7. How does similarity search work?</summary>

Convert the user query to an embedding, search the vector database for the nearest chunk embeddings, return the top-k chunks. Embeddings capture semantic meaning, not just keywords.
</details>

<details>
<summary>8. Give an example of similarity search matching different words with similar meaning.</summary>

Query: "How can I recover my account?" → Returns: "Reset Password" (different words, same concept).
</details>

<details>
<summary>9. What is metadata filtering and what are its benefits?</summary>

Pre-filter chunks by structured fields (e.g., Department, Document Type) before similarity search. Benefits: faster (smaller search space), cheaper (fewer chunks), more accurate (less noise).
</details>

<details>
<summary>10. What is hybrid search?</summary>

Combination of similarity search (embeddings for semantic understanding) + keyword search (exact matching for IDs, codes, legal terms). Best of both for enterprise.
</details>

<details>
<summary>11. When is keyword search better than similarity search?</summary>

For exact IDs, product codes, error codes, or legal citations (e.g., "INC-23451", "Regulation 508(c)") where exact matching is required, not semantic similarity.
</details>

<details>
<summary>12. In a RAG system, list the flow from user query to final answer.</summary>

Query → embedding → metadata filter (optional) → similarity search → retrieve top-k chunks → send chunks to LLM as context → LLM generates grounded answer.
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ Chunking → split documents into small, meaningful sections (not too big = noise, not too small = loses context)
- ✅ Chunk overlap → preserves meaning across boundaries (10–20% overlap prevents split phrases)
- ✅ Indexing pipeline → Document → Chunks → Embeddings → Vector Database
- ✅ Similarity search → query embedding finds nearest chunk embeddings (semantic, not keyword)
- ✅ Metadata filtering → pre-filter by structured fields before search (faster, cheaper, more accurate)
- ✅ Hybrid search → similarity + keyword = semantic understanding + exact matching (best for enterprise)
- ✅ Typical chunk sizes: 200–500 tokens (technical), 500–1,000 tokens (narrative)
- ✅ RAG flow: query → embed → filter → retrieve → send to LLM → grounded answer

---

# Next Topic

**Week 2 – Day 3: Prompt Engineering for Enterprise AI**

- Prompt structure and clarity
- System vs. user prompts
- Few-shot prompting and in-context learning
- Chain-of-thought reasoning
- Grounding and retrieval-aware prompts
- Guardrails and safety in prompts
- Prompt templates and variables for enterprise scale
