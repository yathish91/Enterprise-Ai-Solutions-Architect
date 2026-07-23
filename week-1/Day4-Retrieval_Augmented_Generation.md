# Dayy 4 Retrieval-Augmented Generation (RAG):

#### Learning Goal

By the end of today, you should be able to answer:

What is RAG?
Why do enterprises need RAG?
How does RAG work?
How is RAG different from fine-tuning?
How does Salesforce Agentforce use RAG?


## What is RAG?

Retrieval-Augmented Generation is a technique where an AI system first retrieves relevant information from trusted enterprise sources and then gives that information to the LLM so it can generate an accurate answer.

Think of it as:

Search first → Answer second

Enterprise Flow

User
   │
   ▼
AI Agent
   │
   ▼
Search Knowledge Base
   │
   ▼
Retrieve Relevant Documents
   │
   ▼
LLM
   │
   ▼
Answer

**The LLM never searches by itself.The agent performs the retrieval.

### Example

Customer asks:

"How long is the warranty for Product X?"

The AI Agent:

Searches the company's knowledge base.
Finds the warranty document.
Retrieves the relevant section.
Sends only the relevant content to the LLM.
The LLM generates a clear, natural-language answer.

This makes the answer:

Accurate
Current
Grounded in company data

###  Why Not Fine-Tune the Model?

    This is a common interview question.

    Fine-tuning

    You retrain the model with new data.

    Problems:

    Expensive
    Slow
    Requires retraining when documents change
    Difficult to keep current

    RAG

    You leave the model unchanged.

    Instead:

    Update the documents.
    The retrieval system fetches the latest information.
    The LLM answers using the retrieved content.

    If a company updates its refund policy today, RAG can use the new policy immediately without retraining the model.

## What Happens If Retrieval Fails?

Suppose no relevant document is found.

A well-designed enterprise AI system should:

Say it couldn't find the information.
Ask a clarifying question.
Escalate to a human if needed.
Avoid making up an answer.

Reducing hallucinations is one of the biggest benefits of RAG.

## Architect's Perspective

As an Enterprise AI Solutions Architect, don't stop at "We need RAG."

Ask questions like:

Where does the source data live?
How often is it updated?
Who owns the documents?
Who can access them?
Should all users see the same documents?
How do we enforce permissions?
What happens if nothing relevant is found?

These design decisions are as important as the AI itself.

## Day 4 Key Takeaways

RAG = Retrieve first, generate second.
The LLM does not search enterprise systems by itself.
The AI Agent retrieves information using tools.
RAG provides current, trusted, enterprise-specific information.
RAG is generally preferred over fine-tuning for company knowledge because it's easier to keep up to date.

## Exercise


Why can't an LLM answer questions about my company's internal policies without RAG?
Your understanding (correct)

You said that RAG fetches company-specific data and gives it to the LLM, otherwise the LLM only has general knowledge.

That is exactly the core idea.

Interview-ready answer

    An LLM cannot answer questions about a company's internal policies because those documents are private and are not part of the model's training data. Without RAG, the LLM would either say it doesn't know, provide a generic answer, or hallucinate incorrect information.

    With RAG, the AI Agent retrieves the relevant policy document from trusted enterprise sources and provides it to the LLM as context. The LLM then generates an accurate response based on that retrieved information rather than relying only on its pre-trained knowledge.

Excellent understanding here.






