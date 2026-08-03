

## Learning Objectives

By the end of this chapter, you will understand:

- What an LLM is
- What an AI Workflow is
- What an AI Agent is
- The differences between them
- When to use each approach
- The cost, speed, and reliability tradeoffs between them
- How to quickly decide which one you need

---

# Introduction

Suppose you ask an AI:

```text
Translate this sentence into French.
```

The AI immediately gives you the answer.

Now imagine you ask:

```text
Summarize this PDF.

Translate it into French.

Email it to my manager.
```

This is no longer a single task.

It involves multiple steps.

There are three different ways to solve problems like this:

- LLM
- Workflow
- AI Agent

Understanding the difference is one of the most common interview topics.

---

# What is an LLM?

An LLM (Large Language Model) receives a prompt and generates a response.

It does not make decisions.

It simply answers the question.

### Visual Flow

```text
Question

↓

LLM

↓

Answer
```

### Example

```text
User

↓

What is Java?

↓

LLM

↓

Java is a programming language.
```

The interaction ends after the response.

---

# What is a Workflow?

A Workflow is a predefined sequence of steps.

The order is fixed.

The system follows the same path every time.

### Visual Flow

```text
Input

↓

Step 1

↓

Step 2

↓

Step 3

↓

Result
```

### Example

Suppose you upload a resume.

The application always performs:

```text
Upload Resume

↓

Extract Text

↓

Summarize

↓

Send Email
```

The workflow never changes.

---

# What is an AI Agent?

An AI Agent starts with a goal,

then decides what to do next.

Instead of following fixed steps,

it chooses the best action based on the current situation.

### Visual Flow

```text
Goal

↓

Think

↓

Choose Action

↓

Use Tool

↓

Observe

↓

Goal Completed
```

The Agent can change its plan while working.

---

# The Middle Case: LLM With a Single Tool Call

A common source of confusion is a system that calls one tool and stops.

```text
Question

↓

LLM decides to call a tool

↓

Tool runs once

↓

LLM uses the result to answer

↓

Done
```

This is **not** a full Agent — there's no loop, no re-planning, and no
multi-step decision making. It's closer to a Workflow with one flexible
step. True Agents keep looping (Think → Act → Observe) until the goal is
verified complete, potentially using several different tools along the way.

---

# LLM vs Workflow vs Agent

| LLM | Workflow | Agent |
|------|----------|-------|
| Answers questions | Follows fixed steps | Makes decisions |
| No planning | Predefined process | Dynamic planning |
| No tool selection | Fixed tools | Chooses tools |
| Single response | Same path every time | Adapts to the situation |

---

# Cost, Speed, and Reliability Tradeoffs

Choosing between the three isn't just about capability — it's also an
engineering tradeoff.

| | LLM | Workflow | Agent |
|---|---|---|---|
| Latency | Fastest | Fast, predictable | Slowest (multiple LLM calls) |
| Cost per request | Lowest | Low, predictable | Higher, variable |
| Predictability | High | Highest | Lower (path can change) |
| Debuggability | Easy | Easy | Harder (dynamic decisions) |
| Flexibility | None | None | Highest |

Agents are the most capable option, but they're also the most expensive and
hardest to debug. This is why the next section's checklist matters — reach
for an Agent only when the task genuinely needs it.

---

# Real-World Example

Imagine you're building an AI Travel Assistant.

### Using an LLM

```text
Suggest places to visit in Goa.
```

The LLM gives suggestions.

Done.

---

### Using a Workflow

```text
Search Flights

↓

Search Hotels

↓

Generate Itinerary
```

Every user follows the same steps.

---

### Using an AI Agent

```text
User Goal

↓

Think

↓

Check Weather

↓

Compare Flights

↓

Find Hotels

↓

Adjust Plan

↓

Create Itinerary
```

The Agent decides what to do based on the information it finds.

---

# When Should You Use Each?

### Use an LLM when:

- You need a single answer.
- No tools are required.
- The task is simple.

---

### Use a Workflow when:

- The steps are always the same.
- The process is predictable.
- No decision making is needed.

---

### Use an AI Agent when:

- The task is complex.
- Multiple decisions are required.
- Tool usage is dynamic.
- The plan may change during execution.

---

# Quick Decision Checklist

Before building an Agent, ask:

```text
Can the task be solved with one prompt?
        → Yes → Use an LLM

Are the steps always the same, every time?
        → Yes → Use a Workflow

Does the number or order of steps depend on
what happens along the way?
        → Yes → Use an Agent
```

Most production mistakes come from skipping straight to "Agent" without
asking these questions first.

---

# Industry Insight ⭐

Many companies don't replace everything with AI Agents.

Instead, they combine all three.

```text
Simple Question

↓

LLM

----------------

Business Process

↓

Workflow

----------------

Complex Task

↓

AI Agent
```

Choosing the right approach is an important engineering decision.

---

# Best Practices

Don't build an AI Agent for every problem.

If a simple LLM or Workflow solves the task,

use that instead.

Agents should be used only when decision making is required.

---

Weigh cost and latency, not just capability.

An Agent that works but costs 10x more and responds 5x slower may not be
worth it if a Workflow would do.

---

# Common Beginner Mistakes

### Mistake 1

Thinking every AI application needs an Agent.

Many applications only need an LLM.

---

### Mistake 2

Confusing a Workflow with an Agent.

A Workflow follows fixed steps.

An Agent decides its own steps.

---

### Mistake 3

Using an Agent for simple tasks.

This increases cost and complexity without adding value.

---

### Mistake 4

Calling a single tool call an "Agent."

A one-shot tool call with no loop is closer to a Workflow than a true Agent.

---

# Interview Tip ⭐

A common interview question is:

> **What is the difference between an LLM, a Workflow, and an AI Agent?**

A good answer is:

An LLM generates responses to prompts.

A Workflow follows predefined steps.

An AI Agent can reason, make decisions, choose tools, and adapt its actions to achieve a goal.

A strong follow-up point: Agents trade speed, cost, and predictability for
flexibility, so the right choice depends on the task, not just what's most
capable.

---

# Where is this Used?

- ChatGPT
- Claude
- Gemini
- LangChain
- LangGraph
- OpenAI Agents SDK
- Google ADK
- CrewAI
- Enterprise AI Applications

---

# Key Takeaways

- An LLM generates answers.
- A Workflow follows fixed steps.
- An AI Agent makes decisions and adapts to the situation.
- A single tool call with no loop is not the same as a full Agent.
- Agents cost more, run slower, and are harder to debug than LLMs or Workflows.
- Choose the simplest solution that meets your requirements.
- Understanding these differences is essential for building production AI systems.

---

