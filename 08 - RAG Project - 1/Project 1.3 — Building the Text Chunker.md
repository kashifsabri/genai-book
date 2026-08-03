

## Introduction

In the previous chapter, we successfully extracted text from a PDF.

Now we have one large string.

Example:

```text
Welcome to ABC Company.

Employees receive 24 paid leave days every year.

Working hours are from 9 AM to 6 PM.

Employees can work remotely twice a week.

...
```

Should we generate one embedding for this entire document?

No.

Instead, we split the document into **smaller pieces** called **chunks**.

---

# Why Do We Need Chunking?

Imagine our PDF contains:

```text
300 Pages
```

If we create one embedding for the entire PDF,

the Retriever must retrieve all 300 pages.

This causes:

- Poor retrieval
- High token usage
- Slower responses

Instead we create smaller chunks.

```text
Chunk 1

Company Introduction
```

↓

```text
Chunk 2

Leave Policy
```

↓

```text
Chunk 3

Working Hours
```

Now the Retriever only returns the relevant chunk.

---

# Step 1 — Create chunker.py

Create

```text
chunker.py
```

---

# Step 2 — Create a Simple Chunk Function

```python
def chunk_text(text):
    return text.split("\n\n")
```

This splits the document whenever it finds two line breaks.

Example

Input

```text
Welcome

Employees receive 24 paid leave days.

Working hours are from 9 AM to 6 PM.
```

Output

```python
[
    "Welcome",
    "Employees receive 24 paid leave days.",
    "Working hours are from 9 AM to 6 PM."
]
```

---

# Step 3 — Test the Chunker

Open

```python
app.py
```

```python
from loader import load_pdf
from chunker import chunk_text

text = load_pdf(
    "data/employee_handbook.pdf"
)

chunks = chunk_text(text)

print(chunks)
```

Output

```text
[
'Welcome to ABC Company.',
'Employees receive 24 paid leave days every year.',
'Working hours are from 9 AM to 6 PM.',
...
]
```

Congratulations!

Your first chunker works.

---

# Problem with This Approach

Our current chunker only works if the PDF already contains blank lines.

Many PDFs look like this.

```text
Welcome to ABC Company. Employees receive 24 paid leave days every year. Working hours are from 9 AM to 6 PM...
```

Everything becomes one giant paragraph.

Our chunker fails.

We need a smarter approach.

---

# Step 4 — Character-Based Chunking

Instead of splitting by paragraphs,

split every fixed number of characters.

```python
def chunk_text(
    text,
    chunk_size=500
):
    chunks = []

    for i in range(0, len(text), chunk_size):
        chunks.append(
            text[i:i + chunk_size]
        )

    return chunks
```

---

# Understanding the Logic

Suppose

```text
Text Length = 2000 characters
```

Chunk Size

```text
500
```

The loop creates:

```text
Characters

0 → 499
```

↓

```text
500 → 999
```

↓

```text
1000 → 1499
```

↓

```text
1500 → 1999
```

Result

```text
4 Chunks
```

---

# Step 5 — Test Again

```python
chunks = chunk_text(
    text,
    chunk_size=300
)

print(len(chunks))
```

Output

```text
12
```

Print the first chunk.

```python
print(chunks[0])
```

---

# Problem Again

Look carefully.

Suppose a sentence ends here.

```text
Employees receive 24 paid leave days every year.
```

Our chunker might produce

Chunk 1

```text
Employees receive 24 paid leave
```

Chunk 2

```text
days every year.
```

The sentence has been broken.

Meaning is lost.

---

# Step 6 — Add Chunk Overlap

Instead of jumping exactly 500 characters,

move a little less.

Example

```python
def chunk_text(
    text,
    chunk_size=500,
    overlap=100
):
    chunks = []

    start = 0

    while start < len(text):

        end = start + chunk_size

        chunks.append(
            text[start:end]
        )

        start += chunk_size - overlap

    return chunks
```

---

# How Overlap Works

Suppose

```text
Chunk Size = 500

Overlap = 100
```

Chunk 1

```text
Characters

0 → 499
```

Chunk 2

```text
Characters

400 → 899
```

Notice something.

Characters

```text
400 → 499
```

appear in both chunks.

This preserves context.

---

# Step 7 — Test the Overlap

```python
chunks = chunk_text(
    text,
    chunk_size=300,
    overlap=50
)

print(chunks[0])

print("----------------")

print(chunks[1])
```

You'll notice that the second chunk starts before the first one ends.

---

# Complete chunker.py

```python
def chunk_text(
    text,
    chunk_size=500,
    overlap=100
):
    chunks = []

    start = 0

    while start < len(text):

        end = start + chunk_size

        chunks.append(
            text[start:end]
        )

        start += chunk_size - overlap

    return chunks
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
```

Remaining

```text
Embeddings

↓

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

Imagine reading a book.

Instead of tearing it into random pieces,

you divide it into small sections.

To avoid losing context,

each section repeats a few sentences from the previous one.

Chunk overlap works exactly the same way.

---

# Common Beginner Mistakes

### Mistake 1

Creating chunks that are too large.

Large chunks increase token usage and reduce retrieval accuracy.

---

### Mistake 2

Creating chunks that are too small.

Very small chunks lose important context.

---

### Mistake 3

Ignoring chunk overlap.

Without overlap, important information may be split between two chunks.

---

# Key Takeaways

- Chunking divides large documents into smaller pieces.
- Fixed-size chunking is easy to implement but may split sentences.
- Chunk overlap preserves context between chunks.
- Manual chunking helps you understand what frameworks automate.
- The output of this module becomes the input for the Embedding module.