# Week 2 - Day 4: AI Agents & Agentic AI

> **Learning Goal:** Understand what makes an AI system an "agent" (not just a chatbot), how agents plan and use tools, how multi-agent systems and human-in-the-loop work, and how this maps to Salesforce Agentforce.

> **How to use this guide:**
> 1. Read sections 1–7 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain what an AI Agent is and how it differs from a chatbot.
- Describe the agent loop: perceive → reason/plan → act → observe → repeat.
- Explain tool calling and why agents need it.
- Explain planning and reasoning (incl. ReAct-style reasoning).
- Describe multi-agent systems and when to use them.
- Explain Human-in-the-Loop (HITL) and where it belongs.
- Sketch the Salesforce Agentforce architecture at a high level.

---

# 1. What is an AI Agent?

An **AI Agent** is a system that uses an LLM as its "brain" to **pursue a goal** by
**planning steps, calling tools, observing the results, and looping** until the goal is
met. It doesn't just produce text — it takes actions in the real world (systems, APIs,
data) and adapts based on what it sees.

A simple way to remember it:

> **LLM = Thinks. AI Agent = Thinks + Acts.**

A bare LLM is text-in → text-out. It can't look anything up, can't change any system, and
forgets the goal after a single reply. An agent wraps that brain with four things:

> 🧠 **Memory hook — "GTLM" (Goal, Tools, Loop, Memory):**
> 1. **Goal** — it is trying to *accomplish* something, not just answer once.
> 2. **Tools** — functions / APIs it can call to fetch data or take action.
> 3. **Loop** — it acts, sees the result, and decides the next step; repeats until done.
> 4. **Memory** — it remembers what it has already done within the task.

**A note on agents vs. traditional automation:** Traditional workflow automation (for
example, a business-process flow) follows a *fixed, pre-defined path* that a developer wired
up in advance. An agent is different: it *decides its own path at runtime* using the LLM's
reasoning. Fixed automation = deterministic steps; agent = chooses the next step based on
context.

**Enterprise example:** A customer says *"My invoice is wrong, fix it."*
- A **chatbot** replies "Here's how invoices work" and stops.
- An **agent** looks up the account (tool) → finds the invoice (tool) → detects the error →
  drafts a correction → asks a human to approve (HITL) → applies the fix (tool). Multiple
  steps, real actions, all toward the goal.

**Key line:** *A chatbot answers. An agent gets things done.*

## Agent vs Chatbot

| | Chatbot | AI Agent |
|---|---|---|
| **Goal** | No persistent goal — reacts to each message | Has a goal and works toward completing it |
| **Steps** | Single question → single answer | Plans and executes multiple steps |
| **Tools** | Usually none — answers from the model / a script | Calls tools (APIs, functions, MCP) to retrieve data or act |
| **Acts on systems** | No — it only produces text | Yes — can read and change enterprise systems |
| **Memory** | Little/none of the task | Remembers progress within the task |
| **Recovers from errors** | No — one shot | Yes — observes a bad result and retries/adjusts |

---

# 2. The Agent Loop

The defining feature of an agent is the **loop**: it doesn't answer in one shot, it cycles
**Reason → Act → Observe** until the goal is satisfied.

```text
        Goal / User request
                │
                ▼
          ┌───────────┐
          │  Reason /  │  ◄─────────────┐
          │   Plan     │                │
          └───────────┘                │
                │                       │
                ▼                       │
          ┌───────────┐                │
          │  Act       │  (call a tool) │
          └───────────┘                │
                │                       │
                ▼                       │
          ┌───────────┐                │
          │ Observe    │  (tool result)│
          └───────────┘ ───────────────┘
                │
                ▼ (goal met?)
          Final answer / action
```

- **Reason/Plan** — the LLM decides what to do next ("I need the customer's open cases").
- **Act** — it calls a tool to do it (`get_cases(accountId)`).
- **Observe** — it reads the tool's result and feeds it back into the next reasoning step.

**Why the loop matters:** it lets the agent gather information it didn't have up front,
**recover from bad or empty results** (try a different tool or query), and complete tasks
that need several dependent steps — none of which a single-shot chatbot can do.

---

# 3. Tool Calling

**Tool calling** (a.k.a. function calling) is how an agent reaches outside the model. The
LLM itself contains **no live enterprise data and no ability to act** — so it outputs a
structured request to call a named tool with arguments; the application runs that tool and
returns the result to the model.

**Flow:**

```text
LLM decides: "call get_case(caseId='500xx')"
        │
        ▼
Application executes the real function / API
        │
        ▼
Result returned to the LLM
        │
        ▼
LLM uses it to reason about the next step
```

**Example enterprise tools:**
- `search_knowledge(query)` — retrieve Knowledge articles (RAG).
- `get_case(caseId)` — read a support case.
- `update_opportunity(id, fields)` — change a record (a **write** — needs HITL).
- `send_email(to, body)` — take an external action.

**How it relates to normal API integration:** tool calling is, at its core, a **secure API
call** — the same pattern as any authenticated integration between systems (REST + OAuth +
managed credentials). The key difference is *orchestration*: instead of application code
deciding when to call the API, **the LLM reasons about when and with what arguments to call
the tool**, then uses the result to decide its next step. The underlying engineering
discipline — authentication, error handling, idempotency — is the same.

**Key idea:** tools are the agent's hands. RAG-style *read* tools ground its answers;
*write* tools let it act on systems. In MCP terms (Week 1), tools are exposed through a
standard protocol so the same agent can talk to many systems consistently.

---

# 4. Planning & Reasoning

Hard tasks can't be done in one leap, so the agent **decomposes** them into sub-steps and
reasons about the order.

- **Task decomposition** — break "resolve this complaint" into: identify customer → find
  the order → check policy → decide action → execute → confirm.
- **ReAct pattern (Reason + Act)** — the model interleaves a *thought* ("I should look up
  the refund policy") with an *action* (calls the policy tool), then **reads the result
  before the next thought**. This grounds each step in real data instead of guessing.
- **Reflection / self-critique** — the agent reviews its own draft output ("does this
  actually answer the question / follow policy?") and can retry or correct before finishing.

> 🧠 **Memory hook:** **ReAct = Reason + Act**, alternating. Thought → tool → read result →
> next thought. Never "guess then answer"; always "check then answer."

**Why it matters for enterprises:** reasoning + grounding is what keeps the agent from
hallucinating a confident-but-wrong action on a real customer record.

---

# 5. Multi-Agent Systems

A **multi-agent system** uses several **specialized agents** that coordinate, usually via an
**orchestrator (router)** agent that decides who handles what.

```text
                 User request
                      │
                      ▼
              Orchestrator / Router
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   Sales Agent   Service Agent   Billing Agent
  (its own       (its own        (its own
   tools/data)    tools/data)     tools/data)
```

- **What it is:** each agent has its own role, tools, permissions, and knowledge; the
  orchestrator routes the request to the right specialist(s).
- **When to use it:** the task is too broad for one agent, or different steps need
  **different tools, permissions, or domain knowledge** (e.g., Sales vs. Service vs. Billing).
- **Trade-off:** more capable, but **more complexity, higher cost, harder to debug and
  monitor.** Start with a single agent + tools; add multi-agent only when scope demands it.

---

# 6. Human-in-the-Loop (HITL)

**HITL** means a human must review/approve before the agent takes certain actions. It is the
enterprise safety valve.

**Where HITL belongs:**
- **Irreversible or high-impact actions** — deleting data, issuing refunds, sending money.
- **Writes to systems of record** — creating/updating/closing records (create/update/delete).
- **Low-confidence decisions** — the model is uncertain or the stakes are high.
- **Regulated / sensitive contexts** — anything with compliance or PII exposure risk.

**How it appears in an agent:** a **confirm-before-write** step. The agent proposes the
action ("Close case 500xx and refund $40?") and pauses; only after human approval does it
call the write tool.

**Analogy:** HITL is the AI-era version of an **approval step** — the same instinct behind
approval processes and validation rules in enterprise systems: don't let a sensitive change
commit without a check. For example, an agent should *recommend* closing a support case, but
a human verifies before it is actually closed.

---

# 7. Salesforce Agentforce Architecture (high level)

Mapping the generic agent concepts onto **Agentforce**:

```text
                User request
                     │
                     ▼
              Agentforce Agent
          (topics decide intent)
                     │
        ┌────────────┼─────────────┐
        ▼            ▼             ▼
     Actions     Grounding      Guardrails
   (Flow/Apex/   (Data Cloud +
    prompt tmpl)  Knowledge)
        └────────────┼─────────────┘
                     ▼
                    LLM
                     │
                     ▼
        Response  ──►  Human-in-the-Loop (if high-risk)
                     │
                     ▼
              Final business action
```

- **Topics** — classify the user's intent and scope what the agent can do in that area.
- **Actions = the agent's tools** — implemented as **Flows, Apex, prompt templates, or
  standard actions**. This is how Agentforce "acts."
- **Grounding** — retrieval from **Data Cloud** (unified profiles) and **Knowledge** articles
  so answers are based on trusted company data (RAG).
- **LLM** — reasons over the grounded context and decides the response/action.
- **Guardrails** — safety/compliance rules (no PII exposure, refuse unsafe requests, answer
  only from retrieved context).
- **Human-in-the-Loop** — human approval / handoff for sensitive or irreversible actions.

> 🧠 **Memory hook — map generic → Agentforce:**
> Topics = **intent** · Actions = **tools** · Data Cloud + Knowledge = **grounding** ·
> Guardrails = **safety** · HITL = **approval**.

**Grounding in practice:** Data Cloud unifies data from many sources (ingestion, data model
objects, identity resolution) into a single unified profile. That unified profile is the
grounding layer an Agentforce agent retrieves from. So the enterprise pattern is:
*unify data in Data Cloud → agent retrieves the unified profile + Knowledge → LLM answers
from that trusted context.*

---

# Design Patterns to Remember

- **Single agent + tools** — most enterprise use cases should start here (simplest, easiest
  to monitor).
- **RAG + agent** — retrieve trusted context first, *then* let the agent reason/act on it.
- **Router / multi-agent** — only when scope is too broad for one agent, or steps need
  different tools/permissions.
- **Always add HITL** for irreversible or sensitive actions.
- **Guardrails everywhere** — grounding + refusal + PII protection are not optional in
  enterprise.

---

# Interview Questions & Answers

## Q1. What is the difference between a chatbot and an AI agent?

A chatbot responds to a single message with a single answer — no persistent goal, no tools,
no ability to act on systems. An AI agent uses an LLM as its brain but adds a goal, tools it
can call, memory of the task, and a loop (reason → act → observe) so it can take multiple
steps and change real systems. In short: a chatbot answers; an agent gets things done.

## Q2. Explain the agent loop (reason → act → observe).

The agent **reasons** about what to do next, **acts** by calling a tool, then **observes** the
tool's result and feeds it back into the next reasoning step. It repeats this cycle until the
goal is met. The loop is what lets an agent gather missing information, recover from bad
results, and complete multi-step tasks — things a single-shot chatbot cannot do.

## Q3. When would you use a multi-agent system instead of a single agent?

When the task is too broad for one agent, or different steps need different tools,
permissions, or domain knowledge (e.g., separate Sales, Service, and Billing agents behind a
router). It adds capability but also complexity, cost, and debugging difficulty, so start
with a single agent + tools and only move to multi-agent when scope genuinely requires it.

## Q4. Where should Human-in-the-Loop be enforced in an enterprise agent, and why?

On irreversible or high-impact actions (refunds, deletions, payments), on writes to systems
of record, on low-confidence decisions, and in regulated/PII-sensitive contexts. Because LLMs
can be confidently wrong, a human approval step before executing such actions prevents costly
or non-recoverable mistakes on real customer data.

## Q5. In Agentforce, what plays the role of the agent's "tools"?

**Actions** — implemented as Flows, Apex, prompt templates, or standard actions. Topics
classify intent, Actions let the agent do things, grounding comes from Data Cloud + Knowledge,
and guardrails plus HITL keep it safe.

## Q6. How does an agent avoid hallucinating when it acts on enterprise data?

Through grounding (RAG) — it retrieves trusted context from systems like Data Cloud/Knowledge
and is instructed to answer only from that context — plus reasoning patterns (ReAct) that base
each step on real tool results, guardrails that enforce refusal/fallback, and HITL for risky
writes.

## Q7. How does agent tool-calling relate to a normal API integration?

Tool calling *is* secure API integration — the same pattern as any authenticated call between
systems (REST, OAuth, managed credentials). The difference is orchestration: instead of
application code deciding when to call the API, the **LLM reasons about when and with what
arguments to call the tool**, then uses the result to decide its next step. The integration
discipline (auth, error handling, idempotency) is the same.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats
> re-reading.

<details>
<summary>1. Complete the phrase: "LLM = Thinks. AI Agent = ___"</summary>

Thinks **+ Acts**. It adds a goal, tools, a loop, and memory on top of the LLM brain.
</details>

<details>
<summary>2. Name the four things an agent adds on top of a bare LLM (hint: GTLM).</summary>

**Goal, Tools, Loop** (reason→act→observe), and **Memory** of the task.
</details>

<details>
<summary>3. What are the three steps of the agent loop?</summary>

Reason/Plan → Act (call a tool) → Observe (read result) — repeat until the goal is met.
</details>

<details>
<summary>4. Why can't an LLM act on enterprise systems by itself?</summary>

It's text-in/text-out with no live data and no ability to execute actions. It needs
**tool calling** — it outputs a structured request, the app runs the tool, and returns the
result to the model.
</details>

<details>
<summary>5. What does ReAct stand for and why does it matter?</summary>

**Reason + Act** — the model interleaves a thought with a tool action and reads the result
before the next step. It grounds each step in real data instead of guessing.
</details>

<details>
<summary>6. When should you choose multi-agent over a single agent? What's the cost?</summary>

When the task is too broad for one agent or steps need different tools/permissions/knowledge.
Cost: more complexity, higher cost, harder to debug and monitor. Start single-agent.
</details>

<details>
<summary>7. List four places HITL should be enforced.</summary>

Irreversible/high-impact actions, writes to systems of record, low-confidence decisions,
and regulated/PII-sensitive contexts.
</details>

<details>
<summary>8. In Agentforce, map: Topics, Actions, Data Cloud+Knowledge, Guardrails, HITL.</summary>

Topics = intent classification; Actions = the agent's tools (Flow/Apex/prompt/standard);
Data Cloud + Knowledge = grounding (RAG); Guardrails = safety/compliance; HITL = human
approval for risky actions.
</details>

<details>
<summary>9. Give an example where an agent beats a chatbot.</summary>

"Fix my wrong invoice": agent looks up account → finds invoice → detects error → drafts
correction → gets human approval → applies fix. Multi-step + real actions vs. a chatbot's
single canned answer.
</details>

<details>
<summary>10. How does grounding reduce hallucination in an enterprise agent?</summary>

The agent retrieves trusted context (Data Cloud/Knowledge) and is instructed to answer only
from it; combined with ReAct reasoning on real tool results, guardrails, and HITL for writes.
</details>

<details>
<summary>11. How is agent tool-calling like a normal API integration?</summary>

Both are secure external API calls (REST + OAuth + managed credentials). Difference: the LLM
decides when/how to call the tool, versus application code triggering the call. Same
integration discipline, model-orchestrated.
</details>

<details>
<summary>12. How does an AI agent differ from traditional workflow automation?</summary>

Traditional automation follows a fixed, pre-designed path (deterministic). An agent decides
its own path at runtime using the LLM's reasoning. Fixed automation = wired branches; agent =
chooses the next step.
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ AI Agent → Thinks **and** acts (LLM brain + **GTLM**: Goal, Tools, Loop, Memory)
- ✅ Agent loop → reason → act → observe → repeat until goal met
- ✅ Tool calling → secure API integration, but orchestrated by the model's reasoning
- ✅ Planning → decompose task; ReAct = reason + act + read result; reflection = self-critique
- ✅ Multi-agent → specialized agents + orchestrator for broad tasks (more power, more cost)
- ✅ HITL → human approval for irreversible / high-risk / low-confidence actions
- ✅ Agentforce → Topics (intent) + Actions (Flow/Apex/prompt) + Data Cloud/Knowledge grounding + Guardrails + HITL

---

# Next Topic

**Week 2 – Day 5**

**Evaluation & Guardrails**

- Why evaluation matters (how do you know the agent works?)
- Offline evals vs. online monitoring
- Retrieval quality metrics (hit-rate, precision)
- Answer quality: groundedness, relevance
- Guardrails recap + safety testing
- Leads into: the RAG assistant `DESIGN.md` (bridge to building in Week 4)
