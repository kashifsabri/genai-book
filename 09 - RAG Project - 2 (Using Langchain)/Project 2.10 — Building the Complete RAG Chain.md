

## Introduction

So far, we've built every individual component of our LangChain RAG application.

We now have:

- Document Loader
- Text Splitter
- Embedding Model
- Chroma Vector Store
- Retriever
- Prompt Template
- LCEL

Now it's time to connect everything into one complete RAG pipeline.

Instead of manually calling each component,

LangChain allows us to describe the entire workflow as a single chain.

---

# Final Architecture

Our complete pipeline will look like this.

```text
                 User Question
                        │
        ┌───────────────┴───────────────┐
        ▼                               ▼
  Retriever                    RunnablePassthrough
        │                               │
        ▼                               ▼
 Retrieved Documents             Original Question
        │                               │
        └───────────────┬───────────────┘
                        ▼
              ChatPromptTemplate
                        ▼
                  ChatOpenAI
                        ▼
               StrOutputParser
                        ▼
                  Final Answer
```

This is the architecture used in many production LangChain applications.

---

# Why Do We Need chain.py?

Create:

```text
chain.py
```

Purpose:

- Connect every LangChain component
- Build one reusable RAG pipeline
- Keep application logic out of `app.py`

This file should not:

- Load PDFs
- Split documents
- Create vector databases

It should only build the chain.

---

# Step 1 — Import the Required Components

```python
from langchain_core.runnables import (
    RunnablePassthrough
)

from langchain_core.output_parsers import (
    StrOutputParser
)

from langchain_openai import ChatOpenAI
```

We'll also import:

- Retriever
- Prompt Template
- Configuration

---

# Step 2 — Configure the LLM

Open:

```text
config.py
```

Add:

```python
MODEL_NAME = "gpt-4.1-mini"

TEMPERATURE = 0
```

Why set:

```python
TEMPERATURE = 0
```

For RAG,

we want factual answers,

not creative writing.

Lower temperature reduces randomness.

---

# Step 3 — Create the LLM

```python
from langchain_openai import ChatOpenAI

from config import (
    MODEL_NAME,
    TEMPERATURE
)

llm = ChatOpenAI(

    model=MODEL_NAME,

    temperature=TEMPERATURE
)
```

---

# Step 4 — Understanding the Problem

Our Prompt expects two inputs.

```text
Context

Question
```

But our Retriever only returns:

```text
Documents
```

We need to combine both.

---

# Step 5 — Format Retrieved Documents

The Retriever returns:

```text
Document

Document

Document
```

Our Prompt expects:

```text
One String
```

Let's create a helper function.

```python
def format_docs(documents):

    return "\n\n".join(

        document.page_content

        for document in documents
    )
```

Now,

multiple Document objects become one context string.

---

# Step 6 — Build the Input Dictionary

Our Prompt needs:

```python
{

    "context": ...,

    "question": ...

}
```

We create it like this.

```python
{

    "context":

        retriever

        | format_docs,

    "question":

        RunnablePassthrough()

}
```

Let's understand it.

---

# What Happens Here?

Suppose the user asks:

```text
What is the leave policy?
```

RunnablePassthrough sends:

```text
What is the leave policy?
```

directly to:

```text
question
```

At the same time,

the Retriever searches Chroma.

```text
Question

↓

Retriever

↓

Relevant Documents

↓

format_docs()

↓

Context
```

Both values are combined.

---

# Step 7 — Build the Chain

Now everything comes together.

```python
chain = (

    {

        "context":

            retriever

            | format_docs,

        "question":

            RunnablePassthrough()

    }

    |

    RAG_PROMPT

    |

    llm

    |

    StrOutputParser()

)
```

Congratulations!

You have built a complete LangChain RAG pipeline.

---

# Complete chain.py

```python
from langchain_core.runnables import (
    RunnablePassthrough
)

from langchain_core.output_parsers import (
    StrOutputParser
)

from langchain_openai import ChatOpenAI

from config import (
    MODEL_NAME,
    TEMPERATURE
)

from prompts import RAG_PROMPT


llm = ChatOpenAI(

    model=MODEL_NAME,

    temperature=TEMPERATURE
)


def format_docs(documents):

    return "\n\n".join(

        document.page_content

        for document in documents
    )


def build_chain(retriever):

    chain = (

        {

            "context":

                retriever

                | format_docs,

            "question":

                RunnablePassthrough()

        }

        |

        RAG_PROMPT

        |

        llm

        |

        StrOutputParser()

    )

    return chain
```

---

# Step 8 — Test the Chain

Open:

```text
app.py
```

```python
from chain import build_chain

chain = build_chain(
    retriever
)

answer = chain.invoke(

    "How many paid leave days do employees receive?"
)

print(answer)
```

Example Output

```text
Employees receive 24 paid leave days every year according to the Employee Handbook.
```

Everything is now connected.

---

# Understanding Data Flow

Let's trace one user question.

```text
User Question

↓

Retriever

↓

Relevant Documents

↓

format_docs()

↓

Prompt Template

↓

ChatOpenAI

↓

Output Parser

↓

Final Answer
```

Notice how every component performs exactly one responsibility.

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| Manual function calls | LCEL Chain |
| Manual prompt building | Automatic pipeline |
| Manual LLM call | ChatOpenAI |
| Manual answer extraction | StrOutputParser |
| Everything in app.py | Dedicated chain module |

The concepts remain exactly the same.

Only the implementation is simpler.

---

# Why Doesn't app.py Build the Chain?

Imagine your application grows.

Soon you'll have:

- Chat Chain
- RAG Chain
- Agent Chain
- Summarization Chain
- Translation Chain

Keeping chain construction inside:

```text
chain.py
```

makes your application much cleaner.

Your `app.py` should simply use the chain,

not build it.

---

# Industry Insight

Many enterprise AI applications contain dozens of chains.

Example

```text
Customer Support Chain

Document Search Chain

Code Generation Chain

Email Summarization Chain

SQL Generation Chain
```

Each chain is stored in its own module.

This keeps projects modular and easier to maintain.

---

# Best Practice

Always separate helper functions.

Example

```python
format_docs()
```

Instead of embedding formatting logic inside the chain,

extract it into a reusable function.

This improves readability and simplifies testing.

---

# Debugging Tip

Test every stage independently.

Example

First verify retrieval.

```python
documents = retriever.invoke(

    "Leave Policy"
)

print(documents)
```

Then verify formatting.

```python
print(

    format_docs(documents)
)
```

Then test the prompt.

Finally,

test the complete chain.

Debugging one stage at a time is much easier than debugging the entire pipeline.

---

# Architecture Review

Our complete LangChain RAG pipeline is now finished.

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

ChatOpenAI

↓

StrOutputParser

↓

Answer
```

This is the foundation of most modern LangChain RAG applications.

---

# Common Beginner Mistakes

### Mistake 1

Passing Document objects directly into the Prompt.

Convert them into a readable context string first.

---

### Mistake 2

Building the chain inside `app.py`.

Keep chain construction inside a dedicated module.

---

### Mistake 3

Skipping the Output Parser.

Without it,

the model returns an AIMessage object instead of plain text.

---

### Mistake 4

Using a high temperature for factual RAG systems.

Keep temperature low to improve consistency.

---

# Key Takeaways

- LCEL allows us to build an entire RAG pipeline as one reusable chain.
- `RunnablePassthrough()` forwards the user's question unchanged.
- `format_docs()` converts retrieved Document objects into context.
- `ChatPromptTemplate`, `ChatOpenAI`, and `StrOutputParser` complete the generation pipeline.
- Separating chain construction into `chain.py` keeps the project clean and maintainable.