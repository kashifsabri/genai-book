

## Introduction

Congratulations!

You have completed every building block required to build a modern RAG application using LangChain.

Throughout this project, you learned how to use:

- PyPDFLoader
- RecursiveCharacterTextSplitter
- HuggingFaceEmbeddings
- Chroma
- Retriever
- ChatPromptTemplate
- LCEL
- Streaming
- Conversation History
- Source Citations

Now it's time to combine everything into one complete application.

This chapter doesn't introduce any new concepts.

Instead,

it focuses on integrating everything you've built into a clean, modular application.

---

# Final Architecture

```text
                        User
                          │
                          ▼
                  Ask Question
                          │
                          ▼
             Conversation History
                          │
                          ▼
                    Retriever
                          │
                          ▼
                 Relevant Documents
                          │
                          ▼
                ChatPromptTemplate
                          │
                          ▼
                   ChatOpenAI
                          │
                          ▼
                 StrOutputParser
                          │
          ┌───────────────┴───────────────┐
          ▼                               ▼
     Stream Answer                Source Citations
```

---

# Final Project Structure

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
├── config.py
├── loaders.py
├── splitter.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompts.py
├── chain.py
├── app.py
│
├── requirements.txt
├── .env
├── .gitignore
└── README.md
```

Every file has a single responsibility.

---

# Application Flow

Our application performs the following steps.

```text
Load Existing Chroma Database

↓

Create Retriever

↓

Build LCEL Chain

↓

Accept User Question

↓

Retrieve Documents

↓

Build Prompt

↓

Generate Answer

↓

Stream Response

↓

Display Sources
```

---

# Final app.py

Our application becomes surprisingly small.

```python
from loaders import load_documents
from splitter import split_documents
from vector_store import create_vector_store
from retriever import create_retriever
from chain import build_chain

documents = load_documents()

chunks = split_documents(documents)

vector_store = create_vector_store(chunks)

retriever = create_retriever(vector_store)

chain = build_chain(retriever)

print("LangChain RAG Assistant")
print("Type 'exit' to quit.\n")

while True:

    question = input("You: ")

    if question.lower() == "exit":
        break

    print("\nAssistant:\n")

    for chunk in chain.stream(question):

        print(
            chunk,
            end="",
            flush=True
        )

    print("\n")
```

Notice how little code is required.

Most of the complexity has been moved into reusable modules.

---

# Running the Project

Activate the virtual environment.

```bash
.venv\Scripts\activate
```

Run the application.

```bash
python app.py
```

---

# Example Conversation

User

```text
How many paid leave days do employees receive?
```

Assistant

```text
Employees receive 24 paid leave days every year.
```

---

User

```text
Can they carry them forward?
```

Assistant

```text
Yes.

Unused leave can be carried forward according to the Employee Handbook.
```

---

Sources

```text
employee_handbook.pdf

Page 8
```

---

# What Happens Internally?

When the user asks a question,

our application performs the following steps.

```text
Question

↓

Retriever

↓

Relevant Documents

↓

Prompt Template

↓

ChatOpenAI

↓

Output Parser

↓

Streaming

↓

Answer
```

Everything happens automatically through LCEL.

---

# How Much Code Did We Save?

Let's compare.

Project 1

```text
Loader

Chunker

Embedding Generator

FAISS

Retriever

Prompt Builder

LLM

≈ Hundreds of lines
```

Project 2

```text
Loader

Splitter

Embedding

Chroma

Retriever

Prompt

Chain

≈ Much smaller codebase
```

Notice something.

The concepts are identical.

LangChain simply provides reusable components.

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| Built everything manually | Used LangChain components |
| Custom PDF Loader | PyPDFLoader |
| Custom Chunker | RecursiveCharacterTextSplitter |
| SentenceTransformer | HuggingFaceEmbeddings |
| FAISS | Chroma |
| Manual Retrieval | Retriever |
| Manual Prompt Builder | ChatPromptTemplate |
| OpenAI SDK | ChatOpenAI |
| Manual Pipeline | LCEL |

Understanding Project 1 makes Project 2 much easier.

---

# What Have We Learned?

By completing this project,

you now understand:

- LangChain project structure
- Document loading
- Text splitting
- Embedding models
- Chroma Vector Database
- Retrievers
- Prompt Templates
- LCEL
- Streaming
- Conversation History
- Source Citations

These are the core building blocks used in many production LangChain applications.

---

# Architecture Review

Let's review the complete system.

```text
                     User
                       │
                       ▼
                  app.py
                       │
                       ▼
                 build_chain()
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
 Retriever       Prompt Template       LLM
       │               │                │
       ▼               ▼                ▼
 Chroma DB      ChatPromptTemplate   ChatOpenAI
       │
       ▼
 Embedding Model
       │
       ▼
 Document Chunks
       │
       ▼
 PyPDFLoader
       │
       ▼
      PDF
```

This is a clean,

modular,

production-style architecture.

---

# Professional Tip

Notice that **app.py** contains almost no business logic.

Its only responsibility is to coordinate the application.

Every major feature lives in its own module.

This makes the project:

- Easier to test
- Easier to debug
- Easier to maintain
- Easier to extend

This is how professional software projects are typically organized.

---

# Industry Insight

Many enterprise RAG systems follow this same layered architecture.

The difference is that they often replace:

- Chroma → Pinecone, Weaviate, Milvus
- ChatOpenAI → Azure OpenAI, Claude, Gemini
- Local PDFs → SharePoint, Confluence, S3

Because LangChain abstracts these components,

switching providers usually requires only small code changes.

---

# What's Next?

So far,

our Retriever performs **vector similarity search**.

But what happens when:

- Documents contain tables?
- Documents reference other documents?
- We need hierarchical retrieval?
- We need recursive retrieval?
- We need document summarization before retrieval?

LangChain can do these things,

but another framework was designed specifically for document-centric AI applications.

That framework is:

```text
LlamaIndex
```

In the next project,

we'll build the same RAG application using LlamaIndex and explore features specifically designed for complex document retrieval.

---

# Common Beginner Mistakes

### Mistake 1

Thinking LangChain replaces RAG concepts.

It doesn't.

It simply abstracts their implementation.

---

### Mistake 2

Trying to learn LangChain before understanding RAG.

Understanding the fundamentals first makes LangChain much easier.

---

### Mistake 3

Putting all logic inside `app.py`.

Keep responsibilities separated across modules.

---

### Mistake 4

Treating the framework as a substitute for software engineering.

Good project structure remains important regardless of the framework.

---

# Challenges

## Beginner

- Change the chunk size to 1000.
- Retrieve the top 5 documents instead of 3.
- Replace the sample PDF with your own.

---

## Intermediate

- Support multiple PDFs.
- Display the document name with every answer.
- Add timestamps to the chat output.

---

## Advanced

- Create separate `index.py` and `chat.py` applications.
- Add metadata filtering.
- Allow users to choose the LLM model from `config.py`.
- Build a simple web interface using Streamlit.

---

# Key Takeaways

- You built a complete RAG application using LangChain.
- You understand how LangChain simplifies manual RAG development.
- LCEL provides a clean way to connect AI components.
- Modular project structure improves maintainability.
- You are now ready to explore advanced document retrieval using LlamaIndex.