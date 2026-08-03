

## Project Goal

Build a Retrieval-Augmented Generation (RAG) application using only Python libraries.

No LangChain.

No LlamaIndex.

We'll build every step ourselves.

By the end of this project, you'll understand how RAG actually works internally.

---

# What We Will Build

```

                    PDF
                     │
                     ▼
              Read Document
                     │
                     ▼
              Split into Chunks
                     │
                     ▼
          Generate Embeddings
                     │
                     ▼
              Store in FAISS
                     │
                     ▼
              User Question
                     │
                     ▼
         Generate Question Embedding
                     │
                     ▼
          Search Similar Chunks
                     │
                     ▼
          Build Final Prompt
                     │
                     ▼
                   LLM
                     │
                     ▼
                  Answer

```

---

# Technologies Used

| Library | Purpose |
|----------|----------|
| pypdf | Read PDFs |
| sentence-transformers | Generate Embeddings |
| faiss | Store/Search Vectors |
| numpy | Vector Operations |
| transformers *(or OpenAI API)* | LLM |

---

# Project Structure

```text
rag_from_scratch/

│

├── data/
│     employee_handbook.pdf
│
├── app.py
├── loader.py
├── chunker.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompt_builder.py
├── llm.py
└── requirements.txt
```

---

# What You'll Learn

By building this project, you'll understand:

✅ Reading PDFs

✅ Chunking

✅ Embeddings

✅ FAISS

✅ Similarity Search

✅ Retrieval

✅ Prompt Engineering

✅ Context Injection

✅ LLM Response Generation

---

# Project Roadmap

We'll build the project in small steps.

## Step 1

Read PDFs

↓

## Step 2

Chunk Documents

↓

## Step 3

Generate Embeddings

↓

## Step 4

Create FAISS Index

↓

## Step 5

Search Similar Documents

↓

## Step 6

Build Prompt

↓

## Step 7

Send Prompt to LLM

↓

## Step 8

Generate Final Answer

---

# Final Output

```

Ask a Question:

How many paid leave days do employees receive?

```

↓

```

Searching Documents...

```

↓

```

Retrieved Context:

Employees receive 24 paid leave days every year.

```

↓

```

Final Answer:

Employees receive 24 paid leave days every year according to the Employee Handbook.

```

---

# Before We Begin

Install the required libraries.

```bash
pip install pypdf
pip install sentence-transformers
pip install faiss-cpu
pip install numpy
pip install transformers
```

If you're using an OpenAI model instead of a local model:

```bash
pip install openai
```

---

# In the Next Chapter

We'll start with the first component of every RAG system:

**Step 1 — Reading PDFs**

We'll build our own PDF loader before moving on to chunking and embeddings.