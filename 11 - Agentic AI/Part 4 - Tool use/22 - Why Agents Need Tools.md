

## Learning Objectives

By the end of this chapter, you will understand:

- Why AI Agents need tools
- The limitations of an LLM without tools
- How tools extend an Agent's capabilities
- Common tools used by AI Agents
- How a tool call actually works under the hood
- How to keep tool use safe and reliable

---

# Introduction

Imagine you ask an AI Agent:

```text
What's the weather in Mumbai right now?
```

Can an LLM answer this accurately?

No.

The LLM only knows information from its training data.

It doesn't know the current weather.

To answer this question,

the Agent must use a **Weather API**.

This is why AI Agents need **Tools**.

---

# What is a Tool?

A Tool is any external system that an AI Agent can use to perform a task.

A tool allows the Agent to interact with the real world.

Examples include:

- APIs
- Databases
- Search engines
- Calculators
- File systems
- Email services

Think of tools as the Agent's hands.

---

# Why Do Agents Need Tools?

An LLM can:

- Understand language
- Generate text
- Reason about problems

But it cannot:

- Check today's weather
- Search the internet
- Read your database
- Send an email
- Book a flight

To perform these actions,

it needs tools.

---

# Visual Diagram

```text
User Request

↓

AI Agent

↓

Needs External Information?

│

├── No

│     ↓

│    LLM

│

└── Yes

      ↓

     Tool

      ↓

   Get Result

      ↓

      LLM

      ↓

Final Response
```

The Agent decides when a tool is required.

---

# How a Tool Call Actually Works

Under the hood, a tool call is just structured data.

The LLM doesn't literally "press a button."

It generates a JSON object describing which tool to call and with what arguments.

The Agent framework reads that JSON, executes the real function, and feeds the result back to the LLM.

```text
LLM Output (structured)

↓

{
  "tool": "weather_api",
  "arguments": { "city": "Mumbai" }
}

↓

Agent Framework Executes Function

↓

Result Returned as Text/JSON

↓

LLM Reads Result

↓

Final Answer to User
```

This request-execute-respond cycle is often called the **tool calling loop**, and an Agent may run through it multiple times in a single conversation turn before producing a final answer.

---

# Tool Schemas (Function Definitions)

Before an LLM can call a tool, it needs to know the tool exists and what inputs it expects.

This is done through a **schema** — a structured description of the tool's name, purpose, and parameters.

```json
{
  "name": "get_weather",
  "description": "Get current weather for a given city",
  "parameters": {
    "type": "object",
    "properties": {
      "city": { "type": "string", "description": "City name" }
    },
    "required": ["city"]
  }
}
```

A clear `description` field matters a lot.

The LLM uses it to decide **when** and **how** to call the tool, not just that it exists.

Vague descriptions lead to the Agent picking the wrong tool or passing bad arguments.

---

# Example 1

User

```text
What's 245 × 789?
```

Instead of calculating manually,

the Agent can use:

```text
Calculator Tool
```

Result

```text
193,305
```

---

# Example 2

User

```text
Send an email to my manager.
```

The Agent uses:

```text
Email Tool
```

to send the message.

---

# Example 3

User

```text
Find the cheapest hotel in Goa.
```

The Agent may use:

```text
Travel API

↓

Hotel Search API

↓

Maps API
```

Multiple tools work together to complete the task.

---

# Common Types of Tools

AI Agents commonly use:

### APIs

Retrieve live information.

Examples:

- Weather
- Flights
- Stock prices

---

### Databases

Read or update structured data.

Examples:

- Employee records
- Customer information
- Product inventory

---

### Web Search

Find information that isn't part of the LLM's training data.

---

### Calculators

Perform accurate mathematical calculations.

---

### File Systems

Read and write files.

Examples:

- PDFs
- Excel files
- Images
- Text documents

---

### Communication Tools

Interact with other systems.

Examples:

- Email
- Slack
- Microsoft Teams

---

### Code Execution Tools

Run code in a sandboxed environment.

Useful for:

- Data analysis
- Chart generation
- Testing logic before responding

---

### Retrieval Tools (RAG)

Fetch relevant chunks of information from a private knowledge base or vector database.

Useful when answers must be grounded in a company's own documents rather than public web content.

---

# Python Example 🐍

A simple tool call:

```python
weather = weather_api.get_weather("Mumbai")

print(weather)
```

The LLM doesn't fetch the weather itself.

The tool does.

---

# LLM vs Tool

|LLM|Tool|
|---|---|
|Thinks|Acts|
|Generates text|Performs actions|
|Uses training knowledge|Uses live data|
|Cannot change the real world|Can interact with external systems|

Together,

they form a complete AI Agent.

---

# Real-World Example

Imagine an AI HR Assistant.

User asks:

```text
How many leave days do I have left?
```

The Agent cannot guess.

Instead,

it works like this.

```text
User

↓

Agent

↓

HR Database

↓

Retrieve Leave Balance

↓

LLM

↓

Generate Response
```

The database provides the facts.

The LLM explains them.

---

# Error Handling & Retries

Tools fail. APIs time out, databases go down, arguments are malformed.

A well-designed Agent doesn't just crash — it handles failure gracefully.

```text
Tool Call

↓

Success? 

├── Yes → Continue

└── No  → Retry / Fallback / Ask User for Clarification
```

Common strategies:

- **Retry with backoff** for transient network errors
- **Fallback tools** (e.g. switch to a secondary weather provider)
- **Graceful degradation** — tell the user the tool failed instead of hallucinating a result
- **Timeouts** so one slow tool doesn't freeze the whole Agent

---

# Security Considerations

Tools give an LLM real-world power, so they also introduce real-world risk.

Keep these in mind:

- **Least privilege** — only grant the permissions a tool actually needs (read-only where possible)
- **Input validation** — sanitize arguments before executing, especially for database or file system tools
- **Human-in-the-loop approval** for high-risk actions like sending money, deleting data, or sending emails
- **Sandboxing** for code execution tools, so untrusted code can't affect the host system
- **Audit logging** — record every tool call and result for traceability

---

# Industry Insight ⭐

Modern AI Agents rarely rely on the LLM alone.

Instead,

they combine reasoning with external tools.

Examples include:

- OpenAI Agents SDK
- LangGraph
- Google ADK
- CrewAI

These frameworks allow Agents to call tools whenever additional information or actions are required.

A related, increasingly popular standard is the **Model Context Protocol (MCP)**, which defines a common way for Agents to discover and call tools across different servers and providers — instead of building a custom integration for every tool.

---

# Best Practices

Use tools only when necessary.

Choose the simplest tool that solves the problem.

Always validate tool results before using them.

Keep tools focused on one responsibility.

Write clear, specific tool descriptions — the LLM relies on them to choose correctly.

Log every tool call for debugging and auditing.

Set timeouts and fallbacks so a single failing tool doesn't break the whole Agent.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the LLM can do everything.

LLMs reason well,

but they cannot access live information or perform real-world actions without tools.

---

### Mistake 2

Using tools for tasks the LLM can answer directly.

This increases cost and latency.

---

### Mistake 3

Trusting every tool response.

Tool outputs should always be validated before being used.

---

### Mistake 4

Giving a tool vague names or descriptions.

If the LLM can't tell what a tool does, it will misuse it or ignore it.

---

### Mistake 5

Granting a tool more access than the task requires.

A tool that only needs to _read_ data should never be given _write_ or _delete_ permissions.

---

# Interview Tip ⭐

A common interview question is:

> **Why do AI Agents need tools?**

A good answer is:

AI Agents use tools to access live information and perform real-world actions that an LLM cannot do on its own, such as querying databases, calling APIs, sending emails, or searching the web.

A strong follow-up point: mention that tool **schemas** guide the LLM's decision of when and how to call a tool, and that error handling and permission scoping are what make tool use production-ready rather than a demo.

---

# Where is this Used?

- OpenAI Agents SDK
- LangGraph
- LangChain
- Google ADK
- CrewAI
- AutoGen
- Model Context Protocol (MCP) servers
- Enterprise AI Applications

---

# Key Takeaways

- Tools extend the capabilities of an AI Agent.
- LLMs reason, while tools perform actions.
- Tools provide access to live data and external systems.
- Tool schemas tell the LLM what a tool does and what inputs it needs.
- Robust error handling and least-privilege security make tool use production-ready.
- Modern AI Agents rely heavily on tools.
- Together, the LLM and tools create intelligent, action-oriented systems.

---

