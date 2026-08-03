
## Learning Objectives

By the end of this chapter, you will understand:

- What Self-Consistency Prompting is
- How it differs from Chain of Thought (CoT)
- When to use it
- Its advantages and limitations
- Why temperature settings matter for Self-Consistency to work at all
- How this compares to ensembling in traditional machine learning
- Why it works well for math/logic but poorly for open-ended generation

---

# Introduction

In the previous chapter,

we learned **Chain of Thought (CoT)**.

The model solved a problem by reasoning step by step.

But here's a question.

> What if the model makes a mistake in its reasoning?

If there is only **one reasoning path**,

the final answer may also be wrong.

Instead,

what if the model solved the problem **multiple times** and we selected the most common answer?

This idea is called **Self-Consistency Prompting**.

---

# What is Self-Consistency?

Self-Consistency means:

- Generate multiple reasoning paths.
- Compare the final answers.
- Choose the answer that appears most often.

Instead of trusting one solution,

we trust the most consistent one.

---

# Visual Flow

```text
Question

↓

Reasoning Path 1

↓

Answer A

----------------

Reasoning Path 2

↓

Answer A

----------------

Reasoning Path 3

↓

Answer B

----------------

Most Common Answer

↓

Answer A
```

---

# Example

Question

```text
A shop gives a 20% discount on ₹1000.

Then 10% GST is added.

What is the final price?
```

The model reasons several times.

Run 1

```text
₹880
```

Run 2

```text
₹880
```

Run 3

```text
₹900
```

The most common answer is:

```text
₹880
```

That becomes the final response.

---

# Why Does It Work?

Sometimes,

one reasoning path contains a mistake.

Generating multiple reasoning paths reduces the chance of relying on a single incorrect solution.

Think of it like asking three experienced developers to review the same code.

If two agree,

you're usually more confident in that solution.

---

# You Need Randomness for This to Work

This is an important implementation detail that's easy to miss.

Recall from earlier chapters that **temperature** controls how random the model's token predictions are. If you run the same prompt multiple times at temperature 0, the model will tend to produce the **same reasoning path and the same answer every time** — there's nothing to compare, and Self-Consistency provides no benefit.

```text
Temperature 0        → deterministic, repeated runs mostly give the same output
                        (Self-Consistency has little to gain from repeating this)

Temperature 0.5-1.0   → varied reasoning paths across runs
                        (this is what Self-Consistency actually needs)
```

So in practice, Self-Consistency requires deliberately running the model at a **higher temperature** than you might normally use for a "correct" answer, specifically so the multiple runs can genuinely differ from each other.

---

# Chain of Thought vs Self-Consistency

|Chain of Thought|Self-Consistency|
|---|---|
|One reasoning path|Multiple reasoning paths|
|One final answer|Most common answer|
|Faster|More accurate (for reasoning tasks)|
|Lower cost|Higher cost|

---

# The Parallel to Ensembling in Traditional ML

If you have a traditional machine learning background, this concept isn't new — it's the same underlying idea as **ensemble methods** like bagging or majority-vote classifiers.

```text
Traditional ML ensembling → train multiple models (or same model on different data samples),
                             combine predictions by majority vote or averaging

Self-Consistency          → run the same model multiple times with randomness,
                             combine answers by majority vote
```

Same core principle: independent (or semi-independent) attempts at a problem, combined, tend to be more reliable than any single attempt — as long as the errors aren't all identical/correlated. This is a good analogy to reach for in interviews if asked "why does this work" from a more theoretical angle.

---

# Why This Works Well for Math, But Not for Open-Ended Writing

This is a limitation worth being explicit about.

Self-Consistency relies on being able to compare final answers and pick the most common one. That's straightforward when the answer is a single number, a label, or a short discrete value:

```text
"₹880" vs "₹880" vs "₹900"  → easy to compare, easy to vote
```

It breaks down for open-ended generation:

```text
Three different essays, poems, or explanations of a concept
→ there's no clean way to say which two "match" and vote for one
```

For open-ended tasks, you'd need a different strategy — for example, generating multiple drafts and using a separate evaluation step (sometimes another model call) to judge which is best, rather than a literal majority vote. That's a related but distinct pattern from Self-Consistency as originally defined.

---

# Practical Implementation Notes

If you were implementing this against an API rather than just describing it conceptually:

```text
1. Send the same prompt N times (commonly 3-10 runs) with temperature > 0
2. Extract the final answer from each response
3. Tally identical/equivalent answers
4. Return the majority answer (or flag "no clear majority" if answers are too spread out)
```

Cost scales roughly linearly with N — running 5 reasoning paths costs roughly 5x the tokens of a single CoT call, so this technique is deliberately reserved for cases where the accuracy gain is worth the added cost.

---

# When Should You Use It?

Use Self-Consistency for:

- Mathematics
- Logical reasoning
- Complex planning
- Decision making

These tasks benefit from multiple reasoning attempts.

---

# When Should You Avoid It?

Don't use it for simple tasks.

Example

```text
Translate:

Hello
```

There is no benefit in generating multiple translations.

It only increases:

- Cost
- Latency

---

# Does It Retrain the Model?

No.

The model is not learning.

It simply generates multiple possible solutions and compares them.

---

# Best Practice

Think of Self-Consistency as a quality check.

Use it only when:

- Accuracy is more important than speed.
- The problem requires reasoning.
- The answer format is easy to compare (numbers, labels, short discrete values).

---

# Common Beginner Mistakes

### Mistake 1

Confusing Self-Consistency with Chain of Thought.

Remember:

- CoT = One reasoning path.
- Self-Consistency = Multiple reasoning paths.

---

### Mistake 2

Using it for every prompt.

Most everyday tasks don't need it.

---

### Mistake 3

Ignoring the extra cost.

Running multiple reasoning paths means:

- More tokens
- More API calls
- More latency

---

### Mistake 4

Running all attempts at temperature 0.

Without enough randomness between runs, the reasoning paths converge to the same answer, defeating the purpose of generating multiple paths.

---

### Mistake 5

Applying it to open-ended generation tasks (essays, creative writing) where answers can't be cleanly voted on.

---

# Interview Tip ⭐

A very common interview question is:

> **What is the difference between Chain of Thought and Self-Consistency?**

A good answer is:

- **Chain of Thought** generates **one** reasoning path before producing an answer.
- **Self-Consistency** generates **multiple** reasoning paths and chooses the most consistent final answer.

Self-Consistency is usually more accurate for complex reasoning but costs more.

---

# Interview Tip ⭐

Another good question to be ready for:

> **What setting is required for Self-Consistency to actually provide a benefit, and why?**

Answer:

Temperature above 0. At temperature 0, repeated runs of the same prompt tend to produce the same deterministic output, so there's no diversity of reasoning paths to compare — Self-Consistency needs enough randomness across runs for majority voting to be meaningful.

---

# Key Takeaways

- Self-Consistency extends Chain of Thought.
- It generates multiple reasoning paths.
- The most common answer is selected.
- It requires temperature above 0 — without randomness, repeated runs converge to the same answer.
- It's conceptually similar to ensembling in traditional machine learning.
- It works well when answers are easy to compare (numbers, labels) but poorly for open-ended generation.
- It improves reliability for complex reasoning tasks.
- Cost scales roughly linearly with the number of reasoning paths generated.
- It should be used only when higher accuracy justifies the additional cost.

---

