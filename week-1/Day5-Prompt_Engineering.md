# Week 1 - Day 5: Prompt Engineering

> **Learning Goal:** Understand how to design prompts that control LLM behavior, combine system/user/context prompts effectively, chain prompts for complex tasks, enforce guardrails, and structure outputs for enterprise systems.

> **How to use this guide:**
> 1. Read sections 1–7 once, top to bottom.
> 2. Rehearse the **Interview Q&A** answers out loud.
> 3. Do the **Self-Quiz**: cover the answer, recall it, then expand to check. Retrieval beats re-reading.

---

# Learning Outcomes

By the end of this session, you should be able to:

- Explain what a prompt is and why it matters for LLM behavior.
- Describe the three types of prompts: system, user, and context.
- Explain how system + user + context combine to produce an answer.
- Create prompt templates with placeholders for reusable workflows.
- Explain prompt chaining and when to break a task into multiple prompts.
- Define guardrails and list where they must be enforced in enterprise AI.
- Describe structured outputs and why downstream systems need them.

---

# 1. What is a Prompt?

A **prompt** is the instruction given to an LLM. It is the single most important input you control — the model has no idea what you want it to do until you tell it.

**Simple example:**

```text
Summarize this customer case.
```

**Better prompt:**

```text
Summarize this customer case in 5 bullet points.

Include:
- Customer issue
- Root cause
- Current status
- Next action
- Owner
```

The second prompt is **specific** about format and what to include, so the output is predictable and usable.

> 🧠 **Memory hook:** "Vague prompt = vague output. Specific prompt = specific output."

In enterprise AI, prompts are not typed by hand every time — they are **templated, chained, and governed by guardrails** to ensure consistency, safety, and compliance.

---

# 2. Types of Prompts

There are **three** kinds of prompts that combine to produce an LLM's final answer:

## System Prompt

This is the **most important** prompt in enterprise AI.

The **system prompt** tells the AI:

- **Who it is** (role).
- **What it can and cannot do** (rules, guardrails).
- **How it should behave** (tone, format, refusal instructions).

**Example:**

```text
You are a Salesforce Support Engineer assistant.

Only answer using retrieved Salesforce data.

Never make assumptions.

If data is unavailable, say "I don't have enough information to answer that."

Do not expose customer PII.
```

**Users never see this prompt.** It is defined by the system administrator or developer and applies to every conversation. It controls the AI's behavior.

## User Prompt

This is what the **user types**.

**Example:**

```text
Why was my opportunity lost?
```

The user prompt expresses the request or question. It changes every time.

## Context Prompt

This comes from **RAG** (retrieval). The system retrieves relevant data and injects it as context before the LLM answers.

**Example:**

```text
Opportunity:

Stage: Negotiation
Amount: $500,000
Reason Lost: Competitor offered lower pricing.
Last Activity: 45 days ago.
```

The LLM does not "know" this data — **it must be retrieved and provided as context**. Without context, the model hallucinates.

---

# 3. How System + User + Context Combine

The LLM sees **all three** prompts merged together:

```text
          System Prompt
                │
          User Prompt
                │
          Retrieved Context (RAG)
                │
                ▼
               LLM
                │
                ▼
             Answer
```

**Example of the combined prompt the LLM sees:**

```text
[System] You are a Salesforce Support Engineer assistant. Only answer using retrieved data.

[Context] Opportunity: Stage=Negotiation, Amount=$500k, Lost Reason: Competitor pricing.

[User] Why was my opportunity lost?
```

**LLM output:**

> The opportunity was lost because the competitor offered lower pricing. It was in the Negotiation stage with a value of $500,000, and there has been no activity for 45 days.

The answer is **grounded** in the context and follows the system prompt's rules.

> 🧠 **Memory hook:** **System = rules. User = question. Context = data. LLM = answers using all three.**

---

# 4. Prompt Templates

Instead of writing prompts manually every time, enterprises create **reusable templates** with placeholders.

**Example template:**

```text
Summarize this Case.

Case Number: {{CaseNumber}}
Priority: {{Priority}}
Status: {{Status}}
Owner: {{Owner}}
```

At runtime, the placeholders (e.g., `{{CaseNumber}}`) are replaced with real values from the system:

```text
Summarize this Case.

Case Number: 00123456
Priority: High
Status: Open
Owner: Jane Doe
```

**Why templates matter:**

- **Consistency** — every case summary follows the same structure.
- **Reusability** — write once, use for every case.
- **Maintainability** — update the template once, and all summaries change.

**Enterprise example:** Salesforce Prompt Builder lets you define prompt templates with merge fields, so agents and flows can call them with dynamic data.

---

# 5. Prompt Chaining

Some tasks are too complex for **one prompt**. Instead of writing one giant prompt, **break the task into steps** and chain multiple prompts.

**Example:**

Customer asks:

```text
Why is my customer unhappy?
```

Instead of one giant prompt that tries to do everything, use **four chained prompts**:

```text
Prompt 1 → Summarize the case.
   ↓
Prompt 2 → Identify root causes.
   ↓
Prompt 3 → Recommend actions.
   ↓
Prompt 4 → Draft email.
```

Each prompt **builds on the previous one's output**. This produces **more reliable, structured results** than asking the model to do everything at once.

**Why chaining works:**

- **Focus** — each step has a narrow goal.
- **Error isolation** — if one step fails, you know where to fix it.
- **Easier to test** — validate each step independently.

> 🧠 **Memory hook:** "One giant prompt = chaos. Chained prompts = clarity."

**Enterprise use case:** an agent might chain: retrieve account → retrieve open cases → analyze sentiment → draft resolution → get human approval → execute.

---

# 6. Guardrails

**Guardrails** are the **safety rules** that prevent the AI from doing things it shouldn't.

In enterprise AI, guardrails are **not optional** — they are the difference between a helpful assistant and a compliance/security disaster.

**Examples of guardrails:**

- Don't reveal confidential information.
- Don't generate offensive language.
- Don't provide legal or medical advice.
- Don't approve refunds or financial transactions (human-in-the-loop required).
- Don't expose customer PII (name, email, phone, address).
- Don't invent answers — if data is missing, escalate to a human.

**How guardrails are enforced:**

1. **System prompt rules** — "Never expose PII. If you don't know, say so."
2. **Output filtering** — scan the LLM's response and block/redact prohibited content.
3. **Tool call restrictions** — the AI cannot call a `refund_customer()` tool without human approval.
4. **Human-in-the-loop (HITL)** — certain actions require human review before executing.

**Enterprise flow with guardrails:**

```text
User request
     │
     ▼
System Prompt (defines rules)
     │
     ▼
LLM generates response
     │
     ▼
Guardrails check (filter PII, check policy)
     │
     ├─ Pass → return response
     └─ Fail → refuse or escalate to human
```

> 🧠 **Memory hook:** "Guardrails = the AI's rules of engagement. No guardrails = no production deployment."

---

# 7. Structured Outputs

LLMs naturally produce **unstructured text** (paragraphs). But enterprise systems need **structured data** (JSON, fields) to integrate the output into workflows, APIs, and databases.

**Example:**

Instead of:

> The case is high priority and owned by Jane Doe. It is open and was created yesterday.

You want:

```json
{
  "caseNumber": "00123456",
  "priority": "High",
  "status": "Open",
  "owner": "Jane Doe",
  "createdDate": "2026-07-22"
}
```

**How to get structured output:**

- **Instruct the model** in the prompt: "Return your answer as JSON with these fields: caseNumber, priority, status, owner, createdDate."
- Use **function calling / tool calling** — many LLM APIs support structured output schemas that guarantee the format.
- **Parse and validate** — even with instructions, validate the output before passing it downstream.

**Why it matters:**

- Downstream systems (Flows, APIs, databases) cannot parse paragraphs — they need structured data.
- Structured outputs are **easier to test, monitor, and debug**.

---

# Real Enterprise Example

**User asks:**

```text
Summarize my last support interaction.
```

**What happens behind the scenes:**

1. **User sends the request** (User Prompt).
2. **Agent retrieves the case** from Salesforce (tool call).
3. **RAG retrieves** relevant Knowledge articles (if needed).
4. **System prompt** defines the AI's role and restrictions.
5. **Context prompt** includes the retrieved case details.
6. **LLM generates the summary** using System + User + Context.
7. **Guardrails** ensure no PII is exposed.
8. **Structured output** (if required) formats the result as JSON for downstream systems.

---

# Architect's Mental Model

Whenever you design an enterprise AI solution, ask yourself:

| Question | Answer |
|---|---|
| What are the rules? | **System Prompt** |
| What is the user asking? | **User Prompt** |
| What enterprise data do I need? | **Context** (RAG) |
| What should the AI never do? | **Guardrails** |
| What output format do downstream systems need? | **Structured Output** |

If you can answer those five questions, you are thinking like an AI Solutions Architect.

---

# Interview Questions & Answers

## Q1. What is a prompt, and why does it matter?

A prompt is the instruction given to an LLM. It is the single most important input you control — the model has no idea what you want until you tell it. In enterprise AI, prompts are templated, chained, and governed by guardrails to ensure consistency, safety, and compliance.

## Q2. Explain the three types of prompts and how they combine.

The three types are: **System Prompt** (defines the AI's role, rules, and guardrails — users never see it), **User Prompt** (what the user types), and **Context Prompt** (retrieved enterprise data from RAG). The LLM sees all three merged together: System + User + Context → LLM → Answer. The system prompt controls behavior, the user prompt expresses the request, and the context grounds the answer in real data.

## Q3. What is prompt chaining, and when should you use it?

Prompt chaining breaks a complex task into multiple sequential prompts, where each prompt builds on the previous one's output (e.g., Step 1: summarize case → Step 2: identify root cause → Step 3: recommend action → Step 4: draft email). Use it when one prompt is too complex, or when you need to validate each step independently. Chaining produces more reliable, testable, and maintainable results than one giant prompt.

## Q4. What are guardrails, and where must they be enforced?

Guardrails are safety rules that prevent the AI from doing things it shouldn't: exposing PII, inventing answers, providing legal/medical advice, approving refunds without human approval, generating offensive content. They are enforced via: system prompt rules, output filtering (scan and block prohibited content), tool call restrictions (require HITL for risky actions), and refusal/escalation logic. Guardrails are not optional in enterprise AI.

## Q5. Why do enterprise systems need structured outputs from LLMs?

LLMs naturally produce unstructured text (paragraphs), but downstream systems (Flows, APIs, databases) need structured data (JSON, fields) to integrate the output into workflows. Structured outputs are easier to test, monitor, debug, and parse. You enforce structure by instructing the model in the prompt, using function calling schemas, or parsing/validating the output.

## Q6. How would you design a prompt for an AI assistant that helps Salesforce support engineers?

System Prompt: define the role ("You are a Salesforce Support Engineer assistant"), rules ("Only answer from retrieved data, never assume"), and guardrails ("Don't expose PII, escalate if uncertain"). User Prompt: the engineer's question. Context Prompt: retrieve the relevant case, account, and Knowledge articles via RAG. Combine all three → LLM → validate guardrails → return structured output if needed. Use prompt templates for consistency and chaining for multi-step workflows.

## Q7. Give an example where prompt chaining produces better results than one prompt.

Task: "Why is my customer unhappy and what should I do?" Instead of one giant prompt, chain: Prompt 1 → Summarize the case. Prompt 2 → Identify root causes from the summary. Prompt 3 → Recommend actions based on root causes. Prompt 4 → Draft an email with the recommended actions. Each step focuses on one goal, builds on the previous output, and is easier to test and debug than asking the model to do everything at once.

---

# Self-Quiz (active recall — cover the answers first)

> Read the question, answer out loud or on paper, THEN expand to check. Retrieval beats re-reading.

<details>
<summary>1. What is a prompt?</summary>

An instruction given to an LLM. It is the single most important input you control — the model doesn't know what you want until you tell it.
</details>

<details>
<summary>2. Name the three types of prompts.</summary>

**System Prompt** (defines role/rules/guardrails, users don't see it), **User Prompt** (what the user types), **Context Prompt** (retrieved enterprise data from RAG).
</details>

<details>
<summary>3. How do system, user, and context prompts combine?</summary>

System + User + Context → LLM → Answer. The system prompt controls behavior, the user prompt asks the question, and the context grounds the answer in real data.
</details>

<details>
<summary>4. Why use prompt templates?</summary>

**Consistency** (every output follows the same structure), **reusability** (write once, use many times), and **maintainability** (update once, all outputs change). Templates use placeholders like `{{CaseNumber}}` that are filled at runtime.
</details>

<details>
<summary>5. What is prompt chaining?</summary>

Breaking a complex task into multiple sequential prompts, where each prompt builds on the previous one's output. Example: summarize → analyze → recommend → draft email.
</details>

<details>
<summary>6. When should you chain prompts instead of using one prompt?</summary>

When the task is too complex for one prompt, or when you need to validate/test each step independently. Chaining produces more reliable, structured results.
</details>

<details>
<summary>7. What are guardrails?</summary>

Safety rules that prevent the AI from doing things it shouldn't: exposing PII, inventing answers, providing legal/medical advice, approving refunds without human approval, generating offensive content.
</details>

<details>
<summary>8. How are guardrails enforced?</summary>

Via system prompt rules, output filtering (scan and block prohibited content), tool call restrictions (require HITL for risky actions), and refusal/escalation logic.
</details>

<details>
<summary>9. Why do enterprise systems need structured outputs?</summary>

Downstream systems (Flows, APIs, databases) cannot parse paragraphs — they need structured data (JSON, fields) to integrate the output into workflows.
</details>

<details>
<summary>10. How do you get structured output from an LLM?</summary>

Instruct the model in the prompt ("Return JSON with these fields"), use function calling schemas, or parse/validate the output.
</details>

<details>
<summary>11. Complete the phrase: "Vague prompt = ___"</summary>

Vague output. Specific prompt = specific output.
</details>

<details>
<summary>12. What are the five questions an AI Solutions Architect should answer when designing a solution?</summary>

1. What are the rules? (System Prompt)
2. What is the user asking? (User Prompt)
3. What enterprise data do I need? (Context/RAG)
4. What should the AI never do? (Guardrails)
5. What output format do downstream systems need? (Structured Output)
</details>

---

# Quick Revision (1-Minute Recap)

- ✅ Prompt → instruction to the LLM; vague prompt = vague output
- ✅ Three types → **System** (rules/role, users don't see), **User** (question), **Context** (RAG data)
- ✅ Combined flow → System + User + Context → LLM → Answer
- ✅ Templates → reusable prompts with placeholders (`{{CaseNumber}}`) for consistency
- ✅ Chaining → break complex tasks into sequential prompts (summarize → analyze → recommend → draft)
- ✅ Guardrails → safety rules (no PII, no hallucination, no unapproved refunds, escalate if uncertain)
- ✅ Structured outputs → downstream systems need JSON/fields, not paragraphs
- ✅ Architect's mental model → answer 5 questions: rules (system), user input (user), data (RAG), restrictions (guardrails), format (structured output)

---

# Next Topic

**Week 2 – Day 1: Embeddings & Vector Databases**

- What embeddings are (numbers that represent meaning)
- How similarity search works (cosine similarity, nearest neighbors)
- Vector databases (Pinecone, Weaviate, Milvus, FAISS)
- Indexing strategies (HNSW, IVF)
- When to use vector search vs. keyword search
- Leads into: RAG retrieval pipelines (Week 2 Day 2)
