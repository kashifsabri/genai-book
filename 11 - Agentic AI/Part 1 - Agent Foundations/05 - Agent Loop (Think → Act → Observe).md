

## Learning Objectives

By the end of this chapter, you will understand:

- What the Agent Loop is
- Why every AI Agent needs a loop
- The Think → Act → Observe cycle
- How an Agent knows when to stop

---

# Introduction

Imagine you ask an AI Agent:

```text
Find the cheapest flight to Delhi.
```

Can the Agent answer immediately?

No.

It first needs to:

- Think about the task.
- Take an action.
- Observe the result.
- Decide what to do next.

It repeats this process until the goal is completed.

This repeating cycle is called the **Agent Loop**.

---

# What is the Agent Loop?

The Agent Loop is the process an AI Agent follows while working toward a goal.

Instead of responding once,

the Agent repeatedly:

- Thinks
- Acts
- Observes

until the task is finished.

---

# Visual Diagram

```text
          Goal
            │
            ▼
        Think 
            │
            ▼
         Act 
            │
            ▼
      Observe 👀
            │
            ▼
   Goal Completed?
      │         │
     No         Yes
      │          │
      └──────► Finish
      │
      ▼
    Think Again
```

The loop continues until the goal is achieved.

---

# Loop vs. Single-Shot Response ⭐

It helps to see the difference side by side:

||Single-Shot (Chatbot)|Agent Loop|
|---|---|---|
|Responds after|1 LLM call|Multiple LLM calls|
|Can retry on failure|No|Yes|
|Can use tools mid-task|No|Yes|
|Adapts to new information|No|Yes|
|Risk|Gives its best guess once|Can loop too long if not bounded|

This is the key distinction the rest of the chapter builds on.

---

# Step 1 — Think

The Agent first decides:

- What is the goal?
- What information do I have?
- What should I do next?

Example

```text
User:

Find the cheapest flight.
```

Agent thinks:

```text
I should search flight websites first.
```

---

# Step 2 — Act

The Agent performs an action.

Examples include:

- Searching the web
- Calling an API
- Querying a database
- Using a calculator

Example

```text
Search Flights API
```

---

# Step 3 — Observe

The Agent checks the result.

Example

```text
Cheapest Flight

₹5,200
```

The Agent now asks:

```text
Is my goal complete?
```

If yes,

it stops.

If not,

it thinks again.

---

# The Scratchpad ⭐

For the Agent to "think again" intelligently, it needs to remember what it already tried. This running record of past thoughts, actions, and observations within a single loop is often called the **scratchpad**.

Example scratchpad after two iterations:

```text
Thought 1: Search flights on Site A
Action 1: search("Site A")
Observation 1: No results

Thought 2: Try Site B instead
Action 2: search("Site B")
Observation 2: ₹5,200 found
```

This scratchpad is usually passed back into the LLM on every iteration, so the Agent doesn't repeat the same failed action or forget what it already learned.

---

# Example

User Goal

```text
Find a hotel in Goa under ₹4,000.
```

The Agent works like this.

```text
Think

↓

Search Hotels

↓

Observe Results

↓

Too Expensive

↓

Think Again

↓

Search Another Website

↓

Observe

↓

Hotel Found

↓

Finish
```

The Agent keeps improving until it reaches the goal.

---

# How Does the Loop Know When to Stop? ⭐

"Goal completed" isn't the only way a loop ends. A well-built Agent Loop stops when any of these happen:

- **Goal achieved** — `check_goal(result)` returns true.
- **Max iterations reached** — a hard limit on how many times the loop can run, so it can't go forever.
- **Timeout** — the loop has been running longer than an allowed time.
- **No progress detected** — the Agent keeps getting the same failed observation with no new options left to try.

The next section shows why the max iterations limit matters in the code itself.

---

# Python Example 🐍

The Agent Loop is often implemented like this:

```python
goal_completed = False

while not goal_completed:

    thought = llm.reason()

    action = choose_tool(thought)

    result = execute_tool(action)

    goal_completed = check_goal(result)
```

This is a simplified example.

Real AI Agents have more components,

but the basic loop remains the same.

### A Safer Version ⭐

Notice the loop above has no limit — if `check_goal()` never returns true, it runs forever. Production Agents add a maximum iteration count to guard against this:

```python
goal_completed = False
max_iterations = 10
iteration = 0
scratchpad = []

while not goal_completed and iteration < max_iterations:

    thought = llm.reason(scratchpad)

    action = choose_tool(thought)

    result = execute_tool(action)

    scratchpad.append((thought, action, result))

    goal_completed = check_goal(result)

    iteration += 1

if not goal_completed:
    report_incomplete(scratchpad)
```

This version tracks the scratchpad and stops safely even if the goal is never reached.

---

# Why is the Agent Loop Important?

Without a loop,

the Agent can only respond once.

With the loop,

the Agent can:

- Retry failed tasks
- Use multiple tools
- Adapt to new information
- Solve complex problems

This is what makes an AI Agent different from a chatbot.

---

# Cost and Latency Trade-off ⭐

Every iteration of the loop is usually a new call to the LLM, plus a tool call. This means looping isn't free:

- More iterations = higher cost (more LLM calls)
- More iterations = slower responses (each step takes time)

This is why `max_iterations` isn't just a safety net — it's also a cost control. Good Agent design balances giving the Agent enough attempts to succeed against not letting it loop longer than necessary.

---

# Real-World Example

Suppose you're using an AI Coding Assistant.

Goal

```text
Fix my Python error.
```

The Agent may:

```text
Read Error

↓

Analyze Code

↓

Suggest Fix

↓

Run Tests

↓

Error Still Exists

↓

Try Another Fix

↓

Tests Pass

↓

Finish
```

The Agent doesn't stop after the first attempt.

It keeps working until the problem is solved.

---

# Industry Insight ⭐

Almost every modern Agent framework follows this loop.

Examples include:

- LangGraph
- OpenAI Agents SDK
- Google ADK
- CrewAI
- AutoGen

The implementation differs,

but the **Think → Act → Observe** cycle remains the same.

This pattern is widely known in research as **ReAct** (Reasoning + Acting) — one of the earliest and most influential formulations of the Agent Loop.

---

# Best Practices

Keep each loop focused on one decision.

After every action,

always observe the result before deciding what to do next.

An Agent should never perform actions blindly.

### Also worth doing ⭐

- Always set a `max_iterations` or timeout — never leave the loop unbounded.
- Maintain a scratchpad so the Agent doesn't repeat failed actions.
- Track cost/latency per iteration if the Agent runs in production.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the Agent only thinks once.

In reality,

it thinks after every observation.

---

### Mistake 2

Skipping the observation step.

Without observing results,

the Agent cannot adapt.

---

### Mistake 3

Creating an infinite loop.

The Agent must know when the goal has been achieved or when it should stop.

---

### Mistake 4 ⭐

Forgetting the scratchpad.

If past thoughts and observations aren't passed back into the loop, the Agent may repeat the same failed action over and over, since it has no memory of already trying it.

---

# Interview Tip ⭐

A common interview question is:

> **What is the Think → Act → Observe loop?**

A good answer is:

The Think → Act → Observe loop is the core execution cycle of an AI Agent. The Agent reasons about the task, performs an action, observes the result, and repeats the process until the goal is completed. This pattern is also known as ReAct.

A likely follow-up question is:

> **How do you prevent an Agent Loop from running forever?**

A good answer is:

By setting a maximum number of iterations or a timeout, and by defining clear stopping conditions such as goal completion, no further progress, or an unrecoverable error.

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

# Glossary ⭐

- **Scratchpad** — the running record of past thoughts, actions, and observations within a loop, fed back into the LLM each iteration.
- **Max Iterations** — a hard limit on how many times the loop can run, used to prevent infinite loops.
- **ReAct** — a well-known pattern combining Reasoning and Acting, which the Think → Act → Observe loop is based on.
- **Timeout** — a time-based limit that stops the loop if it runs too long, regardless of iteration count.

---

# Key Takeaways

- The Agent Loop is the heart of every AI Agent.
- It follows the Think → Act → Observe cycle.
- The loop repeats until the goal is completed.
- Observation allows the Agent to adapt and improve.
- A scratchpad lets the Agent remember earlier attempts within the loop.
- The loop must have a stopping condition — such as max iterations or a timeout — besides just goal completion.
- Every iteration has a cost and latency trade-off worth designing for.
- This loop enables AI Agents to solve complex, multi-step tasks.

---

# Quick Check ⭐

1. What are the three steps in the Agent Loop, in order?
2. What is a scratchpad, and why does the Agent need one?
3. Name two ways (besides "goal achieved") that a loop can stop.
4. Why does looping more times cost more, even if it makes the Agent more likely to succeed?

_(Answers can be found by reviewing the sections above.)_

---

