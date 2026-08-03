

## Introduction

In Project 1, we built a complete RAG application from scratch.

We implemented every component ourselves.

- PDF Loader
- Text Chunker
- Embedding Generator
- FAISS Vector Store
- Retriever
- Prompt Builder
- LLM Integration

Although this helped us understand how RAG works internally, we also wrote hundreds of lines of code.

Now imagine building a much larger AI application.

Instead of one PDF, you may have:

- Thousands of documents
- Multiple embedding models
- Different vector databases
- Multiple LLM providers
- Chat history
- Tool calling
- Agents

Managing all of this manually becomes difficult.

This is why frameworks like **LangChain** exist.

---

# What is LangChain?

LangChain is an open-source framework for building applications powered by Large Language Models (LLMs).

Instead of writing every component yourself,

LangChain provides reusable building blocks.

Think of it as a toolbox.

Instead of building every tool yourself,

you simply choose the tools you need.

---

# Why Did We Build RAG Manually First?

Many tutorials start directly with LangChain.

For example,

instead of writing:

```python
text = load_pdf(file_path)
chunks = chunk_text(text)
embeddings = generate_embeddings(chunks)
index = create_vector_store(embeddings)
```

they simply write:

```python
loader = PyPDFLoader("employee_handbook.pdf")
documents = loader.load()
```

This works.

But the reader doesn't understand what happens internally.

Because we built everything ourselves in Project 1,

every LangChain component will now make sense.

---

# Manual RAG vs LangChain RAG

| Project 1 | Project 2 |
|-----------|-----------|
| PdfReader | PyPDFLoader |
| Custom Chunker | RecursiveCharacterTextSplitter |
| SentenceTransformer | HuggingFaceEmbeddings |
| FAISS | Chroma |
| Custom Retriever | LangChain Retriever |
| Custom Prompt | ChatPromptTemplate |
| OpenAI API | ChatOpenAI |
| Custom Pipeline | LCEL Chain |

Notice something.

The concepts are exactly the same.

Only the implementation changes.

---

# How LangChain Simplifies Development

Suppose we want to create a Retriever.

In Project 1,

we wrote:

- Generate query embedding
- Convert to float32
- Search FAISS
- Retrieve chunk indices
- Return original chunks

With LangChain,

we simply write:

```python
retriever = vector_store.as_retriever()
```

The result is the same,

but the code is much shorter.

---

# Does LangChain Replace Python?

No.

LangChain is built using Python.

Understanding Python is still essential.

LangChain simply provides reusable components that reduce repetitive code.

---

# Should You Always Use LangChain?

Not necessarily.

Use LangChain when:

- Building applications quickly
- Working with multiple LLM providers
- Using tools and agents
- Building production AI workflows

Avoid unnecessary frameworks for very small projects where plain Python is sufficient.

The right tool depends on the problem.

---

# What We'll Build

We'll rebuild the same RAG application from Project 1.

This time, we'll use LangChain components.

Our pipeline will look like this.

```text
PDF
 │
 ▼
PyPDFLoader
 │
 ▼
RecursiveCharacterTextSplitter
 │
 ▼
HuggingFaceEmbeddings
 │
 ▼
Chroma
 │
 ▼
Retriever
 │
 ▼
ChatPromptTemplate
 │
 ▼
ChatOpenAI
 │
 ▼
Answer
```

At the end of this project,

you'll notice that the application becomes much smaller,

while still performing the same tasks.

---

# What You'll Learn

In this project, you'll learn how to use:

- PyPDFLoader
- RecursiveCharacterTextSplitter
- HuggingFaceEmbeddings
- Chroma
- Retriever
- ChatPromptTemplate
- ChatOpenAI
- LCEL (LangChain Expression Language)
- Streaming Responses
- Conversation History

These are the components most commonly used in modern LangChain applications.

---

# What's Next?

Before writing any code,

let's set up our project.

In the next chapter,

we'll install LangChain, organize the project structure, and prepare our development environment.