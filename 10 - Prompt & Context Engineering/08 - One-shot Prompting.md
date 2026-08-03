

## Learning Objectives

By the end of this chapter, you will understand:

- What One-shot Prompting is
- Why one example improves the output
- When to use it
- When not to use it
- Why a single example can also mislead the model if chosen poorly
- How to pick a representative example instead of a convenient one
- Where One-shot fits compared to Zero-shot and Few-shot

---

# Introduction

Suppose you want the AI to write product descriptions in your company's style.

Simply saying:

```text
Write a product description.
```

may not produce the style you want.

Instead,

show the AI **one example**.

This is called **One-shot Prompting**.

---

# What is One-shot Prompting?

One-shot Prompting means giving the model:

- One example
- Then asking it to perform a similar task

The model learns the pattern from that single example.

---

# Structure

```text
Example

↓

New Input

↓

AI Output
```

The example acts as a guide.

---

# Example

Prompt

```text
Example

Input:
Laptop

Output:
A lightweight laptop designed for students and professionals.

----------------------

Now write for:
Smartphone
```

Possible Output

```text
A powerful smartphone designed for fast performance and everyday use.
```

The AI follows the style of the example.

---

# Why Does It Work?

The example tells the model:

- Tone
- Writing style
- Format
- Level of detail

Instead of guessing,

the model imitates the pattern.

---

# Without One-shot

Prompt

```text
Write a product description for a smartphone.
```

Possible outputs may vary a lot.

---

# With One-shot

Prompt

```text
Example
Laptop

↓

A lightweight laptop designed for students and professionals.

----------------------

Now write for:
Smartphone
```

The output becomes more consistent.

---

# The Hidden Risk: One Example Can Also Mislead

This is the most important thing to understand beyond the basics.

With only one example, the model has no way to tell what's a **general pattern** and what's just a **coincidence of that one instance**. It will often generalize from details you didn't intend to teach.

Example of the trap:

```text
Example
Input: Laptop
Output: A lightweight laptop designed for students and professionals, priced under $999.
```

If your one example happens to mention a price, a specific audience, or an unusual sentence structure, the model may treat _all of that_ as the required pattern — not just the parts you actually cared about. With only one data point, the model can't distinguish signal from noise.

This is why One-shot works best when:

- The pattern you want is simple and unambiguous (e.g. one clear output format)
- The single example doesn't contain accidental, non-representative details

And why One-shot is risky when:

- The task has edge cases the one example doesn't cover
- The example itself is unusual, overly simple, or overly complex compared to typical inputs

---

# Choosing a Representative Example

Don't just grab the first example that comes to mind — pick (or write) one that reflects the "typical" case, not an edge case.

```text
Good example    → typical length, typical complexity, typical tone for the real task
Bad example      → unusually short/long, an edge case, or overly specific to one scenario
```

If you're not confident one example can represent the whole task, that's usually a sign you need Few-shot Prompting instead — covered in the next chapter.

---

# When Should You Use One-shot Prompting?

Use it when you want:

- A specific writing style
- A specific response format
- Consistent outputs
- Standardized responses

One-shot tends to work best for **format demonstration** — for example, showing the model exactly what shape of JSON or exact output structure you expect — rather than teaching a nuanced or highly variable pattern.

---

# Real-World Example

Suppose you're building an AI email assistant.

Example

```text
Customer Question:
Can I return my order?

Reply:
Yes.
You can return your order within 30 days.
```

Now ask

```text
Customer Question:
Can I exchange my order?
```

The AI follows the same response style.

---

# One-shot vs Zero-shot

|Zero-shot|One-shot|
|---|---|
|No examples|One example|
|Relies on model knowledge|Learns from one example|
|Simpler|More consistent|

---

# Best Practice

Choose a **good example**.

The AI assumes your example is the correct pattern.

A poor example often leads to poor outputs.

Also use delimiters (as covered in the Prompt Anatomy chapter) to clearly separate the example from the new input — otherwise the model can blur where the example ends and the actual task begins.

---

# Common Beginner Mistakes

### Mistake 1

Using an incorrect example.

The model will copy bad patterns.

---

### Mistake 2

Using an example unrelated to the task.

The example should closely match the task you want the AI to perform.

---

### Mistake 3

Thinking one example teaches new knowledge.

It doesn't.

The example teaches **format and style**, not new facts.

---

### Mistake 4

Picking an example that's an edge case instead of a typical case.

An unusual example teaches the model an unusual (and often wrong) pattern.

---

# Quick Self-Check Questions

- Why can a single example accidentally teach the model something you didn't intend?
- What kind of task is One-shot best suited for — format demonstration or nuanced pattern learning? Why?
- If you're unsure whether one example is enough to represent a task, what should you do instead?

---

# Key Takeaways

- One-shot Prompting provides exactly one example.
- The example guides the model's style and format.
- It produces more consistent results than Zero-shot.
- A single example can also mislead the model if it contains unintended or non-representative details.
- Choose a typical, representative example — not an edge case or an unusually simple/complex one.
- One-shot works best for simple format demonstration; more nuanced patterns need Few-shot.
- Use high-quality examples, and delimiters to separate them clearly from the new input.
- One-shot improves guidance, not the model's knowledge.

---

