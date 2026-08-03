

## Introduction

In the previous chapter,

we split our documents into smaller chunks.

Now we need to convert those chunks into vectors.

This process is called **Embedding Generation**.

The concept hasn't changed since Project 1.

The only difference is that LangChain provides a wrapper around embedding models.

Instead of using:

```python
SentenceTransformer()
```

we'll use:

```text
HuggingFaceEmbeddings
```

---

# Project 1 vs Project 2

Project 1

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer(
    "all-MiniLM-L6-v2"
)

embeddings = model.encode(chunks)
```

Project 2

```python
from langchain_huggingface import (
    HuggingFaceEmbeddings
)

embedding_model = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
```

Notice that LangChain doesn't create a new embedding model.

It simply wraps the existing Sentence Transformers model.

---

# Why Use HuggingFaceEmbeddings?

LangChain works with many embedding providers.

Examples:

- Hugging Face
- OpenAI
- Google
- Cohere
- Azure OpenAI
- Ollama

Instead of changing your application,

you simply replace the embedding class.

This makes your code much more flexible.

---

# Step 1 — Why Do We Need embeddings.py?

Create:

```text
embeddings.py
```

Purpose:

- Load the embedding model.
- Provide a reusable embedding object.
- Keep embedding configuration separate from the rest of the application.

This file should not:

- Create vector databases
- Retrieve documents
- Generate answers

It has only one responsibility.

---

# Step 2 — Import the Embedding Class

```python
from langchain_huggingface import (
    HuggingFaceEmbeddings
)
```

---

# Step 3 — Load Configuration

Open:

```text
config.py
```

Add:

```python
EMBEDDING_MODEL = (
    "sentence-transformers/all-MiniLM-L6-v2"
)
```

Now every module uses the same model.

---

# Step 4 — Create the Embedding Model

```python
from langchain_huggingface import (
    HuggingFaceEmbeddings
)

from config import EMBEDDING_MODEL

embedding_model = HuggingFaceEmbeddings(
    model_name=EMBEDDING_MODEL
)
```

That's it.

The model is now ready.

---

# Step 5 — Understanding What Happens

When LangChain creates this object,

internally it loads:

```text
SentenceTransformer
```

Exactly like we did manually in Project 1.

The difference is that LangChain now provides a standard interface that works with many vector stores and retrievers.

---

# Step 6 — Test the Model

Open:

```text
app.py
```

```python
from embeddings import embedding_model

vector = embedding_model.embed_query(
    "How many paid leave days do employees receive?"
)

print(len(vector))
```

Example Output

```text
384
```

Our query has been converted into an embedding.

---

# Step 7 — Embed Multiple Documents

LangChain provides another method.

```python
texts = [

    "Employees receive 24 paid leave days.",

    "Office timings are from 9 AM to 6 PM."
]

vectors = embedding_model.embed_documents(
    texts
)

print(len(vectors))
```

Output

```text
2
```

Each document receives its own embedding.

---

# What's the Difference?

LangChain provides two methods.

---

## embed_query()

Used for:

```text
User Questions
```

Example

```python
embedding_model.embed_query(
    "What is the leave policy?"
)
```

---

## embed_documents()

Used for:

```text
Documents
```

Example

```python
embedding_model.embed_documents(
    texts
)
```

Although both generate embeddings,

using the correct method makes your code easier to understand.

---

# Complete embeddings.py

```python
from langchain_huggingface import (
    HuggingFaceEmbeddings
)

from config import EMBEDDING_MODEL


embedding_model = HuggingFaceEmbeddings(
    model_name=EMBEDDING_MODEL
)
```

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| `SentenceTransformer` | `HuggingFaceEmbeddings` |
| `model.encode()` | `embed_documents()` |
| `model.encode()` | `embed_query()` |
| Direct library usage | LangChain wrapper |

The underlying embedding model is still the same.

LangChain simply provides a consistent interface.

---

# Why Doesn't This File Generate Embeddings?

You might wonder:

> Why didn't we create a function like:

```python
generate_embeddings()
```

The answer is simple.

In LangChain,

the Vector Store handles embedding generation automatically.

Later we'll write:

```python
Chroma.from_documents(

    documents=chunks,

    embedding=embedding_model
)
```

Notice that we don't manually generate embeddings anymore.

The Vector Store asks the embedding model to generate them internally.

This removes a lot of repetitive code.

---

# Industry Insight

One of LangChain's biggest advantages is provider independence.

Today you might use:

```text
HuggingFaceEmbeddings
```

Tomorrow,

you could switch to:

```text
OpenAIEmbeddings
```

or

```text
GoogleGenerativeAIEmbeddings
```

The rest of your application remains almost unchanged.

This flexibility is one reason why LangChain is popular in production systems.

---

# Best Practice

Load the embedding model only once.

Avoid creating a new embedding model every time a request arrives.

Embedding models consume memory and take time to initialize.

Creating them once during application startup improves performance.

---

# Debugging Tip

Always verify that your embedding model works before creating the Vector Store.

Example

```python
vector = embedding_model.embed_query(
    "Hello World"
)

print(len(vector))
```

If this step fails,

fix it before moving on.

Many Vector Store errors are actually caused by embedding model configuration issues.

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
```

Remaining

```text
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

Using different embedding models for indexing and querying.

Always use the same model for both.

---

### Mistake 2

Creating a new embedding model inside every function.

Load it once and reuse it.

---

### Mistake 3

Trying to manually generate embeddings before creating the Vector Store.

LangChain handles this automatically when building the Vector Store.

---

# Key Takeaways

- `HuggingFaceEmbeddings` is a LangChain wrapper around Sentence Transformers.
- `embed_documents()` is used for document embeddings.
- `embed_query()` is used for user queries.
- The embedding model should be loaded only once.
- LangChain automatically uses the embedding model when creating the Vector Store.