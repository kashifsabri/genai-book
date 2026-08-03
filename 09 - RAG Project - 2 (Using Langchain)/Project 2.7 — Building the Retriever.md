

## Introduction

Our Chroma database is now ready.

It contains:

- Documents
- Embeddings
- Metadata

The next question is:

> **How do we search this database?**

In Project 1,

we manually:

- Generated the query embedding
- Searched FAISS
- Retrieved vector IDs
- Mapped IDs back to chunks

With LangChain,

this entire process is replaced by one component:

```text
Retriever
```

---

# What is a Retriever?

A Retriever is responsible for finding the most relevant documents for a user's question.

It sits between:

```text
User Question

↓

Vector Database

↓

Relevant Documents
```

Notice something important.

The Retriever **does not generate answers**.

Its only responsibility is retrieving relevant documents.

---

# Project 1 vs Project 2

Project 1

```text
Question

↓

Generate Query Embedding

↓

Search FAISS

↓

Retrieve IDs

↓

Map IDs to Chunks

↓

Return Chunks
```

Project 2

```python
retriever = vector_store.as_retriever()
```

Everything happens internally.

---

# Why Do We Need retriever.py?

Create:

```text
retriever.py
```

Purpose:

- Configure the Retriever
- Define search settings
- Return a reusable Retriever object

This file should never:

- Build prompts
- Call the LLM
- Generate answers

---

# Step 1 — Import Configuration

```python
from config import TOP_K
```

Open:

```text
config.py
```

Add:

```python
TOP_K = 3
```

Now,

the number of retrieved documents can be changed from one place.

---

# Step 2 — Create the Retriever

Suppose we already have a Chroma Vector Store.

Creating a Retriever is simple.

```python
retriever = vector_store.as_retriever()
```

That's it.

The Retriever is ready.

---

# Step 3 — Configure Search

By default,

LangChain uses standard similarity search.

We can configure it.

```python
retriever = vector_store.as_retriever(

    search_kwargs={

        "k": TOP_K
    }

)
```

Now the Retriever always returns the top 3 matching documents.

---

# Step 4 — Create a Function

```python
from config import TOP_K


def create_retriever(vector_store):

    return vector_store.as_retriever(

        search_kwargs={

            "k": TOP_K
        }

    )
```

Simple.

Reusable.

---

# Step 5 — Retrieve Documents

Suppose the user asks:

```text
How many paid leave days do employees receive?
```

Retrieve relevant documents.

```python
documents = retriever.invoke(

    "How many paid leave days do employees receive?"
)
```

Notice that we're using:

```python
invoke()
```

instead of:

```python
search()
```

This is the standard LangChain way of executing runnables.

---

# What is Returned?

The Retriever returns:

```text
List[Document]
```

Example

```text
[
    Document(...),

    Document(...),

    Document(...)
]
```

Each Document still contains:

- page_content
- metadata

---

# Step 6 — Inspect the Results

```python
for document in documents:

    print(document.page_content)

    print(document.metadata)
```

Example Output

```text
Employees receive 24 paid leave days every year.

{
    "source":"employee_handbook.pdf",

    "page":8
}
```

Notice that metadata travels with every retrieved document.

---

# Complete retriever.py

```python
from config import TOP_K


def create_retriever(vector_store):

    return vector_store.as_retriever(

        search_kwargs={

            "k": TOP_K
        }

    )
```

---

# Step 7 — Test the Retriever

Open:

```text
app.py
```

```python
from loaders import load_documents

from splitter import split_documents

from vector_store import create_vector_store

from retriever import create_retriever


documents = load_documents()

chunks = split_documents(
    documents
)

vector_store = create_vector_store(
    chunks
)

retriever = create_retriever(
    vector_store
)

results = retriever.invoke(

    "How many paid leave days do employees receive?"
)

for document in results:

    print(document.page_content)
```

Everything works.

Our Retriever is now searching the Chroma database.

---

# Search Types

The Retriever supports different search strategies.

---

## Similarity Search

Returns the nearest vectors.

```python
retriever = vector_store.as_retriever(

    search_type="similarity"
)
```

This is the default and most commonly used option.

---

## Max Marginal Relevance (MMR)

Returns relevant documents while reducing duplicates.

```python
retriever = vector_store.as_retriever(

    search_type="mmr"
)
```

MMR tries to provide a more diverse set of retrieved documents.

This is useful when many chunks contain similar information.

---

## Similarity Score Threshold

Only returns documents above a certain similarity score.

```python
retriever = vector_store.as_retriever(

    search_type="similarity_score_threshold",

    search_kwargs={

        "score_threshold":0.8
    }
)
```

Useful when you prefer "no answer" over poor-quality retrieval.

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| Manual FAISS search | LangChain Retriever |
| Manual query embeddings | Automatic |
| Manual index lookup | Automatic |
| Returned text chunks | Returns `Document` objects |
| Custom retrieval logic | Built-in retrieval abstraction |

The underlying retrieval process remains the same.

LangChain simply automates it.

---

# Industry Insight

Most production RAG systems don't use the default Retriever configuration.

Engineers typically experiment with:

- Different search strategies
- Different values of `k`
- Metadata filtering
- Hybrid search
- Reranking

These configurations are evaluated using tools like **RAGAS** before deployment.

The Retriever is one of the most frequently tuned components in a production RAG system.

---

# Best Practice

Avoid hardcoding values like:

```python
k=3
```

Instead,

store them inside:

```text
config.py
```

This makes experimentation much easier.

---

# Debugging Tip

Before sending retrieved documents to the LLM,

always inspect them.

Example

```python
for document in results:

    print(document.metadata)

    print(document.page_content)

    print("-" * 50)
```

If retrieval is poor,

improving the prompt won't help.

Always verify that the Retriever is returning the correct documents first.

---

# Architecture Review

Our LangChain pipeline now looks like this.

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

↓

Retriever

↓

Relevant Documents
```

The Retriever has completed the **Retrieval** phase of RAG.

Next,

we'll begin the **Generation** phase.

---

# Common Beginner Mistakes

### Mistake 1

Expecting the Retriever to generate answers.

It only retrieves relevant documents.

The LLM generates the final answer.

---

### Mistake 2

Setting a very high value for `k`.

Retrieving too many documents increases token usage and may reduce answer quality.

---

### Mistake 3

Ignoring metadata.

Metadata is valuable for citations, filtering, and debugging.

---

### Mistake 4

Sending retrieved documents directly to the user.

The Retriever's output should first be processed by a Prompt Template and then passed to the LLM.

---

# Key Takeaways

- A Retriever searches the Vector Store for relevant documents.
- LangChain automatically handles query embeddings and similarity search.
- The Retriever returns `Document` objects, not answers.
- Different search strategies are available for different use cases.
- Retrieval quality is one of the most important factors in a successful RAG application.