

## Introduction

In the previous chapter, we converted every chunk into an embedding.

Now we have something like this.

```text
Chunk 1

↓

[0.21, -0.43, 0.72, ...]
```

```text
Chunk 2

↓

[-0.15, 0.61, -0.18, ...]
```

```text
Chunk 3

↓

[0.48, 0.11, -0.33, ...]
```

These embeddings are stored in memory.

But how do we search them efficiently?

The answer is **FAISS**.

---

# What is FAISS?

FAISS stands for:

```text
Facebook AI Similarity Search
```

Its job is simple.

```text
Embeddings

↓

Store

↓

Search Similar Embeddings
```

FAISS does **not** understand language.

It only works with numbers.

---

# Step 1 — Install FAISS

```bash
pip install faiss-cpu
```

---

# Step 2 — Create vector_store.py

Create a new file.

```text
vector_store.py
```

---

# Step 3 — Import Libraries

```python
import faiss
import numpy as np
```

---

# Step 4 — Why Convert to NumPy?

The Sentence Transformer returns embeddings as NumPy arrays.

FAISS expects:

- NumPy arrays
- Data type = float32

Let's check.

```python
print(type(embeddings))
```

Output

```text
<class 'numpy.ndarray'>
```

Now check the data type.

```python
print(embeddings.dtype)
```

Output

```text
float64
```

FAISS prefers:

```text
float32
```

Convert them.

```python
embeddings = embeddings.astype("float32")
```

---

# Step 5 — Find the Embedding Dimension

Every embedding has the same length.

Example

```python
dimension = embeddings.shape[1]

print(dimension)
```

Output

```text
384
```

Our FAISS index must know this dimension.

---

# Step 6 — Create a FAISS Index

```python
index = faiss.IndexFlatL2(dimension)
```

Let's understand this line.

```text
IndexFlatL2
```

means:

- Flat → Compare every vector.
- L2 → Use Euclidean Distance.

For learning,

this is the simplest index.

---

# Step 7 — Store Embeddings

```python
index.add(embeddings)
```

That's it.

Your vectors are now stored inside FAISS.

---

# Step 8 — Check the Number of Vectors

```python
print(index.ntotal)
```

Example

```text
27
```

FAISS now stores:

```text
27 Embeddings
```

---

# Step 9 — Create a Reusable Function

```python
import faiss
import numpy as np


def create_vector_store(embeddings):

    embeddings = embeddings.astype("float32")

    dimension = embeddings.shape[1]

    index = faiss.IndexFlatL2(dimension)

    index.add(embeddings)

    return index
```

---

# Step 10 — Test the Vector Store

Open

```python
app.py
```

```python
from loader import load_pdf
from chunker import chunk_text
from embeddings import generate_embeddings
from vector_store import create_vector_store

text = load_pdf(
    "data/employee_handbook.pdf"
)

chunks = chunk_text(text)

embeddings = generate_embeddings(chunks)

index = create_vector_store(
    embeddings
)

print(index.ntotal)
```

Output

```text
27
```

Congratulations!

You have created your first Vector Database.

---

# Step 11 — Save the FAISS Index

Suppose your application closes.

Should we rebuild the index every time?

No.

Save it.

```python
faiss.write_index(
    index,
    "employee_index.faiss"
)
```

A file is created.

```text
employee_index.faiss
```

---

# Step 12 — Load the Index

Instead of rebuilding,

load it.

```python
index = faiss.read_index(
    "employee_index.faiss"
)
```

Loading is much faster than regenerating embeddings.

---

# Complete vector_store.py

```python
import faiss
import numpy as np


def create_vector_store(embeddings):

    embeddings = embeddings.astype("float32")

    dimension = embeddings.shape[1]

    index = faiss.IndexFlatL2(dimension)

    index.add(embeddings)

    return index


def save_vector_store(index, file_path):
    faiss.write_index(index, file_path)


def load_vector_store(file_path):
    return faiss.read_index(file_path)
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

↓

FAISS
```

Remaining

```text
Retriever

↓

Prompt Builder

↓

LLM
```

---

# Real-World Example

Imagine a library.

Instead of walking through every shelf every time,

the library creates an index.

When someone asks for a book,

the index quickly tells you where to look.

FAISS works in a similar way.

It stores vectors so they can be searched efficiently.

---

# Common Beginner Mistakes

### Mistake 1

Passing `float64` embeddings to FAISS.

Convert them to `float32`.

```python
embeddings = embeddings.astype("float32")
```

---

### Mistake 2

Using the wrong embedding dimension.

Always use:

```python
embeddings.shape[1]
```

---

### Mistake 3

Forgetting that FAISS stores only vectors.

The original text chunks are **not** stored inside the index.

You must keep the chunks separately so you can map search results back to the original text.

---

# Key Takeaways

- FAISS stores embeddings for fast similarity search.
- `IndexFlatL2` is the simplest FAISS index.
- Embeddings should be converted to `float32`.
- Save the index to avoid rebuilding it every time.
- Keep the original text chunks alongside the FAISS index because FAISS stores only vectors.