

## Learning Objectives

By the end of this chapter, you will understand:

- What Prompt Chaining is
- Why it is used
- When to use it
- How it differs from Least-to-Most Prompting

---

# Introduction

Suppose you ask an AI to:

```text
Write a blog on Artificial Intelligence.
```

The AI can do it in one prompt.

But what if you want a **high-quality blog**?

Instead of doing everything at once,

you can split the work into multiple prompts.

Example

```text
Generate an outline.

↓

Write each section.

↓

Review the article.

↓

Improve the writing.

↓

Generate the final blog.
```

This technique is called **Prompt Chaining**.

---

# What is Prompt Chaining?

Prompt Chaining is a technique where the **output of one prompt becomes the input to the next prompt**.

Instead of solving everything in one step,

we solve it through multiple prompts.

---

# Visual Flow

```text
Prompt 1

↓

Output

↓

Prompt 2

↓

Output

↓

Prompt 3

↓

Final Answer
```

Each prompt has one clear responsibility.

---

# Example

Suppose we want to summarize a research paper.

Instead of one large prompt,

we create a chain.

```text
Prompt 1

Extract the important points.

↓

Prompt 2

Summarize the important points.

↓

Prompt 3

Rewrite the summary for beginners.
```

Each prompt improves the previous output.

---

# Another Example

Building an AI Coding Assistant.

```text
Prompt 1

Understand the requirement.

↓

Prompt 2

Generate the code.

↓

Prompt 3

Review the code.

↓

Prompt 4

Fix any issues.
```

Instead of one huge prompt,

the work is divided into stages.

---

# Why Does Prompt Chaining Work?

Large tasks are often easier when divided into smaller tasks.

Each prompt focuses on **one responsibility**.

This improves:

- Accuracy
- Maintainability
- Reusability

---

# Prompt Chaining vs Least-to-Most

They look similar,

but they solve different problems.

| Least-to-Most | Prompt Chaining |
|---------------|-----------------|
| Breaks one problem into smaller sub-problems | Connects multiple prompts together |
| Focuses on planning | Focuses on workflow |
| Usually one conversation | Multiple prompt stages |

Think of it like this.

Least-to-Most answers:

> **"How should I divide this problem?"**

Prompt Chaining answers:

> **"How should multiple prompts work together?"**

---

# Real-World Example

Many enterprise AI applications use Prompt Chaining.

Customer Support

```text
Customer Question

↓

Detect Language

↓

Retrieve Documents

↓

Generate Answer

↓

Check Tone

↓

Return Response
```

Every stage uses a separate prompt.

---

# When Should You Use It?

Use Prompt Chaining when:

- The task is complex
- Different stages require different prompts
- You want better control over the workflow
- Each step can be validated independently

---

# When Should You Avoid It?

Don't use Prompt Chaining for simple tasks.

Example

```text
What is Java?
```

One prompt is enough.

Multiple prompts would only increase:

- Cost
- Latency
- Complexity

---

# Best Practice

Each prompt should have **one responsibility**.

Bad

```text
Analyze,

summarize,

translate,

and explain.
```

Better

```text
Prompt 1

Analyze.

↓

Prompt 2

Summarize.

↓

Prompt 3

Translate.
```

Small prompts are easier to maintain and debug.

---

# Common Beginner Mistakes

### Mistake 1

Creating very long chains.

More prompts mean:

- More API calls
- Higher cost
- More latency

Keep the chain as short as possible.

---

### Mistake 2

Combining unrelated tasks into one prompt.

Each prompt should perform one job.

---

### Mistake 3

Not validating intermediate outputs.

If Prompt 1 produces incorrect output,

every following prompt may also fail.

Check important intermediate results.

---

# Interview Tip ⭐

A common interview question is:

> **Why use Prompt Chaining instead of one large prompt?**

A good answer is:

Prompt Chaining divides a complex workflow into smaller, manageable steps.

Each step has a single responsibility,

making the system easier to debug, maintain, and improve.

---

# Key Takeaways

- Prompt Chaining connects multiple prompts into a workflow.
- The output of one prompt becomes the input to the next.
- It is useful for complex, multi-stage tasks.
- Each prompt should have one clear responsibility.
- Prompt Chaining is widely used in production AI systems.

---

