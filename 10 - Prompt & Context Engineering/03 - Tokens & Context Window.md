## Learning Objectives

By the end of this chapter, you will understand:

- What a token is
- Why LLMs use tokens instead of words
- What a context window is
- Why context windows have limits
- Why tokens matter in Prompt Engineering
- How tokenizers actually split text (a brief look at BPE)
- The difference between input tokens and output tokens for cost
- Practical strategies for managing limited context

---

# Introduction

When we chat with an LLM,

we think in:

- Words
- Sentences
- Paragraphs

But an LLM doesn't.

It thinks in **tokens**.

Understanding tokens is one of the most important concepts in Prompt Engineering.

---

# What is a Token?

A **token** is the smallest piece of text that an LLM processes.

A token is **not always a word**.

Example

```text
Hello
```

This may be:

```text
1 Token
```

But a longer word like:

```text
unbelievable
```

might become:

```text
un

believ

able
```

Three tokens.

---

# Example

Sentence

```text
I love artificial intelligence.
```

The model might read it like this.

```text
I

love

artificial

intelligence

.
```

Instead of one sentence,

the model sees a sequence of tokens.

---

# Why Not Just Use Words?

Because words are different in every language.

For example,

```text
playing

played

player

plays
```

All are related.

Breaking them into tokens helps the model recognize these patterns more efficiently.

---

# How Does a Model Decide Where to Split?

Tokenizers are built using an algorithm called **Byte Pair Encoding (BPE)**, or variants of it.

The basic idea:

```text
Start with individual characters

↓

Find the most frequently occurring pair of characters/tokens in a huge training corpus

↓

Merge that pair into a single token

↓

Repeat thousands of times
```

This is why common words (`the`, `is`, `and`) are usually a single token, while rare words, made-up words, typos, and non-English text get split into several smaller pieces.

Practical implication: if you're working with code, technical jargon, or a language other than English, expect more tokens per word than with plain English text.

---

# What is a Context Window?

The **Context Window** is the maximum number of tokens an LLM can remember during one conversation.

Think of it as the model's working memory.

---

# A Simple Analogy

Imagine writing on a whiteboard.

The whiteboard has limited space.

```text
□□□□□□□□□□□□
```

Once it's full,

you must erase something before writing more.

An LLM works the same way.

The context window is its whiteboard.

---

# What Fits Inside the Context Window?

Everything.

```text
System Prompt

+

Chat History

+

Retrieved Documents (RAG)

+

Your Question

+

Model's Previous Answers
```

All of these share the same context window.

---

# Example

Suppose a model has a context window of:

```text
8,000 Tokens
```

Your conversation contains:

```text
System Prompt

500 Tokens

+

Chat History

2,000 Tokens

+

RAG Documents

3,000 Tokens

+

Your Question

300 Tokens
```

The total becomes:

```text
5,800 Tokens
```

This still fits.

---

# What Happens When It Becomes Too Large?

Suppose the conversation grows to:

```text
9,500 Tokens
```

But the model supports only:

```text
8,000 Tokens
```

Now something must be removed.

Usually,

older conversation history is discarded,

or documents are shortened.

---

# Why Does This Matter?

Imagine asking:

```text
Summarize this 500-page book.
```

The entire book may not fit into the model's context window.

This is one of the reasons **RAG** exists.

Instead of sending the whole book,

we retrieve only the relevant pages.

---

# A Big Context Window Isn't Always Reliable

Even when a model technically supports a very large context window, stuffing it full doesn't guarantee good results.

Research on long-context models has repeatedly found a **"lost in the middle"** pattern: models are noticeably better at using information placed near the _start_ or _end_ of the context than information buried in the _middle_.

Practical takeaway:

- Don't assume "the context window is big enough, so I'll just dump everything in"
- Place the most important information (the actual question, key instructions) near the start or end of the prompt
- Retrieval quality still matters even with huge context windows — relevant, well-ranked content beats a wall of loosely related text

---

# Tokens Affect Cost

Most LLM providers charge based on tokens.

More tokens mean:

- Higher cost
- More latency
- Slower responses

A shorter prompt is often faster and cheaper.

---

# Input Tokens vs Output Tokens

Not all tokens are priced the same.

```text
Input tokens  → your prompt, chat history, retrieved documents
Output tokens → what the model generates in response
```

Output tokens are typically **more expensive** than input tokens, and generating them also takes longer (since generation is one token at a time). This is why:

- Asking a model to "be concise" or "answer in one paragraph" isn't just a style choice — it directly affects cost and latency
- Long chain-of-thought reasoning or verbose output can be expensive at scale, even if the input prompt is short

---

# Prompt Engineering Tip

Don't add unnecessary information.

Bad Prompt

```text
Hello!

Hope you're doing well today.

Can you please, if possible, explain Python?
```

Better Prompt

```text
Explain Python to a beginner.
```

Both achieve the same goal,

but the second uses fewer tokens.

---

# Managing Limited Context in Real Systems

When a conversation or document is too large to fit, a few common strategies are used:

```text
Truncation        → simply cut off the oldest messages
Summarization      → compress older history into a short summary, keep that instead
Sliding window     → keep only the last N messages/tokens
Chunking + RAG      → break large documents into pieces, retrieve only relevant chunks
```

Most production AI systems use a combination of these rather than relying on a single large context window.

---

# Industry Insight

Modern models support much larger context windows than earlier LLMs.

However,

a larger context window does **not** mean you should send everything.

Good AI systems retrieve only the information needed for the current task.

This improves:

- Accuracy
- Speed
- Cost

---

# Best Practice

Treat tokens like money.

Every unnecessary token:

- Costs more
- Slows the model
- Uses valuable context space

Write prompts that are **clear**, not **long**.

---

# Common Beginner Mistakes

### Mistake 1

Thinking words and tokens are the same.

They are not.

One word may become multiple tokens.

---

### Mistake 2

Believing bigger prompts are always better.

More text doesn't always produce better answers.

Only relevant context should be included.

---

### Mistake 3

Ignoring token limits.

Large prompts may exceed the model's context window.

---

### Mistake 4

Assuming a large context window means placement doesn't matter.

Even in large-context models, information in the middle of a long prompt can get less attention than information at the start or end.

---

# Quick Self-Check Questions

- Why might a technical document use more tokens than a casual conversation of the same word count?
- Why are output tokens generally more expensive than input tokens?
- If a chatbot's conversation history keeps growing, what are two different strategies to keep it within the context window?

---

# Key Takeaways

- LLMs process tokens, not words.
- A token is a small unit of text, typically created using an algorithm like Byte Pair Encoding (BPE).
- The context window is the model's working memory.
- Prompts, chat history, and RAG documents all share the same context window.
- A large context window doesn't guarantee good use of that context — the "lost in the middle" effect means placement matters.
- Input and output tokens are often priced differently, with output tokens usually costing more.
- Real systems manage limited context using truncation, summarization, sliding windows, or chunking with RAG.
- Fewer, more relevant tokens usually lead to lower cost and better performance.