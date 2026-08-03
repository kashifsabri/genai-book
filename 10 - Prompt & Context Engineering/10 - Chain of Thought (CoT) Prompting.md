
## Learning Objectives

By the end of this chapter, you will understand:

- What Chain of Thought (CoT) Prompting is
- Why it improves reasoning
- When to use it
- When not to use it
- Why CoT actually works, mechanically, given how the model generates tokens
- The difference between Zero-shot CoT and Few-shot CoT
- Why CoT's visible reasoning isn't always a faithful account of how the model got there
- How prompted CoT differs from built-in "reasoning models"

---

# Introduction

Some questions are easy.

Example

```text
What is the capital of India?
```

The model can answer immediately.

But some questions require reasoning.

Example

```text
A shop gives a 20% discount on a ₹1000 product.

After the discount,

a 10% GST is added.

What is the final price?
```

Instead of jumping to the answer,

it's better to solve it **step by step**.

This idea is called **Chain of Thought Prompting**.

---

# What is Chain of Thought Prompting?

Chain of Thought (CoT) Prompting encourages the model to solve a problem through intermediate reasoning steps before giving the final answer.

Think of it like solving a math problem on paper.

You don't write only the final answer.

You show the steps.

---

# Example

Without CoT

```text
What is 25 × 18?
```

The model gives:

```text
450
```

Correct.

But you don't know how it reached that answer.

---

# With CoT

Prompt

```text
Solve the problem step by step.

What is 25 × 18?
```

Response

```text
25 × 18
=
25 × (20 - 2)
=
500 - 50
=
450
```

The reasoning is visible.

---

# Why Does CoT Work?

Breaking a complex problem into smaller steps reduces mistakes.

Instead of solving everything at once,

the model solves one step at a time.

```text
Problem

↓

Step 1

↓

Step 2

↓

Step 3

↓

Final Answer
```

---

# The Mechanical Reason CoT Works

Recall from an earlier chapter that generation is **autoregressive** — each new token is predicted based on every token that came before it, including tokens the model just generated itself.

This explains precisely why CoT helps:

```text
Without CoT → model must "arrive" at the correct final token almost immediately,
              with no intermediate tokens to build on

With CoT    → each reasoning step becomes additional input for predicting the next step,
              effectively giving the model more "working space" to get to the right answer
```

In other words, CoT isn't just a presentation choice — it changes what information is available to the model at each prediction step. Asking directly for the final answer forces the model to compress all the reasoning into a single leap; writing intermediate steps lets each step condition on the ones before it, the same way a human benefits from writing down work instead of doing it all in their head.

---

# Zero-shot CoT vs Few-shot CoT

You saw a preview of this in the Zero-shot chapter — worth making the distinction explicit now.

```text
Zero-shot CoT   → no worked examples given, just a trigger phrase
                  e.g. "Let's think step by step."

Few-shot CoT     → one or more full worked examples are shown, demonstrating
                  the exact reasoning style expected, before the real question
```

Few-shot CoT

```text
Q: A store has 10 apples and sells 3. How many are left?
A: The store starts with 10 apples. It sells 3. 10 - 3 = 7. The answer is 7.

Q: A shop gives a 20% discount on a ₹1000 product, then adds 10% GST. What is the final price?
A:
```

Few-shot CoT tends to produce more consistent reasoning formats than zero-shot CoT, at the cost of a longer, more expensive prompt — the same tradeoff seen throughout the few-shot chapter.

---

# Does the Visible Reasoning Always Reflect What Actually Happened?

This is a nuance worth knowing for a deeper interview conversation.

The reasoning steps a model writes out under CoT are still just generated tokens — text the model produces because it's a plausible continuation, not a transcript of some separate internal reasoning process. Research on CoT faithfulness has found cases where:

- A model reaches the same final answer even when the written reasoning steps are altered or subtly wrong
- The stated reasoning doesn't always match what actually determined the final answer

Practical implication: CoT output is genuinely useful for improving accuracy and for debugging _what the model considered_, but it shouldn't be treated as a guaranteed, fully faithful window into the model's "true" reasoning process.

---

# Prompted CoT vs Built-in Reasoning Models

It's worth distinguishing what this chapter covers from a related but different thing you may have heard about.

```text
Prompted CoT (this chapter)
→ A prompting technique: you ask a standard model to "think step by step"
  in its visible output

Reasoning models (e.g. models with an extended "thinking" mode)
→ Models specifically trained to perform extended internal reasoning
  before producing a final answer, often with much of that reasoning
  hidden or handled differently from the visible response
```

Prompted CoT is something _you_ trigger through wording, on any capable model. Built-in reasoning behavior is something the model is trained to do more automatically, sometimes without you needing to ask for it explicitly. Knowing this distinction is useful if you're asked to compare "prompting a model to reason" versus "using a model designed for reasoning."

---

# When Should You Use CoT?

Use CoT for tasks involving:

- Mathematics
- Logical reasoning
- Multi-step calculations
- Planning
- Decision making

---

# When Should You Avoid CoT?

Don't use CoT for simple tasks.

Example

```text
Translate "Hello" into Hindi.
```

There is no reasoning involved.

Adding CoT only makes the response longer.

---

# Real-World Example

Prompt

```text
A customer bought 3 products costing ₹500 each.

They received a 10% discount.

Calculate the final amount.

Think step by step.
```

The model is more likely to calculate correctly than if it jumps directly to the answer.

---

# Does CoT Make the Model Smarter?

No.

The model doesn't gain new knowledge.

CoT simply encourages the model to organize its reasoning before answering.

---

# Best Practice

Use CoT only when reasoning is required.

Examples:

✅ Math

✅ Business calculations

✅ Planning

✅ Logic puzzles

Avoid it for simple factual questions.

---

# Common Beginner Mistakes

### Mistake 1

Using CoT for every prompt.

Not every task requires reasoning.

---

### Mistake 2

Expecting CoT to fix incorrect knowledge.

If the model doesn't know a fact,

reasoning won't invent the correct answer.

---

### Mistake 3

Ignoring the extra cost.

CoT usually generates more tokens,

which increases:

- Cost
- Latency

---

### Mistake 4

Treating the model's written reasoning as a guaranteed, fully accurate explanation of how it reached its answer.

It's a helpful signal, not a certainty — CoT output can be unfaithful to the model's actual internal process.

---

# Interview Tip ⭐

A common interview question is:

> **When would you use Chain of Thought Prompting?**

Answer:

Use it for **multi-step reasoning tasks** such as mathematics, planning, logical reasoning, and complex decision making.

Avoid it for simple factual or translation tasks because it increases token usage without improving the answer.

---

# Interview Tip ⭐

Another common question:

> **Why does asking a model to "think step by step" actually improve its answers?**

Answer:

Because generation is autoregressive — each token is predicted using everything generated so far. Writing intermediate reasoning steps gives the model more context to condition on before committing to a final answer, instead of forcing it to jump straight to a conclusion in one step.

---

# Key Takeaways

- Chain of Thought (CoT) encourages step-by-step reasoning.
- It works because generation is autoregressive — intermediate steps give the model more to condition on before the final answer.
- It is useful for complex problems.
- Zero-shot CoT uses a trigger phrase; Few-shot CoT uses worked examples — the same cost/consistency tradeoff as Zero-shot vs Few-shot applies.
- Visible CoT reasoning isn't always a fully faithful account of the model's actual process — useful, but not a guarantee.
- Prompted CoT (a technique) is different from built-in reasoning models (trained to reason more automatically).
- It improves reasoning, not knowledge.
- It increases token usage and response length.
- Use it only when reasoning is required.

---

