# Week 1 - Day 2: Understanding LLMs & Enterprise AI Architecture

> **Learning Goal:** Understand that LLMs are reasoning engines, not databases, and learn how enterprise AI systems orchestrate trusted data sources through AI agents while keeping humans in control of critical decisions.

> **How to use this guide:**
> 1. Read sections 1–6 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain what an LLM is and what it is NOT (it's not a database).
- Describe the difference between data storage systems and reasoning engines.
- Explain the role of an AI Agent as an orchestrator in enterprise architecture.
- Describe how enterprise systems (Salesforce, Data Cloud, Knowledge Bases) serve as sources of truth.
- Explain the division of responsibilities: data storage vs. agent orchestration vs. LLM reasoning.
- Describe Human-in-the-Loop (HITL) and identify where it should be applied.
- Sketch a typical enterprise AI architecture flow.

---

# 1. What an LLM Is (and Is NOT)

> 🧠 **Memory hook:** "An LLM is not a database. It is a reasoning and language generation engine."

Many people think ChatGPT stores company data and simply retrieves it. **This is wrong.**

**What stores data:**
- **CRM systems (Salesforce)** store business data — Accounts, Opportunities, Cases, Contacts.
- **Databases** store structured information (records, tables, relationships).
- **Knowledge Bases** store documents, articles, policies, and procedures.

**What an LLM does:**
- Understands context (natural language comprehension).
- Reasons over information presented to it.
- Generates natural language responses.
- Does NOT store enterprise data inside the model.

The LLM works on whatever information is **retrieved and fed to it at runtime**. It has no access to your company's live systems unless explicitly connected through an agent architecture.

**Key line:** *An LLM reasons over data. It does not replace where data lives.*

---

# 2. Enterprise AI Architecture: The Real Flow

Instead of thinking:

```text
User → ChatGPT → Answer
```

Think:

```text
            User
             │
             ▼
          AI Agent
             │
    ┌────────┼────────┐
    │        │        │
    ▼        ▼        ▼
Salesforce  Data   Knowledge
  (CRM)    Cloud     Base
    │        │        │
    └────────┼────────┘
             │
             ▼
           LLM
             │
             ▼
    Natural-language
        response
```

- The **AI Agent** decides which systems to query and what actions to take.
- **Salesforce, Data Cloud, Knowledge Bases, ERPs, APIs** provide the trusted data.
- The **LLM** receives that retrieved context, reasons over it, and generates a response.

**Key idea:** AI Agents orchestrate tasks using enterprise systems; LLMs generate and reason over the final response. The agent is the **orchestrator**; the LLM is the **brain**.

---

# 3. Division of Responsibilities

Understanding **who does what** is critical for enterprise AI architecture.

| System | Responsibility |
|--------|---------------|
| **Salesforce (CRM)** | Source of truth: stores Accounts, Opportunities, Cases, Contacts, business records |
| **Data Cloud** | Unified data from multiple sources; identity resolution; grounding layer |
| **Knowledge Base** | Stores articles, policies, procedures, FAQs |
| **AI Agent** | Orchestrates: calls APIs, retrieves records, searches knowledge, executes workflows, decides which tool to use, coordinates multiple systems |
| **LLM** | Reasons: summarizes, drafts emails, explains information, reasons over retrieved context, conducts natural language conversations |

> 🧠 **Memory hook — three layers:**
> 1. **Data layer** (Salesforce, Data Cloud, Knowledge) = source of truth
> 2. **Agent layer** = orchestrator (decides what to retrieve/act on)
> 3. **LLM layer** = reasoning engine (generates response from retrieved data)

**Why this division matters:** mixing these responsibilities leads to bad architecture — for example, trying to store live business data in the LLM (impossible and insecure), or asking the LLM to decide which API to call without an agent orchestration layer (unreliable).

---

# 4. Real Enterprise Example

A customer asks:

**"What's happening with my support case?"**

The AI flow is:

```text
    Customer request
          │
          ▼
       Agent
          │
          ├─► Salesforce Case API
          │        │
          │        └─► retrieve case details
          │
          ├─► Knowledge Base
          │        │
          │        └─► search related articles
          │
          └─► Feed results to LLM
               │
               ▼
          LLM reasons over
        retrieved context
               │
               ▼
     Natural-language response
```

**Key point:** The LLM does NOT know the case status on its own — it only works with the information **retrieved by the agent**. The agent pulls live data from Salesforce; the LLM summarizes and explains it in natural language.

---

# 5. Human-in-the-Loop (HITL)

**Human-in-the-Loop** means a human must review or approve before the AI takes certain actions. This is one of the most important enterprise concepts.

**Where HITL is essential:**
- Sending customer emails (reputation risk, compliance).
- Closing support cases (customer satisfaction, premature closure).
- Approving refunds (financial impact).
- Legal responses (liability, regulatory).
- Financial approvals (fraud, authorization).
- Executive reports (accuracy, accountability).

**Example discussion:** An AI agent should NOT automatically close customer cases. Even if internal checks suggest the issue is resolved, it's better to present the information to a human for review or confirmation before closing. This helps maintain customer trust and prevents premature case closure.

**Why HITL matters in enterprise:** LLMs can be confidently wrong. A human approval step before executing irreversible or high-impact actions prevents costly mistakes on real customer data and preserves trust.

> 🧠 **Memory hook:** If it's irreversible, sensitive, or customer-facing, **add HITL**.

---

# 6. Enterprise AI Architecture Mindset

As an Enterprise AI Solutions Architect, always ask:

| Question | Why it matters |
|----------|----------------|
| **Where does the data live?** | Identifies the source of truth — Salesforce, Data Cloud, external systems |
| **Who owns the data?** | Determines access, permissions, security, compliance |
| **Which system performs the action?** | Clarifies where writes happen (Salesforce updates vs. external API calls) |
| **When should AI be involved?** | Not everything needs AI — fixed logic is often better/cheaper/faster |
| **When should a human approve the outcome?** | High-risk, irreversible, or sensitive actions require HITL |

**This is the architectural thinking companies expect.** You are not building a chatbot; you are designing a **multi-system integration with AI-powered reasoning** while maintaining governance and trust.

---

# Key Concepts Summary

- ✅ **LLM ≠ Database** — LLMs reason and generate language; they do not store enterprise data.
- ✅ **AI Agent = Orchestrator** — decides which systems to query and what actions to take.
- ✅ **Salesforce = Source of Truth** — live business data (Accounts, Cases, Opportunities, Contacts).
- ✅ **APIs connect enterprise systems** — agent calls APIs to retrieve data and execute actions.
- ✅ **AI uses retrieved data to generate responses** — the LLM only sees what the agent gives it.
- ✅ **Human-in-the-Loop is critical** — for trust, governance, and preventing irreversible mistakes.

---

# Interview Questions & Answers

## Q1. What is the difference between an LLM and a database?

A database stores structured records and retrieves them via queries — it is a **source of truth** for live data. An LLM is a **reasoning and language generation engine** that understands natural language, reasons over information, and generates text responses. It does NOT store enterprise data; it only processes whatever context is fed to it at runtime. Salesforce stores your Cases; the LLM explains them.

## Q2. Why shouldn't an LLM directly access enterprise data?

Because LLMs do not store data — they process text inputs and generate text outputs. Enterprise data lives in systems like Salesforce, Data Cloud, and databases. Direct access is also a security and governance risk; instead, an **AI Agent** mediates: it retrieves data from trusted systems via secure APIs, passes the relevant context to the LLM, and the LLM reasons over it. This separation preserves access control, audit trails, and the source of truth.

## Q3. What is an AI Agent in enterprise architecture?

An AI Agent is the **orchestrator** that sits between the user and enterprise systems. It decides which tools to call (Salesforce APIs, Knowledge Base searches, workflows), retrieves data, feeds it to the LLM for reasoning, and executes actions. It coordinates multiple systems and handles the **sequence of steps** needed to complete a task. The agent uses the LLM as its reasoning brain, but the agent is responsible for orchestration and acting on systems.

## Q4. Explain Human-in-the-Loop with a real-world example.

Human-in-the-Loop (HITL) means a human must review or approve before the AI takes certain actions. Example: a customer support agent (AI) analyzes a case and determines it's resolved, drafting a closure note and proposing to close the case. Instead of closing it automatically, the system presents the recommendation to a **human agent** who reviews and confirms. This prevents premature closure, maintains customer trust, and ensures accountability for business-critical decisions.

## Q5. Why is Salesforce considered the source of truth in an enterprise architecture?

Because Salesforce (or the CRM/ERP) stores the **live, authoritative business records** — Accounts, Opportunities, Cases, Contacts, custom objects. Any write (create, update, delete) must go to that system to be official. The AI Agent retrieves from Salesforce and writes back to it; the LLM only reasons over what is retrieved. If you need to know a customer's open Cases, you query Salesforce. If you need to update an Opportunity, you write to Salesforce. It is the **system of record**.

## Q6. Describe the three-layer enterprise AI architecture.

1. **Data layer** — Salesforce, Data Cloud, Knowledge Bases, external systems. Stores the source of truth.
2. **Agent layer** — orchestrates: retrieves data, calls APIs, decides which tool to use, coordinates steps.
3. **LLM layer** — reasons over retrieved context, generates natural language, summarizes, explains.

The agent pulls data from layer 1, feeds it to layer 3, and the LLM produces a grounded response. Each layer has a clear responsibility.

## Q7. When should AI NOT be involved in a task?

When the task is **deterministic, rule-based, or low-complexity** and can be handled by traditional automation (workflow rules, validation rules, fixed logic). AI adds cost and unpredictability; if a simple "if X then Y" covers it, use that. Also avoid AI for tasks requiring guaranteed correctness (e.g., regulatory calculations) unless validated and HITL-approved. Use AI where **reasoning, language understanding, or context synthesis** adds value.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats re-reading.

<details>
<summary>1. Complete the phrase: "An LLM is not a database. It is a ___ and ___ engine."</summary>

**Reasoning and language generation** engine. It understands context, reasons, and generates text — it does not store enterprise data.
</details>

<details>
<summary>2. Name the three layers of enterprise AI architecture.</summary>

1. **Data layer** (Salesforce, Data Cloud, Knowledge) — source of truth
2. **Agent layer** — orchestrator (retrieves, decides, acts)
3. **LLM layer** — reasoning engine (generates response)
</details>

<details>
<summary>3. What does an AI Agent do?</summary>

Orchestrates: calls APIs, retrieves Salesforce records, searches knowledge articles, executes workflows, makes decisions, coordinates multiple systems. It's the **orchestrator** that uses the LLM as its reasoning brain.
</details>

<details>
<summary>4. What does the LLM do in enterprise AI?</summary>

Summarizes, drafts emails, explains information, reasons over retrieved context, conducts natural language conversations. It does NOT store or retrieve data — it only processes what the agent feeds it.
</details>

<details>
<summary>5. Why can't the LLM answer "What's my case status?" on its own?</summary>

Because the LLM does not have access to live Salesforce data. The **agent** must retrieve the case from Salesforce via an API, then feed that data to the LLM, which reasons over it and generates a natural language response.
</details>

<details>
<summary>6. Give three examples where Human-in-the-Loop should be enforced.</summary>

Closing support cases, sending customer emails, approving refunds, legal responses, financial approvals, executive reports. Any irreversible, sensitive, or customer-facing action should require human review.
</details>

<details>
<summary>7. Why shouldn't an AI agent automatically close a customer support case?</summary>

Even if checks suggest the issue is resolved, premature closure damages customer trust. A human should review and confirm before closing, preserving accountability and customer satisfaction.
</details>

<details>
<summary>8. What five questions should an Enterprise AI Solutions Architect always ask?</summary>

1. Where does the data live?
2. Who owns the data?
3. Which system performs the action?
4. When should AI be involved?
5. When should a human approve the outcome?
</details>

<details>
<summary>9. What is the real enterprise AI flow (draw or describe)?</summary>

User → AI Agent → (Salesforce / Data Cloud / Knowledge Base / ERP / APIs) → agent retrieves data → feeds to LLM → LLM reasons and generates response → back to user. The agent orchestrates; the LLM reasons.
</details>

<details>
<summary>10. Why is Salesforce the "source of truth"?</summary>

It stores the live, authoritative business records (Accounts, Cases, Opportunities, Contacts). Any official write must go to Salesforce; the AI retrieves from it and writes back to it. The LLM only reasons over what is retrieved.
</details>

<details>
<summary>11. How does dividing data/agent/LLM responsibilities improve architecture?</summary>

Clear separation prevents bad patterns (storing data in the LLM, skipping orchestration, mixing reasoning with data storage). Each layer has one job: store truth, orchestrate, or reason. This enables security, governance, testability, and scale.
</details>

<details>
<summary>12. When should you NOT use AI for a task?</summary>

When the task is deterministic, rule-based, or low-complexity and can be handled by fixed automation (workflow rules, validation rules). AI adds cost and unpredictability; use it where **reasoning, language, or context synthesis** adds value.
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ LLM = reasoning engine, NOT a database (does not store enterprise data)
- ✅ Enterprise data lives in Salesforce, Data Cloud, Knowledge Bases
- ✅ AI Agent = orchestrator (retrieves data, calls APIs, decides tools, coordinates steps)
- ✅ LLM = reasons over retrieved context and generates natural language
- ✅ Three layers: Data (source of truth) → Agent (orchestrator) → LLM (reasoning)
- ✅ HITL required for irreversible, sensitive, customer-facing actions (closes, refunds, emails)
- ✅ Architect mindset: Where's the data? Who owns it? Which system acts? When AI? When human?

---

# Next Topic

**Week 1 – Day 3: AI Agents, MCP and Enterprise Tool Calling**

- Deep dive into AI Agents (goal, tools, loop, memory)
- Model Context Protocol (MCP) — standardized tool calling
- Tool calling (function calling) — how agents act on enterprise systems
- Planning, reasoning, and the ReAct pattern
- Multi-agent systems and when to use them
- Grounding, guardrails, and evaluation principles
