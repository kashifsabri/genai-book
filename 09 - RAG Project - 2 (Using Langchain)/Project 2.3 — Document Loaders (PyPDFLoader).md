

## Introduction

In Project 1,

we created our own PDF loader using:

```python
PdfReader
```

We manually:

- Opened the PDF
- Read every page
- Extracted the text
- Combined all pages into one string

Now we'll see how LangChain simplifies this process.

Instead of writing our own loader,

we'll use:

```text
PyPDFLoader
```

---

# What is a Document Loader?

A Document Loader is responsible for reading documents from different sources and converting them into a format that LangChain understands.

Instead of returning plain text,

LangChain returns **Document objects**.

These objects contain:

- Document content
- Metadata

This extra information becomes very useful later for:

- Metadata Filtering
- Source Citations
- Multi-document Retrieval

---

# Project 1 vs Project 2

In Project 1

```python
reader = PdfReader(file_path)

text = ""

for page in reader.pages:
    text += page.extract_text()
```

In Project 2

```python
loader = PyPDFLoader(file_path)

documents = loader.load()
```

Notice how much code LangChain removes.

---

# Step 1 — Install the Required Library

If you followed the previous chapter,

the library is already installed.

Otherwise,

install it.

```bash
pip install langchain-community
```

---

# Step 2 — Why Do We Need loaders.py?

Instead of writing loader code inside:

```text
app.py
```

we'll create:

```text
loaders.py
```

Purpose:

- Load documents
- Return LangChain Document objects

Nothing else.

This keeps our project modular.

---

# Step 3 — Import PyPDFLoader

Open:

```text
loaders.py
```

Import the loader.

```python
from langchain_community.document_loaders import PyPDFLoader
```

---

# Step 4 — Create the Loader

```python
from config import PDF_PATH

loader = PyPDFLoader(PDF_PATH)
```

At this point,

nothing has been loaded yet.

We've only created the loader.

---

# Step 5 — Load the Documents

```python
documents = loader.load()
```

That's it.

The PDF has now been converted into LangChain Documents.

---

# What is Returned?

Many beginners expect:

```python
str
```

Instead,

LangChain returns:

```python
list[Document]
```

Each page becomes one Document object.

Example

```text
[
    Document(...),
    Document(...),
    Document(...)
]
```

---

# Step 6 — Inspect the Documents

Check how many pages were loaded.

```python
print(len(documents))
```

Example Output

```text
15
```

Our PDF has:

```text
15 Pages

↓

15 Document Objects
```

---

# Step 7 — View the First Document

```python
print(documents[0])
```

Output (simplified)

```text
Document(
    page_content="Welcome to ABC Company...",

    metadata={
        "source":
        "employee_handbook.pdf",

        "page":0
    }
)
```

Notice something important.

A Document contains two things.

---

## Page Content

The actual text.

```python
documents[0].page_content
```

---

## Metadata

Information about the document.

```python
documents[0].metadata
```

Example

```python
{
    "source":"employee_handbook.pdf",

    "page":0
}
```

Metadata is one of the biggest advantages of LangChain.

We'll use it later for:

- Source citations
- Metadata filtering

---

# Step 8 — Create a Reusable Function

Instead of writing this inside app.py,

create a function.

```python
from langchain_community.document_loaders import PyPDFLoader

from config import PDF_PATH


def load_documents():

    loader = PyPDFLoader(
        PDF_PATH
    )

    return loader.load()
```

Simple.

Reusable.

Easy to test.

---

# Step 9 — Test the Loader

Open:

```text
app.py
```

```python
from loaders import load_documents

documents = load_documents()

print(len(documents))

print(documents[0].page_content)
```

Example Output

```text
15

Welcome to ABC Company...
```

Everything works.

---

# Complete loaders.py

```python
from langchain_community.document_loaders import PyPDFLoader

from config import PDF_PATH


def load_documents():

    loader = PyPDFLoader(
        PDF_PATH
    )

    return loader.load()
```

---

# Understanding the Flow

```text
employee_handbook.pdf

↓

PyPDFLoader

↓

Document Objects

↓

Text Splitter
```

Notice that the loader doesn't split the text.

It simply reads the document.

Chunking comes next.

---

# Why Does LangChain Return Document Objects?

Imagine you have:

```text
100 PDFs
```

If everything were plain text,

you'd lose information like:

- File Name
- Page Number
- Author
- Creation Date

Document objects preserve this metadata.

Later,

our chatbot will be able to say:

```text
Answer found in:

employee_handbook.pdf

Page 12
```

This wouldn't be possible with plain strings.

---



# Comparison with Project 1

Let's compare what changed after moving to LangChain.

| Project 1 | Project 2 |
|-----------|-----------|
| `PdfReader` | `PyPDFLoader` |
| Returned plain text (`str`) | Returns `Document` objects |
| No metadata | Includes metadata automatically |
| Manual page handling | Automatic page handling |
| Custom implementation | Built-in LangChain component |

Notice something important.

LangChain did **not** introduce a new concept.

It simply automated what we built manually in Project 1.

Because you already understand how a PDF loader works,

using `PyPDFLoader` should now feel natural rather than magical.

---

# Industry Insight

In enterprise AI applications,

documents rarely come from a single PDF.

A typical system may load documents from:

- PDFs
- Microsoft Word files
- PowerPoint presentations
- HTML pages
- Company Wikis
- SharePoint
- Confluence
- Google Drive
- Amazon S3

One of LangChain's biggest strengths is that it provides a common interface for loading documents from many different sources.

Regardless of where the data comes from,

the next stages of the pipeline remain the same.

```text
Document Loader
        │
        ▼
Document Objects
        │
        ▼
Text Splitter
        │
        ▼
Embeddings
```

This allows developers to change the data source without rewriting the rest of the application.



---

# Professional Tip

Avoid converting Document objects into plain text immediately.

Keep them as Document objects for as long as possible.

Many LangChain components,

such as text splitters, vector stores, and retrievers,

work directly with Document objects.

Keeping them intact preserves metadata throughout the pipeline.

---

# Common Beginner Mistakes

### Mistake 1

Expecting `load()` to return a string.

It returns a list of `Document` objects.

---

### Mistake 2

Ignoring metadata.

Metadata becomes extremely useful for citations, filtering, and debugging.

---

### Mistake 3

Performing chunking inside the loader.

The loader's only responsibility is reading documents.

Splitting happens in the next module.

---

# Key Takeaways

- `PyPDFLoader` replaces our custom PDF loader.
- LangChain returns `Document` objects instead of plain text.
- Each `Document` contains both content and metadata.
- Keeping metadata allows advanced RAG features later.
- The output of this module becomes the input for the Text Splitter.