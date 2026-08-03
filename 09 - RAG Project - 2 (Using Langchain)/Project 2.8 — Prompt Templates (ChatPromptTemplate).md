

## Introduction

Our Retriever can now find the most relevant documents.

The next question is:

> **How do we send those documents to the LLM?**

In Project 1,

we built our own prompt using:

```python
PROMPT_TEMPLATE.format(...)
```

It worked perfectly.

LangChain provides a cleaner and more structured way to build prompts.

We'll use:

```text
ChatPromptTemplate
```

---

# What is a Prompt Template?

A Prompt Template is simply a template with placeholders.

Instead of hardcoding the prompt every time,

we define it once.

Later,

LangChain automatically fills in the required values.

Example

```text
Context

↓

{context}

Question

↓

{question}
```

↓

Final Prompt

---

# Project 1 vs Project 2

Project 1

```python
PROMPT_TEMPLATE = """
Context:
{context}

Question:
{question}
"""
```

Project 2

```python
ChatPromptTemplate.from_template(...)
```

The idea is identical.

LangChain simply manages the formatting for us.

---

# Why Do We Need prompts.py?

Create:

```text
prompts.py
```

Purpose:

- Store prompt templates
- Keep prompts separate from business logic
- Make prompts reusable

This file should never:

- Load documents
- Retrieve data
- Call the LLM

It should only define prompts.

---

# Step 1 — Import ChatPromptTemplate

```python
from langchain_core.prompts import (
    ChatPromptTemplate
)
```

---

# Step 2 — Create the Prompt

```python
prompt = ChatPromptTemplate.from_template(
"""
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
)
```

Notice the placeholders.

```text
{context}

{question}
```

LangChain will replace these automatically.

---

# Step 3 — Store the Prompt

Instead of creating it inside app.py,

store it inside:

```text
prompts.py
```

```python
from langchain_core.prompts import (
    ChatPromptTemplate
)

RAG_PROMPT = ChatPromptTemplate.from_template(
"""
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
)
```

---

# Step 4 — Format the Prompt

Suppose we have:

```python
context = "Employees receive 24 paid leave days every year."

question = "How many leave days do employees receive?"
```

Generate the prompt.

```python
formatted_prompt = RAG_PROMPT.invoke(

    {

        "context":context,

        "question":question
    }

)
```

LangChain replaces the placeholders automatically.

---

# Step 5 — Inspect the Prompt

```python
print(formatted_prompt)
```

Example Output

```text
You are an AI assistant.

Answer the user's question using ONLY the provided context.

Context

Employees receive 24 paid leave days every year.

Question

How many leave days do employees receive?

Answer:
```

Always inspect your prompt before connecting it to an LLM.

---

# Complete prompts.py

```python
from langchain_core.prompts import (
    ChatPromptTemplate
)

RAG_PROMPT = ChatPromptTemplate.from_template(
"""
You are an AI assistant.

Answer the user's question using ONLY the provided context.

If the answer cannot be found,

reply:

"I don't have enough information."

Do not guess or make up information.

Context:

{context}

Question:

{question}

Answer:
"""
)
```

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| String Template | ChatPromptTemplate |
| `.format()` | `.invoke()` |
| Manual formatting | Automatic formatting |
| Plain string | LangChain Prompt Object |

The prompt itself hasn't changed.

Only the way we build it has.

---

# Why Use ChatPromptTemplate?

Suppose later you decide to add:

- Chat History
- Conversation Memory
- System Messages
- Tool Messages

With plain strings,

this quickly becomes messy.

ChatPromptTemplate is designed for chat-based LLMs and scales much better.

---

# Using Multiple Messages

Most modern chat models don't receive one long string.

Instead,

they receive messages.

Example

```text
System Message

↓

You are a helpful assistant.
```

↓

```text
Human Message

↓

What is the leave policy?
```

ChatPromptTemplate supports this naturally.

Example

```python
from langchain_core.prompts import (

    ChatPromptTemplate
)

prompt = ChatPromptTemplate.from_messages(

    [

        (

            "system",

            "You are an AI assistant."

        ),

        (

            "human",

            """
Context:

{context}

Question:

{question}
"""
        )

    ]
)
```

This approach is commonly used in production applications.

---

# Industry Insight

Most enterprise AI applications separate prompts from application code.

Some organizations even store prompts in:

- Databases
- Configuration files
- Prompt Management Platforms

This allows prompt engineers to improve prompts without modifying application logic.

---

# Best Practice

Give your prompt a meaningful name.

Example

```python
RAG_PROMPT
```

instead of

```python
prompt
```

As projects grow,

multiple prompts may exist.

Clear names improve readability.

---

# Debugging Tip

If the LLM produces poor answers,

print the final prompt first.

Many problems come from:

- Missing context
- Empty variables
- Incorrect placeholders
- Formatting mistakes

Always verify the prompt before changing the model.

---

# Architecture Review

Our RAG pipeline now looks like this.

```text
PDF

↓

Loader

↓

Splitter

↓

Embeddings

↓

Chroma

↓

Retriever

↓

ChatPromptTemplate
```

The next step is connecting the prompt to the LLM.

---

# Common Beginner Mistakes

### Mistake 1

Hardcoding prompts inside app.py.

Keep prompts in a dedicated module.

---

### Mistake 2

Using vague instructions.

Clearly tell the model:

- Use only the provided context.
- Don't guess.
- Say when information is unavailable.

---

### Mistake 3

Not checking the final prompt.

Always inspect what the LLM actually receives.

---

### Mistake 4

Creating multiple copies of the same prompt.

Store one reusable prompt template and use it throughout the application.

---

# Key Takeaways

- `ChatPromptTemplate` replaces manual string formatting.
- Prompt templates improve readability and maintainability.
- Placeholders are filled automatically using `.invoke()`.
- Keep prompts separate from application logic.
- A well-designed prompt is essential for reducing hallucinations and improving answer quality.