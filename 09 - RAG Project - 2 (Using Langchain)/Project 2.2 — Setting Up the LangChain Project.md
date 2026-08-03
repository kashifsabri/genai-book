
## Introduction

Before writing any LangChain code,

let's prepare our project.

A well-organized project makes development easier as the application grows.

Instead of placing everything inside one file,

we'll separate different responsibilities into different modules.

This is the same approach used in professional software development.

---

# Project Structure

Create the following project structure.

```text
rag_langchain/

│

├── data/
│      employee_handbook.pdf
│
├── db/
│
├── logs/
│
├── loaders.py
├── splitter.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompts.py
├── chain.py
├── config.py
├── app.py
│
├── requirements.txt
├── .env
├── .gitignore
└── README.md
```

We'll create these files one by one as we build the project.

---

# Understanding the Project Structure

Let's understand the purpose of each folder and file.

---

## data/

Stores all source documents.

Example:

```text
employee_handbook.pdf
leave_policy.pdf
remote_work.pdf
```

Keeping documents inside a dedicated folder makes the project easier to manage.

---

## db/

Stores the persistent Chroma database.

Unlike Project 1,

we won't rebuild the vector database every time the application starts.

The embeddings will be stored here.

---

## logs/

Stores application logs.

Examples:

```text
application.log
errors.log
```

Logging becomes increasingly important as projects grow.

---

## loaders.py

Responsible for loading documents.

In this project,

LangChain's **PyPDFLoader** will replace our custom PDF loader.

---

## splitter.py

Responsible for splitting documents into chunks.

We'll use:

```text
RecursiveCharacterTextSplitter
```

instead of our custom chunker.

---

## embeddings.py

Loads the embedding model.

The same embedding model should be used for:

- Indexing documents
- Searching documents

---

## vector_store.py

Creates and manages the Chroma vector database.

Responsibilities include:

- Creating the database
- Saving embeddings
- Loading an existing database

---

## retriever.py

Creates a LangChain Retriever.

Instead of manually searching FAISS,

LangChain provides a Retriever interface.

---

## prompts.py

Stores prompt templates.

Separating prompts from application logic makes them easier to update.

---

## chain.py

Builds the complete LangChain pipeline.

Instead of manually connecting every component,

we'll build a reusable chain.

---

## config.py

Stores application configuration.

Examples:

```python
PDF_DIRECTORY = "data"

CHROMA_DIRECTORY = "db"

MODEL_NAME = "gpt-4.1-mini"

EMBEDDING_MODEL = "sentence-transformers/all-MiniLM-L6-v2"

CHUNK_SIZE = 500

CHUNK_OVERLAP = 100
```

Changing these values later becomes much easier.

---

## app.py

The entry point of the application.

Its responsibilities are:

- Load configuration
- Build the chain
- Accept user questions
- Display answers

Notice that **app.py** should contain very little business logic.

Most of the work happens inside the other modules.

---

# Why Use Multiple Files?

Imagine everything lived inside one file.

```text
app.py

2500 Lines
```

Finding bugs would become difficult.

Adding new features would become risky.

Instead,

each module has a single responsibility.

This follows the **Single Responsibility Principle (SRP)**.

---

# Create a Virtual Environment

Creating a virtual environment keeps project dependencies isolated.

Create the environment.

Windows

```powershell
python -m venv .venv
```

Linux/macOS

```bash
python3 -m venv .venv
```

---

# Activate the Environment

Windows

```powershell
.venv\Scripts\activate
```

Linux/macOS

```bash
source .venv/bin/activate
```

After activation,

your terminal should show something similar to:

```text
(.venv)
```

---

# Create requirements.txt

We'll gradually add libraries to this file.

For now:

```text
langchain
langchain-community
langchain-openai
langchain-huggingface
langchain-chroma
chromadb
pypdf
python-dotenv
```

Install them.

```bash
pip install -r requirements.txt
```

---

# Create .gitignore

Never upload unnecessary files to GitHub.

Create:

```text
.gitignore
```

Example:

```text
.venv/

__pycache__/

.env

db/

*.pyc
```

This keeps your repository clean.

---

# Create README.md

Every professional project should include documentation.

For now,

add a simple title.

```markdown
# LangChain RAG

A Retrieval-Augmented Generation (RAG) application built using LangChain.
```

We'll improve this README as the project grows.

---

# Project Workflow

Our development process will follow these steps.

```text
Project Setup
      │
      ▼
Document Loader
      │
      ▼
Text Splitter
      │
      ▼
Embeddings
      │
      ▼
Chroma
      │
      ▼
Retriever
      │
      ▼
Prompt
      │
      ▼
Chain
      │
      ▼
LLM
      │
      ▼
Answer
```

Each component will be built and tested before moving to the next one.

---

# Professional Tip

Don't wait until the end of the project to organize your code.

Starting with a clean project structure makes future development much easier.

A well-organized project is often easier to maintain than one with fewer lines of code but poor structure.

---

# Common Beginner Mistakes

### Mistake 1

Putting every class and function inside `app.py`.

Create dedicated modules with clear responsibilities.

---

### Mistake 2

Skipping a virtual environment.

This can lead to dependency conflicts between projects.

---

### Mistake 3

Committing `.env` or database files to GitHub.

Always use `.gitignore` to exclude sensitive or generated files.

---

# Key Takeaways

- A clean project structure improves maintainability.
- Each module should have one clear responsibility.
- Use a virtual environment to isolate dependencies.
- Store configuration, prompts, and business logic in separate files.
- We'll build and test one module at a time.