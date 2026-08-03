

## Introduction

Throughout Project 2, we used many LangChain components.

This appendix serves as a quick reference.

Instead of searching through previous chapters,

you can quickly find the class, method, or component you need.

This is not a replacement for the earlier chapters.

It is a practical reference for day-to-day development.

---

# Installation

```bash
pip install langchain
pip install langchain-community
pip install langchain-openai
pip install langchain-huggingface
pip install langchain-chroma
pip install chromadb
pip install pypdf
pip install python-dotenv
```

---

# Common Imports

## Document Loader

```python
from langchain_community.document_loaders import PyPDFLoader
```

---

## Text Splitter

```python
from langchain_text_splitters import (
    RecursiveCharacterTextSplitter
)
```

---

## Embeddings

```python
from langchain_huggingface import (
    HuggingFaceEmbeddings
)
```

---

## Vector Store

```python
from langchain_chroma import Chroma
```

---

## Prompt Templates

```python
from langchain_core.prompts import (
    ChatPromptTemplate,
    MessagesPlaceholder
)
```

---

## Chat Model

```python
from langchain_openai import ChatOpenAI
```

---

## Output Parser

```python
from langchain_core.output_parsers import (
    StrOutputParser
)
```

---

## LCEL

```python
from langchain_core.runnables import (

    RunnablePassthrough,

    RunnableLambda,

    RunnableParallel
)
```

---

## Conversation History

```python
from langchain_core.runnables.history import (
    RunnableWithMessageHistory
)

from langchain_community.chat_message_histories import (
    ChatMessageHistory
)
```

---

# Document Loaders

| Loader | Purpose |
|---------|----------|
| PyPDFLoader | PDF Documents |
| DirectoryLoader | Entire Folder |
| CSVLoader | CSV Files |
| TextLoader | Text Files |
| JSONLoader | JSON Files |
| UnstructuredMarkdownLoader | Markdown Files |
| WebBaseLoader | Web Pages |

---

# Text Splitters

| Splitter | Purpose |
|-----------|----------|
| RecursiveCharacterTextSplitter | General Purpose (Recommended) |
| CharacterTextSplitter | Fixed Character Splitting |
| TokenTextSplitter | Token-based Splitting |
| MarkdownHeaderTextSplitter | Markdown Documents |
| HTMLHeaderTextSplitter | HTML Documents |

---

# Embedding Models

| Embedding | Provider |
|------------|----------|
| HuggingFaceEmbeddings | Hugging Face |
| OpenAIEmbeddings | OpenAI |
| OllamaEmbeddings | Ollama |
| GoogleGenerativeAIEmbeddings | Google |

---

# Vector Stores

| Vector Store | Persistence | Metadata |
|--------------|-------------|----------|
| Chroma | ✅ | ✅ |
| FAISS | Manual | Limited |
| Pinecone | Cloud | ✅ |
| Weaviate | Cloud | ✅ |
| Milvus | Cloud/Self-hosted | ✅ |
| Qdrant | Cloud/Self-hosted | ✅ |

---

# Creating a Chroma Database

```python
vector_store = Chroma.from_documents(

    documents=documents,

    embedding=embedding_model,

    persist_directory="db"
)
```

---

# Loading an Existing Database

```python
vector_store = Chroma(

    persist_directory="db",

    embedding_function=embedding_model
)
```

---

# Creating a Retriever

```python
retriever = vector_store.as_retriever()
```

---

# Retriever Search Types

## Similarity

```python
retriever = vector_store.as_retriever(

    search_type="similarity"
)
```

---

## Maximum Marginal Relevance (MMR)

```python
retriever = vector_store.as_retriever(

    search_type="mmr"
)
```

---

## Similarity Score Threshold

```python
retriever = vector_store.as_retriever(

    search_type="similarity_score_threshold",

    search_kwargs={

        "score_threshold":0.8
    }
)
```

---

# Retriever Parameters

```python
search_kwargs={

    "k":3
}
```

Common Parameters

| Parameter | Meaning |
|-----------|---------|
| k | Number of Documents |
| score_threshold | Minimum Similarity Score |
| fetch_k | Documents Considered Before Filtering |

---

# Prompt Templates

## Single Template

```python
ChatPromptTemplate.from_template()
```

---

## Multiple Messages

```python
ChatPromptTemplate.from_messages()
```

---

## Conversation History

```python
MessagesPlaceholder(

    variable_name="history"
)
```

---

# Chat Models

```python
llm = ChatOpenAI(

    model="gpt-4.1-mini",

    temperature=0
)
```

---

# Output Parser

```python
StrOutputParser()
```

Purpose

```text
AIMessage

↓

Plain String
```

---

# LCEL Components

## Pipe Operator

```python
|
```

Connects components together.

---

## RunnablePassthrough

Passes input unchanged.

---

## RunnableLambda

Runs a custom Python function.

---

## RunnableParallel

Executes multiple operations simultaneously.

---

## RunnableWithMessageHistory

Adds conversation history.

---

# LCEL Example

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

---

# Running a Chain

## invoke()

```python
chain.invoke(question)
```

Returns the complete response.

---

## stream()

```python
chain.stream(question)
```

Streams the response token by token.

---

## batch()

```python
chain.batch(

    questions
)
```

Processes multiple inputs together.

---

# Useful Document Properties

```python
document.page_content
```

Returns the text.

---

```python
document.metadata
```

Returns metadata.

Example

```python
{

    "source":

    "employee_handbook.pdf",

    "page":8
}
```

---

# Conversation History

```python
RunnableWithMessageHistory
```

Requires

- Runnable
- Session ID
- History Placeholder

---

# Environment Variables

```text
OPENAI_API_KEY=

LANGCHAIN_API_KEY=
```

Load

```python
load_dotenv()
```

---

# Recommended Project Structure

```text
project/

├── data/
├── db/
├── logs/

├── config.py
├── loaders.py
├── splitter.py
├── embeddings.py
├── vector_store.py
├── retriever.py
├── prompts.py
├── chain.py
├── app.py

├── requirements.txt
├── README.md
├── .env
└── .gitignore
```

---

# Debugging Checklist

Before debugging the LLM,

verify these in order.

```text
PDF Loaded?

↓

Documents Created?

↓

Chunks Created?

↓

Embeddings Working?

↓

Chroma Created?

↓

Retriever Returning Documents?

↓

Prompt Correct?

↓

LLM Responding?

↓

Streaming Working?
```

Debug one stage at a time.

---

# Best Practices

✅ Keep prompts in `prompts.py`

✅ Keep configuration in `config.py`

✅ Use environment variables

✅ Keep modules small

✅ Use streaming for chat applications

✅ Use logging

✅ Reuse the Chroma database

✅ Separate indexing and querying

✅ Keep conversation history session-based

✅ Return source citations

---

# Common Mistakes

❌ Rebuilding Chroma every run

❌ Hardcoding API keys

❌ Putting everything inside `app.py`

❌ Using different embedding models for indexing and querying

❌ Ignoring metadata

❌ Skipping source citations

❌ Using a high temperature for factual RAG

❌ Not testing components individually

---

# LangChain RAG Pipeline

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

Streaming

↓

Answer

+

Source Citations

+

Conversation History
```

---

# One-Minute Revision

```text
Load Documents

↓

Split Documents

↓

Create Embeddings

↓

Store in Chroma

↓

Create Retriever

↓

Build Prompt

↓

Build LCEL Chain

↓

Generate Answer

↓

Stream Response

↓

Display Sources

↓

Maintain Conversation History
```

---

# What's Next?

You now have a solid understanding of building RAG applications using LangChain.

In the next project, we'll explore **LlamaIndex**, a framework designed specifically for document-centric AI applications. You'll learn how it differs from LangChain and when it's the better choice for advanced retrieval systems.