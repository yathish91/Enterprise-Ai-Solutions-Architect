# Day 3:  AI Agents, MCP, and Enterprise Tool Calling

## LLM vs AI Agent

### LLM : 

An LLM can:

Understand language
Answer questions
Summarize
Generate code
Explain concepts

But it cannot directly:

Query Salesforce
Update an Opportunity
Create a Case
Send an email
Access your company's internal systems

Think of an LLM as the brain, not the hands.

### AI Agent : 

An AI Agent has both a brain and hands.

It can:

Think
Decide
Use tools
Call APIs
Search knowledge
Execute workflows
Ask follow-up questions
Perform multi-step tasks

Think of it as a skilled employee who knows when to ask, when to act, and when to wait for approval.

### Example

A sales manager asks:

"Show me all opportunities closing this month worth over ₹1 crore, summarize the risks, and email my team."

An LLM alone can't do this.

An AI Agent can:

Understand the request.
Query Salesforce.
Filter the opportunities.
Summarize each one.
Draft the email.
Wait for approval (Human-in-the-Loop).
Send the email after approval.

##  What is MCP (Model Context Protocol)?

MCP is an open protocol that standardizes how AI models communicate with external tools and data sources.

Think of it as a universal adapter.

Instead of writing custom integrations for every model and every system:

One system exposes an MCP server.
AI clients that support MCP can connect using the same protocol.

This reduces integration effort and makes enterprise AI systems more interoperable.

Without MCP
LLM
 ├── Custom Salesforce connector
 ├── Custom SAP connector
 ├── Custom Jira connector
 ├── Custom SQL connector
 └── Custom SharePoint connector

Every new system needs another custom integration.

With MCP
LLM
   │
   ▼
MCP Client
   │
   ▼
MCP Server
   ├── Salesforce
   ├── SAP
   ├── Jira
   ├── SQL
   └── SharePoint

The AI uses a common protocol instead of bespoke integrations.

## Day 3 Key Takeaways

An LLM reasons and generates language.
An AI Agent reasons and takes actions using tools.
Tool calling gives AI access to enterprise systems and live data.
MCP provides a standardized way for AI models to connect to tools and data sources.
Enterprise AI is about secure orchestration, not replacing business systems.

## How an Enterprise AI Architect Thinks

When you describe a solution, always ask:

Who understands the request? → LLM
Who decides what actions to take? → AI Agent
Who owns the business data? → Salesforce
Who executes the action? → Salesforce API/Tool
Who generates natural language? → LLM
Who performs external actions like sending emails? → Email Tool
Does this action require Human-in-the-Loop? → Depends on the business risk