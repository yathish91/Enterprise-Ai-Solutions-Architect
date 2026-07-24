# Week 1 Summary — How Enterprise AI Works

> **The one question Week 1 answers:** *"What actually happens between a user asking a
> question and the AI giving a safe, correct answer?"*

By the end of this week, you should be able to explain that whole journey in plain words.
Here's the short version of each day.

---

## Day 1 — What is Enterprise AI?

**In simple words:** Enterprise AI means using AI to solve real business problems — not just
building a chatbot for fun.

- Consumer AI answers general questions. Enterprise AI works with a company's own data,
  systems, and rules.
- Companies invest in it to save time, cut costs, and make better decisions.
- **Takeaway:** Enterprise AI is about connecting AI to the business, safely.

---

## Day 2 — LLMs, AI Agents & Human-in-the-Loop

**In simple words:** An LLM is the "thinking" part. An agent is the part that also "does"
things.

- An **LLM** is very good with language, but it is **not a database** — it doesn't *know*
  your company's live data.
- The company's real records (in a system like a CRM) are the **source of truth**, not the
  model's memory.
- A quick way to remember it:
  - **LLM = Thinks**
  - **AI Agent = Thinks + Acts**
- **Human-in-the-Loop (HITL):** for risky actions, a person checks before anything final
  happens. Example: the AI can *suggest* closing a support case, but a human approves it.

```text
User
 │
 ▼
AI Agent
 ├── Business data (source of truth)
 ├── Knowledge base
 └── LLM
```

---

## Day 3 — Tool Calling & MCP

**In simple words:** The AI doesn't hold your data — it *calls tools* to fetch or change it.

- **Tools** are actions the AI can trigger, like "look up this customer" or "update this
  record."
- **MCP (Model Context Protocol)** is a shared standard so AI can connect to many tools the
  same way — think of it as **USB-C for AI**: one common plug instead of a different cable
  for every device.
- **Takeaway:** The agent stays smart and general; the tools do the real work of touching
  systems.

---

## Day 4 — Retrieval-Augmented Generation (RAG)

**In simple words:** Look it up first, then answer.

- An LLM can't reliably answer company-specific questions on its own, so we **fetch the
  relevant information first** and hand it to the model.
- The AI **retrieves** the right records or documents, then the LLM writes the answer using
  them.
- **RAG vs. fine-tuning:** RAG *gives the model the facts at question time*; fine-tuning
  *retrains the model's style/skills*. For fresh, company-specific facts, RAG is usually the
  answer.
- **Golden rule:** **Search first → Generate second.**

```text
User → AI Agent → Retrieve information → LLM → Answer
```

---

## Day 5 — Prompt Engineering

**In simple words:** A good prompt is more than a question — it's clear instructions plus
context plus rules.

A strong enterprise prompt combines four things:
1. **Clear instructions** — what to do.
2. **Context** — the retrieved data to use.
3. **Safety rules (guardrails)** — what *not* to do (e.g., don't guess, don't leak private
   info).
4. **Output format** — how the answer should look (e.g., structured/JSON).

```text
System Prompt  →  User Prompt  →  Retrieved Context  →  LLM  →  Response
```

---

## The Complete Picture

Putting all five days together, here's the full enterprise AI flow:

```text
                User
                  │
                  ▼
             User Prompt
                  │
                  ▼
              AI Agent
                  │
      ┌───────────┼────────────┐
      ▼           ▼            ▼
 Business data  Knowledge   APIs / Tools
      │           │            │
      └───────────┼────────────┘
                  ▼
          Retrieved Context
                  │
                  ▼
           System Prompt
                  │
                  ▼
                 LLM
                  │
                  ▼
        Structured Response
                  │
                  ▼
        Human-in-the-Loop (if needed)
                  │
                  ▼
          Final Business Action
```

This same shape shows up in most enterprise AI systems, no matter which vendor builds them.

---

## Key Terms (one line each)

| Term | Meaning |
|---|---|
| **LLM** | Understands and generates language. |
| **AI Agent** | Plans tasks and uses tools to get things done. |
| **Tool Calling** | Lets the agent read from and act on real systems. |
| **MCP** | A standard way to connect AI to many tools ("USB-C for AI"). |
| **RAG** | Fetches trusted information before the model answers. |
| **System Prompt** | Sets the AI's role and rules. |
| **User Prompt** | The user's actual question. |
| **Context** | The retrieved data the answer is based on. |
| **Guardrails** | Safety and compliance rules. |
| **Structured Output** | A machine-readable answer (often JSON). |
| **Human-in-the-Loop** | A person approves important actions. |

---

## What's Next

**Week 2** goes one level deeper into the machinery that makes this work well:
embeddings, vector databases, chunking, prompt engineering for the enterprise, agents, and
how to evaluate and guard the whole system.

➡️ Start with **[Week 2 – Day 1: Embeddings & Vector Databases](../week-2/Day1-Embeddings_&_Vector_Databases.md)**
