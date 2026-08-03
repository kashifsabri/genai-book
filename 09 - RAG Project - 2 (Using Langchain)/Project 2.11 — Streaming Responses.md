

## Introduction

In the previous chapter,

our RAG application generated the complete answer before displaying it.

Example

```text
User

↓

Question

↓

LLM

↓

Wait...

↓

Complete Answer
```

This works,

but it doesn't provide the best user experience.

Modern AI applications like:

- ChatGPT
- Claude
- Gemini
- Microsoft Copilot

don't wait until the entire answer is generated.

Instead,

they display the response one token at a time.

This is called **Streaming**.

---

# What is Streaming?

Streaming means displaying the response while the LLM is still generating it.

Instead of waiting for the complete answer,

the user immediately starts seeing the response.

Example

Without Streaming

```text
Thinking...

Thinking...

Thinking...

Answer appears all at once.
```

With Streaming

```text
Employees...

Employees receive...

Employees receive 24...

Employees receive 24 paid leave days...
```

This makes the application feel much faster.

---

# Why Use Streaming?

Streaming improves:

- User Experience
- Perceived Performance
- Responsiveness

Even if the total response time remains the same,

users feel that the application is faster because they receive immediate feedback.

---

# Project 1 vs Project 2

Project 1

```python
answer = generate_answer(prompt)

print(answer)
```

Project 2

```python
for chunk in chain.stream(question):

    print(
        chunk,
        end=""
    )
```

The chain now returns small pieces of the response instead of waiting for the entire answer.

---

# Does Streaming Change the RAG Pipeline?

No.

Only the final step changes.

Before

```text
Retriever

↓

Prompt

↓

LLM

↓

Complete Answer
```

After

```text
Retriever

↓

Prompt

↓

LLM

↓

Streaming Tokens

↓

Final Answer
```

Retrieval remains exactly the same.

---

# Step 1 — No Changes Required

The good news is that our chain remains exactly the same.

```python
chain = build_chain(
    retriever
)
```

No modifications are required.

Streaming is supported automatically.

---

# Step 2 — Replace invoke()

Previously,

we used:

```python
answer = chain.invoke(question)

print(answer)
```

Now replace it with:

```python
for chunk in chain.stream(question):

    print(
        chunk,
        end=""
    )
```

That's it.

Streaming is enabled.

---

# Understanding stream()

The method:

```python
chain.stream()
```

returns an iterator.

Instead of one large response,

it returns many small pieces.

Example

```text
Employees
```

↓

```text
 receive
```

↓

```text
 24
```

↓

```text
 paid
```

↓

```text
 leave
```

↓

...

Your application prints each piece as it arrives.

---

# Step 3 — Update app.py

Old Version

```python
answer = chain.invoke(question)

print(answer)
```

New Version

```python
print("\nAnswer\n")

for chunk in chain.stream(question):

    print(

        chunk,

        end="",

        flush=True
    )
```

Notice:

```python
flush=True
```

This forces Python to display each token immediately instead of waiting.

---

# What Does the User See?

Instead of:

```text
Thinking...

Thinking...

Employees receive 24 paid leave days every year.
```

The user sees:

```text
Employees receive 24 paid leave days every year...
```

appearing gradually.

This is the behavior users expect from modern AI applications.

---

# Complete Example

```python
question = input(
    "Ask a Question: "
)

print("\nAnswer\n")

for chunk in chain.stream(question):

    print(

        chunk,

        end="",

        flush=True
    )
```

---

# Comparison with invoke()

| invoke() | stream() |
|-----------|----------|
| Waits for the complete response | Displays tokens immediately |
| Returns one string | Returns streamed chunks |
| Simpler | Better user experience |
| Suitable for scripts | Suitable for chat applications |

---

# When Should You Use invoke()?

Use:

```python
invoke()
```

when:

- Generating reports
- Batch processing
- Running automated tasks
- APIs that require the full response

---

# When Should You Use stream()?

Use:

```python
stream()
```

when building:

- Chatbots
- AI Assistants
- Customer Support Applications
- Interactive AI Systems

Almost every conversational AI application uses streaming.

---

# Industry Insight

Streaming is considered a standard feature in modern AI products.

Applications like:

- ChatGPT
- Claude
- Gemini
- Microsoft Copilot
- Perplexity

all stream responses.

Users have become accustomed to seeing answers appear gradually.

If your application waits silently for several seconds,

it often feels slower,

even if the total processing time is identical.

---

# Best Practice

Display a small message before streaming.

Example

```text
Searching documents...

Generating answer...
```

This reassures users that the application is working.

---

# Debugging Tip

If streaming doesn't work,

verify that:

- Your chat model supports streaming.
- Your API key is valid.
- The chain works correctly using `invoke()` first.

Always confirm that the normal chain works before enabling streaming.

---

# Architecture Review

Our RAG pipeline now supports real-time responses.

```text
Question

↓

Retriever

↓

Prompt

↓

ChatOpenAI

↓

Streaming

↓

User
```

Only the output behavior has changed.

The Retrieval and Generation pipeline remains identical.

---

# Common Beginner Mistakes

### Mistake 1

Thinking streaming makes the model faster.

Streaming improves the user experience,

but it doesn't reduce the model's computation time.

---

### Mistake 2

Using `invoke()` for chat applications.

Streaming provides a much better interactive experience.

---

### Mistake 3

Forgetting `flush=True`.

Without it,

Python may buffer the output,

preventing real-time display.

---

### Mistake 4

Trying to debug streaming before testing `invoke()`.

Always verify the standard chain first.

---

# Key Takeaways

- Streaming displays the response as it is generated.
- `chain.stream()` replaces `chain.invoke()` for interactive applications.
- Streaming improves user experience without changing the underlying RAG pipeline.
- Most modern AI assistants use streaming responses.
- The rest of the LangChain RAG architecture remains unchanged.