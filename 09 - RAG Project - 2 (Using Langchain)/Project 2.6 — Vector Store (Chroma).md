
# Project 2.6 — Vector Store (Chroma)

## Introduction

In the previous chapter,

we loaded our embedding model.

Now we need a place to store those embeddings.

In Project 1,

we used:

```text
FAISS
```

In this project,

we'll use:

```text
Chroma
```

Chroma is one of the most popular vector databases used with LangChain.

It not only stores embeddings,

but also stores:

- Documents
- Metadata
- Embeddings

This makes it much easier to build production-ready RAG applications.

---

# Project 1 vs Project 2

Project 1

```text
Chunks

↓

Generate Embeddings

↓

FAISS
```

Project 2

```text
Documents

↓

Chroma

↓

Embeddings Generated Automatically
```

Notice something.

We no longer manually generate embeddings.

Chroma does it internally.

---

# What is Chroma?

Chroma is a vector database.

Its responsibilities include:

- Storing documents
- Generating embeddings
- Storing embeddings
- Performing similarity search
- Returning relevant documents

Unlike FAISS,

Chroma keeps both the vectors and the original documents together.

---

# Why Use Chroma?

Compared to FAISS,

Chroma provides:

✅ Persistent Storage

✅ Metadata Support

✅ Tight LangChain Integration

✅ Easy Retrieval

This makes it an excellent choice for LangChain applications.

---

# Step 1 — Why Do We Need vector_store.py?

Create:

```text
vector_store.py
```

Purpose:

- Create the Chroma database
- Save documents
- Load existing databases
- Return a reusable Vector Store

This module should only manage the vector database.

---

# Step 2 — Import Chroma

```python
from langchain_chroma import Chroma
```

---

# Step 3 — Load Configuration

Open:

```text
config.py
```

Add:

```python
CHROMA_DIRECTORY = "db"
```

This directory will store our database.

---

# Step 4 — Create the Vector Store

```python
from langchain_chroma import Chroma

from config import CHROMA_DIRECTORY

from embeddings import embedding_model


vector_store = Chroma.from_documents(

    documents=chunks,

    embedding=embedding_model,

    persist_directory=CHROMA_DIRECTORY
)
```

Let's understand what's happening.

---

# What Happens Internally?

When we call:

```python
Chroma.from_documents()
```

LangChain automatically:

```text
Document Objects

↓

Generate Embeddings

↓

Store Embeddings

↓

Store Metadata

↓

Create Chroma Database
```

All of this happens with a single method call.

---

# Step 5 — Create a Function

```python
from langchain_chroma import Chroma

from config import CHROMA_DIRECTORY

from embeddings import embedding_model


def create_vector_store(documents):

    vector_store = Chroma.from_documents(

        documents=documents,

        embedding=embedding_model,

        persist_directory=CHROMA_DIRECTORY
    )

    return vector_store
```

Simple.

Reusable.

---

# Step 6 — Test the Vector Store

Open:

```text
app.py
```

```python
from loaders import load_documents

from splitter import split_documents

from vector_store import create_vector_store


documents = load_documents()

chunks = split_documents(
    documents
)

vector_store = create_vector_store(
    chunks
)

print("Vector Store Created Successfully")
```

Output

```text
Vector Store Created Successfully
```

---

# Where is the Database Stored?

After running the application,

you'll notice a new folder.

```text
db/
```

Inside it,

Chroma stores its database files.

Unlike FAISS,

you don't need to manually save anything.

Persistence happens automatically.

---

# Complete vector_store.py

```python
from langchain_chroma import Chroma

from config import CHROMA_DIRECTORY

from embeddings import embedding_model


def create_vector_store(documents):

    return Chroma.from_documents(

        documents=documents,

        embedding=embedding_model,

        persist_directory=CHROMA_DIRECTORY
    )
```

---

# Loading an Existing Database

Once the database has been created,

there's no need to rebuild it every time.

You can simply load it.

```python
from langchain_chroma import Chroma

from config import CHROMA_DIRECTORY

from embeddings import embedding_model


vector_store = Chroma(

    persist_directory=CHROMA_DIRECTORY,

    embedding_function=embedding_model
)
```

Notice that we're loading an existing database instead of creating a new one.

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| FAISS | Chroma |
| Stored only vectors | Stores documents, vectors, and metadata |
| Manual embedding generation | Automatic embedding generation |
| Manual save/load | Built-in persistence |
| Manual retrieval logic | Built-in Retriever integration |

The workflow is much simpler.

---

# Industry Insight

Many production RAG systems separate **Indexing** and **Querying** into different applications.

Example:

```text
Indexer

↓

Load PDFs

↓

Chunk Documents

↓

Generate Embeddings

↓

Store in Chroma

──────────────

Chat Application

↓

Load Existing Chroma Database

↓

Retrieve Documents

↓

Answer Questions
```

This architecture avoids rebuilding the database every time users ask questions.

Large companies often update the vector database on a schedule, such as nightly or whenever new documents are added.

---

# Best Practice

Separate database creation from database loading.

Create the database only when documents change.

Load the existing database during normal application startup.

This significantly improves application startup time.

---

# Debugging Tip

After creating the database,

verify that the database directory has been created.

Example:

```text
db/

├── chroma.sqlite3

├── ...
```

If the directory is empty,

the database was not created successfully.

Check:

- Embedding model
- Document loading
- File permissions

before continuing.

---

# Project Architecture

Completed

```text
PDF

↓

PyPDFLoader

↓

RecursiveCharacterTextSplitter

↓

HuggingFaceEmbeddings

↓

Chroma
```

Remaining

```text
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

Rebuilding the database every time the application starts.

Create it once.

Load it afterward.

---

### Mistake 2

Deleting the database folder accidentally.

The `db/` directory contains your indexed documents.

Deleting it means rebuilding the entire database.

---

### Mistake 3

Using different embedding models when creating and loading the database.

Always use the same embedding model.

Otherwise,

retrieval quality may be poor.

---

# Key Takeaways

- Chroma is a vector database with built-in LangChain support.
- It stores documents, embeddings, and metadata together.
- Embeddings are generated automatically during indexing.
- Chroma supports persistent storage.
- Separate indexing and querying for better performance in production systems.