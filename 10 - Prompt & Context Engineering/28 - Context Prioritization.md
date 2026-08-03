

## Learning Objectives

By the end of this chapter, you will understand:

- What Context Prioritization is
- Why it is important
- How AI Agents decide what information to use
- Best practices for prioritizing context
- How relevance is actually scored, not just guessed
- How to resolve conflicts when sources disagree
- How placement in context reinforces priority

---

# Introduction

Imagine you're solving a problem.

You have access to:

- 100 documents
- Chat history
- Tool results
- User preferences

Should you send everything to the LLM?

No.

Some information is more important than others.

Choosing the most useful information is called **Context Prioritization**.

---

# What is Context Prioritization?

Context Prioritization is the process of selecting the **most relevant information** before sending it to the LLM.

The goal is simple:

> Give the model the **right** context, not **all** the context.

---

# Visual Flow

```text
Available Context

↓

Rank by Relevance

↓

Keep Most Important

↓

LLM

↓

Answer
```

---

# Example

Suppose a user asks:

```text
What is our refund policy?
```

Available Context

```text
Employee Handbook

Refund Policy

Company History

Office Locations

Holiday Calendar
```

Only one document is important.

```text
Refund Policy
```

The others are ignored.

---

# How Relevance Is Actually Scored

"Rank by relevance" sounds abstract until you see what it's built from. In practice, prioritization usually combines several concrete signals into a score, rather than relying on vague judgment:

|Signal|What it captures|Example|
|---|---|---|
|**Semantic similarity**|How closely a chunk's embedding matches the question's embedding|Refund Policy scores high for "What is our refund policy?"|
|**Recency**|How recently the information was created or updated|A 2026 pricing sheet outranks a 2019 one|
|**Source authority**|How trustworthy or official the source is|Official Handbook outranks a random internal Slack message|
|**Explicit rules/metadata**|Hard filters the application applies before scoring|Only include documents tagged `public` for an external chatbot|

A simplified scoring function:

```python
def score(chunk, question_vector):
    similarity = cosine_similarity(chunk.embedding, question_vector)
    recency_boost = recency_score(chunk.updated_at)   # newer = higher
    authority_boost = SOURCE_WEIGHTS.get(chunk.source, 1.0)
    return similarity * authority_boost + recency_boost

ranked_chunks = sorted(candidates, key=lambda c: score(c, q_vec), reverse=True)
top_context = ranked_chunks[:5]
```

This is the same idea as re-ranking in RAG (Chapter 24) — prioritization is really retrieval's ranking step applied more broadly, across _all_ context sources, not just retrieved documents.

---

# Resolving Conflicting Information

A common real-world problem: two sources disagree. The Employee Handbook says vacation is 24 days; a Slack message from last week says it changed to 20. Which does the model trust?

This needs an explicit policy, not a hope that the model figures it out:

- **Recency-first** — prefer the most recently updated source (good for policies that change over time).
- **Authority-first** — prefer the officially designated source of truth regardless of date (good for legal/compliance content).
- **Surface the conflict** — instead of silently picking one, instruct the model to tell the user both exist: _"The handbook says 24 days, but a recent update mentions 20 — please confirm."_

```text
System instruction example:
"If sources conflict, prefer the most recently dated one,
and explicitly note the discrepancy to the user."
```

Without an explicit rule, the model's choice becomes unpredictable — and unpredictability in something like a refund policy is a real business risk.

---

# Placement Reinforces Priority

Prioritization isn't only about _what_ to include — it connects back to _where_ to put it (Chapter 23's "lost in the middle" effect). The highest-priority information should also get the best position in context: near the system prompt or right before the final question, not buried in the middle of a long stack of documents.

```text
Low Priority Docs → Medium Priority Docs → HIGHEST Priority Doc → Question
```

Ranking without repositioning only solves half the problem — a highly relevant document dropped in the middle of ten others can still be under-weighted by the model.

---

# Why is Context Prioritization Important?

Sending unnecessary information can:

- Increase cost
- Increase latency
- Confuse the model

Prioritizing context helps the model focus on what matters.

---

# Real-World Example

Suppose you're building an AI Customer Support Bot.

User

```text
Where is my order?
```

Available Context

```text
Previous Chat

Shipping Status

Company Policy

Product Manual

Marketing Brochure
```

The AI should prioritize:

- Shipping Status
- Previous Chat

The marketing brochure is irrelevant.

---

# Common Priorities

Most AI systems prioritize information like this:

```text
1. Current User Question

↓

2. Tool Outputs

↓

3. Retrieved Documents

↓

4. Recent Conversation

↓

5. Long-Term Memory
```

The exact order depends on the application.

---

# Best Practice

Ask yourself:

> Does this information help answer the current question?

If not,

don't include it.

Also:

- Score relevance using concrete signals (similarity, recency, authority) rather than vague judgment.
- Define an explicit conflict-resolution rule for when sources disagree.
- Place the highest-priority information at the start or end of context, not the middle.

---

# Common Beginner Mistakes

### Mistake 1

Sending every retrieved document.

---

### Mistake 2

Giving equal importance to all context.

Not all information is equally valuable.

---

### Mistake 3

Ignoring recent tool outputs.

Tool results are often the most relevant context.

---

### Mistake 4

Ranking relevance without an explicit conflict-resolution rule.

When two sources disagree, the model needs a defined policy (recency-first, authority-first, or surface the conflict) — not an implicit guess.

---

### Mistake 5

Prioritizing content without repositioning it.

A top-ranked document still gets under-weighted if it's buried in the middle of a long context — ranking and placement need to work together.

---

# Interview Tip ⭐

A common interview question is:

> Why is Context Prioritization important?

Answer:

Because LLMs have limited context windows.

Selecting only the most relevant information — scored using signals like semantic similarity, recency, and source authority — reduces cost, improves accuracy, and avoids distracting the model.

---

# Where is this Used?

- ChatGPT
- Claude
- Gemini
- LangChain
- LangGraph
- OpenAI Agents SDK
- Google ADK
- Enterprise AI Agents

---

# Key Takeaways

- Context Prioritization selects the most relevant information.
- Relevance is usually scored using concrete signals: semantic similarity, recency, source authority, and explicit metadata rules.
- Conflicting sources need an explicit resolution policy — recency-first, authority-first, or surfacing the conflict.
- Placement matters as much as ranking — put top-priority context at the start or end, not the middle.
- More context is not always better.
- Good prioritization improves accuracy and reduces cost.
- It is a core part of modern AI Agents.

---

