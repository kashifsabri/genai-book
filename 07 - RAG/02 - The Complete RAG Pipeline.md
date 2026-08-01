

## Introduction

In the previous chapter, we learned why RAG is needed.

Now let's understand **how a RAG system actually works**.

A RAG application is not just an LLM.

It is a pipeline made up of multiple components working together.

Understanding this pipeline is one of the most important parts of learning RAG.

---

# 2.1 What Happens When a User Asks a Question?

Suppose a user asks:

```text
What is the company's leave policy?
```

A RAG system does **not** send this question directly to the LLM.

Instead, it performs several steps.

```text
User Question
      │
      ▼
Convert Question into an Embedding
      │
      ▼
Search the Vector Database
      │
      ▼
Retrieve Relevant Chunks
      │
      ▼
Build a Prompt
      │
      ▼
Send Prompt to the LLM
      │
      ▼
Generate Answer
```

Let's understand each step.

---

# 2.2 Step 1 — User Asks a Question

Everything starts with a question.

Example:

```text
How many paid leave days do employees receive?
```

At this stage,

the system only has plain text.

---

# 2.3 Step 2 — Convert the Question into an Embedding

The computer cannot compare plain text efficiently.

So the question is converted into an **Embedding**.

Example:

```text
How many paid leave days do employees receive?

↓

Embedding

↓

[0.23, -0.91, 0.54, ...]
```

This numerical representation allows the system to compare the question with stored documents.

We'll study embeddings in detail in the next chapter.

---

# 2.4 Step 3 — Search the Vector Database

The Vector Database already contains embeddings for all company documents.

The system compares the user's embedding with the stored embeddings.

Its goal is simple.

> Find the documents that are most similar to the user's question.

Example:

```text
Question

↓

"Leave Policy"

↓

Vector Database

↓

Top 3 Matching Chunks
```

---

# 2.5 Step 4 — Retrieve the Relevant Chunks

Suppose the document contains:

```text
Employees receive 24 paid leave days every year.
```

The system retrieves this paragraph.

It does **not** retrieve the entire PDF.

Only the most relevant parts are selected.

These parts are called **Chunks**.

---

# 2.6 Step 5 — Build the Prompt

Now the system combines:

- The user's question
- The retrieved chunks

Example:

```text
Context:

Employees receive 24 paid leave days every year.

Question:

How many paid leave days do employees receive?
```

This complete prompt is sent to the LLM.

---

# 2.7 Step 6 — Generate the Final Answer

The LLM reads the prompt.

Since the required information is already included,

it can answer correctly.

Example:

```text
Employees receive 24 paid leave days every year.
```

Notice something important.

The LLM didn't search the PDF.

The retrieval system did.

The LLM simply used the provided context to generate the answer.

---

# 2.8 The Two Pipelines in RAG

Many beginners think there is only one pipeline.

Actually, there are **two**.

## Pipeline 1 — Indexing Pipeline

This pipeline runs **before users ask questions**.

```text
Documents
      │
      ▼
Load Documents
      │
      ▼
Split into Chunks
      │
      ▼
Generate Embeddings
      │
      ▼
Store in Vector Database
```

This usually happens only once.

---

## Pipeline 2 — Retrieval Pipeline

This pipeline runs **every time a user asks a question**.

```text
User Question
      │
      ▼
Generate Embedding
      │
      ▼
Search Vector Database
      │
      ▼
Retrieve Chunks
      │
      ▼
Build Prompt
      │
      ▼
LLM
      │
      ▼
Answer
```

Both pipelines are equally important.

---

# 2.9 Why Separate These Pipelines?

Imagine you have 10,000 PDF files.

You don't want to:

- Read every PDF
- Split every PDF
- Create embeddings

every time someone asks a question.

Instead,

you prepare everything in advance.

Later,

only the retrieval pipeline runs.

This makes RAG much faster.

---

# 2.10 Python Example (Pipeline Overview)

In LangChain, the workflow looks like this.

```python
documents
      ↓
Text Splitter
      ↓
Embeddings
      ↓
Vector Store
      ↓
Retriever
      ↓
LLM
```

You'll build each of these components in the upcoming chapters.

Don't worry if some names are new.

We'll learn them one by one.

---

# 2.11 Real-World Example

Imagine visiting a library.

Before the library opens,

the librarian:

- Organizes books
- Labels shelves
- Creates a catalog

This is the **Indexing Pipeline**.

Later,

when you ask:

```text
Do you have a Python book?
```

the librarian quickly searches the catalog,

finds the correct shelf,

and gives you the book.

This is the **Retrieval Pipeline**.

A RAG system works in exactly the same way.

---

# What's Next?

Now we understand the complete RAG pipeline.

The next question is:

> **How does the computer compare two pieces of text?**

It cannot compare plain English directly.

Instead,

it converts text into **Embeddings**.

In the next chapter, we'll learn:

**03. Embeddings**

---

# Common Beginner Mistakes

### Mistake 1

Thinking the LLM searches documents.

It doesn't.

The retriever searches the documents.

---

### Mistake 2

Thinking documents are processed every time a question is asked.

They aren't.

Documents are processed once during indexing.

---

### Mistake 3

Thinking RAG has only one pipeline.

It has two:

- Indexing Pipeline
- Retrieval Pipeline

---

# Key Takeaways

- A RAG system consists of multiple components working together.
- The Indexing Pipeline prepares documents before users ask questions.
- The Retrieval Pipeline runs when a user asks a question.
- The retriever finds relevant chunks.
- The LLM generates an answer using the retrieved context.
- Understanding these two pipelines makes the rest of RAG much easier.