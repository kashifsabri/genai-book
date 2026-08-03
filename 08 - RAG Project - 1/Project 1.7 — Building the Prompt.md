
## Introduction

Our Retriever can now find the most relevant document chunks.

Example

```text
Employees receive 24 paid leave days every year.

Unused leave can be carried forward.

Employees may work remotely twice a week.
```

Now another question arises.

How do we send this information to the LLM?

Do we simply ask:

```text
How many leave days do employees receive?
```

No.

The LLM doesn't know these documents exist.

We must include them in the prompt.

This process is called **Context Injection**.

---

# What is Context Injection?

Context Injection means providing the retrieved documents to the LLM before asking the user's question.

Instead of this:

```text
Question

↓

LLM
```

We do this:

```text
Retrieved Context

+

User Question

↓

LLM
```

Now the LLM can answer using the retrieved information.

---

# Step 1 — The Wrong Way

Suppose we only ask:

```text
How many leave days do employees receive?
```

The LLM may answer:

```text
Most companies provide around 20–30 leave days.
```

It guessed.

This is called a hallucination.

---

# Step 2 — A Better Prompt

Instead, include the retrieved context.

```text
Context

Employees receive 24 paid leave days every year.

Question

How many leave days do employees receive?
```

Now the LLM has the required information.

---

# Step 3 — The Best Prompt

A good prompt also gives clear instructions.

Example

```text
You are an AI assistant.

Answer the question using ONLY the provided context.

If the answer is not present in the context,

reply:

"I don't have enough information."

Context

Employees receive 24 paid leave days every year.

Question

How many leave days do employees receive?
```

This greatly reduces hallucinations.

---

# Step 4 — Create prompt_builder.py

Create

```text
prompt_builder.py
```

---

# Step 5 — Build the Prompt Template

Instead of writing the prompt directly inside a function,

let's store it in a reusable template.

This makes the prompt:

- Easier to read
- Easier to update
- Easier to reuse
- Similar to how production RAG systems manage prompts

Create a prompt template.

```python
PROMPT_TEMPLATE = """
You are an AI assistant.

Answer the user's question using ONLY the provided context.

If the answer cannot be found in the context,
reply:

"I don't have enough information."

Do not guess or make up information.

Context:
{context}

Question:
{question}

Answer:
"""
```

Now create a function that fills the placeholders.

```python
def build_prompt(context, question):
    return PROMPT_TEMPLATE.format(
        context=context,
        question=question
    )
```

Notice the placeholders:

```text
{context}
```

and

```text
{question}
```

When the function is called,

they are automatically replaced with the retrieved document chunks and the user's question.

Example:

```python
context = """
Employees receive 24 paid leave days every year.

Unused leave can be carried forward.
"""

question = "How many leave days do employees receive?"

prompt = build_prompt(
    context=context,
    question=question
)

print(prompt)
```

Output

```text
You are an AI assistant.

Answer the user's question using ONLY the provided context.

If the answer cannot be found in the context,
reply:

"I don't have enough information."

Do not guess or make up information.

Context:

Employees receive 24 paid leave days every year.

Unused leave can be carried forward.

Question:

How many leave days do employees receive?

Answer:
```

Using a separate template makes your code cleaner and more maintainable.

In larger applications, prompt templates are often stored in separate files or managed through dedicated prompt management systems. By defining the template separately, you can improve or replace prompts without changing the application logic.

---

# Step 6 — Combine Retrieved Chunks

Remember,

our Retriever returns multiple chunks.

Example

```python
[
    "Employees receive 24 paid leave days every year.",

    "Unused leave can be carried forward."
]
```

Convert them into one string.

```python
context = "\n\n".join(results)
```

Output

```text
Employees receive 24 paid leave days every year.

Unused leave can be carried forward.
```

Perfect.

---

# Step 7 — Build the Final Prompt

```python
prompt = build_prompt(
    context=context,
    question=query
)

print(prompt)
```

Output

```text
You are an AI assistant.

Answer the user's question using ONLY the context below.

If the answer is not found,

reply:

"I don't have enough information."

Context

Employees receive 24 paid leave days every year.

Unused leave can be carried forward.

Question

How many leave days do employees receive?

Answer
```

This is the exact prompt we'll send to the LLM.

---

# Why This Prompt Is Better

Notice what we've done.

We told the LLM:

✅ Where the information comes from.

✅ Not to invent answers.

✅ What to do if information is missing.

Without these instructions,

hallucinations become much more common.

---

# Complete prompt_builder.py

```python
def build_prompt(
    context,
    question
):

    return f"""
You are an AI assistant.

Answer the user's question using ONLY the provided context.

If the answer cannot be found,

reply:

"I don't have enough information."

Context:
{context}

Question:
{question}

Answer:
"""
```

---

# Test the Prompt

Open

```python
app.py
```

```python
context = "\n\n".join(results)

prompt = build_prompt(
    context,
    query
)

print(prompt)
```

Read the prompt carefully.

Always inspect the final prompt during development.

Many RAG bugs come from poorly formatted prompts.

---

# Project Architecture

Completed

```text
PDF

↓

Loader

↓

Chunker

↓

Embeddings

↓

FAISS

↓

Retriever

↓

Prompt Builder
```

Remaining

```text
LLM

↓

Answer
```

---

# Real-World Example

Imagine taking an exam.

If someone asks you:

```text
What is the leave policy?
```

without giving you the handbook,

you'll probably guess.

But if they hand you the handbook first,

you'll answer using the document.

That's exactly what Context Injection does.

It gives the LLM the handbook before asking the question.

---

# Common Beginner Mistakes

### Mistake 1

Sending only the user's question.

Always include the retrieved context.

---

### Mistake 2

Not telling the LLM to stay within the context.

This increases hallucinations.

---

### Mistake 3

Passing chunks separately.

Combine all retrieved chunks into one context string before building the prompt.

---

### Mistake 4

Using a vague system instruction.

Be explicit:

- Use only the provided context.
- Don't guess.
- Say when the information is unavailable.

---

# Key Takeaways

- Prompt Building combines retrieved context with the user's question.
- Context Injection is a core part of every RAG system.
- A well-designed prompt reduces hallucinations.
- Retrieved chunks should be combined before creating the prompt.
- The next step is sending this prompt to an LLM to generate the final answer.