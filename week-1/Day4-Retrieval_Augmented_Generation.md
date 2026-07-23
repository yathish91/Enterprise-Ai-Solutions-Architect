# Week 1 - Day 4: Retrieval-Augmented Generation (RAG)

> **Learning Goal:** Understand what RAG is, why enterprises need it instead of fine-tuning, how the retrieve-then-generate flow works, and how this grounds AI agents in trusted company data.

> **How to use this guide:**
> 1. Read sections 1–6 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain what Retrieval-Augmented Generation (RAG) is and why it matters for enterprise AI.
- Describe the retrieve-then-generate flow: search first → answer second.
- Distinguish between RAG and fine-tuning, and when to use each.
- Explain how RAG reduces hallucination and keeps answers current.
- Describe what happens when retrieval fails and how to handle it.
- Sketch how RAG fits into an enterprise architecture (data sources, permissions, updates).
- Explain how Salesforce Agentforce uses RAG with Data Cloud and Knowledge.

---

# 1. What is RAG?

**Retrieval-Augmented Generation (RAG)** is a technique where an AI system first **retrieves relevant information from trusted sources** and then **gives that information to the LLM** so it can generate an accurate, grounded answer.

The simple way to remember it:

> 🧠 **Memory hook:** **Search first → Generate second**

An LLM by itself has only the knowledge it was trained on — general information from public sources, frozen at a point in time. It has **no access to your company's internal documents, policies, customer records, or product catalogs**. Without RAG, the LLM will either say "I don't know," give a generic answer, or hallucinate something confidently wrong.

RAG solves this by:
1. **Retrieving** the relevant company documents or data.
2. **Augmenting** the LLM's prompt with that retrieved context.
3. **Generating** an answer based on the retrieved information, not just the model's training.

**Key principle:** The LLM never searches enterprise systems by itself. The **AI Agent** (or application) performs the retrieval using tools, then passes the results to the LLM.

---

# 2. The RAG Flow

```text
         User Question
               │
               ▼
         AI Agent (orchestrator)
               │
               ▼
    Search Knowledge Base / Data
         (retrieval tool)
               │
               ▼
    Retrieve Relevant Documents
               │
               ▼
         LLM (receives retrieved context)
               │
               ▼
         Generated Answer
         (grounded in retrieved docs)
```

**Step-by-step:**

1. **User asks a question** — e.g., "What is the warranty for Product X?"
2. **Agent retrieves** — searches the company knowledge base or database for documents/records matching "Product X warranty."
3. **Agent filters and ranks** — returns the top relevant sections (not the entire database).
4. **Agent augments the prompt** — passes the question + retrieved context to the LLM: "Here is the warranty policy document [text]. Answer the user's question based only on this."
5. **LLM generates** — produces a natural-language answer grounded in the retrieved content.

The answer is:
- **Accurate** — based on real company data.
- **Current** — reflects the latest version of the document.
- **Grounded** — not a hallucination.

---

# 3. RAG vs. Fine-Tuning

This is a common interview question: **When should you use RAG vs. fine-tuning?**

| | **Fine-Tuning** | **RAG** |
|---|---|---|
| **What it is** | Retrain the model with new data | Retrieve data at query time and pass it to the unchanged model |
| **When to use** | Teach the model a new *style, format, or task* (e.g., medical note structure) | Give the model access to *current, private, or frequently updated knowledge* (e.g., company policies, product specs) |
| **Cost** | Expensive (training compute + data labeling) | Lower (vector search + storage) |
| **Speed** | Slow — retraining can take hours to days | Fast — update the document, retrieval uses it immediately |
| **Updating knowledge** | Must retrain every time content changes | Just update the document; no retraining |
| **Hallucination risk** | Still possible — model might blend training data incorrectly | Lower — LLM is instructed to answer only from retrieved context |
| **Use case example** | Train a model to write in a specific legal format | Answer questions about the company's refund policy |

> 🧠 **Memory hook:** Fine-tuning teaches the model **how to respond**. RAG teaches it **what to respond with** (by giving it the facts).

**Enterprise rule of thumb:** For company knowledge (policies, FAQs, product docs, customer records), **default to RAG**. Only fine-tune if you need to change the model's behavior (e.g., style, structured output format) and RAG + prompt engineering isn't enough.

**Example:** If your company updates the refund policy today, RAG can use the new policy in answers immediately. Fine-tuning would require collecting new training data, retraining the model, and redeploying — a process that could take weeks.

---

# 4. How RAG Reduces Hallucination

Hallucination = the LLM confidently makes up facts that aren't true.

RAG reduces this by:

1. **Grounding** — the LLM is given real documents/data to base its answer on, not just its training.
2. **Instruction to cite sources** — the prompt can say "Answer only from the retrieved context. If the context doesn't contain the answer, say you don't know."
3. **Retrieval as a filter** — if no relevant document is found, the system can refuse to answer instead of guessing.

**What happens if retrieval fails (no relevant documents found)?**

A well-designed enterprise RAG system should:
- Say it couldn't find the information ("I don't have a document on that topic").
- Ask a clarifying question ("Can you be more specific about which product?").
- Escalate to a human agent if the request is important.
- **Never make up an answer.**

This "fallback to safe" behavior is critical for trust in production systems.

---

# 5. Architect's View: RAG System Design Questions

As an Enterprise AI Solutions Architect, don't stop at "we need RAG." Ask:

- **Where does the source data live?** (Knowledge base, Data Cloud, Salesforce records, external CMS, files, databases?)
- **How often is it updated?** (Real-time sync, nightly batch, manual publishing?)
- **Who owns the documents?** (Product team, support, legal, marketing?)
- **Who can access them?** (Public, internal only, role-based access control?)
- **Should all users see the same documents?** (Do we need to filter by user permissions, region, or account?)
- **How do we enforce permissions?** (Metadata-based filtering, record-level security?)
- **What happens if nothing relevant is found?** (Fallback message, escalation, prompt the user to rephrase?)
- **How do we measure retrieval quality?** (Hit rate, precision, user feedback?)

These **design decisions** are as important as the AI model itself. A brilliant LLM with bad retrieval is useless.

---

# 6. RAG in Salesforce Agentforce

Agentforce uses RAG to ground agents in **trusted Salesforce data**:

```text
          User Question
               │
               ▼
       Agentforce Agent
               │
    ┌──────────┴──────────┐
    ▼                     ▼
Data Cloud            Knowledge
(unified profiles,    (articles, docs)
customer records)
    │                     │
    └──────────┬──────────┘
               ▼
         LLM (grounded)
               │
               ▼
         Answer / Action
```

- **Data Cloud** — unified customer profiles, consolidated from multiple sources (CRM, service, marketing, external). The agent retrieves from this unified view.
- **Knowledge** — company knowledge articles (FAQs, policies, product docs). Standard Salesforce Knowledge object.
- **Agent retrieval = tool calling** — the agent calls a search tool (e.g., `search_knowledge(query)` or `get_customer_profile(id)`) and receives the results as context.
- **Grounding** — the LLM is instructed to answer based on the retrieved data, not just general knowledge.
- **Permissions respected** — retrieval respects Salesforce security (record-level, field-level, sharing rules), so users only see data they're allowed to see.

**Why Data Cloud matters for RAG:** enterprise data is fragmented across systems (CRM, ERP, support, marketing, external). Data Cloud **unifies it** (ingestion + identity resolution + a unified data model). The agent retrieves from the unified profile instead of having to query ten different systems — simpler, faster, and a single source of truth.

**Two types of retrieval in Agentforce:**
1. **Retrieve business records** (customer accounts, cases, opportunities) — operational data, usually from Data Cloud or Salesforce objects.
2. **Retrieve knowledge documents** (policies, FAQs, product specs) — from Knowledge or external document stores.

Both are RAG; the data sources differ.

---

# Interview Questions & Answers

## Q1. What is Retrieval-Augmented Generation (RAG)?

RAG is a technique where an AI system first retrieves relevant information from trusted enterprise sources (documents, databases, knowledge bases), then augments the LLM's prompt with that retrieved content, so the LLM can generate an accurate answer grounded in real data. The key idea is: search first, then generate — the LLM never searches by itself; the agent or application does the retrieval.

## Q2. Why can't an LLM answer questions about a company's internal policies without RAG?

Because the LLM was trained on public data and has no access to private company documents. Internal policies, product specs, and customer records aren't in its training. Without RAG, the LLM would either say "I don't know," give a generic answer, or hallucinate incorrect information. With RAG, the agent retrieves the actual policy document and provides it as context, so the LLM can generate an accurate response based on that trusted source.

## Q3. When should you use RAG vs. fine-tuning?

Use RAG when you need to give the model access to current, private, or frequently updated knowledge (company policies, product catalogs, customer records). Use fine-tuning when you need to teach the model a new style, format, or task behavior (e.g., writing medical notes in a specific structure). RAG is faster, cheaper, and easier to keep up to date — just change the document, no retraining needed. Fine-tuning is for changing how the model responds, not what it knows.

## Q4. How does RAG reduce hallucination?

RAG grounds the LLM's answer in real retrieved documents instead of letting it rely solely on its training (which can be outdated or incomplete). The prompt instructs the LLM to "answer only from the retrieved context; if the context doesn't contain the answer, say you don't know." If retrieval finds no relevant documents, the system can refuse to answer instead of guessing. This grounding + instruction + fallback approach dramatically reduces confident-but-wrong answers.

## Q5. What should happen if the retrieval step finds no relevant documents?

A well-designed system should say it couldn't find the information, ask a clarifying question, or escalate to a human agent. It should never make up an answer. This "fail safe" behavior is critical for trust — better to admit "I don't know" than to hallucinate confidently on a customer issue.

## Q6. As an architect, what questions should you ask when designing a RAG system?

Where does the source data live? How often is it updated? Who owns it, who can access it, and how do we enforce permissions? Should all users see the same documents, or do we filter by role/region/account? What happens if retrieval finds nothing? How do we measure retrieval quality? These design decisions — data sources, update cadence, security, fallback behavior — are as important as the model.

## Q7. How does Salesforce Agentforce use RAG?

Agentforce agents retrieve from two main sources: **Data Cloud** (unified customer profiles, operational records) and **Knowledge** (company articles, policies, docs). The agent calls search tools (e.g., `search_knowledge(query)` or `get_profile(id)`), receives the results, and passes them to the LLM as grounding context. Retrieval respects Salesforce security (record-level, sharing rules), so users only see data they're authorized to access. This grounds the agent's answers in trusted Salesforce data.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats re-reading.

<details>
<summary>1. Complete the phrase: "RAG = ___ first → ___ second."</summary>

**Search** first → **Generate** second. Retrieve the relevant information, then let the LLM generate the answer from it.
</details>

<details>
<summary>2. Why can't an LLM answer questions about your company's internal policies without RAG?</summary>

The LLM was trained on public data and has no access to private company documents. Without RAG, it would either say "I don't know," give a generic answer, or hallucinate. RAG retrieves the actual document and provides it as context.
</details>

<details>
<summary>3. What are the five steps of the RAG flow?</summary>

1. User asks a question. 2. Agent retrieves relevant documents. 3. Agent filters/ranks results. 4. Agent augments the LLM prompt with retrieved context. 5. LLM generates an answer grounded in that context.
</details>

<details>
<summary>4. When should you use RAG vs. fine-tuning?</summary>

RAG: when you need current, private, or frequently updated knowledge (policies, specs, records). Fine-tuning: when you need to change the model's style, format, or task behavior. RAG is faster, cheaper, and easier to update.
</details>

<details>
<summary>5. How does RAG reduce hallucination?</summary>

By grounding the LLM in real retrieved documents and instructing it to answer only from that context. If retrieval finds nothing, the system refuses to answer instead of guessing.
</details>

<details>
<summary>6. What should happen if retrieval finds no relevant documents?</summary>

The system should say it couldn't find the information, ask a clarifying question, or escalate to a human. Never make up an answer.
</details>

<details>
<summary>7. Name three design questions an architect should ask when building a RAG system.</summary>

Where does the source data live? How often is it updated? Who can access it and how do we enforce permissions? (Also: what happens if retrieval fails? How do we measure quality?)
</details>

<details>
<summary>8. In Salesforce Agentforce, what are the two main sources agents retrieve from?</summary>

**Data Cloud** (unified customer profiles, operational records) and **Knowledge** (company articles, policies, docs).
</details>

<details>
<summary>9. What does "grounding" mean in the context of RAG?</summary>

Grounding means basing the LLM's answer on real retrieved data (documents, records) instead of just its training. It anchors the response in trusted enterprise sources.
</details>

<details>
<summary>10. Why is RAG preferred over fine-tuning for company knowledge?</summary>

Because RAG is easier to keep up to date. If a document changes, retrieval uses the new version immediately — no retraining needed. Fine-tuning requires retraining every time content changes, which is slow and expensive.
</details>

<details>
<summary>11. Who performs the retrieval in a RAG system — the LLM or the agent?</summary>

The **agent** (or application). The LLM has no ability to search external systems by itself. The agent calls a retrieval tool, gets the results, and passes them to the LLM.
</details>

<details>
<summary>12. Why does Data Cloud matter for RAG in Agentforce?</summary>

Enterprise data is fragmented across systems. Data Cloud unifies it (ingestion + identity resolution + unified data model). The agent retrieves from the unified profile — simpler, faster, and a single source of truth.
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ RAG = **Search first → Generate second** (retrieve relevant docs, then LLM answers from them)
- ✅ Why RAG? LLMs have no access to private, current company data — RAG provides it as context
- ✅ RAG vs. fine-tuning: RAG for **what to know** (current knowledge); fine-tuning for **how to respond** (style/format)
- ✅ RAG reduces hallucination by **grounding** the answer in retrieved documents and instructing "answer only from context"
- ✅ If retrieval fails → say "I don't know," ask for clarification, or escalate (never guess)
- ✅ Architect questions: data sources, update cadence, permissions, fallback, retrieval quality
- ✅ Agentforce RAG: agent retrieves from **Data Cloud** (profiles/records) + **Knowledge** (articles/docs), respects Salesforce security

---

# Next Topic

**Week 1 – Day 5: Prompt Engineering**

- What is a prompt and why does structure matter?
- Zero-shot, few-shot, and chain-of-thought prompting
- System vs. user messages
- How to write clear instructions, provide examples, and handle edge cases
- Prompt engineering patterns for enterprise AI (grounding, refusal, structured output)
- Leads into: building production-grade prompts for agents
