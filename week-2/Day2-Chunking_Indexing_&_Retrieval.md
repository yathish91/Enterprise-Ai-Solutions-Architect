# Topic :  Chunking, Indexing & Retrieval

### Goal: Understand why RAG systems split documents into chunks, how indexing works, and how retrieval quality is improved.

## Part 1 – Why Can't We Store Entire Documents?

Imagine your company has a 150-page employee handbook.

An employee asks: "How many maternity leave days are available?"

    If you send all 150 pages to the LLM:
        ❌ Too much context
        ❌ Expensive
        ❌ Slow
        ❌ Many irrelevant pages

Instead, split the handbook into small pieces (chunks), find the relevant ones, and send only those to the LLM.
This is the core idea behind chunking.

## Part 2 – What is Chunking?

A chunk is a small section of a larger document.

Example document:

Employee Handbook

    Section 1
    Company Vision

    Section 2
    Working Hours

    Section 3
    Leave Policy

    Section 4
    Medical Insurance

    Section 5
    Resignation Process

After chunking:

    Chunk 1
    Company Vision

    Chunk 2
    Working Hours

    Chunk 3
    Leave Policy

    Chunk 4
    Medical Insurance

    Chunk 5
    Resignation Process

Each chunk gets its own embedding.

## Part 3 – Why Not Make Huge Chunks?

Suppose one chunk contains:

    Vacation Policy
    Expense Reimbursement
    Promotion Policy
    Cyber Security
    Parking Rules

A user asks: "What is the promotion cycle?"
    The chunk contains too much unrelated information.The LLM may become distracted by irrelevant content, increasing cost and reducing answer quality.

## Part 4 – Why Not Tiny Chunks?

Example:

    Annual
    Leave
    Policy

These chunks lose context.

The phrase Annual Leave Policy becomes meaningless when split apart.

This is why chunk size matters.

## Part 5 – Chunk Overlap

Suppose the document is:

    Salesforce provides Agentforce.
    Agentforce uses Retrieval Augmented Generation.
    It can access Data Cloud.
    It can summarize customer history.

Without overlap:

Chunk 1
    Salesforce provides Agentforce.
    Agentforce uses Retrieval...

Chunk 2
    Generation.
    It can access Data Cloud...

The phrase "Retrieval Augmented Generation" gets split.

With overlap:

Chunk 1
    Salesforce provides Agentforce.
    Agentforce uses Retrieval Augmented Generation.

Chunk 2

    Agentforce uses Retrieval Augmented Generation.
    It can access Data Cloud.

The overlap preserves meaning.

## Part 6 – What is Indexing?

After chunking:

    Document
        ↓
    Chunks
        ↓
    Embeddings
        ↓
Stored in Vector Database

This storage process is called indexing.

Think of it like creating a searchable catalog for your AI.

## Part 7 – Similarity Search

User asks: "How can I recover my account?"

Stored chunks: 
    Reset Password
    Refund Policy
    Holiday Calendar

The embedding model identifies "Reset Password" as the closest semantic match, even if the wording differs.

## Part 8 – Metadata Filtering

Each chunk can include metadata:

Chunk	               Metadata
Leave Policy	         HR
Sales Guide	            Sales
Security Manual	        IT

User asks:"How do I apply for leave?"

Instead of searching every document, the system can first filter by: Department = HR ,Then perform semantic search only within HR documents.

Benefits:
    Faster retrieval
    Lower cost
    Higher accuracy

##  Part 9 – Similarity Search vs Hybrid Search

####Similarity Search , Uses embeddings.

Question: "Money back"
    Returns: Refund Policy

because the meanings are similar.

#### Keyword Search

    Looks for exact words.

Search: Refund
    Returns documents containing the exact word.

#### Hybrid Search

Combines both approaches.

Embedding Search + Keyword Search = Hybrid Search

Why enterprises use it:

    Semantic understanding
    Exact matching for IDs, product names, error codes, and legal terms

Example:

User asks: "INC-23451"
    A keyword search is better than embeddings because ticket IDs require exact matches.

## Part 10 – Enterprise Example

Imagine Salesforce Knowledge contains:

    Password Reset
    MFA Setup
    Data Cloud
    Opportunity Management
    Agentforce

User asks: "I forgot my password."
Flow:
    Convert the query to an embedding.
    Filter metadata (e.g., IT Knowledge Base).
    Retrieve the top matching chunks.
    Send those chunks to the LLM.
    Generate an accurate response.

This is how many enterprise AI assistants operate.

## Part 11 – Common Interview Question

### Why do we chunk documents instead of storing whole PDFs?
    Large documents exceed an LLM's context window, increase cost, and often include irrelevant information. Chunking breaks documents into meaningful sections, allowing the retrieval system to fetch only the most relevant content. This improves retrieval accuracy, reduces latency and token usage, and provides better context for the LLM to generate precise answers.

### Why don't we simply send the entire PDF to the LLM instead of chunking it?
    We don't send the entire PDF to the LLM because it is inefficient and often exceeds the model's context window. Even if the document fits, most of it is usually irrelevant to the user's question, which increases token usage, latency, and cost.

    Instead, we split the document into meaningful chunks and create embeddings for each chunk. When a user asks a question, we convert the query into an embedding and perform a similarity search in the vector database to retrieve only the most relevant chunks. Those retrieved chunks are then provided to the LLM as context, enabling it to generate a more accurate, grounded, and cost-effective response. Chunk overlap further improves retrieval quality by preserving context across chunk boundaries.

### A customer says:"I asked the AI assistant, 'How do I reset my password?' but it returned a leave policy instead."As an Enterprise AI Solutions Architect, how would you troubleshoot this?
Think through the system end to end. Consider questions like:
Could the embeddings be poor?
Is the chunk size too large or too small?
Is the wrong metadata being searched?
Is the vector database returning the wrong chunks?
Is the prompt failing to use the retrieved context? 

    I would troubleshoot the RAG pipeline step by step. First, I'd verify that the Password Reset document was indexed correctly. Next, I'd review the chunking strategy, ensuring chunks are meaningful and include appropriate overlap. Then I'd validate that the embedding model and vector search are retrieving the correct chunks for the query. I'd also check metadata filters to make sure the search isn't restricted to the wrong document set. Finally, I'd inspect the prompt to confirm the LLM is instructed to answer only from the retrieved context. By isolating each stage—indexing, chunking, retrieval, and generation—I can identify where the failure occurs.
