# Day 2: Understanding LLMs & Enterprise AI Architecture

Main Takeaway

"An LLM is not a database. It is a reasoning and language generation engine."

This was the biggest lesson from Day 2.

Many people think ChatGPT stores company data and simply retrieves it. In reality:

CRM (Salesforce) stores business data.
Databases store structured information.
Knowledge Bases store documents.
LLMs understand context, reason, and generate responses.
AI Agents orchestrate tasks using these systems.
How Enterprise AI Works

Instead of thinking:

User → ChatGPT → Answer

Think:

User
   │
   ▼
AI Agent
   │
   ├── Salesforce
   ├── Data Cloud
   ├── Knowledge Base
   ├── ERP
   ├── APIs
   └── LLM

The AI Agent decides which tools to use, while the LLM generates and reasons over the final response.

Division of Responsibilities
Salesforce
Source of truth
Stores Accounts
Stores Opportunities
Stores Cases
Stores Contacts
AI Agent

Responsible for:

Calling APIs
Retrieving Salesforce records
Searching knowledge articles
Executing workflows
Making decisions
Orchestrating multiple systems
LLM

Responsible for:

Summarization
Drafting emails
Explaining information
Reasoning over retrieved context
Natural language conversations
Real Enterprise Example

A customer asks:

"What's happening with my support case?"

The AI flow is:

Customer
     │
     ▼
Agent
     │
     ▼
Salesforce Case
     │
     ▼
Knowledge Base
     │
     ▼
LLM
     │
     ▼
Natural-language response

The LLM does not know the case status on its own—it only works with the information retrieved by the agent.

Human in the Loop (HITL)

One of the most important enterprise concepts you learned was that AI should not automatically make critical business decisions.

Examples where human approval is essential:

Sending customer emails
Closing support cases
Approving refunds
Legal responses
Financial approvals
Executive reports

A key discussion point was that an AI agent should not automatically close customer cases. Even if internal checks suggest the issue is resolved, it's better to present the information to a human for review or confirmation before closing. This helps maintain customer trust and prevents premature case closure.

Enterprise AI Architecture Mindset

As an Enterprise AI Solutions Architect, think in terms of:

Where does the data live?
Who owns the data?
Which system performs the action?
When should AI be involved?
When should a human approve the outcome?

This is the architectural thinking companies expect.

Key Concepts Learned
LLM ≠ Database
AI Agent = Orchestrator
Salesforce = Source of Truth
APIs connect enterprise systems
AI uses retrieved data to generate responses
Human-in-the-Loop is critical for enterprise trust and governance

## Interview Questions to Practice

What is the difference between an LLM and a database?
Why shouldn't an LLM directly access enterprise data?
What is an AI Agent?
Explain Human-in-the-Loop with a real-world example.
Why is Salesforce considered the source of truth in an enterprise architecture?

### One-Sentence Summary

Enterprise AI is not about replacing enterprise systems with an LLM—it's about using AI Agents to orchestrate trusted enterprise data and applying LLMs for reasoning and natural language, while keeping humans in control of important business decisions.