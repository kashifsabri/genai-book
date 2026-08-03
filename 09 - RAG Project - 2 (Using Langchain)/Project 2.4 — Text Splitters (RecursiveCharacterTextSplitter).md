

## Introduction

In the previous chapter,

we loaded our PDF using:

```text
PyPDFLoader
```

The output was:

```text
Document Objects
```

However,

these documents are still too large for an LLM to process efficiently.

Just like in Project 1,

we need to split them into smaller chunks.

Instead of writing our own chunking logic,

LangChain provides a built-in text splitter.

We'll use:

```text
RecursiveCharacterTextSplitter
```

---

# Why Do We Need a Text Splitter?

Imagine a document with:

```text
100 Pages
```

Generating one embedding for the entire document would make retrieval inefficient.

Instead,

we split it into smaller chunks.

```text
Document

↓

Chunk 1

↓

Chunk 2

↓

Chunk 3
```

Each chunk receives its own embedding.

---

# Project 1 vs Project 2

Project 1

```python
chunks = chunk_text(
    text,
    chunk_size=500,
    overlap=100
)
```

Project 2

```python
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=100
)

chunks = text_splitter.split_documents(documents)
```

Notice that LangChain removes all of the manual chunking logic.

---

# Why Recursive Character Text Splitter?

LangChain provides several text splitters.

The most commonly used one is:

```text
RecursiveCharacterTextSplitter
```

Why?

Because it tries to preserve meaning.

Instead of splitting text randomly,

it attempts to split using:

1. Paragraphs
2. Blank lines
3. Sentences
4. Spaces
5. Characters

Only if one method fails does it move to the next.

This produces much cleaner chunks.

---

# Step 1 — Create splitter.py

Create:

```text
splitter.py
```

Purpose:

Split large documents into smaller chunks while preserving context.

---

# Step 2 — Import the Splitter

```python
from langchain_text_splitters import (
    RecursiveCharacterTextSplitter
)
```

---

# Step 3 — Create the Splitter

```python
from config import (
    CHUNK_SIZE,
    CHUNK_OVERLAP
)

text_splitter = RecursiveCharacterTextSplitter(

    chunk_size=CHUNK_SIZE,

    chunk_overlap=CHUNK_OVERLAP
)
```

Notice that we aren't hardcoding values.

They come from:

```text
config.py
```

---

# Step 4 — Split the Documents

```python
chunks = text_splitter.split_documents(
    documents
)
```

That's it.

The splitter automatically creates smaller Document objects.

---

# What is Returned?

Input

```text
15 Documents
```

↓

Output

```text
42 Chunk Documents
```

Notice something.

The output is still:

```text
Document Objects
```

The metadata is preserved automatically.

---

# Step 5 — Inspect the Chunks

```python
print(len(chunks))
```

Example Output

```text
42
```

Print the first chunk.

```python
print(chunks[0].page_content)
```

Print its metadata.

```python
print(chunks[0].metadata)
```

Example

```python
{
    "source":
    "employee_handbook.pdf",

    "page":0
}
```

Even after splitting,

the metadata is still available.

---

# Step 6 — Create a Function

```python
from langchain_text_splitters import (
    RecursiveCharacterTextSplitter
)

from config import (
    CHUNK_SIZE,
    CHUNK_OVERLAP
)


def split_documents(documents):

    splitter = RecursiveCharacterTextSplitter(

        chunk_size=CHUNK_SIZE,

        chunk_overlap=CHUNK_OVERLAP
    )

    return splitter.split_documents(
        documents
    )
```

---

# Step 7 — Test the Splitter

Open:

```text
app.py
```

```python
from loaders import load_documents
from splitter import split_documents

documents = load_documents()

chunks = split_documents(
    documents
)

print(len(chunks))

print(chunks[0].page_content)
```

Example Output

```text
42

Welcome to ABC Company...
```

Everything works correctly.

---

# Complete splitter.py

```python
from langchain_text_splitters import (
    RecursiveCharacterTextSplitter
)

from config import (
    CHUNK_SIZE,
    CHUNK_OVERLAP
)


def split_documents(documents):

    splitter = RecursiveCharacterTextSplitter(

        chunk_size=CHUNK_SIZE,

        chunk_overlap=CHUNK_OVERLAP
    )

    return splitter.split_documents(
        documents
    )
```

---

# Comparison with Project 1

Let's compare what changed.

| Project 1 | Project 2 |
|-----------|-----------|
| Custom chunking function | `RecursiveCharacterTextSplitter` |
| Returned strings | Returns `Document` objects |
| Manual overlap logic | Built-in overlap support |
| Manual implementation | LangChain abstraction |

Notice that the concept is identical.

Only the implementation has changed.

---

# Why Keep Document Objects?

Remember,

LangChain doesn't convert chunks into plain strings.

Each chunk is still a Document object.

Example

```text
Document

├── page_content
└── metadata
```

This allows metadata to flow through the entire pipeline.

Later,

our Retriever can tell us exactly:

- Which PDF
- Which page

the answer came from.

---

# Industry Insight

In enterprise RAG systems,

chunking is one of the most important design decisions.

Changing:

```text
Chunk Size

500

↓

1000
```

can significantly change retrieval quality.

There is no universally perfect chunk size.

Engineers often experiment with different chunk sizes and evaluate them using tools like **RAGAS** before deploying a production system.

---

# Best Practice

Always define:

```python
CHUNK_SIZE

CHUNK_OVERLAP
```

inside:

```text
config.py
```

Avoid hardcoding these values throughout the application.

This makes experimentation much easier.

---

# Debugging Tip

Always inspect a few chunks before generating embeddings.

Example

```python
for i, chunk in enumerate(chunks[:3]):

    print(f"\nChunk {i+1}")

    print(chunk.page_content)

    print(chunk.metadata)
```

Many retrieval issues are actually caused by poor chunking rather than poor embeddings.

---

# Project Architecture

Completed

```text
PDF

↓

PyPDFLoader

↓

RecursiveCharacterTextSplitter
```

Remaining

```text
Embeddings

↓

Chroma

↓

Retriever

↓

Prompt

↓

Chain

↓

LLM
```

---

# Common Beginner Mistakes

### Mistake 1

Creating chunks that are too large.

Large chunks increase token usage and often reduce retrieval quality.

---

### Mistake 2

Setting overlap to zero.

Without overlap,

important information can be split across chunk boundaries.

---

### Mistake 3

Converting Document objects into strings too early.

Many LangChain components work directly with Document objects.

Keep them intact whenever possible.

---

# Key Takeaways

- `RecursiveCharacterTextSplitter` replaces our custom chunker.
- It automatically creates overlapping chunks.
- It preserves document metadata.
- Chunk size and overlap should be configurable.
- Proper chunking is one of the biggest factors affecting RAG performance.