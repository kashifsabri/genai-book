

## Learning Objectives

By the end of this chapter, you will understand:

- What Few-shot Prompting is
- Why multiple examples improve consistency
- When to use it
- Its limitations
- How many examples is actually "enough" (diminishing returns)
- Why class balance among examples matters for classification tasks
- How Few-shot compares to fine-tuning, and when to choose one over the other
- What "dynamic few-shot" is, and how it connects to RAG

---

# Introduction

Suppose you want the AI to classify customer reviews.

You give it one example.

It works.

But what if the reviews are very different?

One example may not be enough.

Instead,

give the AI **multiple examples**.

This is called **Few-shot Prompting**.

---

# What is Few-shot Prompting?

Few-shot Prompting means providing **a few examples** before asking the model to solve a new problem.

The examples help the model recognize the expected pattern.

---

# Structure

```text
Example 1

↓

Example 2

↓

Example 3

↓

New Input

↓

AI Output
```

---

# Example

Prompt

```text
Example 1

Input:
Apple

Output:
Fruit

----------------

Example 2

Input:
Carrot

Output:
Vegetable

----------------

Example 3

Input:
Potato

Output:
Vegetable

----------------

Now classify:

Banana
```

Response

```text
Fruit
```

The model learns the pattern from several examples.

---

# Why Does Few-shot Work?

Instead of guessing,

the model observes:

- The format
- The pattern
- The expected output

The more representative the examples,

the better the output.

---

# How Many Examples Is "Enough"?

There's no universal number, but it's worth knowing the general pattern seen in practice and research:

```text
0 examples  → Zero-shot
1 example   → One-shot
2-5 examples → often captures most of the benefit
5-10+ examples → diminishing returns; sometimes even hurts performance
```

Adding more examples helps up to a point, but past a certain count you mostly add tokens (cost + latency) without meaningfully improving output. Very long example sets can also push the actual task further from the end of the prompt, working against the "recency" effects discussed in earlier chapters.

Practical approach: start with 3-5 well-chosen examples, test, and only add more if you can show it actually improves results on a test set — don't add examples "just in case."

---

# Class Balance Matters in Classification Tasks

This is a subtle failure mode worth knowing for interviews.

If you're using Few-shot for a classification task and your examples are imbalanced:

```text
Example 1: Positive
Example 2: Positive
Example 3: Positive
Example 4: Negative
```

The model can pick up on the _frequency_ of labels, not just their meaning — and start leaning toward whichever label appeared more often in your examples, regardless of the actual input. This mirrors a well-known issue in traditional ML with imbalanced training data.

Best practice: keep roughly equal representation across the categories/labels you want the model to predict.

---

# Real-World Example

Suppose you're building an AI sentiment classifier.

Prompt

```text
Review:
The food was amazing.

Sentiment:
Positive

----------------

Review:
The delivery was very late.

Sentiment:
Negative

----------------

Review:
The app is easy to use.

Sentiment:
Positive

----------------

Review:
The customer support was rude.

Sentiment:
?
```

Response

```text
Negative
```

The model follows the examples.

---

# Zero-shot vs One-shot vs Few-shot

|Technique|Examples Given|
|---|---|
|Zero-shot|0|
|One-shot|1|
|Few-shot|2 or more|

Think of it like teaching someone.

```text
Zero-shot

↓

"No example."

One-shot

↓

"Here's one example."

Few-shot

↓

"Here are several examples."
```

---

# When Should You Use Few-shot?

Use it when:

- You need consistent outputs
- The task has a specific format
- The task is difficult
- The model struggles with Zero-shot

Examples:

- Classification
- Data extraction
- Email generation
- Report writing
- Code generation

---

# When Should You Avoid Few-shot?

Few-shot prompts are longer.

Longer prompts mean:

- More tokens
- Higher cost
- More latency

If Zero-shot already produces good results,

there is no need to use Few-shot.

---

# Few-shot vs Fine-tuning

This is a very common interview question, so it's worth having a clear answer ready.

```text
Few-shot Prompting              Fine-tuning
───────────────────────────────────────────────
No training required            Requires training data + a training job
Examples live in the prompt     Examples are baked into model weights
Costs tokens every request      Costs tokens only for the actual input
Easy to update (edit prompt)    Requires retraining to update
Works immediately               Takes time and infra to set up
Limited by context window       Not limited by context window
```

General guidance:

- Start with Few-shot — it's fast, cheap to iterate on, and requires no infrastructure
- Consider fine-tuning when: you have a large, stable dataset of examples, the task is high-volume enough that per-request example tokens become expensive, or you need behavior that's hard to reliably induce through prompting alone (very specific tone, domain-specific formats used constantly)

In interviews, the key insight to demonstrate is that few-shot and fine-tuning solve a similar problem (teaching the model a pattern) through very different mechanisms — one through the prompt at inference time, one by updating weights ahead of time.

---

# Dynamic Few-shot (Connecting to RAG)

In production systems, the examples shown to the model often aren't fixed — they're selected at runtime based on the current input. This is sometimes called **dynamic few-shot** or **example retrieval**.

```text
User input arrives

↓

Search a database of past examples using embedding similarity

↓

Retrieve the K most similar examples

↓

Insert those examples into the prompt

↓

Send to the model
```

This is essentially RAG applied to examples instead of documents — instead of always showing the same 3 examples, you show the ones most relevant to the specific input. It tends to outperform a fixed example set, especially when the task covers a wide variety of input types.

---

# Best Practice

Use **high-quality and diverse examples**.

Bad

```text
Three almost identical examples.
```

Better

```text
Three different examples that cover different situations.
```

This helps the model generalize better.

---

# Common Beginner Mistakes

### Mistake 1

Adding too many examples.

More examples increase cost and may not improve performance.

---

### Mistake 2

Using inconsistent examples.

If every example follows a different format,

the model becomes confused.

---

### Mistake 3

Choosing poor examples.

The model learns patterns from the examples you provide.

Poor examples lead to poor outputs.

---

### Mistake 4

Using an imbalanced set of examples for classification tasks.

Unequal representation of labels can bias the model toward the majority label in your examples.

---

# Interview Tip ⭐

A common interview question is:

> **Why does Few-shot Prompting often perform better than Zero-shot?**

Answer:

Because the examples demonstrate the expected **pattern, format, and style**, allowing the model to perform **In-Context Learning** without changing its weights.

Remember:

**The model is not being retrained.**

It is simply learning from the examples provided in the prompt.

---

# Interview Tip ⭐

Another common question:

> **When would you choose Few-shot Prompting over Fine-tuning, or vice versa?**

Answer:

Few-shot is preferred when you need to move fast, the task changes often, or you don't have enough data to fine-tune reliably. Fine-tuning is preferred at scale — when the same pattern is needed on a very high volume of requests (so per-request example tokens add up), or when prompting alone can't reliably produce the desired behavior.

---

# Key Takeaways

- Few-shot Prompting uses multiple examples.
- It improves consistency and output quality.
- It is useful for complex or structured tasks.
- More examples help only up to a point — typically 3-5 captures most of the benefit, with diminishing returns after that.
- For classification tasks, keep examples balanced across labels to avoid biasing the model toward one class.
- Few-shot and fine-tuning solve similar problems differently — few-shot through the prompt, fine-tuning through updated weights.
- Dynamic few-shot (retrieving the most relevant examples per input) is a common production pattern, closely related to RAG.
- More examples also increase token usage and cost.
- Use Few-shot only when it provides a clear benefit.

---

