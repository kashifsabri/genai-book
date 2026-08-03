

Before we start building any project, let's establish a few engineering principles.

These guidelines will be followed throughout every project in this book.

The goal isn't just to build AI applications.

The goal is to build them the way professional software engineers do.

---

# 1. Every File Must Have One Responsibility

Each file should do one job and do it well.

For example,

```text
loader.py
```

should only load documents.

It should not:

- Generate embeddings
- Query the vector database
- Call the LLM

Similarly,

```text
retriever.py
```

should only retrieve relevant documents.

Keeping responsibilities separate makes the project easier to understand, test, and maintain.

---

# 2. Organize the Project Properly

Avoid putting everything into one file.

Instead, organize related files into a clean project structure.

Example:

```text
rag_project/

│

├── data/
├── cache/
├── indexes/
├── logs/
│
├── loader.py
├── splitter.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompts.py
├── llm.py
├── chain.py
├── config.py
├── app.py
│
├── requirements.txt
├── .env
├── README.md
└── .gitignore
```

A well-organized project is much easier to extend as new features are added.

---

# 3. Create Files Only When Needed

We'll create files gradually.

Instead of generating ten files on the first day,

we'll create each file when we actually need it.

For every new file, we'll answer:

- Why do we need this file?
- What problem does it solve?
- What should it contain?
- What should it NOT contain?

This approach makes the project easier to understand.

---

# 4. Keep Configuration Separate

Never scatter configuration values throughout your code.

Instead,

store them in one place.

Example:

```python
PDF_PATH = "data/employee_handbook.pdf"

CHUNK_SIZE = 500

CHUNK_OVERLAP = 100

MODEL_NAME = "gpt-4.1-mini"
```

When something changes,

only one file needs to be updated.

---

# 5. Never Hardcode Secrets

API keys,

passwords,

database URLs,

and tokens should never appear inside source code.

Instead,

store them inside:

```text
.env
```

Example:

```text
OPENAI_API_KEY=your_api_key_here
```

This improves security and makes sharing the project much safer.

---

# 6. Build Small, Test Often

Professional developers don't write thousands of lines of code before testing.

Instead, they build one component at a time.

Example:

```text
Loader

↓

Test

↓

Chunker

↓

Test

↓

Embeddings

↓

Test

↓

Retriever

↓

Test
```

Small iterations make debugging much easier.

---

# 7. Write Reusable Code

Avoid copying the same logic into multiple places.

If a piece of code might be reused,

move it into its own function or module.

Reusable code is easier to maintain and reduces bugs.

---

# 8. Think Like an Engineer

Throughout every project,

don't just ask:

> "Does it work?"

Also ask:

- Is it readable?
- Can another developer understand it?
- Can it be reused?
- Can it be tested?
- Can it be extended later?

Writing maintainable code is just as important as writing working code.

---

# 9. Learn the "Why", Not Just the "How"

Whenever we introduce:

- A new file
- A new library
- A new folder
- A new design choice

we'll first understand:

- Why we're using it.
- What problem it solves.
- What alternatives exist.
- Why this approach is appropriate for the current project.

Understanding the reasoning behind design decisions is an important part of becoming a software engineer.

---

# 10. Build Like a Real Project

The projects in this book are not small coding exercises.

Each project is designed as if it were part of a real software application.

As we progress through the book,

we'll gradually introduce professional practices such as:

- Logging
- Configuration Management
- Error Handling
- Testing
- Environment Variables
- Docker
- Monitoring
- Deployment

By the final project,

you'll be working with an architecture similar to what is used in production AI systems.

---

# Project Workflow

Every project in this book will follow the same development process.

```text
Understand the Problem
          │
          ▼
Design the Project Structure
          │
          ▼
Create the Required Files
          │
          ▼
Implement One Module
          │
          ▼
Test the Module
          │
          ▼
Refactor the Code
          │
          ▼
Repeat Until Complete
```

Following a consistent workflow makes projects easier to build, debug, and maintain.

---

# What to Expect

Throughout the projects, you'll regularly see sections such as:

- Why Are We Building This?
- File Overview
- Folder Structure
- Best Practice
- Professional Tip
- Debugging Tip
- Common Mistakes
- Industry Insight
- Architecture Review

These sections are included to help you think beyond writing code and start thinking like a software engineer who designs, builds, and maintains real-world AI applications.