

## Learning Objectives

By the end of this chapter, you will understand:

- What Reasoning is
- What Acting is
- The difference between them
- Why both are essential for AI Agents

---

# Introduction

Imagine you ask an AI Agent:

```text
Find the cheapest laptop under ₹60,000.
```

Before doing anything,

the Agent first thinks:

```text
Which websites should I check?

Should I compare prices?

Should I read reviews?
```

This is **Reasoning**.

After deciding,

the Agent starts searching websites and comparing products.

This is **Acting**.

An AI Agent becomes powerful because it can both **think** and **act**.

---

# What is Reasoning?

Reasoning is the process of thinking before taking action.

The Agent analyzes the situation and decides:

- What is the goal?
- What information is missing?
- What should I do next?

Reasoning happens **inside the LLM**.

---

# Example of Reasoning

Goal

```text
Plan a trip to Goa.
```

The Agent thinks:

```text
I need to:

- Find flights
- Find hotels
- Check the weather
- Create an itinerary
```

At this stage,

the Agent has not done anything yet.

It is only planning.

---

# What is Acting?

Acting is the process of executing the plan.

The Agent performs real actions using tools.

Examples include:

- Searching the web
- Calling an API
- Reading a database
- Sending an email

Acting happens **outside the LLM**.

---

# Example of Acting

The Agent executes:

```text
Search Flights

↓

Search Hotels

↓

Check Weather
```

These are real actions,

not thoughts.

---

# Reasoning vs. Acting at a Glance ⭐

||Reasoning|Acting|
|---|---|---|
|Happens where|Inside the LLM|Outside the LLM, via tools|
|Produces|A decision or plan|A real-world effect|
|Reversible?|Yes — just a thought|Not always — e.g. sending an email can't be unsent|
|Cost|Uses LLM tokens|Uses tool/API calls, may cost money or time|
|Risk if skipped|Actions become random or wasteful|Task never actually gets done|

---

# Visual Diagram

```text
        Goal

          │

          ▼

    Reason 🤔

          │

 Decide What To Do

          │

          ▼

      Act ⚡

          │

 Use Tools / APIs

          │

          ▼

   Observe Results

          │

          ▼

    Reason Again
```

The Agent constantly switches between reasoning and acting.

---

# Where Does Observation Fit In? ⭐

This chapter focuses on Reasoning and Acting, but you may notice the diagram above also includes **Observe**. That's because Reasoning and Acting are two thirds of the loop introduced in the previous chapter — Observation is what connects them on the next cycle.

```text
Reason → decide what to do
Act     → do it
Observe → check what happened, feeding back into the next Reason step
```

Reasoning and Acting are the two _types_ of work an Agent does; Observation is what makes the next round of reasoning better-informed.

---

# Real-World Example

Suppose you're using an AI Shopping Agent.

Goal

```text
Find the best smartphone under ₹30,000.
```

Reasoning

```text
Which shopping websites should I check?

Should I compare ratings?

Should I read reviews?
```

Acting

```text
Search Amazon

↓

Search Flipkart

↓

Compare Prices

↓

Read Reviews
```

Reasoning decides.

Acting executes.

---

# Python Example 🐍

A simple Agent may work like this:

```python
thought = llm.reason(goal)

action = choose_tool(thought)

result = execute_tool(action)

print(result)
```

Here:

- `reason()` represents thinking.
- `execute_tool()` represents acting.

---

# Why are Both Important?

Imagine an Agent that only reasons.

```text
Think

↓

Think

↓

Think

↓

Never acts
```

It never completes the task.

---

Now imagine an Agent that only acts.

```text
Search

↓

Search

↓

Search
```

Without thinking,

it may perform unnecessary or incorrect actions.

A successful Agent needs **both**.

---

# Common Reasoning Techniques ⭐

Not all reasoning looks the same. A few widely used approaches:

- **Chain-of-Thought (CoT)** — the LLM reasons step by step in text before deciding on an action, rather than jumping straight to an answer.
- **ReAct** — reasoning and acting are interleaved: think a little, act, observe, think a little more — rather than doing all the reasoning up front.
- **Self-reflection** — after acting, the Agent explicitly reasons about whether its own last action actually worked, not just what to do next.

Most modern Agent frameworks use some version of ReAct, since it lets the Agent adjust its reasoning based on real results instead of planning everything blindly in advance.

---

# Industry Insight ⭐

Modern AI frameworks separate reasoning from execution.

Typically:

```text
LLM

↓

Reason

----------------

Application

↓

Execute Tool
```

This separation makes AI Agents more reliable and secure.

### Why Separation Improves Security ⭐

Keeping reasoning (inside the LLM) and acting (inside the application) as separate layers means the application can add checks the LLM can't bypass, such as:

- Validating a tool's inputs before running it
- Enforcing permission rules (e.g., "this Agent can read the database but not write to it")
- Requiring human approval for high-risk actions

If reasoning and acting were the same step, there'd be no place to insert these checks — the LLM's decision would become the action immediately, with nothing in between to catch a mistake.

---

# Best Practices

Reason before acting.

Don't call tools unless they are needed.

After every action,

observe the result and reason again if necessary.

### Also worth doing ⭐

- Keep an eye on how much reasoning happens before each action — excessive reasoning adds latency and token cost without always improving the result.
- Let the application layer validate an action before it executes, not just the LLM's reasoning.

---

# Common Beginner Mistakes

### Mistake 1

Thinking reasoning and acting are the same.

Reasoning is thinking.

Acting is doing.

---

### Mistake 2

Using tools without reasoning.

This increases cost and may produce poor results.

---

### Mistake 3

Reasoning forever without taking action.

An Agent must eventually execute its plan.

---

### Mistake 4 ⭐

Over-reasoning on simple tasks.

Not every action needs a long chain of thought first. Excessive reasoning before a simple action adds unnecessary latency and token cost — reasoning depth should match task complexity.

---

# Interview Tip ⭐

A common interview question is:

> **What is the difference between Reasoning and Acting in an AI Agent?**

A good answer is:

Reasoning is the process of thinking and deciding what to do next.

Acting is the process of executing those decisions using tools or external systems.

Reasoning happens inside the LLM, while acting happens through the application and its tools.

A likely follow-up question is:

> **Why is it useful to separate reasoning from acting in an Agent's architecture?**

A good answer is:

Because it creates a checkpoint between "the LLM decided this" and "this actually happened" — the application layer can validate inputs, enforce permissions, or require human approval before an action runs, which wouldn't be possible if reasoning and acting were the same step.

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

- **Chain-of-Thought (CoT)** — reasoning step by step in text before producing a final decision or answer.
- **Self-reflection** — an Agent reasoning about whether its own previous action succeeded, not just what to do next.
- **Application Layer** — the part of the system (outside the LLM) that actually executes tools and can enforce validation or permission checks.

---

# Key Takeaways

- Reasoning means thinking before acting.
- Acting means executing actions using tools.
- AI Agents continuously switch between reasoning and acting.
- Observation is what connects one round of reasoning to the next.
- Both reasoning and acting are required to solve complex tasks.
- Reasoning depth should match task complexity — more isn't always better.
- Separating reasoning from acting makes Agents more reliable and secure, by giving the application a place to validate actions before they run.

---

# Quick Check ⭐

1. In which layer does Reasoning happen, and in which layer does Acting happen?
2. How does Observation relate to Reasoning and Acting?
3. Name one reasoning technique besides plain step-by-step thinking.
4. Why does separating reasoning from acting make an Agent more secure?

_(Answers can be found by reviewing the sections above.)_

---

# Congratulations! 🎉

You have completed **Part 1 – Agent Foundations**.

You now understand:

- What an AI Agent is
- How it differs from an LLM and a Workflow
- The components of an Agent
- The Agent Lifecycle
- The Think → Act → Observe loop
- The difference between Reasoning and Acting

These concepts form the foundation for everything that follows.

---

