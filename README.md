# 🧠 Enterprise AI — Solutions Architect Study Notes

> A structured, beginner-friendly walk through how **enterprise AI actually works** — from
> "what is an LLM?" all the way to agents, RAG, vector search, prompt engineering, and how
> to keep it all safe and reliable.

These are open, self-study notes written in plain language. Each day is a short, focused
lesson you can read in one sitting, with diagrams, real-world enterprise examples,
interview questions, and a self-quiz to test yourself.

---

## 📌 What is this?

A learning path for anyone who wants to understand **AI in the enterprise** — not just how to
chat with a model, but how real companies build trustworthy AI systems on top of their own
data and workflows.

No prior AI experience needed. If you understand the basics of software and APIs, you can
follow along.

## 👤 Who is it for?

- Developers, consultants, and architects moving into AI.
- Anyone preparing for **AI / Solutions Architect** interviews.
- People who learn best from clear explanations, diagrams, and quizzes.

## 🚀 How to use these notes

1. **Read in order** — each day builds on the last (there's a "Next Topic" link at the bottom
   of every file).
2. **Say the interview answers out loud** — don't just read them.
3. **Do the self-quiz** — cover the answer, recall it, then check. Testing yourself beats
   re-reading.

---

## 🗺️ Syllabus

### Week 0 — Foundations
How the web works underneath any AI system.

| Topic | Notes |
|---|---|
| Internet, DNS, HTTP/S, TCP, APIs, OAuth, Load Balancers, CDNs | [Basics](week-0/Basics.md) |

### Week 1 — How Enterprise AI Works (end to end)
From a user's question to a safe, grounded answer.

| Day | Topic | Notes |
|---|---|---|
| 1 | What is Enterprise AI | [Day 1](week-1/Day1-What_is_Enterprise_AI.md) |
| 2 | Understanding LLMs & Enterprise AI Architecture | [Day 2](week-1/Day2-Understanding_LLMs_&_Enterprise_AI_Architecture.md) |
| 3 | AI Agents, MCP & Enterprise Tool Calling | [Day 3](week-1/Day3-AI_Agents_MCP_and_Enterprise_Tool_Calling.md) |
| 4 | Retrieval-Augmented Generation (RAG) | [Day 4](week-1/Day4-Retrieval_Augmented_Generation.md) |
| 5 | Prompt Engineering | [Day 5](week-1/Day5-Prompt_Engineering.md) |
| — | Week 1 Recap | [Summary](week-1/summary.md) |

### Week 2 — Building Blocks of AI Search & Reliable Agents
The pieces that make enterprise AI accurate, safe, and production-ready.

| Day | Topic | Notes |
|---|---|---|
| 1 | Embeddings & Vector Databases | [Day 1](week-2/Day1-Embeddings_&_Vector_Databases.md) |
| 2 | Chunking, Indexing & Retrieval | [Day 2](week-2/Day2-Chunking_Indexing_&_Retrieval.md) |
| 3 | Prompt Engineering for Enterprise AI | [Day 3](week-2/Day3-Prompt_Engineering_for_Enterprise_AI.md) |
| 4 | AI Agents & Agentic AI | [Day 4](week-2/Day4-AI_Agents_&_Agentic_AI.md) |
| 5 | Evaluation & Guardrails | [Day 5](week-2/Day5-Evaluation_&_Guardrails.md) |

---

## 🧩 The big picture

By the end of Week 1, you can explain the complete flow of an enterprise AI system:

```text
        User asks a question
                 │
                 ▼
             AI Agent
                 │
   ┌─────────────┼──────────────┐
   ▼             ▼              ▼
Business data  Knowledge base  APIs / Tools
   └─────────────┼──────────────┘
                 ▼
        Retrieved Context (grounding)
                 │
                 ▼
               LLM  (with a system prompt + guardrails)
                 │
                 ▼
        Structured, grounded answer
                 │
                 ▼
     Human-in-the-Loop (for risky actions)
                 │
                 ▼
        Final business action
```

Week 2 then zooms into the parts that make this reliable: how meaning is turned into
**vectors**, how documents are **chunked and searched**, how **prompts** control behavior,
how **agents** plan and act, and how you **evaluate and guard** the whole thing.

---

## 🔑 Key concepts covered

| Concept | In one line |
|---|---|
| **LLM** | Reasons over and generates language. |
| **AI Agent** | Thinks *and* acts — plans steps and uses tools. |
| **Tool Calling** | Lets the AI read from and act on real systems. |
| **MCP** | A standard "USB-C" protocol for connecting AI to tools and data. |
| **RAG** | Retrieves trusted company data *before* the model answers. |
| **Embeddings** | Turn text into numbers so similar meanings sit close together. |
| **Vector DB** | Stores embeddings for fast "find the most similar" search. |
| **Chunking** | Splits big documents into searchable, meaningful pieces. |
| **Prompt Engineering** | Instructions + context + rules that shape the answer. |
| **Guardrails** | Safety and compliance limits on inputs and outputs. |
| **Evaluation** | How you *prove* the system works (not just hope it does). |
| **Human-in-the-Loop** | A person approves high-risk actions before they happen. |

---

## 📚 Note

These notes are shared openly for learning. They're written to be practical and
interview-ready — clear explanations first, jargon second. Corrections and suggestions are
welcome.
