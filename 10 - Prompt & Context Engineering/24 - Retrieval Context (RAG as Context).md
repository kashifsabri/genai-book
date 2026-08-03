

## Learning Objectives

By the end of this chapter, you will understand:

- What Retrieval Context is
- Why AI Agents use RAG
- How retrieval improves answers
- When retrieval is necessary
- How retrieval actually works under the hood (chunking, embeddings, vector search)
- What a basic RAG pipeline looks like in code
- Common failure modes: bad chunking, wrong top-k, stale data, no citations

---

# Introduction

Suppose you ask an AI:

```text
What is our company's leave policy?
```

Can the model answer?

Probably not.

Why?

Because your company's leave policy was **never part of its training data**.

The AI first needs to retrieve that information.

This retrieved information becomes part of the model's context.

This is called **Retrieval Context**.

---

# What is Retrieval Context?

Retrieval Context is information fetched from an external knowledge source and added to the prompt before the model generates an answer.

The knowledge source could be:

- PDFs
- Databases
- Websites
- Company Documents
- Vector Databases

---

# Visual Flow

```text
User Question

↓

Retriever

↓

Relevant Documents

↓

LLM

↓

Answer
```

Instead of guessing,

the model answers using retrieved information.

---

# How Retrieval Actually Works

"The retriever finds relevant documents" sounds simple, but there are real steps behind it. RAG has two phases: **indexing** (done ahead of time) and **retrieval** (done per question).

### Phase 1: Indexing (offline, done once)

```text
Documents (PDFs, pages, etc.)

↓ split into chunks

Chunks (e.g. 300-800 tokens each)

↓ convert each chunk to a vector

Embeddings (lists of numbers representing meaning)

↓ store

Vector Database
```

- **Chunking**: documents are split into smaller pieces, because embedding an entire 50-page PDF as one unit loses precision. Chunk size is a real design decision — too small loses context, too large dilutes relevance.
- **Embedding**: each chunk is passed through an embedding model that converts its meaning into a vector (a list of numbers). Chunks with similar meaning end up with similar vectors.
- **Storage**: these vectors are stored in a vector database (e.g. Pinecone, Weaviate, pgvector, Chroma) alongside the original text.

### Phase 2: Retrieval (online, per question)

```text
User Question

↓ embed the question

Question Vector

↓ similarity search

Top-K Most Similar Chunks

↓ inject into context

LLM
```

The question is embedded using the **same** embedding model, then compared against every stored chunk using a similarity metric (commonly cosine similarity). The **top-k** most similar chunks (e.g. top 5) are pulled back and added to the prompt as context.

---

# A Minimal RAG Example in Code

```python
# --- Indexing (done once, ahead of time) ---
chunks = split_into_chunks(handbook_text, chunk_size=500, overlap=50)
chunk_vectors = [embed(chunk) for chunk in chunks]
vector_db.upsert(chunk_vectors, metadata=chunks)

# --- Retrieval (done per question) ---
question = "How many vacation days do employees receive?"
question_vector = embed(question)

top_chunks = vector_db.similarity_search(question_vector, top_k=5)

context = "\n\n".join(chunk.text for chunk in top_chunks)

prompt = f"""Answer using ONLY the context below. If the answer isn't in the context, say so.

Context:
{context}

Question: {question}"""

response = model.create(messages=[{"role": "user", "content": prompt}])
```

Note the instruction _"Answer using ONLY the context"_ — this is a common technique to reduce hallucination by explicitly grounding the model.

---

# Example

User

```text
How many vacation days do employees receive?
```

Retriever finds:

```text
Employee Handbook

↓

Employees receive 24 paid vacation days annually.
```

The model uses this information to answer.

---

# Without Retrieval

Question

```text
What is our leave policy?
```

LLM

```text
I'm not sure.
```

or

The model may hallucinate.

---

# With Retrieval

Question

```text
What is our leave policy?
```

↓

Retriever

↓

Leave Policy Document

↓

LLM

↓

Correct Answer

````

The model now has the required information.

---

# Why is Retrieval Important?

LLMs have limited knowledge.

They don't automatically know:

- Your company policies
- Today's news
- Internal documents
- Customer databases

Retrieval gives the model access to that information.

---

# Real-World Example

Suppose you're building an AI HR Assistant.

The user asks:

```text
Can I work remotely on Fridays?
````

The AI retrieves:

```text
Remote Work Policy.pdf
```

Then answers based on that document,

not from memory.

---

# Retrieval vs Memory

This is an important distinction.

|Retrieval|Memory|
|---|---|
|Fetches information from external sources|Stores previous conversations or user preferences|
|Document knowledge|User knowledge|
|Usually uses RAG|Usually uses chat history or databases|

Example

Retrieval

```text
Company Handbook
```

Memory

```text
User prefers morning meetings.
```

Both become part of the context,

but they come from different places.

---

# Improving Retrieval Quality

Basic vector search isn't always enough. Common improvements used in production RAG systems:

- **Hybrid search** — combine vector (semantic) search with traditional keyword search (e.g. BM25). Pure semantic search can miss exact terms like product codes or names.
- **Re-ranking** — retrieve a larger candidate set (e.g. top 20), then use a smaller, more precise re-ranking model to pick the best 5. This catches cases where the fastest similarity search isn't the most accurate.
- **Metadata filtering** — narrow the search using structured filters first (e.g. "only search documents tagged `HR` and dated after 2024") before running similarity search.
- **Citations / source attribution** — return which document and section each answer came from, so users can verify it. This also makes hallucinations easier to catch.

---

# When Should You Use Retrieval?

Use Retrieval when the answer depends on:

- Company documents
- Product manuals
- Research papers
- Policies
- Frequently changing information

---

# When Should You Avoid It?

Don't retrieve documents for simple questions.

Example

```text
What is Python?
```

The model already knows this.

Retrieval would only make the response:

- Slower
- More expensive

---

# Best Practice

Retrieve only the **most relevant** documents.

Don't send an entire knowledge base.

More context is not always better.

Also:

- Choose chunk size deliberately — test smaller and larger chunks against real questions.
- Keep the vector index up to date; stale embeddings mean stale (or wrong) answers.
- Always instruct the model to answer only from retrieved context when factual accuracy matters, and to say when it doesn't know.

---

# Common Beginner Mistakes

### Mistake 1

Retrieving too many documents.

Only retrieve what is needed.

---

### Mistake 2

Retrieving unrelated documents.

Poor retrieval often leads to poor answers.

---

### Mistake 3

Thinking retrieval replaces the LLM.

It doesn't.

The retriever finds information.

The LLM explains it.

---

### Mistake 4

Using a single fixed chunk size and top-k value without testing.

Chunking strategy and top-k size have a huge impact on answer quality — they should be tuned, not guessed.

---

### Mistake 5

Letting the index go stale.

If source documents change (e.g. an updated policy) but the vector database isn't re-indexed, the AI will confidently answer with outdated information.

---

# Interview Tip ⭐

A common interview question is:

> **Why do AI Agents use RAG?**

A good answer is:

RAG allows AI Agents to retrieve up-to-date or private information from external sources before generating a response, typically by embedding documents into a vector database and retrieving the most similar chunks at query time.

This improves accuracy and reduces hallucinations.

---

# Key Takeaways

- Retrieval Context comes from external knowledge sources.
- RAG is one way of providing retrieval context.
- RAG has two phases: indexing (chunk + embed + store) and retrieval (embed the question + similarity search).
- Chunking strategy and top-k choice directly affect answer quality.
- Hybrid search and re-ranking improve retrieval accuracy beyond basic vector search.
- Citations/source attribution make answers verifiable and hallucinations easier to spot.
- Retrieval improves accuracy by giving the model relevant information.
- Retrieve only the documents needed for the current question.
- Keep the vector index up to date — stale data leads to stale answers.
- Retrieval and Memory are different concepts.

---

