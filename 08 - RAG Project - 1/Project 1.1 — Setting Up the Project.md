

## Introduction

Before writing any code,

let's create the project structure.

A good folder structure makes the project:

- Easy to understand
- Easy to maintain
- Easy to debug
- Easy to extend

Instead of putting everything into one file,

we'll separate each responsibility.

---

# Final Project Structure

```text
rag_from_scratch/

│

├── data/
│     employee_handbook.pdf
│
├── loader.py
├── chunker.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompt_builder.py
├── llm.py
├── app.py
│
├── requirements.txt
└── README.md
```

---

# Understanding Each File

## data/

Stores all documents.

Example

```text
employee_handbook.pdf
```

Later you can also store:

- PDFs
- DOCX files
- TXT files

---

## loader.py

Responsible for reading documents.

Example

```text
PDF

↓

Plain Text
```

Nothing else.

---

## chunker.py

Responsible for splitting documents.

Example

```text
Large Document

↓

Chunk 1

↓

Chunk 2

↓

Chunk 3
```

---

## embeddings.py

Responsible for converting chunks into embeddings.

Example

```text
Chunk

↓

Embedding
```

---

## vector_store.py

Stores embeddings inside FAISS.

Also performs similarity search.

---

## retriever.py

Searches the FAISS index.

Returns the most relevant chunks.

---

## prompt_builder.py

Creates the final prompt.

Example

```text
Question

+

Retrieved Context

↓

Prompt
```

---

## llm.py

Communicates with the LLM.

This file is responsible only for generating responses.

---

## app.py

The main entry point.

This file connects every module.

```text
Loader

↓

Chunker

↓

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

# Why Split the Project?

Imagine putting everything into one file.

```text
app.py

2000+ lines
```

Finding bugs becomes difficult.

Instead,

each file has only one responsibility.

This follows the **Single Responsibility Principle (SRP)**.

---

# Create the Project

Create a new folder.

```bash
mkdir rag_from_scratch
```

Move into it.

```bash
cd rag_from_scratch
```

Create the folders.

```bash
mkdir data
```

Create the files.

```bash
touch loader.py
touch chunker.py
touch embeddings.py
touch vector_store.py
touch retriever.py
touch prompt_builder.py
touch llm.py
touch app.py
touch requirements.txt
```

If you're using Windows PowerShell:

```powershell
New-Item loader.py -ItemType File
New-Item chunker.py -ItemType File
New-Item embeddings.py -ItemType File
New-Item vector_store.py -ItemType File
New-Item retriever.py -ItemType File
New-Item prompt_builder.py -ItemType File
New-Item llm.py -ItemType File
New-Item app.py -ItemType File
New-Item requirements.txt -ItemType File
```

---

# Project Architecture

```text
                PDF
                 │
                 ▼
          loader.py
                 │
                 ▼
         chunker.py
                 │
                 ▼
      embeddings.py
                 │
                 ▼
      vector_store.py
                 │
                 ▼
        retriever.py
                 │
                 ▼
     prompt_builder.py
                 │
                 ▼
             llm.py
                 │
                 ▼
            app.py
```

---

# What Happens Next?

Now that our project structure is ready,

we'll start implementing each module.

The first module is:

**loader.py**

We'll learn how to:

- Read PDF files
- Extract text
- Handle multiple pages
- Return clean text for the chunking stage