

## Introduction

In the previous chapter, we split our PDF into smaller chunks.

Now we need to convert every chunk into numbers.

Remember,

computers cannot understand text directly.

Instead,

they understand vectors.

Example

```text
Employees receive 24 paid leave days.

↓

[0.21, -0.45, 0.72, ...]
```

This process is called **Embedding Generation**.

---

# Our Goal

Convert this

```text
Chunk 1

Employees receive 24 paid leave days.
```

↓

into

```text
Embedding

[0.21, -0.45, 0.72, ...]
```

for every chunk.

---

# Step 1 — Install the Library

We'll use Sentence Transformers.

```bash
pip install sentence-transformers
```

---

# Step 2 — Create embeddings.py

Create a new file.

```text
embeddings.py
```

---

# Step 3 — Load the Embedding Model

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer(
    "all-MiniLM-L6-v2"
)
```

This downloads the model the first time.

After that,

it loads from your local cache.

---

# Why This Model?

We'll use:

```text
sentence-transformers/all-MiniLM-L6-v2
```

because it is:

- Free
- Fast
- Small
- Excellent for learning
- Widely used in RAG tutorials

---

# Step 4 — Generate One Embedding

Suppose we have one chunk.

```python
chunk = "Employees receive 24 paid leave days every year."
```

Generate its embedding.

```python
embedding = model.encode(chunk)

print(embedding)
```

Output (shortened)

```text
[0.18, -0.42, 0.73, ...]
```

Congratulations!

You've converted text into a vector.

---

# Step 5 — Check the Shape

Let's see how many numbers are inside the embedding.

```python
print(embedding.shape)
```

Example Output

```text
(384,)
```

This means the model represents every sentence using **384 numbers**.

Every chunk will produce a vector with the same length.

---

# Step 6 — Generate Embeddings for All Chunks

Instead of one chunk,

we have many.

Example

```python
chunks = [
    "Employees receive 24 paid leave days.",
    "Office timings are from 9 AM to 6 PM.",
    "Employees can work remotely."
]
```

Generate embeddings.

```python
embeddings = model.encode(chunks)

print(embeddings.shape)
```

Output

```text
(3, 384)
```

What does this mean?

- 3 chunks
- 384 values per chunk

---

# Understanding the Shape

```text
Chunk 1

↓

384 Numbers
```

```text
Chunk 2

↓

384 Numbers
```

```text
Chunk 3

↓

384 Numbers
```

Together,

they become

```text
(3, 384)
```

This is called an **Embedding Matrix**.

---

# Step 7 — Create a Reusable Function

Let's move this into a function.

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer(
    "all-MiniLM-L6-v2"
)


def generate_embeddings(chunks):
    return model.encode(chunks)
```

Simple and reusable.

---

# Step 8 — Test the Function

Open

```python
app.py
```

```python
from loader import load_pdf
from chunker import chunk_text
from embeddings import generate_embeddings

text = load_pdf(
    "data/employee_handbook.pdf"
)

chunks = chunk_text(text)

embeddings = generate_embeddings(chunks)

print(embeddings.shape)
```

Example Output

```text
(27, 384)
```

This means:

```text
27 Chunks

↓

27 Embeddings
```

Perfect!

---

# Step 9 — Why Are Embeddings Needed?

Suppose your document says:

```text
Employees receive paid leave.
```

The user asks:

```text
How many vacation days do employees get?
```

The words are different.

A keyword search might fail.

Embeddings capture the **meaning** of the text,

making semantic search possible.

---

# Complete embeddings.py

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer(
    "all-MiniLM-L6-v2"
)


def generate_embeddings(chunks):
    return model.encode(chunks)
```

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
```

Remaining

```text
FAISS

↓

Retriever

↓

Prompt Builder

↓

LLM
```

---

# Real-World Example

Imagine every document has a fingerprint.

Instead of comparing the entire document,

the computer compares fingerprints.

Embeddings are like digital fingerprints that represent the meaning of a piece of text.

---

# Common Beginner Mistakes

### Mistake 1

Generating one embedding for the entire PDF.

Always generate one embedding per chunk.

---

### Mistake 2

Thinking embeddings contain readable text.

They don't.

They are numerical representations of meaning.

---

### Mistake 3

Using different embedding models during indexing and retrieval.

If documents are embedded with one model and queries with another,

the vectors may not be comparable.

Always use the same embedding model for both.

---

# Key Takeaways

- Embeddings convert text into vectors.
- Every chunk gets its own embedding.
- The `SentenceTransformer` model generates these embeddings.
- Similar meanings produce similar vectors.
- These embeddings will be stored in FAISS in the next step.