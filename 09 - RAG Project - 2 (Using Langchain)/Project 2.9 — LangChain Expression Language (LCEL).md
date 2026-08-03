

## Introduction

So far, we've built every individual component of our RAG application.

We now have:

- Document Loader
- Text Splitter
- Embedding Model
- Chroma Vector Store
- Retriever
- Prompt Template

The only thing left is connecting these components together.

In Project 1,

we manually called every function.

```python
text = load_pdf()

chunks = chunk_text(text)

embeddings = generate_embeddings(chunks)

index = create_vector_store(embeddings)

results = retrieve(question)

prompt = build_prompt(context, question)

answer = generate_answer(prompt)
```

As applications grow,

this approach becomes difficult to maintain.

LangChain provides a cleaner way to connect components.

This is called **LCEL**.

---

# What is LCEL?

LCEL stands for:

```text
LangChain Expression Language
```

It is LangChain's modern way of connecting components together.

Instead of manually calling functions,

we create a pipeline.

```text
Retriever

↓

Prompt

↓

LLM

↓

Output
```

Each component passes its output to the next component.

---

# Why Do We Need LCEL?

Imagine building an application with:

- Prompt
- LLM
- Output Parser
- Memory
- Tools
- Logging
- Retry Logic

Connecting everything manually becomes difficult.

LCEL allows us to describe the entire workflow as one chain.

---

# Understanding the Pipe Operator

The most important symbol in LCEL is:

```python
|
```

This is called the **Pipe Operator**.

Think of it like a pipeline.

Example

```text
Water Pipe

Tank

↓

Pipe

↓

Tap
```

LCEL works the same way.

```text
Retriever

↓

Prompt

↓

LLM

↓

Output Parser
```

Each component passes its result to the next.

---

# Example

```python
chain = (

    prompt

    |

    llm

    |

    output_parser

)
```

Read this as:

```text
Prompt

↓

LLM

↓

Output Parser
```

Simple.

Readable.

---

# What is Runnable?

Every LCEL component is called a **Runnable**.

Examples include:

- Retriever
- Prompt Template
- Chat Model
- Output Parser

Since they all follow the same interface,

they can be connected together using:

```python
|
```

---

# RunnablePassthrough

Sometimes,

we want to pass a value through the pipeline unchanged.

Example

```python
from langchain_core.runnables import (

    RunnablePassthrough
)
```

Usage

```python
RunnablePassthrough()
```

Suppose the user asks:

```text
What is the leave policy?
```

We want this question to continue through the chain while the Retriever searches for context.

RunnablePassthrough allows exactly that.

---

# RunnableLambda

Sometimes,

we need a small custom Python function inside our chain.

Example

```python
from langchain_core.runnables import (

    RunnableLambda
)
```

Example

```python
RunnableLambda(

    lambda text: text.upper()
)
```

Although we won't use it much in this project,

it's useful for:

- Formatting
- Cleaning data
- Custom processing

inside a chain.

---

# RunnableParallel

Suppose we need to execute multiple tasks simultaneously.

Example

```text
Question

↓

Retriever

↓

Metadata Lookup

↓

Prompt
```

Instead of running them one after another,

we can run them in parallel.

LangChain provides:

```python
RunnableParallel
```

This improves efficiency for independent tasks.

---

# StrOutputParser

LLMs often return structured response objects.

Most of the time,

we only need the final text.

LangChain provides:

```python
from langchain_core.output_parsers import (

    StrOutputParser
)
```

Example

```python
output_parser = StrOutputParser()
```

Its job is simple.

```text
LLM Response

↓

Plain String
```

---

# Building Our First LCEL Chain

Let's connect three components.

```python
chain = (

    RAG_PROMPT

    |

    llm

    |

    StrOutputParser()

)
```

The flow becomes:

```text
Prompt

↓

LLM

↓

Plain Text
```

Notice how readable this is.

---

# A Bigger Picture

Eventually,

our RAG chain will look like this.

```text
Question

│

├───────────────┐

▼               ▼

Retriever   RunnablePassthrough

│               │

└───────┬───────┘

        ▼

ChatPromptTemplate

        ▼

ChatOpenAI

        ▼

StrOutputParser

        ▼

Final Answer
```

This entire workflow becomes one reusable object.

---

# Why Is LCEL Powerful?

Instead of writing dozens of function calls,

we describe **how data flows** through the application.

This makes the code:

- Easier to read
- Easier to debug
- Easier to extend

As new components are added,

the chain simply grows.

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| Manual function calls | LCEL pipeline |
| Functions connected manually | Components connected using `|` |
| Plain Python flow | Declarative workflow |
| Manual output handling | Output Parsers |

The application logic remains the same.

Only the way we connect components changes.

---

# Industry Insight

Modern LangChain applications are built almost entirely using LCEL.

Older tutorials often use:

```python
LLMChain
```

or

```python
RetrievalQA
```

These approaches are now considered legacy.

Today,

LCEL is the recommended way to build LangChain applications because it is:

- More flexible
- More composable
- Easier to extend
- Better suited for production workflows

When reading newer LangChain documentation,

you'll see LCEL used almost everywhere.

---

# Best Practice

Build small chains first.

Example

```text
Prompt

↓

LLM

↓

Parser
```

Once this works,

add:

- Retriever
- Memory
- Tools

One component at a time.

---

# Debugging Tip

Test every Runnable individually before connecting them.

Example

```python
formatted_prompt = RAG_PROMPT.invoke(

    {

        "context":"Test Context",

        "question":"Test Question"

    }

)

print(formatted_prompt)
```

Then test:

```python
llm.invoke(
    formatted_prompt
)
```

Finally,

connect them using LCEL.

This makes debugging much easier.

---

# Architecture Review

We now have every building block needed for a complete LangChain RAG application.

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

ChatPromptTemplate

↓

LCEL
```

Only one thing remains.

Connecting everything into one complete RAG chain.

---

# Common Beginner Mistakes

### Mistake 1

Thinking LCEL is a new AI model.

It isn't.

LCEL is simply a way of connecting LangChain components.

---

### Mistake 2

Building one huge chain immediately.

Start with a small chain and expand it gradually.

---

### Mistake 3

Ignoring the Output Parser.

Without an Output Parser,

the LLM returns structured message objects instead of plain text.

---

### Mistake 4

Learning older LangChain APIs first.

Focus on LCEL.

It is the modern standard used in current LangChain applications.

---

# Key Takeaways

- LCEL stands for LangChain Expression Language.
- LCEL connects LangChain components into a pipeline.
- The `|` operator passes output from one component to the next.
- Every LCEL component is a Runnable.
- `RunnablePassthrough`, `RunnableLambda`, `RunnableParallel`, and `StrOutputParser` are fundamental LCEL building blocks.
- LCEL is the recommended way to build modern LangChain applications.