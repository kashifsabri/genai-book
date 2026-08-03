

## Learning Objectives

By the end of this chapter, you will understand:

- What Replanning is
- Why AI Agents need Replanning
- When an Agent should create a new plan
- The difference between Reflection and Replanning
- The difference between a simple Retry and a full Replan
- Common triggers that cause an Agent to replan
- How to set safe stopping conditions and escalate to a human when needed

---

# Introduction

Imagine you ask an AI Agent:

```text
Book the cheapest flight to Delhi.
```

The Agent creates a plan.

```text
Search Airline A

↓

Book Flight
```

But when it searches,

it discovers:

```text
No flights available.
```

Should the Agent stop?

No.

It should create a **new plan**.

Maybe:

```text
Search Airline B

↓

Compare Prices

↓

Book Flight
```

Changing the plan when circumstances change is called **Replanning**.

---

# What is Replanning?

Replanning is the process of creating a new plan when the original plan can no longer achieve the goal.

Instead of continuing with a failed plan,

the Agent adapts and chooses a better approach.

---

# Why Do AI Agents Need Replanning?

Real-world environments change.

Examples include:

- A flight becomes unavailable.
- A website is offline.
- An API returns an error.
- A tool fails.
- The user's requirements change.

Without Replanning,

the Agent would simply fail.

---

# Visual Diagram

```text
Goal

↓

Create Plan

↓

Execute Plan

↓

Problem Found

↓

Replan

↓

New Plan

↓

Continue
```

The Agent adapts instead of giving up.

---

# Retry vs Replan: What's the Difference?

These are easy to mix up, but they're different responses to failure:

||Retry|Replan|
|---|---|---|
|**What changes**|Nothing — same action, same plan|The strategy itself changes|
|**When to use**|Transient/temporary failures (timeout, rate limit, flaky network)|The plan is fundamentally unable to reach the goal|
|**Example**|API timed out → call it again|Airline has no flights at all → search a different airline|
|**Risk if used wrongly**|Retrying a plan that can't work just wastes time and cost|Replanning for a one-off glitch is unnecessarily expensive|

A good rule of thumb: **retry first for transient errors**, and only **replan** if a retry also fails or if the failure clearly isn't temporary (e.g., "no flights available" won't fix itself with a retry).

---

# Example

User Goal

```text
Reserve a hotel under ₹4,000.
```

Original Plan

```text
Search Hotel Website A
```

Result

```text
No rooms available.
```

New Plan

```text
Search Hotel Website B

↓

Compare Prices

↓

Book Hotel
```

The goal remains the same,

but the plan changes.

---

# Another Example

User Goal

```text
Summarize this PDF.
```

Original Plan

```text
Read PDF
```

Problem

```text
The PDF is corrupted.
```

New Plan

```text
Ask the user to upload another copy.
```

Sometimes,

the best plan is to ask for more information.

---

# Common Replanning Triggers

Not all failures are the same. Recognizing the trigger type helps the Agent pick the right response:

|Trigger|Example|Typical Response|
|---|---|---|
|**Tool/API failure**|A booking API returns an error|Retry, then replan with a different tool|
|**Resource unavailable**|Flight sold out, item out of stock|Replan immediately (retrying won't help)|
|**New information**|User adds a constraint mid-task ("actually, keep it under $500")|Replan around the new constraint|
|**Environment change**|A website's layout changed, breaking a scraper|Replan with a different method/source|
|**Goal change**|User changes their mind about what they want|Restart from Goal Analysis, not just Replanning|

---

# Python Example 🐍

A simple replanning example:

```python
plan = create_plan(goal)

result = execute(plan)

if result == "failed":
    plan = create_new_plan(goal)
    execute(plan)
```

Production AI Agents use more advanced logic,

but the idea is the same.

---

# Reflection vs Replanning

Many beginners confuse these concepts.

|Reflection|Replanning|
|---|---|
|Reviews the current work|Creates a new plan|
|Improves an answer|Changes the strategy|
|Goal stays the same|Goal stays the same|
|Plan usually stays the same|Plan changes|

Think of it like this:

Reflection asks:

> "Can I improve what I just did?"

Replanning asks:

> "Should I take a different approach?"

---

# Real-World Example

Suppose you're using an AI Coding Agent.

Goal

```text
Fix my Python program.
```

Original Plan

```text
Apply Fix A

↓

Run Tests
```

Result

```text
Tests Failed.
```

The Agent replans.

```text
Analyze Error Again

↓

Generate Fix B

↓

Run Tests

↓

Success
```

The Agent changes its strategy instead of repeating the same mistake.

---

# Setting Stopping Conditions (and Escalating to a Human)

Replanning must be bounded, or a stuck Agent can loop indefinitely, burning time and cost. A practical implementation tracks an attempt counter and defines a ceiling:

```python
MAX_ATTEMPTS = 3
attempts = 0
plan = create_plan(goal)

while attempts < MAX_ATTEMPTS:
    result = execute(plan)
    if result == "success":
        break
    attempts += 1
    plan = create_new_plan(goal, reason=result.error)

if attempts == MAX_ATTEMPTS and result != "success":
    escalate_to_human(goal, history=attempts)
```

When the limit is reached, the best move is usually **not** another silent replan — it's to stop and either:

- Report back to the user what was tried and why it failed, or
- Escalate to a human operator (common in customer support and coding Agents)

This human-in-the-loop escalation is what separates a robust production Agent from one that either fails silently or loops forever.

---

# Industry Insight ⭐

Production AI Agents are designed to handle failures gracefully.

Instead of stopping when something goes wrong,

they often:

- Try another tool
- Search another source
- Ask the user for clarification
- Generate a new plan

This ability makes AI Agents much more reliable than simple workflows.

---

# Best Practices

Allow Agents to replan when:

- A tool fails.
- New information becomes available.
- The original plan cannot achieve the goal.

However,

avoid endless replanning.

Always define stopping conditions.

Prefer a quick retry for transient failures before paying the cost of a full replan.

---

# Common Beginner Mistakes

### Mistake 1

Repeating the same failed action.

If a plan fails,

change the strategy.

---

### Mistake 2

Replanning too quickly.

Sometimes a temporary error only requires a retry,

not a completely new plan.

---

### Mistake 3

Creating infinite replanning loops.

Always set limits on the number of replanning attempts.

---

### Mistake 4

Hitting the replanning limit and failing silently.

If an Agent exhausts its attempts without success, it should clearly report what it tried and why it failed — or escalate to a human — rather than returning a vague or incomplete answer.

---

# Interview Tip ⭐

A common interview question is:

> **What is Replanning in an AI Agent?**

A good answer is:

Replanning is the process of creating a new plan when the original plan cannot achieve the goal. It allows the Agent to adapt to failures, changing conditions, or new information.

A strong follow-up point: mature Agents distinguish retry (same plan, transient error) from replan (new strategy, structural failure), and always cap replanning attempts with a stopping condition that escalates to a human if exceeded.

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

- Replanning creates a new strategy when the current plan fails.
- It helps AI Agents adapt to changing conditions.
- Replanning is different from Reflection.
- Retry handles transient errors; Replan handles structural failures — know which one to use.
- Common triggers include tool failure, unavailable resources, new information, and goal changes.
- Production AI Agents frequently replan instead of stopping.
- Always cap replanning attempts and escalate to a human when the limit is reached.
- Good Agents know when to retry, when to replan, and when to stop.

---


