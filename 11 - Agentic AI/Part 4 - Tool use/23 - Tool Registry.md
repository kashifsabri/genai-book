

## Learning Objectives

By the end of this chapter, you will understand:

- What a Tool Registry is
- Why every AI Agent needs a Tool Registry
- How an Agent discovers available tools
- What information a Tool Registry stores
- How Tool Registries work in production AI systems
- How registries scale when there are hundreds of tools
- How dynamic and remote tool registries work (e.g. MCP)

---

# Introduction

Imagine an AI Agent has access to these tools:

- Weather API
- Calculator
- Email Service
- Database
- Web Search

Now the user asks:

```text
What's the weather in Mumbai?
```

How does the Agent know that a **Weather API** exists?

How does it know **not** to use the Calculator?

It first checks something called the **Tool Registry**.

---

# What is a Tool Registry?

A Tool Registry is a central catalog of all the tools available to an AI Agent.

It tells the Agent:

- Which tools exist
- What each tool does
- What inputs each tool accepts
- What outputs each tool returns

Think of it as the Agent's toolbox.

---

# Visual Diagram

```text
User Request

↓

AI Agent

↓

Tool Registry

↓

Available Tools

↓

Select Best Tool

↓

Execute Tool
```

The Agent cannot use a tool until it knows the tool exists.

---

# Why Do Agents Need a Tool Registry?

Imagine having a toolbox with 100 tools,

but none of them are labeled.

You wouldn't know:

- Which tool to use
- What each tool does

A Tool Registry solves this problem.

It organizes every available tool.

---

# What Information Does a Tool Registry Store?

Each tool usually contains information like:

```text
Tool Name

Description

Input Parameters

Output Format

Permissions

Availability
```

This information helps the Agent choose the correct tool.

In production systems, a registry entry often stores a few more fields too:

```text
Version

Owner / Team

Tags / Category

Authentication Requirements

Rate Limits

Cost per Call
```

These extra fields don't change how the Agent picks a tool,

but they matter a lot for maintaining the registry at scale.

---

# Example

Suppose the registry contains:

```text
Weather Tool

Purpose:

Get current weather.

Input:

City Name

Output:

Temperature and Forecast
```

When the user asks:

```text
What's the weather in Delhi?
```

The Agent immediately knows which tool to use.

---

# Another Example

The Tool Registry may contain:

```text
Calculator

↓

Mathematical Calculations

--------------------

Email Tool

↓

Send Emails

--------------------

Database Tool

↓

Query Database
```

Each tool has a clearly defined responsibility.

---

# Python Example 🐍

A simple Tool Registry:

```python
tools = {
    "weather": "Get weather",
    "calculator": "Perform calculations",
    "email": "Send emails"
}

print(tools.keys())
```

Production frameworks use much richer tool definitions,

but the concept is the same.

---

# How Does an Agent Use the Tool Registry?

The process is simple.

```text
User Request

↓

Read Tool Registry

↓

Find Matching Tool

↓

Select Tool

↓

Execute Tool

↓

Return Result
```

The Tool Registry is consulted **before** tool selection.

---

# Static vs Dynamic Registries

Not every registry looks the same.

**Static Registry** — the list of tools is fixed at startup, hardcoded into the Agent's configuration. Simple, predictable, easy to debug.

**Dynamic Registry** — tools can be added, removed, or updated while the Agent is running, often by querying a remote server.

```text
Agent Starts

↓

Query Remote Tool Server

↓

Fetch Current Tool List + Schemas

↓

Register Tools for This Session
```

Dynamic registries are common in enterprise settings,

where new internal tools are added constantly

and Agents shouldn't need a redeploy to use them.

---

# Registries at Scale: Too Many Tools

A small Agent might have 5 tools. A large enterprise Agent might have 500.

Passing all 500 tool schemas to the LLM on every request is wasteful and can hurt selection accuracy — the model has to read through irrelevant tools every time.

Common solutions:

```text
User Request

↓

Semantic Search over Tool Registry

↓

Shortlist Top-K Relevant Tools

↓

Pass Only Shortlisted Tools to LLM

↓

LLM Selects Final Tool
```

Other scaling techniques:

- **Namespacing** — group tools by domain (`hr.get_leave_balance`, `finance.get_invoice`)
- **Tagging/categorization** — filter tools by category before selection
- **Lazy loading** — only load a tool's full schema when it's actually likely to be used

---

# Real-World Example

Imagine an AI HR Assistant.

Available tools:

```text
Employee Database

Leave Management System

Payroll System

Email Service
```

User asks:

```text
How many leave days do I have left?
```

The Agent checks the Tool Registry,

finds the **Leave Management System**,

and ignores the other tools.

---

# Registries and the Model Context Protocol (MCP)

A growing standard for tool registries is the **Model Context Protocol (MCP)**.

Instead of hardcoding tool definitions inside the Agent, an MCP server exposes a list of tools that any compatible Agent can discover at runtime.

```text
Agent

↓

Connect to MCP Server

↓

list_tools()

↓

Receive Tool Schemas

↓

Register Tools Dynamically
```

This means the same tool (say, a Slack or GitHub integration) can be built once as an MCP server and reused across many different Agents and frameworks, instead of being reimplemented for each one.

---

# Industry Insight ⭐

Every major Agent framework has a Tool Registry.

Examples include:

- LangChain Tool Registry
- LangGraph Registered Tools
- OpenAI Agents SDK Tools
- Google ADK Tool Registry
- CrewAI Tool Registry
- MCP Servers (protocol-level, framework-agnostic)

Although the implementation differs,

the idea is always the same:

Maintain a central list of available tools.

---

# Best Practices

Keep each tool focused on a single responsibility.

Write clear descriptions for every tool.

Remove unused or duplicate tools.

Version tools so updates don't silently break existing Agents.

Monitor tool availability/health so the Agent doesn't select a tool that's currently down.

A well-organized Tool Registry improves tool selection accuracy.

---

# Common Beginner Mistakes

### Mistake 1

Creating one tool that does everything.

Small, specialized tools are easier for the Agent to understand.

---

### Mistake 2

Giving tools vague descriptions.

The Agent relies on tool descriptions to decide when to use them.

---

### Mistake 3

Registering duplicate tools.

Multiple tools with the same purpose can confuse the Agent.

---

### Mistake 4

Dumping every registered tool into the LLM's context on every request.

At small scale this is fine, but it doesn't scale — use shortlisting or namespacing once the registry grows large.

---

### Mistake 5

Never versioning tools.

Silently changing a tool's inputs or outputs can break Agents that were built against the old schema.

---

# Interview Tip ⭐

A common interview question is:

> **What is a Tool Registry in an AI Agent?**

A good answer is:

A Tool Registry is a central catalog of all the tools available to an AI Agent. It stores information about each tool, allowing the Agent to discover, understand, and select the most appropriate tool for a given task.

A strong follow-up point: mention that registries can be **static or dynamic**, and that at scale, Agents often use **semantic shortlisting** rather than exposing every tool to the LLM at once — and that **MCP** is emerging as a standard way to expose tool registries across frameworks.

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

- A Tool Registry is a catalog of available tools.
- It helps the Agent discover and understand tools.
- Each tool includes metadata such as its purpose, inputs, and outputs.
- Registries can be static (fixed at startup) or dynamic (updated at runtime).
- At scale, Agents shortlist relevant tools instead of exposing the entire registry every time.
- MCP is a framework-agnostic standard for exposing tool registries.
- Production AI Agents rely on Tool Registries for efficient tool management.
- Good Tool Registries make tool selection faster and more accurate.

---

