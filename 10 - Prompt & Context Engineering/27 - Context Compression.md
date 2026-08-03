

## Learning Objectives

By the end of this chapter, you will understand:

- What Context Compression is
- Why it is needed
- How it works
- Why it is important for AI Agents
- The main compression techniques used in practice (filtering, extractive, abstractive, pruning)
- How to measure a compression ratio and its tradeoffs
- The risk of over-compressing and losing important facts

---

# Introduction

Imagine you're preparing for an exam.

You have a 500-page textbook.

Would you read the entire book before answering one question?

Probably not.

You would read only the relevant chapter or a short summary.

AI Agents work the same way.

Instead of sending huge amounts of information to the LLM,

they first **compress the context**.

This is called **Context Compression**.

---

# What is Context Compression?

Context Compression is the process of reducing the amount of information sent to the LLM while keeping only the most relevant parts.

The goal is simple:

> **Less information, same meaning.**

---

# Visual Flow

```text
Large Context

↓

Remove Unnecessary Information

↓

Keep Important Information

↓

LLM

↓

Answer
```

---

# Example

Suppose a retriever finds this document.

```text
Page 1
Company History
...
Page 10
Leave Policy
...
Page 50
Office Locations
```

User asks:

```text
How many vacation days do employees get?
```

Instead of sending all 50 pages,

the AI sends only:

```text
Leave Policy

Employees receive 24 paid vacation days annually.
```

The context is much smaller,

but still contains the answer.

---

# The Main Compression Techniques

"Compression" isn't one technique — it's a family of them, usually combined:

### 1. Filtering (selection)

Don't touch the text at all — just decide _which_ chunks/documents/messages to include in the first place. This is what retrieval's `top_k` already does (Chapter 24), and it's the cheapest form of compression because there's nothing to compute — you simply exclude what's irrelevant.

### 2. Extractive Compression

Pull out the exact sentences or passages that matter, discarding the rest word-for-word. No new text is generated.

```text
Original (500 words) → Extract 2 relevant sentences → 40 words
```

Fast and low-risk (the wording isn't changed), but can miss context that spans multiple sentences.

### 3. Abstractive Compression (Summarization)

Use a model to _rewrite_ the content in fewer words, capturing the meaning rather than exact wording — this is what most people mean by "summarizing." Often done with a smaller, cheaper model so the compression step itself doesn't become expensive.

```python
# Compress a large retrieved chunk before sending it to the main model
summary = cheap_model.create(
    messages=[{
        "role": "user",
        "content": f"Summarize the following in 2-3 sentences, keeping any numbers or dates exact:\n\n{large_chunk}"
    }]
)

final_context = summary.content  # much smaller, sent to the main model
```

### 4. Pruning (conversation/context trimming)

Removing older or lower-value parts of the _existing_ context — e.g. dropping early conversation turns, or removing a tool result once it's no longer relevant (covered in Chapters 23 and 25 as sliding windows / summarization).

In production, these are usually layered: filter first (retrieval), then extract or summarize what's left, then prune as the conversation grows.

---

# Measuring Compression

A simple way to reason about compression is the **compression ratio** — how much smaller the context became:

```text
compression_ratio = original_tokens / compressed_tokens
```

A 10,000-token document compressed to 500 tokens has a ratio of 20:1. Higher ratios save more cost and latency, but raise the risk of losing information — there's a real tradeoff, not a free win.

---

# The Risk of Over-Compression

Compression can go wrong in a specific way: it can **remove the exact detail the user needed**, especially numbers, dates, names, or exceptions/edge cases in a policy.

```text
Original: "Employees receive 24 paid vacation days annually,
except contractors, who receive 12."

Over-compressed: "Employees receive vacation days."
```

The second version is shorter, but now wrong for contractors. Good compression prompts explicitly instruct the summarizing model to preserve exact numbers, dates, names, and exceptions rather than smoothing them away for brevity.

---

# Why is Context Compression Important?

Large contexts cause problems.

They increase:

- Cost
- Latency
- Token usage

They can also distract the model with unnecessary information.

Compressed context is usually:

- Faster
- Cheaper
- More relevant

---

# Real-World Example

Suppose you're building a Legal AI Assistant.

A contract contains 120 pages.

The user asks:

```text
What is the payment deadline?
```

The AI should send only the section about payments,

not the entire contract.

---

# Where is Context Compression Used?

You'll see it in:

- RAG Systems
- AI Agents
- Chatbots
- Document Q&A
- Enterprise Search

Almost every production AI application performs some form of context compression.

---

# Best Practice

Only include information that helps answer the user's question.

Think:

```text
Relevant

↓

Useful

↓

Necessary
```

Everything else can be removed.

Also:

- Prefer filtering over summarizing when possible — it's cheaper and carries no risk of distorting meaning.
- When summarizing, explicitly instruct the model to preserve numbers, dates, names, and exceptions.
- Track your compression ratio, and test compressed answers against the originals to catch information loss.

---

# Common Beginner Mistakes

### Mistake 1

Sending the entire document.

More information does not always mean better answers.

---

### Mistake 2

Removing important information.

Compress the context,

not the meaning.

---

### Mistake 3

Thinking compression changes the answer.

Good compression removes unnecessary details while preserving the important facts.

---

### Mistake 4

Summarizing without protecting critical details.

Numbers, dates, exceptions, and named entities are exactly what tends to get lost in aggressive summarization — call this out explicitly in the compression prompt.

---

### Mistake 5

Compressing everything the same way.

Filtering, extraction, and summarization have different cost/risk tradeoffs — use the cheapest technique that gets the job done rather than defaulting to full summarization everywhere.

---

# Interview Tip ⭐

A common interview question is:

> **Why is Context Compression important in RAG and AI Agents?**

A good answer is:

Context Compression reduces the number of tokens sent to the LLM by keeping only the most relevant information — through techniques like filtering, extraction, or summarization.

This lowers cost, reduces latency, and often improves answer quality, as long as important details (numbers, dates, exceptions) are explicitly preserved.

---

# Key Takeaways

- Context Compression reduces unnecessary information.
- Common techniques: filtering (selection), extractive compression, abstractive summarization, and pruning — often layered together.
- Compression ratio (original tokens ÷ compressed tokens) is a useful way to measure how aggressive compression is.
- Over-compression risks losing exact numbers, dates, names, or exceptions — protect these explicitly.
- It helps save tokens, cost, and time.
- The goal is to preserve meaning while reducing size.
- It is widely used in RAG and Agentic AI systems.
- Good compression improves efficiency without losing important information.

---

