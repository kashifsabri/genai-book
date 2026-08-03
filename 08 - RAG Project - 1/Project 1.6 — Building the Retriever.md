

## Introduction

Our FAISS index now contains embeddings for every document chunk.

However,

the user doesn't ask questions using embeddings.

The user asks questions using plain English.

Example:

```text
How many paid leave days do employees receive?
```

Our Retriever must:

1. Convert the question into an embedding.
2. Search the FAISS index.
3. Find the nearest vectors.
4. Return the corresponding document chunks.

---

# Retrieval Pipeline

```text
User Question
      │
      ▼
Generate Query Embedding
      │
      ▼
Search FAISS
      │
      ▼
Return Vector IDs
      │
      ▼
Retrieve Original Chunks
```

---

# Step 1 — Create retriever.py

Create a new file.

```text
retriever.py
```

---

# Step 2 — Import Libraries

```python
import numpy as np
```

---

# Step 3 — Understanding FAISS Search

FAISS searches using vectors.

It does **not** search using text.

So before searching,

we must convert the user's question into an embedding.

Example

```text
Question

↓

Embedding

↓

FAISS Search
```

---

# Step 4 — Generate Query Embedding

Suppose the user asks:

```python
query = "How many paid leave days do employees receive?"
```

Convert it into an embedding.

```python
query_embedding = model.encode(query)
```

FAISS expects:

```python
(float32)
```

Convert it.

```python
query_embedding = np.array(
    [query_embedding],
    dtype="float32"
)
```

Notice something.

We wrapped the embedding inside another list.

Why?

Because FAISS expects:

```text
(number_of_vectors, embedding_dimension)
```

Even if we're searching with one query,

FAISS still expects a 2D array.

---

# Step 5 — Search FAISS

Searching is simple.

```python
distances, indices = index.search(
    query_embedding,
    k=3
)
```

Let's understand the output.

Suppose we get

```python
indices

[[2, 5, 1]]
```

This means

```text
Best Match

↓

Chunk 2
```

Second Best

↓

```text
Chunk 5
```

Third Best

↓

```text
Chunk 1
```

---

# Step 6 — Retrieve Original Chunks

Remember,

FAISS stores only vectors.

The actual text is still inside our

```python
chunks
```

list.

Retrieve it.

```python
results = []

for index_id in indices[0]:
    results.append(
        chunks[index_id]
    )
```

Now

```python
results
```

contains the actual text.

---

# Step 7 — Create a Function

```python
import numpy as np


def retrieve(
    query,
    model,
    index,
    chunks,
    k=3
):

    query_embedding = model.encode(query)

    query_embedding = np.array(
        [query_embedding],
        dtype="float32"
    )

    distances, indices = index.search(
        query_embedding,
        k
    )

    results = []

    for index_id in indices[0]:
        results.append(chunks[index_id])

    return results
```

Simple.

Reusable.

Easy to understand.

---

# Step 8 — Test the Retriever

Open

```python
app.py
```

```python
query = "How many paid leave days do employees receive?"

results = retrieve(
    query=query,
    model=model,
    index=index,
    chunks=chunks
)

for result in results:
    print(result)
```

Example Output

```text
Employees receive 24 paid leave days every year.

Employees may carry forward unused leave.

Office timings are from 9 AM to 6 PM.
```

Notice that the Retriever doesn't generate answers.

It only returns relevant chunks.

---

# Step 9 — Understanding Distances

FAISS also returns:

```python
distances
```

Example

```python
[[0.15, 0.42, 0.83]]
```

Smaller values mean the vectors are closer together.

Usually,

the first result is the best match.

For now,

we'll ignore these values.

Later,

they can help us filter weak matches.

---

# Complete retriever.py

```python
import numpy as np


def retrieve(
    query,
    model,
    index,
    chunks,
    k=3
):

    query_embedding = model.encode(query)

    query_embedding = np.array(
        [query_embedding],
        dtype="float32"
    )

    distances, indices = index.search(
        query_embedding,
        k
    )

    results = []

    for index_id in indices[0]:
        results.append(chunks[index_id])

    return results
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

↓

Retriever
```

Remaining

```text
Prompt Builder

↓

LLM

↓

Final Answer
```

---

# Real-World Example

Imagine asking a librarian:

```text
Where is the company's leave policy?
```

The librarian doesn't explain the policy.

Instead,

they bring you the correct pages from the handbook.

That's exactly what the Retriever does.

It retrieves information.

It does **not** answer the question.

---

# Common Beginner Mistakes

### Mistake 1

Passing the question directly to FAISS.

FAISS only understands vectors.

Always convert the query into an embedding first.

---

### Mistake 2

Forgetting to convert the query embedding to `float32`.

Use:

```python
query_embedding = np.array(
    [query_embedding],
    dtype="float32"
)
```

---

### Mistake 3

Expecting FAISS to return document text.

FAISS returns vector positions (indices), not the original text.

Use those indices to retrieve chunks from your `chunks` list.

---

# Key Takeaways

- The Retriever converts a user question into an embedding.
- FAISS searches for the nearest vectors.
- FAISS returns indices, not document text.
- Those indices are used to fetch the original chunks.
- The Retriever prepares the context that will be sent to the LLM.