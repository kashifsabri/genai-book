
## Introduction

Congratulations!

We've built every individual component of our RAG system.

Now it's time to connect them into one complete application.

Our final pipeline looks like this.

```text
                PDF
                 │
                 ▼
            Load PDF
                 │
                 ▼
          Split into Chunks
                 │
                 ▼
       Generate Embeddings
                 │
                 ▼
          Build FAISS Index
                 │
                 ▼
          User Question
                 │
                 ▼
      Generate Query Embedding
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
            Final Answer
```

---

# Final Project Structure

```text
rag_from_scratch/

│

├── data/
│     employee_handbook.pdf
│
├── loader.py
├── chunker.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompt_builder.py
├── llm.py
├── app.py
│
├── requirements.txt
└── README.md
```

---

# Step 1 — Import Everything

Open

```text
app.py
```

```python
from loader import load_pdf
from chunker import chunk_text
from embeddings import (
    model,
    generate_embeddings
)
from vector_store import (
    create_vector_store
)
from retriever import retrieve
from prompt_builder import build_prompt
from llm import generate_answer
```

---

# Step 2 — Load the PDF

```python
text = load_pdf(
    "data/employee_handbook.pdf"
)
```

---

# Step 3 — Create Chunks

```python
chunks = chunk_text(
    text,
    chunk_size=500,
    overlap=100
)
```

---

# Step 4 — Generate Embeddings

```python
embeddings = generate_embeddings(
    chunks
)
```

---

# Step 5 — Create FAISS Index

```python
index = create_vector_store(
    embeddings
)
```

---

# Step 6 — Ask the User a Question

```python
question = input(
    "Ask a question: "
)
```

---

# Step 7 — Retrieve Relevant Chunks

```python
retrieved_chunks = retrieve(
    query=question,
    model=model,
    index=index,
    chunks=chunks,
    k=3
)
```

---

# Step 8 — Build Context

```python
context = "\n\n".join(
    retrieved_chunks
)
```

---

# Step 9 — Build the Prompt

```python
prompt = build_prompt(
    context=context,
    question=question
)
```

---

# Step 10 — Generate the Answer

```python
answer = generate_answer(
    prompt
)
```

---

# Step 11 — Display the Result

```python
print("\nRetrieved Context\n")
print(context)

print("\nAnswer\n")
print(answer)
```

---

# Complete app.py

```python
from loader import load_pdf
from chunker import chunk_text
from embeddings import (
    model,
    generate_embeddings
)
from vector_store import (
    create_vector_store
)
from retriever import retrieve
from prompt_builder import build_prompt
from llm import generate_answer


text = load_pdf(
    "data/employee_handbook.pdf"
)

chunks = chunk_text(
    text,
    chunk_size=500,
    overlap=100
)

embeddings = generate_embeddings(
    chunks
)

index = create_vector_store(
    embeddings
)

question = input(
    "Ask a question: "
)

retrieved_chunks = retrieve(
    query=question,
    model=model,
    index=index,
    chunks=chunks,
    k=3
)

context = "\n\n".join(
    retrieved_chunks
)

prompt = build_prompt(
    context=context,
    question=question
)

answer = generate_answer(
    prompt
)

print("\nRetrieved Context\n")
print(context)

print("\nAnswer\n")
print(answer)
```

---

# Running the Project

Run

```bash
python app.py
```

Example

```text
Ask a question:

How many paid leave days do employees receive?
```

---

# Output

```text
Retrieved Context

Employees receive 24 paid leave days every year.

Unused leave can be carried forward.
```

```text
Answer

Employees receive 24 paid leave days every year according to the Employee Handbook.
```

Congratulations!

You have built your first RAG application completely from scratch.

---

# How Data Flows Through the System

```text
employee_handbook.pdf
          │
          ▼
      loader.py
          │
          ▼
     chunker.py
          │
          ▼
   embeddings.py
          │
          ▼
 vector_store.py
          │
          ▼
    retriever.py
          │
          ▼
 prompt_builder.py
          │
          ▼
       llm.py
          │
          ▼
     Final Answer
```

---

# Project Limitations

This project is designed for learning.

It has some limitations.

- Only supports PDF files.
- Creates embeddings every time the application starts.
- Rebuilds the FAISS index on every run.
- No conversation memory.
- No metadata filtering.
- No streaming responses.
- Uses a single document.

We'll improve all of these in the next projects.

---

# What You'll Build Next

In Project 2, we'll rebuild the same application using **LangChain**.

Instead of writing hundreds of lines of code ourselves,

LangChain provides ready-made components for:

- Document Loaders
- Text Splitters
- Embeddings
- Vector Stores
- Retrievers
- Prompt Templates
- Chains

You'll notice that the application becomes much shorter, because LangChain abstracts many of the implementation details you've already learned.

---

# Common Beginner Mistakes

### Mistake 1

Rebuilding embeddings every time the application starts.

In production, generate embeddings once and reuse them.

---

### Mistake 2

Skipping error handling.

Always validate that:

- The PDF was loaded.
- Text was extracted.
- Chunks were created.
- Retrieval returned results.

---

### Mistake 3

Treating this as a production-ready system.

This project is meant to teach the internals of RAG.

Production systems require persistence, logging, monitoring, caching, authentication, and many other features.

---

# Key Takeaways

- You built every component of a RAG pipeline from scratch.
- You understand how documents become searchable knowledge.
- You know how retrieval and prompt construction work internally.
- Frameworks like LangChain simplify these steps but do not change the underlying concepts.
- This foundation will make advanced RAG frameworks much easier to learn.