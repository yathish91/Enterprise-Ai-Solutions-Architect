# Module 2 - Understanding the Enterprise AI Stack

## Learning Objective

    By the end of this module, you should be able to answer:
    "How does a user's question become an AI-generated answer in an enterprise application?"


## The Big Picture


                    User
                     │
                     ▼
         Enterprise Application
      (Salesforce, Teams, Web App)
                     │
                     ▼
             AI Agent / Orchestrator
                     │
     ┌───────────────┼────────────────┐
     ▼               ▼                ▼
Enterprise Data      Tools           LLM
(Salesforce, SAP)   (Email, APIs)   (GPT, Claude)
     │                                │
     └───────────────┬────────────────┘
                     ▼
              Final Response

### Layer 1: The User

The user isn't asking for AI—they're trying to get work done.

Examples:

"Summarize this account."
"Which opportunities are at risk?"
"Draft a customer email."
"Find similar support cases."

The AI should fit naturally into their workflow.

Architect's question: How can AI reduce the number of clicks or the time needed to complete this task?

### Layer 2: Enterprise Application

This is where users interact with AI.

Examples:

Salesforce
Microsoft Teams
Slack
ServiceNow
A custom web application

Since you're a Salesforce Architect, imagine an "Ask AI" button on an Opportunity page. The user shouldn't need to switch to another application.

### Layer 3: AI Agent (The Brain of the Workflow)

This is one of the most important layers.

Many people think GPT is the brain.

In enterprise AI, the Agent is the brain of the workflow.

The Agent decides:

What information is needed?
Which systems should I query?
Should I call Salesforce?
Should I search SharePoint?
Should I send an email?
Should I ask the LLM to summarize?

Think of the Agent as a project manager coordinating different specialists.

### Layer 4: Enterprise Data

This is where the real business value comes from.

Examples:

Salesforce CRM
SAP
Snowflake
SharePoint
Confluence
SQL databases

Without access to enterprise data, the AI can only provide generic answers.

Think of enterprise data as the organization's memory.

### Layer 5: Tools

Sometimes the AI needs to do more than read data.

It may need to:

Create a Salesforce Case.
Update an Opportunity.
Send an email.
Create a Jira ticket.
Schedule a meeting.
Trigger a Flow.

These actions are performed through tools, APIs, or workflows.