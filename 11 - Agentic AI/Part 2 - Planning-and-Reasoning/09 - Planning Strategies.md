

## Learning Objectives

By the end of this chapter, you will understand:

- What Planning Strategies are
- Why AI Agents need different planning approaches
- The most common planning strategies
- How an Agent chooses the right strategy
- The trade-offs (pros/cons) of each strategy
- Named planning patterns used in real-world Agent frameworks
- How Agents replan when a plan fails mid-execution

---

# Introduction

Suppose you ask an AI Agent:

```text
Plan my weekend trip to Goa.
```

How should the Agent solve this?

Should it:

- Book the flight first?
- Find the hotel first?
- Check the weather first?

There isn't only one correct way.

An AI Agent chooses a **planning strategy** based on the task.

---

# What are Planning Strategies?

Planning Strategies are different ways an AI Agent organizes its work to achieve a goal.

A good strategy helps the Agent:

- Work efficiently
- Reduce mistakes
- Complete tasks faster

Different problems require different strategies.

---

# Why are Planning Strategies Important?

Imagine building a house.

You wouldn't:

```text
Paint the walls

↓

Lay the foundation
```

The order matters.

Similarly,

an AI Agent must decide the best sequence of actions before starting.

---

# Common Planning Strategies

## 1. Sequential Planning

The Agent completes one task at a time.

```text
Task 1

↓

Task 2

↓

Task 3
```

Each step depends on the previous one.

---

### Example

```text
Book Flight

↓

Book Hotel

↓

Create Itinerary
```

This is the most common planning strategy.

---

## 2. Parallel Planning

Some tasks are independent.

The Agent can perform them at the same time.

```text
        Goal
          │
    ┌─────┴─────┐
    ▼           ▼
Task 1      Task 2
    │           │
    └─────┬─────┘
          ▼
      Final Result
```

---

### Example

```text
Check Weather

+

Search Hotels
```

These tasks don't depend on each other,

so they can run together.

---

## 3. Hierarchical Planning

The Agent first creates a high-level plan,

then breaks each step into smaller tasks.

```text
Plan Vacation

↓

Travel

↓

Book Flight

↓

Select Seat
```

Large goals become easier to manage.

---

## 4. Reactive Planning

Sometimes,

the Agent cannot plan everything in advance.

It must react to new information.

Example

```text
Search Flight

↓

Flight Sold Out

↓

Search Another Airline
```

The Agent changes its plan while working.

---

# Pros and Cons of Each Strategy

Every strategy trades off speed, reliability, and complexity differently:

|Strategy|Pros|Cons|Best For|
|---|---|---|---|
|**Sequential**|Simple, predictable, easy to debug|Slow — no step starts until the previous finishes|Tasks with strict dependencies (e.g., pay → confirm → ship)|
|**Parallel**|Fast, efficient use of time/resources|Harder to debug; needs conflict handling if tasks touch shared state|Independent lookups (e.g., weather + hotel search)|
|**Hierarchical**|Manages complexity well; large goals stay organized|More planning overhead upfront; extra layer to maintain|Large, multi-part goals (e.g., building software)|
|**Reactive**|Adapts to real-world surprises; robust to failure|Less predictable; can loop or drift without safeguards|Environments with uncertainty (e.g., booking systems, live APIs)|

---

# Which Strategy Should an Agent Choose?

It depends on the task.

|Task|Strategy|
|---|---|
|Booking a flight|Sequential|
|Checking weather and hotels|Parallel|
|Building a software project|Hierarchical|
|Handling unexpected errors|Reactive|

There is no single strategy that works for every problem.

---

# Named Planning Patterns in Real Agent Frameworks

The four strategies above are the underlying _concepts_. In practice, Agent frameworks implement them under specific named patterns worth knowing:

- **Plan-and-Execute** — the Agent creates a full plan up front (often hierarchical), then executes it step by step, only replanning if a step fails. Used in LangGraph and many production Agents because it's cheaper than replanning after every single step.
- **ReAct (Reason + Act)** — the Agent interleaves reasoning and action one step at a time, deciding the next action only after observing the result of the last one. This is essentially fine-grained Reactive Planning.
- **ReWOO (Reasoning WithOut Observation)** — the Agent plans all tool calls up front without waiting on intermediate results, then executes them (often in parallel), reducing repeated LLM calls.
- **Tree-of-Thought Planning** — the Agent explores multiple possible plans (branches) before committing, useful when a goal has several valid solution paths and picking wrong is costly.

Knowing these names is useful because they show up directly in framework documentation (LangGraph, CrewAI, AutoGen) and in interviews.

---

# Real-World Example

Suppose you're using an AI Shopping Agent.

Goal

```text
Buy the best laptop.
```

The Agent may use:

```text
Compare Prices

+

Read Reviews

↓

Choose Best Laptop

↓

Place Order
```

Here,

price comparison and review analysis happen in parallel,

while ordering happens afterward.

---

# Python Example 🐍

A simple sequential plan:

```python
plan = [
    "Book flight",
    "Book hotel",
    "Create itinerary"
]

for step in plan:
    print(step)
```

Real AI Agents create these plans automatically based on the user's goal.

---

# Replanning: What Happens When a Step Fails?

A plan is not guaranteed to survive contact with the real world. Good Agents include a **replanning loop**:

```text
Execute Step

↓

Did it succeed?

├── Yes → Move to Next Step

└── No  → Re-analyze Situation → Generate New Step(s) → Retry
```

This is what separates Reactive Planning from simple Sequential Planning: Sequential Planning assumes the plan is correct once made; Reactive Planning assumes it might need to change and builds in a check-and-adjust loop.

---

# Industry Insight ⭐

Production AI Agents rarely use just one planning strategy.

They often combine multiple strategies.

Example:

```text
Create High-Level Plan

↓

Run Independent Tasks in Parallel

↓

React to Unexpected Events

↓

Complete Goal
```

This makes Agents more efficient and flexible.

---

# Best Practices

Choose the simplest planning strategy that solves the problem.

Don't use complex planning for simple tasks.

Allow the Agent to change its plan if new information becomes available.

When running tasks in parallel, make sure they don't write to the same shared resource without coordination — this avoids race conditions.

---

# Common Beginner Mistakes

### Mistake 1

Thinking every task should be solved sequentially.

Some tasks can run in parallel.

---

### Mistake 2

Creating overly complicated plans.

Simple tasks need simple plans.

---

### Mistake 3

Never updating the plan.

A good Agent should adapt when conditions change.

---

### Mistake 4

Running independent-looking tasks in parallel without checking for hidden dependencies or shared resources.

For example, "Check Budget" and "Book Hotel" may look independent, but if both read/write the same budget total, running them in parallel can cause incorrect results. Always verify tasks are truly independent before parallelizing.

---

# Interview Tip ⭐

A common interview question is:

> **What are Planning Strategies in AI Agents?**

A good answer is:

Planning Strategies are different ways an AI Agent organizes and executes tasks. Common strategies include Sequential, Parallel, Hierarchical, and Reactive planning. The Agent chooses the strategy based on the problem it needs to solve.

A strong follow-up point: in real frameworks these map to named patterns like Plan-and-Execute, ReAct, and ReWOO — showing awareness of these names signals hands-on familiarity, not just theory.

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

- Planning Strategies define how an Agent approaches a task.
- Different problems require different strategies.
- Sequential, Parallel, Hierarchical, and Reactive planning are the most common approaches.
- Each strategy has trade-offs between speed, predictability, and complexity.
- Real frameworks implement these as named patterns: Plan-and-Execute, ReAct, ReWOO, Tree-of-Thought.
- Production AI Agents often combine multiple planning strategies.
- Good Agents include a replanning loop to recover when a step fails.
- Choosing the right strategy improves efficiency and reliability.

---

