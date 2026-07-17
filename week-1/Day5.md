# Day 5 - Prompt Engineering

## What is a Prompt?

A prompt is simply the instruction given to an LLM.

Example:

Summarize this customer case.

Better prompt:

Summarize this customer case in 5 bullet points.

Include:
- Customer issue
- Root cause
- Current status
- Next action
- Owner

## Types of Prompts

There are three important ones in enterprise AI.

### 1. System Prompt

This is the most important.

It tells the AI:

Who it is
What its role is
What it should never do

Example:

You are a Salesforce Support Engineer.

Only answer using retrieved Salesforce data.

Never make assumptions.

If data is unavailable, say you don't know.

Users never see this prompt.

It controls the AI's behavior.

### 2. User Prompt

This is what the user types.

Example:

Why was my opportunity lost?

### 3. Context Prompt

This comes from RAG.

Example:

Opportunity:

Stage:
Negotiation

Amount:
₹50 Lakhs

Reason Lost:
Competitor offered lower pricing.

Last Activity:
45 days ago.

The AI combines:

System Prompt + User Prompt + Context

to generate the final answer.

## Enterprise Flow

System Prompt
       │
User Prompt
       │
Retrieved Context
       │
       ▼
      LLM
       │
       ▼
    Answer

## Prompt Templates

Instead of writing prompts every time, enterprises create reusable templates.

Example:

Summarize this Case.

Case Number:
{{CaseNumber}}

Priority:
{{Priority}}

Status:
{{Status}}

Owner:
{{Owner}}

The placeholders are filled automatically.


## Prompt Chaining

Some tasks are too complex for one prompt.

Instead of one huge prompt, break it into steps.

Example:

Customer asks:

Why is my customer unhappy?

Instead of:

One giant prompt.

Use multiple prompts.

Prompt 1

Summarize the case.

↓

Prompt 2

Identify root causes.

↓

Prompt 3

Recommend actions.

↓

Prompt 4

Draft email.

Each prompt builds on the previous one.

This often produces more reliable results.

## Guardrails

One of the most important enterprise concepts.

Guardrails prevent AI from doing things it shouldn't.

Examples:

Don't reveal confidential information.

Don't generate offensive language.

Don't provide legal advice.

Don't approve refunds.

Don't expose customer PII.

Don't invent answers.

If unsure:

Escalate to a human.

## Real Enterprise Example

Customer asks:

"Summarize my last support interaction."

What happens?

User sends the request.
Agent retrieves the case from Salesforce.
RAG retrieves relevant knowledge articles (if needed).
System prompt defines the AI's role and restrictions.
User prompt contains the request.
Context includes the retrieved case details.
LLM generates the summary.
Guardrails ensure no sensitive information is exposed.

## Day 5 Key Takeaways

A prompt is an instruction to the LLM.
System prompts define the AI's behavior.
User prompts express the user's request.
Context prompts provide retrieved enterprise data.
Prompt templates improve consistency.
Prompt chaining breaks complex tasks into manageable steps.
Guardrails enforce safety, compliance, and business rules.
Structured outputs make AI responses usable by other systems.
Salesforce Prompt Builder applies these concepts within the Salesforce platform.


## Architect's Mental Model

Whenever you're designing an enterprise AI solution, ask yourself:

What are the rules? → System Prompt
What is the user asking? → User Prompt
What enterprise data do I need? → Context (RAG)
What should the AI never do? → Guardrails
What output format do downstream systems need? → Structured Output

If you answer those five questions, you're already thinking like an AI Solutions Architect.