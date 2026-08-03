

## Learning Objectives

By the end of this chapter, you will understand:

- What Prompt Patterns are
- Why they are used in production AI systems
- The most common prompt patterns
- How to choose the right pattern

---

# Introduction

Imagine you're building an AI application.

Should every prompt be written from scratch?

No.

Just like software engineers use **design patterns**,

AI engineers use **Prompt Patterns**.

These are proven ways of solving common AI problems.

---

# What are Prompt Patterns?

Prompt Patterns are reusable prompt designs used to solve common tasks.

Instead of inventing a new prompt every time,

developers reuse patterns that are already known to work.

---

# Why are Prompt Patterns Important?

Prompt Patterns help developers build AI applications that are:

- Consistent
- Reliable
- Easy to maintain
- Easy to scale

Instead of solving the same problem repeatedly,

they reuse proven solutions.

---

# Common Prompt Patterns

## 1. Role Pattern

Tell the AI who it should act as.

Example

```text
You are a Senior Java Developer.

Explain Dependency Injection.
```

This guides the style and expertise of the response.

---

## 2. Structured Output Pattern

Tell the AI exactly how to format the answer.

Example

```text
Return the answer as JSON.
```

Useful for APIs and automation.

---

## 3. Step-by-Step Pattern

Ask the AI to solve the problem one step at a time.

Example

```text
Solve this problem step by step.
```

Useful for reasoning tasks.

---

## 4. Template Pattern

Use variables instead of writing new prompts.

Example

```text
Write an email to {name}
about {topic}.
```

This makes prompts reusable.

---

## 5. Retrieval Pattern

Provide external information before asking the AI to answer.

Example

```text
Context:

{Retrieved Document}

Question:

{User Question}
```

This is widely used in RAG systems.

---

## 6. Few-Shot Pattern

Show the AI examples of correct input-output pairs before asking it to solve a new one.

Example

```text
Q: Translate "Good morning" to French.
A: Bonjour

Q: Translate "Thank you" to French.
A: Merci

Q: Translate "See you later" to French.
A:
```

Useful when the desired style, tone, or format is hard to describe in words but easy to show.

---

## 7. Constraint Pattern

Explicitly tell the AI what it must not do or what limits it must respect.

Example

```text
Answer the question below.

Do not mention competitor products.
Do not exceed 50 words.
Do not make up information you are unsure of.
```

Useful for compliance, safety, and brand-guideline requirements.

---

## 8. Self-Verification Pattern

Ask the AI to check its own answer before returning it.

Example

```text
Solve the problem.

Then review your solution for mistakes before giving the final answer.
```

Useful for math, code, and other tasks where errors are costly.

---

## 9. Tool-Use Pattern

Let the AI call external tools or functions instead of answering from memory alone.

Example

```text
You have access to a "get_weather(city)" function.

If the user asks about weather, call the function
instead of guessing the answer.
```

Useful when the AI needs live data or needs to take real actions, not just generate text.

---

# Choosing the Right Pattern

Different tasks need different patterns.

| Task | Pattern |
|------|----------|
| Customer Support | Retrieval Pattern |
| Email Writing | Template Pattern |
| Coding | Role + Step-by-Step |
| Data Extraction | Structured Output |
| Question Answering | Retrieval + Structured Output |
| Matching a Specific Style/Tone | Few-Shot Pattern |
| Compliance-Sensitive Responses | Constraint Pattern |
| Math / Code Generation | Step-by-Step + Self-Verification |
| Live Data / Actions | Tool-Use Pattern |

There is no single pattern that works for every task.

---

# Real-World Example

Suppose you're building an AI HR Assistant.

Instead of writing one huge prompt,

you combine multiple patterns.

```text
Role

↓

HR Assistant

+

Retrieved Policy

+

Structured Output

↓

Final Response
```

Production AI systems often combine several patterns together.

---

# Industry Insight ⭐

Most enterprise AI applications don't use just one prompt pattern.

They combine multiple patterns based on the task.

For example:

```text
Role

+

Context

+

Tool Output

+

Structured Output

↓

LLM
```

This produces more reliable and consistent responses.

---

# Best Practices

Choose the simplest pattern that solves the problem.

Don't make prompts more complicated than necessary.

---

Reuse prompt patterns across similar applications.

This makes maintenance much easier.

---

Test every pattern before deploying it.

A pattern that works for one task may not work for another.

---

# Common Beginner Mistakes

### Mistake 1

Trying to solve every problem with one giant prompt.

Break complex tasks into smaller patterns.

---

### Mistake 2

Using advanced patterns for simple tasks.

Simple questions often need simple prompts.

---

### Mistake 3

Ignoring context.

Even the best prompt pattern fails without the right information.

---

### Mistake 4

Describing the desired format in words when an example would work better.

If a style is hard to explain, show a Few-Shot example instead.

---

### Mistake 5

Letting the AI answer from memory when it should be calling a tool.

This leads to outdated or made-up data. Use the Tool-Use Pattern instead.

---

# Interview Tip ⭐

A common interview question is:

> **What are Prompt Patterns?**

A good answer is:

Prompt Patterns are reusable prompt designs that solve common AI tasks. They help developers build AI applications that are more consistent, maintainable, and reliable.

---

# Where is this Used?

- ChatGPT Applications
- Claude
- Gemini
- LangChain
- LangGraph
- OpenAI Agents SDK
- Google ADK
- Enterprise AI Applications

---

# Key Takeaways

- Prompt Patterns are reusable prompt designs.
- They help solve common AI tasks efficiently.
- Different tasks require different patterns.
- Production AI systems often combine multiple patterns.
- Few-shot examples work better than long explanations for style or format.
- Constraint and self-verification patterns reduce risky or incorrect answers.
- Tool-use patterns let the AI act on live data instead of guessing.
- Prompt Patterns improve consistency and maintainability.

---

