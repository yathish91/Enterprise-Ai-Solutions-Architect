# Topic: Embeddings & Vector Databases (The Foundation of Enterprise AI Search)

### Goal: Understand why companies use vector databases instead of keyword search and how this powers enterprise AI assistants.

## Part 1  – Why LLMs Need More Than Prompting

Imagine a customer asks:

    "Show me the support case where the customer complained about duplicate invoices."

A normal database searches exact words.

If the document instead says:

    "Customer received multiple invoices for the same order."

Keyword search may fail.

An LLM also cannot remember every enterprise document because:

    Context windows are limited.
    Company data changes constantly.
    Retraining the model for every document isn't practical.

Instead, we:

    Convert documents into vectors (embeddings).
    Store those vectors.
    Search for similar meanings.
    Send the retrieved content to the LLM.

### This pattern is called Retrieval-Augmented Generation (RAG).

## Part 2: What is an Embedding?

An embedding is simply a list of numbers representing the meaning of text.

Example:
    "I love Salesforce"

             ↓

    [0.23, -0.71, 1.02, 0.44, ...]

Another sentence:

    "I enjoy Salesforce"

            ↓

    [0.20, -0.68, 1.01, 0.41]

These vectors are close together because the meanings are similar.

A different sentence:

    "I need pizza"

    ↓

    [-0.82, 2.01, -0.43]

It is much farther away.
The closer two vectors are, the more semantically similar they are.

## Part 3 : Why Keywords Fail

Suppose your support articles contain:

Refund Policy

The customer asks: How do I get my money back?

    Keyword search: ❌ No "refund" word.
    Embedding search:✅ Understands that "money back" and "refund" are closely related.

That's why enterprise AI uses semantic search.

## Part 4 : Vector Databases

A vector database stores embeddings and allows fast similarity search.

Popular options include:

    Pinecone
    Weaviate
    ChromaDB
    Milvus
    pgvector (PostgreSQL extension)

They answer questions like:
    Find the 5 most similar documents.
    instead of: SELECT * FROM table WHERE title LIKE '%refund%'

## Part 5  – How RAG Works

    Customer Question
        │
        ▼
    Embedding Model
        │
        ▼
    Vector Database
        │
    Top Matching Documents
        │
        ▼
       LLM
        │
        ▼
    Final Answer

Example:

User: Why did Opportunity ABC fail?

The system:

    Converts the question into an embedding.
    Searches vectors.
    Retrieves emails, notes, and cases.
    Sends only the relevant information to the LLM.
    The LLM produces an answer based on those documents.

## Part 6 – Enterprise Examples

Salesforce Agentforce
Knowledge articles
Case history
CRM records
Data Cloud
Emails

An agent retrieves relevant information before answering.

    Microsoft Copilot
    Outlook emails
    Teams chats
    SharePoint documents
    OneDrive files

It searches your organization's data rather than relying only on the model's training.

Internal Company Assistant

An employee asks:

    "How do I request production access?"

The assistant retrieves:

    Internal wiki
    HR policy
    Security documentation

Then the LLM generates the response.

Architecture You'll See in Interviews

            Application
                │
            User Question
                │
            Embedding Model
                │
            Vector DB
                │
            Relevant Documents
                │
               LLM
                │
            Response
