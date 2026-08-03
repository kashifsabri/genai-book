
## Learning Objectives

By the end of this chapter, you will understand:

- What Latency is
- Why latency matters
- How latency is measured
- What causes slow AI responses
- How to reduce latency

---

# Introduction

Imagine using an AI assistant.

You ask:

```text
Summarize this document.
```

Response Time:

```text
2 seconds
```

Good.

Now imagine another assistant.

Same question.

Response Time:

```text
30 seconds
```

Even if the answer is excellent,

most users won't wait.

This is why **Latency** matters.

---

# What is Latency?

Latency is the time taken by an AI system to generate a response.

It is usually measured in:

- Milliseconds (ms)
- Seconds (s)

Lower latency means faster responses.

---

# Visual Flow

```text
User Request

↓

Processing

↓

LLM

↓

Response

= Latency
```

---

# How is Latency Measured?

Latency isn't just one number — teams usually track several related metrics.

### Time to First Token (TTFT)

How long the user waits before seeing _any_ output at all.

```text
User sends request → ... → First word appears
```

This matters a lot for perceived speed, even if the full response takes longer.

---

### Total Response Time

The time from request to the fully completed response.

---

### Tokens Per Second (TPS)

Once generation starts, how fast the output streams out.

```text
Total Output Tokens ÷ Generation Time = TPS
```

A higher TPS means the response fills in faster once it starts.

---

# What Increases Latency?

Several factors make AI applications slower.

### Large Prompts

More tokens require more processing.

---

### Large Responses

Long outputs take more time to generate.

---

### Multiple LLM Calls

Examples include:

- Prompt Chaining
- ReAct
- Reflexion
- Multi-Agent Workflows

Every additional LLM call adds latency.

---

### Tool Calls

Calling:

- Weather APIs
- Databases
- Web Search

adds network delay.

---

### Large Models

Larger models are generally slower than smaller ones.

---

# Example

Simple Chatbot

```text
User

↓

One LLM Call

↓

Answer
```

Latency:

Low

---

Research Agent

```text
User

↓

Retriever

↓

Planner

↓

Web Search

↓

Calculator

↓

Reflexion

↓

Final Answer
```

Latency:

Much Higher

---

# Real-World Example

Suppose you're building an AI Customer Support Bot.

If users wait:

```text
25 seconds
```

many will leave.

A slightly less detailed answer in:

```text
2 seconds
```

often provides a better user experience.

---

# Latency vs Accuracy

Often,

improving accuracy also increases latency.

Examples

|Technique|Latency|
|---|---|
|Zero-shot|Low|
|Few-shot|Low|
|Chain of Thought|Medium|
|ReAct|High|
|Reflexion|Higher|
|Multi-Agent Workflow|Very High|

The goal is to find the right balance.

---

# How Can We Reduce Latency?

Use the simplest workflow that solves the problem.

Examples:

- Use smaller prompts.
- Reduce unnecessary tool calls.
- Avoid long reasoning for simple tasks.
- Retrieve fewer documents.
- Run independent tasks in parallel when possible.

---

# Streaming Responses

Instead of waiting for the entire response to finish, the output can be streamed to the user token-by-token as it's generated.

```text
Without Streaming: [ ... wait ... ] → Full answer appears at once
With Streaming:     Word appears → word → word → ... → done
```

Streaming doesn't reduce total processing time, but it dramatically improves **perceived** latency — the user sees progress immediately instead of staring at a blank screen.

---

# Caching for Latency

Just like caching reduces cost (Chapter 38), it also reduces latency — a cached answer can be returned almost instantly, skipping the LLM call entirely.

```text
Repeated question? → Serve cached response (fast)
New question?       → Call the LLM (slower)
```

---

# Parallelizing Independent Calls

If parts of a workflow don't depend on each other, run them at the same time instead of one after another.

```text
Sequential:
Call A (2s) → Call B (2s) → Call C (2s) = 6s total

Parallel:
Call A, Call B, Call C at once = ~2s total
```

This is especially useful in multi-tool or multi-agent workflows where several lookups can happen simultaneously.

---

# Setting Latency Budgets (SLAs)

Production teams often define a maximum acceptable latency for a feature, called a **latency budget** or **SLA (Service Level Agreement)**.

```text
Example SLA: 95% of responses must complete in under 3 seconds
```

Every design choice — model size, number of tool calls, reasoning depth — is then evaluated against whether it fits inside this budget.

---

# Best Practice

Optimize for the user's experience.

Not every question needs:

- ReAct
- Reflexion
- Multiple Agents

Simple tasks should receive simple solutions.

---

# Common Beginner Mistakes

### Mistake 1

Using complex workflows for every request.

Many questions need only one LLM call.

---

### Mistake 2

Ignoring tool latency.

The LLM may respond quickly,

but external APIs can be slow.

---

### Mistake 3

Optimizing only for accuracy.

A perfect answer delivered too late may provide a poor user experience.

---

### Mistake 4

Running independent steps sequentially.

If two tool calls don't depend on each other, running them one after another wastes time that parallelization could save.

---

### Mistake 5

Not streaming responses for long outputs.

Users perceive a system as slow even when total time is reasonable, if they see nothing until the very end.

---

# Interview Tip ⭐

A common interview question is:

> **How can you reduce latency in an AI application?**

A good answer is:

Reduce unnecessary LLM calls, retrieve only relevant context, minimize tool usage, use smaller models when appropriate, stream responses, cache repeated queries, and execute independent tasks in parallel whenever possible.

---

# Where is this Used?

- ChatGPT
- Claude
- Gemini
- LangChain
- LangGraph
- OpenAI Agents SDK
- Google ADK
- Enterprise AI Applications

---

# Key Takeaways

- Latency is the time required to generate a response.
- It can be broken down into Time to First Token, total response time, and tokens per second.
- Multiple LLM calls and tool usage increase latency.
- Streaming improves perceived latency even without reducing total time.
- Caching and parallelizing independent calls both cut real latency.
- Latency budgets (SLAs) help teams design within acceptable response times.
- Faster isn't always better, but users expect responsive systems.
- Balance latency with accuracy based on the application's needs.
- Good AI Engineers optimize for both quality and user experience.

---

