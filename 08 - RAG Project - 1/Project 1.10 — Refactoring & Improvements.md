
## Introduction

Congratulations!

You've built a complete RAG application from scratch.

It works.

But if you were building this for a real company, would you deploy it as it is?

Probably not.

Real-world software is not just about making something work.

It's also about making it:

- Faster
- Cleaner
- Easier to maintain
- Easier to scale
- Easier to debug

In this chapter, we'll improve our project like a software engineer.

---

# Improvement 1 — Save the FAISS Index

Currently,

every time we run:

```bash
python app.py
```

our application:

- Reads the PDF
- Creates chunks
- Generates embeddings
- Builds the FAISS index

again.

This is unnecessary.

Instead,

generate the index once and save it.

```python
from vector_store import (
    save_vector_store
)

save_vector_store(
    index,
    "employee_index.faiss"
)
```

Next time,

simply load it.

```python
from vector_store import (
    load_vector_store
)

index = load_vector_store(
    "employee_index.faiss"
)
```

This makes startup much faster.

---

# Improvement 2 — Cache Embeddings

Generating embeddings is one of the slowest steps.

Instead of generating them every time,

save them.

```python
import numpy as np

np.save(
    "embeddings.npy",
    embeddings
)
```

Load them later.

```python
embeddings = np.load(
    "embeddings.npy"
)
```

This avoids recomputing embeddings for unchanged documents.

---

# Improvement 3 — Save the Chunks

Remember,

FAISS stores only vectors.

The original chunks must also be saved.

```python
import json

with open(
    "chunks.json",
    "w",
    encoding="utf-8"
) as file:

    json.dump(
        chunks,
        file,
        indent=4
    )
```

Load them later.

```python
with open(
    "chunks.json",
    "r",
    encoding="utf-8"
) as file:

    chunks = json.load(file)
```

Now,

both vectors and text can be reused.

---

# Improvement 4 — Move Configuration to config.py

Avoid hardcoding values throughout your project.

Instead,

create:

```text
config.py
```

```python
PDF_PATH = "data/employee_handbook.pdf"

CHUNK_SIZE = 500

CHUNK_OVERLAP = 100

TOP_K = 3

MODEL_NAME = "all-MiniLM-L6-v2"

FAISS_INDEX = "employee_index.faiss"

EMBEDDINGS_FILE = "embeddings.npy"

CHUNKS_FILE = "chunks.json"
```

Now your application becomes easier to configure.

---

# Improvement 5 — Add Logging

Instead of writing:

```python
print("Loading PDF...")
```

use Python's logging module.

```python
import logging

logging.basicConfig(
    level=logging.INFO
)

logging.info(
    "Loading PDF..."
)
```

Benefits:

- Better debugging
- Different log levels
- Easy troubleshooting

---

# Improvement 6 — Handle Exceptions

Suppose the PDF doesn't exist.

Without error handling,

your application crashes.

Instead,

use:

```python
try:

    text = load_pdf(
        PDF_PATH
    )

except FileNotFoundError:

    print(
        "PDF file not found."
    )
```

Handle common errors gracefully.

Examples:

- Missing PDF
- Empty PDF
- Corrupted PDF
- Missing API key
- Empty retrieval results

---

# Improvement 7 — Separate Indexing and Querying

Currently,

every time the application starts,

it performs both indexing and querying.

A better design is:

```text
Index Documents

↓

Save FAISS

↓

Exit
```

Later,

run a different application.

```text
Load FAISS

↓

Ask Questions
```

Most production systems separate these two processes.

---

# Improvement 8 — Support Multiple Documents

Instead of one PDF,

store many.

```text
data/

employee_handbook.pdf

leave_policy.pdf

remote_work.pdf

salary_policy.pdf
```

Load them all.

```python
import os

pdf_files = [
    file
    for file in os.listdir("data")
    if file.endswith(".pdf")
]
```

This creates a larger knowledge base.

---

# Improvement 9 — Organize the Project

Our project is growing.

A cleaner structure is:

```text
rag_from_scratch/

│

├── data/

├── indexes/

│      employee_index.faiss

├── cache/

│      embeddings.npy
│      chunks.json

├── loader.py
├── chunker.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompt_builder.py
├── llm.py
├── config.py
├── app.py
```

Everything has its own place.

---

# Improvement 10 — Prepare for Production

Our current application is perfect for learning.

A production-ready version would add:

- Metadata Filtering
- Persistent Vector Database
- Authentication
- Conversation Memory
- Streaming Responses
- Logging
- Monitoring
- Docker
- API Layer
- Web Interface

These topics will be covered in later projects.

---

# Before Refactoring

```text
PDF

↓

Everything Happens

↓

Answer
```

---

# After Refactoring

```text
Index Documents
        │
        ▼
 Save FAISS + Cache
        │
        ▼
Load Saved Index
        │
        ▼
Retrieve
        │
        ▼
Prompt
        │
        ▼
LLM
        │
        ▼
Answer
```

Notice that indexing and querying are now independent.

This is how most production RAG systems are designed.

---

# Real-World Example

Imagine a search engine.

Every time you searched Google,

would it crawl the entire internet again?

Of course not.

Google builds its index first.

Searches only use the existing index.

A production RAG system follows the same principle.

---

# Common Beginner Mistakes

### Mistake 1

Rebuilding embeddings every time the application starts.

Generate them once and reuse them.

---

### Mistake 2

Hardcoding configuration values.

Move them into a dedicated configuration file.

---

### Mistake 3

Using `print()` everywhere.

Use Python's `logging` module for better debugging.

---

### Mistake 4

Ignoring error handling.

Always anticipate missing files, invalid data, and API failures.

---

### Mistake 5

Mixing indexing and querying in one workflow.

Separate them into independent processes.

---

# Key Takeaways

- A working prototype is not the same as a production-ready application.
- Save FAISS indexes and embeddings to improve performance.
- Store chunks separately because FAISS only stores vectors.
- Centralize configuration in a `config.py` file.
- Use logging and exception handling to improve reliability.
- Separate indexing from querying for better scalability.
- These improvements make the project easier to maintain and prepare it for the more advanced RAG systems you'll build in the next projects.