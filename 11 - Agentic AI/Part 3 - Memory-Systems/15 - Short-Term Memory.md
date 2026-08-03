

## Learning Objectives

By the end of this chapter, you will understand:

- What Short-Term Memory is
- Why AI Agents need it
- How it works
- Its limitations
- Common Short-Term Memory techniques
- How Short-Term Memory differs from Long-Term Memory and RAG

---

# Introduction

Imagine you're chatting with an AI Agent.

You say:

```text
My name is Kashif.
```

A few seconds later, you ask:

```text
What's my name?
```

The Agent replies:

```text
Your name is Kashif.
```

How did it remember?

It used **Short-Term Memory**.

---

# What is Short-Term Memory?

Short-Term Memory is the temporary memory an AI Agent uses while completing the current conversation or task.

It helps the Agent remember recent information,

but only for a limited period.

Think of it as the Agent's **working memory**.

Technically, an LLM has no memory of its own between calls. Each request is stateless. Short-Term Memory is not something the model "has" — it's something the surrounding application manages by re-sending the relevant history with every request.

---

# Visual Diagram

```text
Conversation Starts

↓

User Message

↓

Store in Short-Term Memory

↓

Next User Message

↓

Use Recent Information

↓

Conversation Ends

↓

Memory Cleared
```

Short-Term Memory usually lasts only for the current session.

---

# Why Do Agents Need Short-Term Memory?

Without Short-Term Memory,

every new message would be treated as a completely new conversation.

The Agent would forget:

- Your name
- Previous questions
- Earlier answers
- Current task

This would make conversations frustrating.

---

# Example

User

```text
I'm learning Python.
```

Later,

User

```text
Suggest a project.
```

The Agent remembers you're learning Python and suggests:

```text
Build a To-Do Application in Python.
```

Without Short-Term Memory,

the Agent wouldn't know which language you're learning.

---

# Context Window

An LLM does **not** remember forever.

It only sees the information inside its **Context Window**.

Think of the Context Window as the Agent's desk.

Only the papers on the desk are visible.

Older papers must be removed when the desk becomes full.

The Context Window is measured in **tokens**, not words or characters. A token is roughly ¾ of a word in English. Modern LLMs typically offer context windows ranging from 8K tokens (small/older models) to over 1 million tokens (newer, large-context models) — but a bigger window doesn't remove the need for memory management. Larger windows cost more per request and can suffer from "lost in the middle" effects, where information buried in the center of a very long context is recalled less reliably than information near the start or end.

---

# Visual Diagram

```text
Context Window

┌───────────────────────┐
│ Recent Messages       │
│ Current Task          │
│ Recent Tool Results   │
└───────────────────────┘

Older messages are removed
when the window becomes full.
```

---

# Conversation Buffer

One common way to implement Short-Term Memory is a **Conversation Buffer**.

It simply stores recent messages.

Example

```text
User: Hello

Assistant: Hi

User: My name is Kashif.

Assistant: Nice to meet you.

User: What's my name?
```

The Agent reads the recent conversation to answer correctly.

A plain Conversation Buffer stores **everything**, which is simple but doesn't scale — it grows without limit and eventually exceeds the Context Window or becomes expensive to send on every turn.

---

# Sliding Window

Conversations can become very long.

Instead of storing everything,

many AI Agents use a **Sliding Window**.

It keeps only the most recent messages.

Example

```text
Message 1 ❌

Message 2 ❌

Message 3 ✅

Message 4 ✅

Message 5 ✅
```

Older messages are removed as new ones arrive.

A Sliding Window is cheap and fast, but it has a weakness: if the user mentioned something important several turns ago (like an order number or a name), it can silently fall out of the window and be forgotten.

---

# Summarization Buffer

A more advanced technique is the **Summarization Buffer**.

Instead of deleting old messages outright, the Agent compresses them into a short summary and keeps that summary in the Context Window alongside recent raw messages.

```text
[Older Messages] → Summarize → "User is Kashif, learning Python,
                                 building a To-Do app."

[Recent Messages] → Kept as-is
```

This way, important details survive even after the original messages are dropped, without wasting tokens on the full text.

Some frameworks combine this with **incremental summarization** — updating the running summary after every few turns, rather than re-summarizing the whole conversation each time.

---

# Entity Memory

A related technique is **Entity Memory**, which extracts and tracks specific facts (entities) mentioned in the conversation — names, dates, order numbers, preferences — separately from the raw message text.

```text
Entities Tracked:
  name: Kashif
  learning: Python
  order_id: 12345
```

This is useful because it lets the Agent recall a key fact accurately even if the sentence it came from has already been trimmed or summarized away.

---

# Token Limits

Every LLM has a maximum number of tokens it can process.

When that limit is reached,

older information must be:

- Removed
- Summarized
- Stored elsewhere

This is one reason why Long-Term Memory exists.

As a rule of thumb, teams often reserve a fixed **token budget** for memory (e.g., 20-30% of the context window) so there's always room left for the current user message, system instructions, and the model's response.

---

# Memory Trimming

When the Context Window becomes full,

the Agent trims unnecessary information.

Example

Remove:

- Greetings
- Small talk
- Repeated information

Keep:

- User preferences
- Current task
- Important facts

This keeps the conversation efficient.

---

# Comparing the Techniques

|Technique|Keeps Full Detail?|Token Cost|Risk of Forgetting|
|---|:-:|:-:|:-:|
|Conversation Buffer|✅ Yes|High (grows forever)|Low (until it overflows)|
|Sliding Window|✅ Yes (recent only)|Low|High (old info dropped)|
|Summarization Buffer|⚠️ Compressed|Medium|Medium (summary may lose nuance)|
|Entity Memory|⚠️ Key facts only|Low|Low for tracked facts|

In practice, most production Agents combine two or more of these — for example, a Sliding Window for recent turns plus a running summary for everything older.

---

# Python Example 🐍

A simple Conversation Buffer:

```python
conversation = []

conversation.append("My name is Kashif.")

conversation.append("I like Python.")

print(conversation)
```

A simple Sliding Window (keeping only the last N messages):

```python
conversation = []
WINDOW_SIZE = 3

def add_message(message):
    conversation.append(message)
    # Keep only the most recent WINDOW_SIZE messages
    if len(conversation) > WINDOW_SIZE:
        conversation.pop(0)

add_message("Hello")
add_message("My name is Kashif.")
add_message("I like Python.")
add_message("What's my name?")

print(conversation)
# Oldest message ("Hello") has been dropped
```

Real AI Agents manage memory automatically,

but the idea is similar.

---

# Advantages

Short-Term Memory helps the Agent:

- Maintain natural conversations
- Remember recent information
- Continue ongoing tasks
- Reduce repeated questions

---

# Limitations

Short-Term Memory is temporary.

When:

- The conversation ends
- The Context Window becomes full

the Agent may forget older information.

For long-term knowledge,

another memory system is needed.

---

# Real-World Example

Imagine an AI Customer Support Agent.

User

```text
My order number is 12345.
```

Ten messages later,

the user asks:

```text
Where is my order?
```

Because the order number is still in Short-Term Memory,

the Agent doesn't ask for it again.

---

# Short-Term Memory vs Long-Term Memory vs RAG

These three terms are often confused. Here's the difference:

|Concept|Scope|Persists Across Sessions?|Typical Storage|
|---|---|---|---|
|Short-Term Memory|Current conversation/task|❌ No|In the Context Window (RAM/prompt)|
|Long-Term Memory|Across sessions, over time|✅ Yes|Database, vector store, file|
|RAG (Retrieval-Augmented Generation)|External knowledge, not necessarily personal|✅ Yes (it's a knowledge base)|Vector database / search index|

RAG is often used _to implement_ Long-Term Memory (by retrieving relevant past facts and inserting them into the Context Window), but RAG itself is a retrieval technique, not a type of memory — it can just as easily retrieve public documents that have nothing to do with the conversation history.

---

# Industry Insight ⭐

Most AI frameworks maintain Short-Term Memory automatically.

Examples include:

- LangGraph State
- OpenAI Agents SDK Session
- Google ADK Session State
- CrewAI Conversation Context

Although the implementation differs,

the purpose is the same:

Remember recent information while the task is active.

In **multi-agent systems**, Short-Term Memory decisions get more complex: should each sub-agent have its own isolated memory, or should they share one conversation state? Frameworks like LangGraph and CrewAI let developers choose per-agent (isolated) or per-crew (shared) memory scopes, depending on whether agents need to see each other's intermediate steps.

---

# Best Practices

Store only information that is useful for the current task.

Remove unnecessary conversation history.

When the Context Window becomes large,

summarize or trim older messages.

Prefer structured storage (like Entity Memory) for facts that must never be forgotten mid-conversation, rather than relying on them surviving inside raw chat text.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the LLM remembers everything.

It only sees what is inside the current Context Window.

---

### Mistake 2

Confusing Context Window with Long-Term Memory.

The Context Window is temporary.

Long-Term Memory persists across sessions.

---

### Mistake 3

Keeping every message forever.

This wastes tokens and increases cost.

Trim or summarize when appropriate.

---

### Mistake 4

Assuming a larger context window solves the memory problem.

A bigger window costs more per call and can still suffer recall issues on information buried in the middle of a long conversation. Good memory management still matters, even with large-context models.

---

# Interview Tip ⭐

A common interview question is:

> **What is Short-Term Memory in an AI Agent?**

A good answer is:

Short-Term Memory is temporary memory that stores recent conversation and task information. It helps the Agent maintain context during the current session but is limited by the model's Context Window.

A strong follow-up point: mention that Short-Term Memory is application-managed (not model-managed), since the LLM itself is stateless between calls — this is a detail interviewers often listen for.

---

# Where is this Used?

- OpenAI Agents SDK
- LangGraph
- LangChain
- Google ADK
- CrewAI
- AutoGen
- Enterprise AI Applications

---

# Quick Quiz ✅

1. Why is an LLM considered "stateless" between calls?
2. What's the main risk of using a plain Sliding Window?
3. How does a Summarization Buffer reduce token usage while preserving important details?
4. What's the key difference between Short-Term Memory and RAG?

_(Answers: 1) It has no built-in memory — every request must include the needed context. 2) Important older information can silently fall out of the window and be forgotten. 3) It compresses old messages into a short summary instead of storing them in full. 4) Short-Term Memory is scoped to the current session and lives in the Context Window; RAG retrieves from an external, persistent knowledge base.)_

---

# Key Takeaways

- Short-Term Memory stores recent information.
- It exists only during the current conversation or task.
- It is limited by the Context Window.
- Techniques like Conversation Buffers, Sliding Windows, Summarization Buffers, and Entity Memory help manage it.
- Short-Term Memory is managed by the application, not the model itself.
- When Short-Term Memory is not enough, Long-Term Memory is required.

---

