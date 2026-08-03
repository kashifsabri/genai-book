

## Learning Objectives

By the end of this chapter, you will understand:

- What Multi-Step Planning is
- Why AI Agents use it
- How an Agent executes multiple steps
- Why it is essential for complex tasks
- How state and context flow between steps
- What happens when a step fails mid-plan
- How Multi-Step Planning relates to Hierarchical Planning

---

# Introduction

Imagine you ask an AI Agent:

```text
Plan a 5-day trip to Goa.
```

Can the Agent solve this in one step?

No.

It needs to:

- Find flights
- Find hotels
- Check the weather
- Suggest places to visit
- Create an itinerary

Each step depends on the previous one.

This is called **Multi-Step Planning**.

---

# What is Multi-Step Planning?

Multi-Step Planning is a planning strategy where an AI Agent breaks a goal into multiple steps and executes them in the correct order.

Instead of solving everything at once,

the Agent completes one step,

then moves to the next.

---

# Visual Diagram

```text
Goal

↓

Step 1

↓

Step 2

↓

Step 3

↓

Goal Completed
```

Each step moves the Agent closer to the final goal.

---

# Example

User Goal

```text
Plan my vacation.
```

The Agent creates a plan.

```text
Search Flights

↓

Find Hotels

↓

Check Weather

↓

Create Itinerary

↓

Return Plan
```

One step depends on the completion of the previous step.

---

# Another Example

User Goal

```text
Analyze last month's sales.
```

The Agent performs:

```text
Read Sales Data

↓

Calculate Revenue

↓

Identify Trends

↓

Generate Report
```

Each step builds on the previous one.

---

# Why Do AI Agents Need Multi-Step Planning?

Many real-world problems cannot be solved with a single action.

Multi-Step Planning helps the Agent:

- Solve complex problems
- Organize work logically
- Reduce mistakes
- Handle dependencies between tasks

---

# How State Flows Between Steps

A multi-step plan is only useful if each step can access what earlier steps produced. The Agent maintains a running **working memory** (sometimes called scratchpad or state) that carries outputs forward.

```text
Step 1: Search Flights   → Output: flight_options
Step 2: Find Hotels      → Uses: none (independent)
Step 3: Check Weather    → Uses: destination, dates (from Step 1)
Step 4: Create Itinerary → Uses: flight_options + hotel_options + weather
```

In practice, this is usually implemented by appending each step's result to a shared context object (or conversation history) that gets passed into the prompt for the next step:

```python
state = {}

for step in plan:
    result = execute(step, context=state)
    state[step] = result
```

Without this, later steps would have no way to know what earlier steps found — the plan would just be a list of disconnected actions.

---

# Handling Failures Mid-Plan

Steps can fail — an API might be down, a search might return nothing, a tool call might error out. A multi-step Agent needs a policy for this, not just a linear "step 1 then step 2 then step 3" assumption:

|Failure Response|When to Use|
|---|---|
|**Retry**|Transient errors (timeout, rate limit)|
|**Skip**|The step is optional / non-blocking for the goal|
|**Abort**|The step is required and no fallback exists|
|**Replan**|The failure changes what's possible (e.g., flight sold out → search a different route)|

```text
Execute Step

↓

Failed?

├── No  → Continue to Next Step

└── Yes → Retry / Skip / Abort / Replan (based on step type)
```

This is the same replanning idea introduced in the Planning Strategies chapter — Multi-Step Planning and Reactive Planning often work together in practice.

---

# Multi-Step vs Hierarchical Planning

These two are related but not the same, and it's easy to confuse them:

||Multi-Step Planning|Hierarchical Planning|
|---|---|---|
|**Focus**|The _order_ of execution (do A, then B, then C)|The _structure_ of the goal (break big goal into smaller goals, which break into steps)|
|**Depth**|Usually flat — a single sequence of steps|Layered — goals contain sub-goals, which contain steps|
|**Question it answers**|"What order do I do these actions in?"|"How do I break this big goal into manageable pieces?"|

In practice, Hierarchical Planning is often used to _produce_ the list of steps, and Multi-Step Planning is how that list gets _executed_. They're complementary, not competing strategies.

---

# Python Example 🐍

A simple multi-step plan:

```python
plan = [
    "Search flights",
    "Find hotels",
    "Create itinerary"
]

for step in plan:
    execute(step)
```

Real AI Agents generate these plans automatically,

but the execution follows the same idea.

---

# Single-Step vs Multi-Step Planning

|Single-Step|Multi-Step|
|---|---|
|One action|Multiple actions|
|Simple tasks|Complex tasks|
|Minimal reasoning|More planning and reasoning|
|Low cost|Higher cost|

Choose the strategy based on the complexity of the goal.

---

# Real-World Example

Suppose you're using an AI Research Agent.

Goal

```text
Write a report on Artificial Intelligence.
```

The Agent works like this.

```text
Search Articles

↓

Read Documents

↓

Summarize Information

↓

Write Report

↓

Review Report

↓

Return Final Answer
```

This would be impossible with a single step.

---

# Industry Insight ⭐

Most production AI Agents use Multi-Step Planning.

Examples include:

- Research Agents
- Coding Agents
- Travel Assistants
- HR Assistants

These systems solve problems by completing a sequence of smaller tasks instead of trying to solve everything at once.

A practical cost note: as the number of steps grows, so does the context passed into each subsequent step (previous outputs, tool results, etc.). Production Agents often summarize or prune earlier step outputs rather than passing everything forward verbatim, to avoid hitting context window limits and rising token costs on long plans.

---

# Best Practices

Break large goals into clear, manageable steps.

Each step should have a specific purpose.

Whenever possible,

complete one step before moving to the next.

This makes the Agent easier to debug and maintain.

Carry forward only the state each step actually needs — passing everything to every step wastes context and increases cost.

---

# Common Beginner Mistakes

### Mistake 1

Trying to solve complex tasks in one step.

Large problems should be divided into multiple steps.

---

### Mistake 2

Creating too many unnecessary steps.

A plan should be detailed enough to guide execution,

but not so detailed that it becomes inefficient.

---

### Mistake 3

Ignoring dependencies.

Some tasks cannot begin until earlier tasks are completed.

Always execute dependent tasks in the correct order.

---

### Mistake 4

Losing context between steps.

If the output of Step 1 isn't carried into Step 2, the Agent may repeat work, contradict itself, or produce a final result that ignores earlier findings. Always make sure step outputs are stored and made available to later steps that need them.

---

# Interview Tip ⭐

A common interview question is:

> **What is Multi-Step Planning in AI Agents?**

A good answer is:

Multi-Step Planning is a strategy where an AI Agent breaks a complex goal into multiple dependent steps and executes them in sequence until the goal is achieved.

A strong follow-up point: a production-grade Multi-Step Planner also needs a working memory to pass state between steps, and a failure-handling policy (retry, skip, abort, or replan) for when a step doesn't succeed — not just an ordered list of actions.

---

# Where is this Used?

- OpenAI Agents SDK
- LangGraph
- LangChain
- Google ADK
- CrewAI
- AutoGen
- Enterprise AI Applications

---

# Key Takeaways

- Multi-Step Planning is used for complex tasks.
- The Agent executes multiple steps in sequence.
- Each step moves the Agent closer to its goal.
- State from earlier steps must flow into later steps via working memory.
- A failure-handling policy (retry/skip/abort/replan) is essential for real-world plans.
- Multi-Step Planning handles execution order; Hierarchical Planning handles goal structure — they work together.
- It helps solve problems that cannot be completed with a single action.
- Most production AI Agents rely on Multi-Step Planning.

---

