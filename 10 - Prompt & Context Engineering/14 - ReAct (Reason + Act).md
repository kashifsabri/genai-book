

## Learning Objectives

By the end of this chapter, you will understand:

- What ReAct is
- Why ReAct is important
- How ReAct works
- Why ReAct is the foundation of modern AI Agents
- What the actual Thought / Action / Observation text format looks like
- Why combining reasoning with actions outperforms either one alone
- What can go wrong with a ReAct loop (infinite loops, hallucinated tools) and how to guard against it
- How ReAct relates to Reflexion and to LangGraph specifically

---

# Introduction

Suppose you ask an AI:

```text
What is the weather in Mumbai today?
```

Can the model answer this from memory?

No.

It needs to check the latest weather.

So instead of immediately answering,

the AI should:

- Think
- Decide what tool to use
- Use the tool
- Read the result
- Answer the user

This is called **ReAct**.

---

# What is ReAct?

**ReAct** stands for:

```text
Reason

+

Act
```

Instead of directly answering,

the model first reasons about the problem,

then performs an action if needed.

---

# Visual Flow

```text
Question

↓

Reason

↓

Action

↓

Observation

↓

Final Answer
```

This cycle may repeat multiple times.

---

# Example

User

```text
What's the weather in Mumbai today?
```

Reason

```text
I need the latest weather information.
```

Action

```text
Call Weather API
```

Observation

```text
28°C

Cloudy
```

Answer

```text
The current weather in Mumbai is 28°C and cloudy.
```

---

# What This Actually Looks Like as Text

The examples above are shown as clean separate blocks for clarity, but it's worth seeing what a real ReAct trace looks like as raw model output, since this is often how it's implemented in practice (especially in the original ReAct paper, before dedicated function-calling APIs existed):

```text
Thought: I need the latest weather information for Mumbai, since I can't know today's weather from training data.
Action: get_weather["Mumbai"]
Observation: 28°C, Cloudy
Thought: I now have enough information to answer the user.
Final Answer: The current weather in Mumbai is 28°C and cloudy.
```

The model literally generates this as text, one token at a time (remember: autoregressive generation). The "Action" line is parsed by the surrounding application code, which then actually calls the real tool/API and inserts the result back in as the "Observation" before letting the model continue generating. This is the mechanical link between a prompting pattern (ReAct) and an engineering system (an agent).

---

# Why Combining Reasoning and Acting Works Better Than Either Alone

This is worth understanding precisely, since it's the actual insight behind the original ReAct paper, not just "thinking is good."

```text
Reasoning only (like CoT)   → the model can reason clearly, but if it lacks a fact,
                              it may confidently hallucinate rather than realize it
                              needs to look something up

Acting only (no reasoning)  → the model can call tools, but without reasoning between
                              actions, it's harder to recover from unexpected results
                              or decide what to do next

Reasoning + Acting (ReAct)  → reasoning helps decide WHEN and WHY to act, and helps
                              interpret the observation afterward; acting grounds the
                              reasoning in real, up-to-date information instead of
                              the model's possibly outdated internal knowledge
```

The two capabilities compensate for each other's weaknesses — reasoning without grounding can drift into hallucination, and acting without reasoning can't adapt when something unexpected comes back from a tool.

---

# Why is ReAct Important?

Without ReAct,

the model would guess.

With ReAct,

the model uses tools to find the correct information.

This makes answers:

- More accurate
- More reliable
- More useful

---

# Another Example

User

```text
What is 25 USD in INR?
```

Reason

```text
I need today's exchange rate.
```

Action

```text
Call Currency API
```

Observation

```text
1 USD = 87 INR
```

Answer

```text
25 USD is approximately ₹2175.
```

---

# ReAct Loop

The model doesn't always stop after one action.

Sometimes it repeats the cycle.

```text
Question

↓

Reason

↓

Action

↓

Observation

↓

Reason Again

↓

Action Again

↓

Final Answer
```

This is called the **Agent Loop**.

We'll study it in detail later.

---

# What Can Go Wrong: Loops and Hallucinated Tools

Since this loop is often implemented by real applications, not just described conceptually, it's worth knowing its common failure modes — this is a favorite area for interview follow-up questions.

```text
Infinite/excessive looping   → the model keeps reasoning and acting without
                                 ever reaching a final answer

Hallucinated tool calls       → the model "calls" a tool that doesn't exist,
                                 or passes malformed/invalid arguments

Misreading observations       → the model reasons incorrectly about a tool's
                                 result, leading the loop in the wrong direction

Repeating the same failed action → without a check, the model may retry an
                                 identical failing action rather than adapting
```

Common safeguards used in real systems:

```text
Max iteration limit    → hard cap on how many Reason→Act→Observe cycles are allowed
Tool schema validation  → reject/catch calls to undefined tools or malformed arguments
                          before they execute
Timeout                 → cap total time spent in the loop
Explicit stop condition → the model must clearly signal "Final Answer" to exit the loop
```

Without a max iteration limit in particular, a confused agent can loop indefinitely, burning tokens and cost with no useful output — this is one of the first things to build into any ReAct-style agent.

---

# ReAct vs Reflexion

Another pattern worth distinguishing, since it often gets grouped in with ReAct: **Reflexion**.

```text
ReAct       → Reason, act, observe, repeat — moving forward through a task
Reflexion    → After a failed attempt (or full task), the model explicitly
              critiques its own output/approach, then tries again with that
              self-critique as added context
```

ReAct is about deciding what to do next during a task. Reflexion is about learning from a completed (often failed) attempt before retrying. The two are often combined: a ReAct-style loop to do the work, with a Reflexion step if the final result doesn't meet some check.

---

# ReAct vs Chain of Thought

|Chain of Thought|ReAct|
|---|---|
|Thinks|Thinks + Uses Tools|
|Internal reasoning|Reasoning + External Actions|
|No tool usage|Tool usage supported|

Think of it this way.

Chain of Thought says:

> "Let me think."

ReAct says:

> "Let me think... then use a tool if needed."

---

# ReAct vs Function Calling

This is a very common interview question.

|ReAct|Function Calling|
|---|---|
|Prompting technique|Model/API capability|
|Decides _when_ to use a tool|Executes the selected tool|
|Guides reasoning|Performs the function call|

In simple words:

ReAct is the **decision-making process**.

Function Calling is the **execution mechanism**.

The two are often used together.

---

# Real-World Example

Imagine an AI travel assistant.

User asks:

```text
Book me the cheapest flight to Dubai.
```

The AI may:

```text
Reason

↓

Search Flights

↓

Read Results

↓

Compare Prices

↓

Book Ticket

↓

Answer User
```

This is ReAct in action.

---

# Why is ReAct Important for Agentic AI?

Almost every modern AI Agent follows this pattern.

Examples include:

- OpenAI Agents SDK
- LangGraph
- Google ADK
- CrewAI
- AutoGen

Although their implementations differ,

they all follow the same core idea:

```text
Reason

↓

Act

↓

Observe

↓

Repeat
```

---

# How LangGraph Implements This Loop, Conceptually

Since LangGraph came up earlier in this book, it's worth connecting the dots directly: LangGraph models an agent as a **graph/state machine**, where nodes are steps (reason, act, observe) and edges define what happens next based on the outcome.

```text
[Reason Node] → [Act Node] → [Observe Node] → back to [Reason Node]
                                              (or) → [Final Answer Node]
```

This is essentially the ReAct loop made explicit and controllable as a graph, rather than relying purely on the model to implicitly manage the loop through generated text. It gives developers more control over things like max iterations, conditional branching, and human-in-the-loop checkpoints — the safeguards mentioned above become graph-level guarantees rather than just prompt instructions.

---

# Best Practice

Only use tools when needed.

Example

Question

```text
What is Java?
```

No tool required.

Question

```text
What is today's weather?
```

A tool is required.

Unnecessary tool calls increase:

- Cost
- Latency
- Complexity

---

# Common Beginner Mistakes

### Mistake 1

Thinking ReAct is a framework.

It isn't.

It is a **reasoning pattern**.

---

### Mistake 2

Confusing ReAct with Function Calling.

Remember:

- ReAct decides.
- Function Calling executes.

---

### Mistake 3

Using tools for information the model already knows.

Not every question needs external tools.

---

### Mistake 4

Building a ReAct loop with no maximum iteration limit or stop condition.

Without one, a confused agent can loop indefinitely, wasting cost and time.

---

# Interview Tip ⭐

A very common interview question is:

> **What is the difference between ReAct and Function Calling?**

A good answer is:

- **ReAct** is a prompting and reasoning technique where the model decides whether it needs to use a tool.
- **Function Calling** is the mechanism that allows the model to execute that tool.

ReAct controls the decision.

Function Calling performs the action.

---

# Interview Tip ⭐

Another strong one to prepare for:

> **What could go wrong with a ReAct-style agent in production, and how would you guard against it?**

Answer:

The agent can loop excessively without reaching a final answer, call tools that don't exist or with invalid arguments, or misinterpret an observation and head in the wrong direction. Guardrails include a max iteration limit, tool schema validation before execution, timeouts, and requiring an explicit stop signal (like "Final Answer") to exit the loop.

---

# Key Takeaways

- ReAct stands for **Reason + Act**.
- The model reasons before using a tool.
- It follows a loop of **Reason → Action → Observation**, often written literally as Thought/Action/Observation text that the surrounding application parses.
- Combining reasoning and acting compensates for the weaknesses of each alone — reasoning grounds itself in real information, acting is guided by interpretation of results.
- Real ReAct loops need safeguards: max iterations, tool validation, timeouts, and explicit stop conditions.
- Reflexion is a related but distinct pattern — it adds self-critique after an attempt, rather than deciding what to do next during one.
- ReAct is the foundation of modern AI Agents, and frameworks like LangGraph implement it explicitly as a graph/state machine.
- Function Calling and ReAct are related but different concepts.

---

