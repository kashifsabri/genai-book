## Learning Objectives

By the end of this chapter, you will understand:

- How an LLM reads a prompt
- Why wording matters
- Why order matters
- Why prompts produce different outputs
- What tokens actually are (and why they aren't the same as words)
- Why generation is autoregressive (one token at a time, feeding back into itself)
- How the same prompt can still change between runs

---

# Introduction

When you ask ChatGPT a question,

it doesn't "understand" English like a human.

Instead,

it reads your prompt as a sequence of tokens.

Then,

it predicts the most likely next token.

This process repeats until the answer is complete.

---

# Step 1 — You Write a Prompt

Example

```text
Explain Machine Learning in simple words.
```

---

# Step 2 — The Prompt Becomes Tokens

The model doesn't see sentences.

It sees tokens.

```text
Explain

Machine

Learning

in

simple

words

.
```

Each token is converted into numbers.

---

# Tokens Are Not the Same as Words

This is a common misconception worth clearing up.

A token can be:

- A whole word (`cat`)
- Part of a word (`token`, `ization` → two tokens for "tokenization")
- Just punctuation (`.`, `,`)
- A single character, for rare or unknown words

Example:

```text
"unbelievable" → "un" + "believ" + "able"
```

This is called **subword tokenization**. It's why:

- Word count and token count are not the same thing
- Longer or unusual words (technical terms, names, non-English words) can silently eat up more of your context window than expected
- Pricing and context limits for LLM APIs are measured in tokens, not words or characters

Rule of thumb often used in practice: roughly 1 token ≈ 0.75 words in English.

---

# Step 3 — The Transformer Processes the Tokens

The Transformer analyzes:

- Every token
- Their relationships
- Their context

This is where Attention works.

The model decides which words are important.

---

# Step 4 — The Model Predicts

Instead of generating the whole answer,

the model predicts one token at a time.

Example

```text
Machine
```

↓

```text
Learning
```

↓

```text
is
```

↓

```text
a
```

↓

...

Thousands of predictions create one complete answer.

---

# Generation is Autoregressive

There's a name for what's happening in Step 4.

Each new token is predicted based on **all tokens that came before it** — including the ones the model itself just generated.

```text
Prompt tokens + generated token 1 → predict token 2

Prompt tokens + tokens 1,2 → predict token 3

Prompt tokens + tokens 1,2,3 → predict token 4
```

This is called **autoregressive generation**.

It explains something important: the model can't "plan ahead" and then write — it commits to each token before seeing what comes next. This is part of why techniques like Chain of Thought help: letting the model "think out loud" token by token gives it a chance to reason its way to a better final answer, instead of jumping straight to a conclusion.

---

# Why Does Wording Matter?

Compare these prompts.

Prompt A

```text
Tell me about Java.
```

Prompt B

```text
Explain Java Programming to a beginner in 100 words.
```

The second prompt gives the model:

- Topic
- Audience
- Length

More information leads to better predictions.

---

# Why Does Order Matter?

Compare these.

```text
Summarize this article.

Use simple English.
```

vs

```text
Use simple English.

Summarize this article.
```

The meaning is similar,

but placing important instructions early often makes them more influential.

This connects back to Attention: the model weighs relationships between all tokens, but instructions placed at the very start or very end of a prompt tend to get followed more reliably than instructions buried in the middle — especially in long prompts. This is sometimes called the **"lost in the middle"** effect.

---

# Why the Same Prompt Can Still Give Different Answers

Even with identical wording and identical order, output can vary between runs. This is not a bug — it's a setting.

- **Temperature** controls how random the next-token prediction is. Low temperature (e.g. 0.1) → more predictable, repeatable output. High temperature (e.g. 0.9) → more varied, creative output.
- At temperature 0, the model always picks the most likely next token, which makes output far more (though not always perfectly) consistent.

This is why two people pasting the exact same prompt into the exact same model can still get slightly different answers.

---

# Think Like GPS

Imagine using Google Maps.

Bad instruction

```text
Take me somewhere nice.
```

Good instruction

```text
Take me to Mumbai Airport using the fastest route.
```

The GPS is the same.

Only the instruction changed.

LLMs work similarly.

---

# Quick Self-Check Questions

- Why can two prompts with the same word count use a different number of tokens?
- Why does Chain of Thought prompting take advantage of the fact that generation is autoregressive?
- If you wanted the most consistent, repeatable output possible from a model, what setting would you change?

---

# Key Takeaways

- LLMs read prompts as tokens, not words — subword tokenization means word count ≠ token count.
- They predict one token at a time, and each prediction depends on everything generated so far (autoregressive generation).
- Better prompts provide better context.
- Instructions placed early or late in a prompt tend to be followed more reliably than instructions buried in the middle.
- Temperature controls how deterministic or random the output is, which is why identical prompts can still produce different answers.
- Clear instructions produce better results.
- Prompt quality directly affects output quality.