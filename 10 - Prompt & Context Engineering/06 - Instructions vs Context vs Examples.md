

## Learning Objectives

By the end of this chapter, you will understand:

- What instructions are
- What context is
- What examples are
- Why each one has a different purpose
- How to combine them effectively
- Why the word "context" means different things in different chapters — and how to keep them straight
- Why example order and quality can silently bias the model's output
- How this chapter sets up Zero-shot, One-shot, and Few-shot prompting

---

# Introduction

A good prompt is more than just a question.

It usually contains three important parts:

```text
Instructions

+

Context

+

Examples
```

Each part helps the model in a different way.

Understanding the difference is essential for writing effective prompts.

---

# 1. Instructions

Instructions tell the model **what to do**.

Examples

```text
Summarize the article.
```

```text
Translate this into Hindi.
```

```text
Write Python code.
```

Think of instructions as the **task**.

---

# 2. Context

Context gives the model **background information**.

Example

```text
The audience is beginner developers.
```

```text
The application is built using Spring Boot.
```

Context helps the model make better decisions.

Think of context as the **situation**.

---

# A Word of Caution: "Context" Has Multiple Meanings

You'll see the word "context" used in at least three different ways across this book, and it's worth separating them clearly so they don't get mixed up in an interview.

```text
Context (this chapter)   → background info you add to a prompt, e.g. "the audience is beginners"
Context Window            → the model's total token capacity (system prompt + history + docs + question)
Context Engineering        → the broader discipline of deciding WHAT information to feed the model at all
                             (retrieval, memory, tool outputs — not just wording)
```

All three are related, but they answer different questions. "Context" (this chapter) is about _what you write_. Context Window is about _how much fits_. Context Engineering is about _what you choose to include in the first place_.

---

# 3. Examples

Examples show the model **what the expected output looks like**.

Example

```text
Input:
Apple

Output:
Fruit

Input:
Carrot

Output:
Vegetable
```

Examples help the model recognize patterns.

Think of examples as **guidance**.

---

# Examples Teach by Demonstration, Not Just Description

This is worth calling out explicitly: examples don't just clarify instructions, they can _replace_ them.

If you show the model:

```text
Input: "This movie was amazing!" → Positive
Input: "I hated every minute." → Negative
```

...the model can often infer the task (sentiment classification) and the exact output format (a single word) even if you never explicitly wrote "classify the sentiment" or "respond with one word." This is called **in-context learning**, and it's the foundation of the Few-shot Prompting technique you'll see in a later chapter.

The practical implication: if your examples are inconsistent in format, the model will often copy that inconsistency rather than "figuring out" what you meant.

---

# Example Order and Quality Can Bias the Output

A subtle but important point: the model doesn't treat your examples as neutral reference material — it can pick up on patterns you didn't intend.

```text
Order bias   → if most of your examples show one type of answer, the model leans toward
               repeating that pattern, even when the actual answer should differ
Recency bias  → examples placed closer to the actual question tend to have more influence
               than ones placed earlier
```

Practical tip: keep your example set balanced across the categories/patterns you want the model to learn, and don't put all examples of one type together.

---

# Putting Them Together

```text
Instruction

↓

Explain Docker.

Context

↓

The audience is beginners.

Example

↓

Use simple language and bullet points.
```

Now the model knows:

- What to do
- Who it's for
- What the answer should look like

---

# Poor Prompt

```text
Explain Kubernetes.
```

The model has to guess:

- Audience
- Length
- Style

---

# Better Prompt

```text
Explain Kubernetes.

The audience is beginners.

Use simple English.

Return the answer as bullet points.
```

Now there is much less ambiguity.

---

# Real-World Example

Suppose you're building an AI coding assistant.

Instruction

```text
Generate a REST API.
```

Context

```text
Use Spring Boot 3 and Java 21.
```

Example

```text
Follow this coding style:

@Controller
@Service
@Repository
```

The model is much more likely to produce the desired result.

---

# Best Practice

Before sending a prompt, check:

✅ Is the task clear?

✅ Does the model have enough context?

✅ Would an example improve the output?

If the answer is yes,

include it.

---

# Common Beginner Mistakes

### Mistake 1

Giving instructions without context.

Example

```text
Write code.
```

What language?

What framework?

For what purpose?

---

### Mistake 2

Adding unnecessary context.

Only include information that helps complete the task.

---

### Mistake 3

Using poor examples.

The model often follows your examples.

Bad examples usually lead to bad outputs.

---

### Mistake 4

Using inconsistent example formatting.

If your examples don't follow the same structure, the model has no clear pattern to copy — inconsistency in examples usually produces inconsistency in output.

---

# Quick Self-Check Questions

- What's the difference between "context" as used in this chapter and a model's "context window"?
- Why might a model produce a biased answer even if your instructions were completely neutral?
- Why can examples sometimes make explicit instructions unnecessary?

---

# Key Takeaways

- **Instructions** tell the model what to do.
- **Context** provides background information.
- **Examples** demonstrate the expected output.
- The word "context" is overloaded across this book — this chapter's meaning differs from "context window" and "context engineering."
- Examples teach by demonstration (in-context learning) and can implicitly convey both task and format.
- Example order and consistency can bias output — keep example sets balanced and consistently formatted.
- Combining all three usually produces better results.
- Clear prompts reduce ambiguity and improve consistency.

---

