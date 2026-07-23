# Week 1 - Day 3: AI Agents, MCP and Enterprise Tool Calling

> **Learning Goal:** Understand what makes an AI system an "agent" (not just an LLM), how tool calling connects agents to enterprise systems, and how MCP standardizes those integrations at scale.

> **How to use this guide:**
> 1. Read sections 1–6 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain the difference between an LLM and an AI agent.
- Describe what tool calling is and why agents need it.
- Explain how tool calling relates to standard API integration.
- Define MCP (Model Context Protocol) and explain the problem it solves.
- Explain how MCP is like "USB-C for AI integrations."
- Sketch an enterprise agent + tool calling + MCP architecture at a high level.

---

# 1. LLM vs AI Agent

An **LLM** is a language model — it takes text in and produces text out. It can understand language, answer questions, summarize, generate code, and explain concepts. But it has **no memory**, **no goal**, **no tools**, and **no ability to act on systems**. It cannot query Salesforce, update an Opportunity, create a Case, send an email, or access internal company systems. Think of an LLM as the **brain**, not the hands.

An **AI Agent** wraps an LLM with four things:

> 🧠 **Memory hook — "GTLM" (Goal, Tools, Loop, Memory):**
> 1. **Goal** — it is trying to accomplish something, not just answer once.
> 2. **Tools** — functions / APIs it can call to fetch data or take action.
> 3. **Loop** — it acts, sees the result, and decides the next step; repeats until done.
> 4. **Memory** — it remembers what it has already done within the task.

An AI agent has both a brain **and** hands. It can:

- Think and decide
- Use tools
- Call APIs
- Search knowledge
- Execute workflows
- Ask follow-up questions
- Perform multi-step tasks
- Request human approval when needed

Think of it as a skilled assistant who knows when to ask, when to act, and when to wait for approval.

**Enterprise example:**

A sales manager asks:

> "Show me all opportunities closing this month worth over $1 million, summarize the risks, and email my team."

An **LLM alone** can't do this — it has no data and no way to send email.

An **AI agent** can:

1. Understand the request.
2. Query Salesforce using a tool (`search_opportunities(closeDate, amount)`).
3. Filter and retrieve the opportunities.
4. Analyze and summarize each one.
5. Draft the email.
6. Wait for human approval (Human-in-the-Loop).
7. Send the email after approval using a tool (`send_email`).

**Key line:** *An LLM reasons. An AI agent reasons **and acts**.*

---

# 2. What is Tool Calling?

**Tool calling** (a.k.a. function calling) is how an agent reaches outside the LLM. The LLM itself contains **no live enterprise data and no ability to act** — so it outputs a structured request to call a named tool with arguments; the application executes that tool and returns the result to the model, which then uses it to decide the next step.

**Flow:**

```text
LLM decides: "call search_opportunities(closeDate='2026-07', minAmount=1000000)"
        │
        ▼
Application executes the real API call to Salesforce
        │
        ▼
Result returned to the LLM (JSON list of opportunities)
        │
        ▼
LLM uses it to reason about the next step (analyze or email)
```

**Example enterprise tools:**

- `search_knowledge(query)` — retrieve Knowledge articles (RAG).
- `get_case(caseId)` — read a support case.
- `update_opportunity(id, fields)` — change a record (a **write** — needs HITL).
- `send_email(to, body)` — take an external action.
- `query_salesforce(soql)` — run a SOQL query.

**How it relates to normal API integration:** tool calling is, at its core, a **secure API call** — the same pattern as any authenticated integration between systems (REST + OAuth + managed credentials). The key difference is **orchestration**: instead of application code deciding when to call the API, **the LLM reasons about when and with what arguments to call the tool**, then uses the result to decide its next step. The underlying engineering discipline — authentication, error handling, rate limits, idempotency — is the same.

**Why tool calling matters:** Without tools, an LLM is just a chatbot that makes things up based on its training data. With tools, it becomes an **agent** that can read from and act on real enterprise systems — grounded in live data and capable of business actions.

**Key idea:** tools are the agent's hands. RAG-style *read* tools ground its answers in trusted data; *write* tools let it take business actions. In both cases, proper authentication, authorization, and approval workflows are critical.

---

# 3. Enterprise Tool Calling Architecture

When an agent uses tools in an enterprise setting, the architecture looks like this:

```text
                User request
                     │
                     ▼
              AI Agent (LLM brain)
                     │
         ┌───────────┼────────────┐
         ▼           ▼            ▼
    Tool 1      Tool 2        Tool 3
 (read data)  (search)      (write data)
         │           │            │
         ▼           ▼            ▼
   Salesforce   Knowledge    Email API
     (SOQL)       (RAG)      (SMTP/API)
```

**Key principles:**

- **Authentication** — every tool call uses secure credentials (OAuth, API keys, service accounts). The LLM never sees the credentials; the application manages them.
- **Authorization** — the tool enforces access control (user permissions, field-level security, object-level security). Just because the agent can call a tool doesn't mean it bypasses security.
- **Approval (HITL)** — high-risk actions (writes, irreversible changes, financial operations) require human approval before execution. The agent proposes; a human authorizes.
- **Error handling** — tools can fail (rate limits, network issues, invalid input). The agent must observe the error and retry, adjust, or escalate to a human.
- **Logging & audit** — every tool call is logged (who, what, when, why) for compliance, debugging, and monitoring.

**Who owns what:**

- **LLM** — understands the request, reasons about what to do, generates natural language.
- **AI Agent** — orchestrates the task, decides which tools to call and when, remembers progress.
- **Tools** — execute the actual business logic (query, update, send).
- **Business systems (Salesforce, etc.)** — own the source-of-truth data and enforce security/rules.
- **Human** — approves risky actions, handles exceptions, provides final judgment.

---

# 4. What is MCP (Model Context Protocol)?

MCP is an **open protocol** that standardizes how AI models communicate with external tools and data sources. It's like **USB-C for AI integrations** — instead of writing custom connectors for every model and every system, you implement MCP once on the server side, and any MCP-compatible AI client can connect using the same protocol.

**The problem MCP solves:**

Without a standard protocol, every new integration requires custom code:

```text
LLM
 ├── Custom Salesforce connector
 ├── Custom SAP connector
 ├── Custom Jira connector
 ├── Custom SQL connector
 └── Custom SharePoint connector
```

Every new system → another custom integration. Every new AI model → rewrite all the connectors.

**With MCP:**

```text
LLM (MCP client)
   │
   ▼
MCP Protocol (standardized)
   │
   ▼
MCP Server
   ├── Salesforce
   ├── SAP
   ├── Jira
   ├── SQL
   └── SharePoint
```

The AI uses a **common protocol** instead of bespoke integrations. Add a new system? Write one MCP server. Add a new AI model? If it speaks MCP, it works with all your systems.

**What MCP provides:**

- **Standard tool definitions** — describe functions, parameters, return types.
- **Secure authentication** — OAuth, API keys, token refresh.
- **Stateful sessions** — maintain context across multiple tool calls.
- **Error handling** — standard error codes and retry logic.
- **Interoperability** — any MCP client can talk to any MCP server.

> 🧠 **Memory hook:** **MCP = USB-C for AI**. One protocol, many systems. Plug in once, use everywhere.

**Why it matters for enterprises:** MCP reduces integration effort, makes AI systems more maintainable, and enables a marketplace of pre-built MCP servers (Salesforce, Slack, GitHub, databases) that you can connect to your agent without writing custom code. It's the **standard that makes enterprise AI scale**.

---

# 5. How MCP Works (simplified flow)

Here's how an agent uses MCP to call a tool:

```text
1. Agent (MCP client) sends a request:
   {
     "tool": "get_opportunity",
     "arguments": {"id": "006xx..."}
   }

2. MCP Server receives the request, authenticates, and calls Salesforce API.

3. Salesforce returns the Opportunity record.

4. MCP Server formats the result and sends it back to the client:
   {
     "result": {
       "Name": "Acme Corp Renewal",
       "Amount": 150000,
       "StageName": "Negotiation"
     }
   }

5. Agent (LLM) reads the result and decides the next step.
```

**What the MCP server does:**

- Exposes a catalog of tools (functions) the agent can call.
- Handles authentication and authorization.
- Translates MCP requests into actual API calls (REST, SQL, GraphQL).
- Returns results in a standard format.
- Manages errors, retries, rate limits.

**What the MCP client (agent) does:**

- Discovers available tools from the server.
- Decides which tool to call and with what arguments.
- Receives the result and uses it to reason about the next step.

**Key idea:** MCP is a **contract** between the agent and the tools. The agent doesn't need to know how Salesforce's API works — it just calls `get_opportunity(id)` via MCP, and the server handles the rest.

---

# 6. Enterprise AI Stack: LLM + Agent + Tools + MCP

Putting it all together:

```text
                User request
                     │
                     ▼
              AI Agent (brain + orchestration)
                     │
                     ▼
              MCP Client (tool interface)
                     │
         ┌───────────┼────────────┐
         ▼           ▼            ▼
   MCP Server 1  MCP Server 2  MCP Server 3
   (Salesforce)  (Knowledge)   (Email)
         │           │            │
         ▼           ▼            ▼
   Salesforce    Vector DB     SMTP API
   (REST API)    (embeddings)
```

**The layers:**

1. **User** — asks a question or gives a task.
2. **AI Agent** — understands, plans, orchestrates.
3. **MCP Client** — translates agent tool calls into MCP requests.
4. **MCP Servers** — each owns a domain (Salesforce, Knowledge, Email) and exposes tools.
5. **Business systems** — the source-of-truth data and execution layer.

**What this architecture gives you:**

- **Separation of concerns** — the agent reasons, the tools act, the systems enforce rules.
- **Reusability** — write one MCP server for Salesforce; use it with any MCP-compatible agent.
- **Security** — tools enforce authentication, authorization, and audit; the LLM never sees credentials.
- **Scalability** — add new tools without rewriting the agent; add new agents without rewriting the tools.
- **Governance** — every action is logged, auditable, and reversible (via standard business system controls).

**Key insight:** Enterprise AI is **not about replacing business systems**. It's about **intelligent orchestration** on top of those systems. The agent is a smart layer that reads, reasons, and acts — but Salesforce still owns the data, enforces permissions, and maintains the audit trail.

---

# Design Patterns to Remember

- **Agent = LLM + GTLM** (Goal, Tools, Loop, Memory).
- **Tool calling = secure API integration**, but orchestrated by the model's reasoning.
- **MCP = standard protocol** for connecting agents to tools (like USB-C for AI).
- **Always enforce HITL** for writes, irreversible actions, and high-risk decisions.
- **Never bypass security** — tools must enforce authentication, authorization, and access control.
- **Log everything** — every tool call, every decision, every approval, for audit and debugging.

---

# Interview Questions & Answers

## Q1. What is the difference between an LLM and an AI agent?

An LLM is a language model — text-in, text-out. It can understand language, answer questions, summarize, and generate code, but it has no memory, no goal, no tools, and no ability to act on systems. An AI agent wraps an LLM with a goal (accomplish something), tools (APIs it can call), a loop (reason → act → observe → repeat), and memory (remember progress). In short: an LLM thinks; an agent thinks **and acts**.

## Q2. What is tool calling, and how does it relate to normal API integration?

Tool calling is how an agent reaches outside the LLM to fetch data or take action. The LLM outputs a structured request to call a named tool with arguments; the application executes that tool (a secure API call) and returns the result to the model. It's the same as any authenticated REST API integration — OAuth, error handling, rate limits — but the **orchestration** is done by the LLM's reasoning, not hardcoded application logic. Same integration discipline, model-driven flow.

## Q3. What problem does MCP (Model Context Protocol) solve?

Without a standard protocol, every new integration requires custom code — one connector per system per AI model. MCP provides a universal protocol for how AI models communicate with tools and data sources, like USB-C for AI. Write one MCP server for Salesforce; any MCP-compatible agent can use it. This reduces integration effort, makes systems maintainable, and enables a marketplace of pre-built MCP servers you can plug into your agent without custom code.

## Q4. Why is MCP like "USB-C for AI integrations"?

Just as USB-C is a single connector that works with many devices (laptops, phones, monitors), MCP is a single protocol that works with many AI models and tools. One protocol, many systems — plug in once, use everywhere. It standardizes authentication, tool definitions, error handling, and interoperability, so you don't rewrite integrations every time you add a system or a new AI model.

## Q5. In an enterprise agent, who owns the business data and enforces security?

The **business systems** (Salesforce, SAP, etc.) own the source-of-truth data and enforce security — authentication, authorization, field-level security, object permissions, audit trails. The agent is a smart orchestration layer on top; it reads and acts via tools, but it **never bypasses security**. Every tool call must be authenticated and authorized, and high-risk actions require human approval (HITL).

## Q6. Give an example where tool calling turns an LLM into an agent.

A manager asks: "Show me all opportunities closing this month worth over $1M, summarize the risks, and email my team." An LLM alone can't do this — no data, no email. An agent: (1) calls `search_opportunities(closeDate, minAmount)` tool → gets results, (2) analyzes and summarizes, (3) drafts email, (4) waits for human approval (HITL), (5) calls `send_email` tool → done. Multi-step + real actions + live data = agent.

## Q7. What are the key principles of enterprise tool calling architecture?

Authentication (secure credentials, OAuth, never expose to LLM), authorization (tools enforce access control, no security bypass), approval/HITL (human review for writes and high-risk actions), error handling (tools can fail — agent observes and retries or escalates), and logging & audit (every tool call is logged for compliance, debugging, monitoring). The agent orchestrates; the tools act; the systems enforce rules.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats re-reading.

<details>
<summary>1. Name the four things an AI agent adds on top of a bare LLM (hint: GTLM).</summary>

**Goal, Tools, Loop** (reason→act→observe), and **Memory** of the task.
</details>

<details>
<summary>2. What can an LLM do? What can it NOT do?</summary>

Can: understand language, answer questions, summarize, generate code, explain concepts. Cannot: query Salesforce, update records, send email, access internal systems, remember across turns, take actions. No goal, no tools, no memory.
</details>

<details>
<summary>3. What is tool calling, in one sentence?</summary>

The LLM outputs a structured request to call a named tool with arguments; the app executes the tool and returns the result to the model, which uses it to decide the next step.
</details>

<details>
<summary>4. How is tool calling like a normal API integration? How is it different?</summary>

Same: secure API call (REST, OAuth, error handling, rate limits). Different: the **LLM decides when and with what arguments to call the tool**, versus hardcoded application logic. Same discipline, model-orchestrated.
</details>

<details>
<summary>5. What problem does MCP solve?</summary>

Without MCP, every new system and AI model requires custom integration code. MCP provides a standard protocol (like USB-C) so you write one MCP server per system, and any MCP-compatible agent can use it. Reduces integration effort, enables reuse.
</details>

<details>
<summary>6. Complete the analogy: "MCP is to AI integrations as ___ is to physical connectors."</summary>

**USB-C**. One protocol/connector, many systems/devices — plug in once, use everywhere.
</details>

<details>
<summary>7. What are the two types of tools an agent uses, and why do they matter?</summary>

**Read tools** (query, search, retrieve) — ground the agent in live trusted data (RAG). **Write tools** (update, create, delete, send) — let the agent take business actions. Both need security + HITL for writes.
</details>

<details>
<summary>8. Who enforces security when an agent calls a tool — the LLM or the business system?</summary>

The **business system** (Salesforce, etc.) enforces authentication, authorization, field/object permissions, and audit. The tool is the secure gateway; the LLM never sees credentials or bypasses security.
</details>

<details>
<summary>9. What should always happen before an agent executes a write or high-risk action?</summary>

**Human-in-the-Loop (HITL)** approval. The agent proposes the action; a human reviews and authorizes before it executes.
</details>

<details>
<summary>10. Give an example of an agent task that requires multiple tool calls.</summary>

"Show me all high-value opportunities closing this month, summarize risks, email my team." Agent: (1) calls `search_opportunities` → gets data, (2) analyzes, (3) drafts email, (4) gets HITL approval, (5) calls `send_email` → done.
</details>

<details>
<summary>11. What does an MCP server do?</summary>

Exposes a catalog of tools, handles authentication/authorization, translates MCP requests into actual API calls (REST/SQL), returns results in standard format, manages errors and retries.
</details>

<details>
<summary>12. Why must every tool call be logged in an enterprise agent?</summary>

For compliance, audit trail, debugging, and monitoring. You need to know who did what, when, why, and with whose approval — especially for writes and high-risk actions.
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ LLM = thinks (text-in → text-out). AI Agent = thinks **+ acts** (LLM brain + **GTLM**: Goal, Tools, Loop, Memory)
- ✅ Tool calling = secure API integration, orchestrated by the model's reasoning (not hardcoded logic)
- ✅ Tools are the agent's hands: **read tools** ground in live data; **write tools** take business actions
- ✅ MCP = **USB-C for AI** — standard protocol for connecting agents to tools (one protocol, many systems)
- ✅ MCP solves the custom-integration problem: write one server, any MCP client can use it
- ✅ Enterprise principles: authentication (OAuth, secure), authorization (tools enforce), HITL (approval for writes), logging (audit every call)
- ✅ Who owns what: LLM (understands/reasons), Agent (orchestrates), Tools (act), Business systems (enforce security), Human (approves)

---

# Next Topic

**Week 1 – Day 4: Retrieval-Augmented Generation (RAG)**

- Why LLMs hallucinate and how RAG solves it
- The RAG flow: query → retrieve → ground → generate
- Embeddings and vector search
- Chunking, indexing, and retrieval quality
- Enterprise RAG patterns (Knowledge articles, Data Cloud, Salesforce objects)
- RAG + agent = grounded actions
