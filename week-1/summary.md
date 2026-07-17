# Learning Week 1

Week 1 Learning Objective

The goal of Week 1 was to answer one question:

"How does Enterprise AI actually work?"

By the end of the week, you should be able to explain the complete flow of an enterprise AI solution from a user's question to the final response.

## Day 1 – Introduction to Enterprise AI

What you learned
What Enterprise AI is
Difference between consumer AI and enterprise AI
Why companies are investing heavily in AI
Role of an Enterprise AI Solutions Architect
Key takeaway

Enterprise AI is about solving business problems using AI while integrating with enterprise systems, not just building chatbots.

## Day 2 – LLMs, AI Agents & Human-in-the-Loop

What you learned
What an LLM is
Why an LLM is not a database
Difference between LLM and AI Agent
Salesforce as the source of truth
Human-in-the-Loop (HITL)
Architecture

User
   │
   ▼
AI Agent
   │
   ├── Salesforce
   ├── Knowledge Base
   └── LLM

Biggest lesson

LLM = Thinks
AI Agent = Thinks + Acts

Real-world example

You also discussed why AI should not automatically close customer support cases. Instead, it should recommend closure and let a human verify the outcome before taking the final action. This is a great example of Human-in-the-Loop in practice.

## Day 3 – Tool Calling & MCP

What you learned

    What tools are
    Tool calling
    API integrations
    MCP (Model Context Protocol)

Key idea
        The AI Agent doesn't contain enterprise data. It calls tools to retrieve or update information.

### MCP

You learned that MCP is not just about connecting to multiple servers.

Instead, MCP provides a standard protocol that lets AI models communicate with many enterprise tools in a consistent way.

Think of it as:

    USB-C for AI integrations.

Instead of creating a different connector for every tool, systems expose a common interface.

## Day 4 – Retrieval-Augmented Generation (RAG)

What you learned
    Why enterprises use RAG
    Why LLMs cannot answer company-specific questions on their own
    Difference between RAG and Fine-tuning
    Retrieving business records vs. knowledge documents

Enterprise flow
    User
    │
    ▼
    AI Agent
    │
    ▼
    Retrieve Information
    │
    ▼
    LLM
    │
    ▼
    Answer

Biggest lesson : Search first → Generate second

The LLM doesn't search enterprise systems. The AI Agent retrieves relevant records and documents, then provides that context to the LLM.

## Day 5 – Prompt Engineering

What you learned
    What prompts are
    System Prompt
    User Prompt
    Context
    Prompt Templates
    Prompt Chaining
    Guardrails
    Structured Outputs
    Salesforce Prompt Builder concepts

Enterprise prompt flow
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
    Response

Biggest lesson - A good prompt isn't just a question. It combines:

Clear instructions
Enterprise context
Safety rules
Expected output format
The Complete Enterprise AI Flow

By the end of Week 1, you can explain this complete architecture:

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
 Salesforce   Knowledge Base   APIs
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
      Human-in-the-Loop (if required)
                  │
                  ▼
           Final Business Action

This is the high-level architecture you'll see in many enterprise AI solutions, regardless of the vendor.

## Important Concepts 
Concept	                                                   What it means
---------------------------------------------------------------------------------------
LLM                                             	Reasons over and generates language.
AI Agent	                                        Plans tasks and uses tools to perform actions.
Tool Calling	                                    Lets the agent interact with enterprise systems.
MCP	                                                Standard protocol for connecting AI to tools and data sources.
RAG	                                                Retrieves trusted enterprise information before generation.
System Prompt	                                    Defines the AI's role and behavior.
User                                                Prompt	The user's request.
Context	                                            Retrieved business data and documents.
Guardrails	                                        Safety and compliance rules.
Structured Output	                                Machine-readable responses (often JSON).
Human-in-the-Loop	                                Human approval for sensitive business decisions.
Your Progress
