# Module 1 - What is Enterprise AI?

## 1. What is Artificial Intelligence (AI)?

Artificial Intelligence is a broad field focused on creating systems that can perform tasks that normally require human intelligence.

Examples include:

Understanding language
Recognizing images
Making recommendations
Planning tasks
Answering questions
Driving cars

Think of AI as the umbrella that covers many technologie

## 2. What is Machine Learning (ML)?

Machine Learning is a subset of AI.

Instead of writing rules manually, we let computers learn patterns from data.

Traditional Programming

    Rules + Data
            ↓
        Output


Example:

    IF amount > ₹50,000
    AND country != India

    THEN mark transaction as suspicious

Machine Learning

    Historical Data
            ↓
    ML Model learns patterns
            ↓
    Predicts future results

Example:
You provide 1 million historical banking transactions labeled as "Fraud" or "Not Fraud." The model learns common fraud patterns and predicts whether new transactions are likely to be fraudulent.


## 3. What is Deep Learning?

Deep Learning is a type of Machine Learning that uses neural networks with many layers.

It's especially good for:

Images
Voice
Video
Natural language

This is the technology behind models like GPT.

## 4. What is a Large Language Model (LLM)?

This is the technology powering ChatGPT.

Instead of storing answers like a database, an LLM predicts the most likely next token (a piece of text) based on everything it has seen during training.

When you ask:

"Write a professional email."

The model predicts one token after another until it produces a complete response.

Popular LLMs include:

GPT (OpenAI)
Claude (Anthropic)
Gemini (Google)
Llama (Meta)
Key point

An LLM doesn't "know" facts the way a database does. It generates text by recognizing patterns learned from enormous amounts of training data.

## 5. Why is everyone talking about Generative AI?

Traditional AI classifies or predicts.

Examples:

Is this email spam?
Will this customer churn?

Generative AI creates new content.

Examples:

Writes emails
Generates code
Creates presentations
Summarizes documents
Produces images
Answers questions

This shift from analyzing data to creating useful outputs is why generative AI has become so transformative.

## 6. What is Enterprise AI?

Most companies don't just want a chatbot. They want AI that can securely work with their internal systems.

Imagine a support manager asking:

"Show me all high-priority customer cases that haven't been updated in three days and draft an escalation email."

The AI must:

Understand the request.
Query Salesforce.
Analyze the results.
Draft the email.
Follow the company's security and approval rules.

That's Enterprise AI.

## 7. Consumer AI vs Enterprise AI
Consumer AI	                                        Enterprise AI
Answers general questions	                    Solves business problems
Uses public information	                        Uses company data
One user at a time	                            Supports thousands of employees
Simple conversations	                        Integrates with business systems
Few security controls	                        Strong security, governance, and compliance

Examples:

    - Consumer AI : "Write me a birthday message."

    - Enterprise AI : "Analyze last quarter's sales pipeline and identify the top 10 opportunities at risk, then recommend next actions."

## 8. Typical Enterprise AI Architecture

Here's a simplified view:

Employee
    │
    ▼
Enterprise Application
(Salesforce, Web Portal, Teams)
    │
    ▼
AI Agent
    │
    ▼
Large Language Model
(GPT, Claude, Gemini)
    │
    ▼
Enterprise Knowledge
(Salesforce, SAP, Snowflake, SharePoint)
    │
    ▼
Business Response

Example

A sales manager asks:

    "Which opportunities are likely to close this month?"

    The system might:

    Understand the question.
    Retrieve opportunity data from Salesforce.
    Analyze sales history.
    Generate a summary.
    Suggest next actions.
    Display the results in Salesforce.

The user experiences one seamless interaction, even though several components work together behind the scenes.

### Recap questions

#### How is AI different from Machine Learning?
    AI is the broader discipline of building systems that perform tasks requiring human-like intelligence. Machine Learning is one technique within AI where systems learn patterns from historical data instead of relying on explicitly programmed rules.

#### Why is an LLM different from a traditional database?
An LLM generates responses by predicting the next token based on patterns learned during training. A database, on the other hand, stores and retrieves exact facts. If a fact isn't in the database, it can't return it. If an LLM wasn't trained on something or lacks current information, it may generate an incorrect answer (hallucinate).

#### Why do enterprises connect AI with Salesforce or SAP?
Enterprise data resides in systems like Salesforce, SAP, and Snowflake. AI becomes valuable when it can securely access that business data, identify insights, recommend actions, and automate repetitive work while respecting permissions and company policies.

#### What role does an AI Agent play?

AI Agent is an employee, it can:

    Understand a goal.
    Decide what steps are needed.
    Use tools (Salesforce, email, databases, APIs).
    Gather information.
    Make decisions within defined boundaries.
    Complete the task.

#### If VP of Sales asks: "I already have ChatGPT. Why should I spend millions on Enterprise AI?"

ChatGPT is a powerful language model, but by itself it doesn't know your company's customers, sales pipeline, support cases, or internal policies. Enterprise AI combines an LLM with your business systems—such as Salesforce, SAP, and SharePoint—so employees can securely retrieve company data, automate workflows, and receive recommendations tailored to your business. The value isn't just generating text; it's helping employees make faster, more informed decisions and complete work with less manual effort.

#### analogy 

Imagine you're building a Formula 1 car.

The engine = GPT, Claude, Gemini (the LLM)
The steering = AI Agent
The driver = Employee
The race strategy = Business rules
The fuel = Company data
The entire Formula 1 car = Enterprise AI

#### Framework : "What AI solution would you build?"

Don't start with:

❌ AI

❌ GPT

❌ Data model

❌ Agents

Start with:

Step 1: Pain

What's the biggest business problem?

Step 2: Cost

How much time or money does it waste?

Step 3: AI Opportunity

Where can AI remove friction or improve decisions?

Step 4: Technology

Now decide whether you need RAG, Agents, MCP, or another pattern.

Notice the order.

Business first. Technology second.

#### Enterprise AI architects work:

Understand the business problem.
Design the solution.
Choose the right AI pattern (RAG, Agent, Workflow, etc.).
Select the technology stack.
Measure the business impact.

This approach is what distinguishes architects from developers.

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


### Layer 6: The LLM

This is where GPT, Claude, Gemini, or another model comes in.

The LLM is responsible for:

Understanding natural language.
Summarizing information.
Drafting responses.
Explaining complex topics.
Generating code.
Reasoning over the information it's given.

The LLM is not the entire application—it's one component.

## Putting It All Together

Imagine this request:

"Summarize the Acme account."

Here's what happens:

Step 1

The user clicks Summarize in Salesforce.

Step 2

The AI Agent receives the request.

Step 3

The Agent retrieves:

Account details
Open Opportunities
Recent Cases
Activities
Knowledge Articles
Step 4

The Agent sends that information to the LLM.

Step 5

The LLM generates a concise summary.

Step 6

The summary is displayed back in Salesforce.

Notice something important:

The LLM never connected directly to Salesforce. The Agent handled the retrieval and orchestration.


## Architect's Checklist

Whenever you're designing an AI solution, ask yourself:

Who is the user?
What business problem are they trying to solve?
What enterprise data is needed?
What systems must the AI connect to?
What actions should the AI perform?
Where does the LLM add value?
What security and governance controls are required?

## Why shouldn't we simply give GPT direct access to our Salesforce database?
I wouldn't give GPT direct access to Salesforce because enterprise systems require strong security, governance, and access control. The AI should only retrieve data the current user is authorized to access, and only the data needed for the task. Business logic such as filtering, permissions, and transactions should remain in Salesforce, while the LLM focuses on reasoning and language generation. This approach improves security, reduces cost, minimizes hallucinations by grounding responses in enterprise data, and keeps the solution scalable and auditable."

