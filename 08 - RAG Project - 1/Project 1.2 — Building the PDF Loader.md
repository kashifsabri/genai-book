# Project 1.2 — Building the PDF Loader

## Introduction

Every RAG system starts with documents.

These documents may be:

- PDF
- DOCX
- TXT
- HTML
- Markdown

In this project,

we'll start with PDF files because they are the most common document format.

Our goal is simple:

```text
PDF

↓

Extract Text

↓

Return Plain Text
```

Later,

this text will be sent to the Chunker.

---

# Step 1 — Install the Library

We'll use **pypdf**.

Install it.

```bash
pip install pypdf
```

---

# Why pypdf?

There are many PDF libraries.

Examples:

- pypdf
- pdfplumber
- PyMuPDF (fitz)

For learning,

**pypdf** is:

- Lightweight
- Easy to use
- Pure Python
- Perfect for text-based PDFs

---

# Step 2 — Create a Sample PDF

Inside the project folder,

create:

```text
data/

    employee_handbook.pdf
```

This PDF will be used throughout the entire project.

---

# Step 3 — Import PdfReader

Open

```text
loader.py
```

Import the reader.

```python
from pypdf import PdfReader
```

---

# Step 4 — Read the PDF

Create a reader.

```python
reader = PdfReader(
    "data/employee_handbook.pdf"
)
```

The PDF is now loaded into memory.

---

# Step 5 — Check Number of Pages

Every PDF contains pages.

Let's count them.

```python
print(len(reader.pages))
```

Example Output

```text
15
```

Our sample PDF has 15 pages.

---

# Step 6 — Read One Page

Read the first page.

```python
first_page = reader.pages[0]

text = first_page.extract_text()

print(text)
```

Example Output

```text
Welcome to ABC Company.

Employees receive 24 paid leave days every year.

Working hours are from 9 AM to 6 PM.
```

Great!

We have extracted text from the PDF.

---

# Step 7 — Read Every Page

Instead of reading only one page,

we want the complete document.

```python
from pypdf import PdfReader

reader = PdfReader(
    "data/employee_handbook.pdf"
)

text = ""

for page in reader.pages:
    text += page.extract_text()
```

Now

```python
text
```

contains the contents of the entire PDF.

---

# Step 8 — Add Line Breaks

Reading becomes easier if we separate pages.

```python
text = ""

for page in reader.pages:
    text += page.extract_text()
    text += "\n\n"
```

Now each page is separated.

---

# Step 9 — Create a Function

Instead of writing code everywhere,

create a reusable function.

```python
from pypdf import PdfReader


def load_pdf(file_path: str) -> str:
    reader = PdfReader(file_path)

    text = ""

    for page in reader.pages:
        page_text = page.extract_text()

        if page_text:
            text += page_text
            text += "\n\n"

    return text
```

Now the loader can be reused anywhere.

---

# Step 10 — Test the Loader

Create

```text
app.py
```

```python
from loader import load_pdf

text = load_pdf(
    "data/employee_handbook.pdf"
)

print(text)
```

Run

```bash
python app.py
```

Output

```text
Welcome to ABC Company.

Employees receive 24 paid leave days every year.

Working hours are from 9 AM to 6 PM.

...
```

Congratulations!

You have built the first component of your RAG pipeline.

---

# Project Architecture

Completed

```text
PDF

↓

loader.py

↓

Plain Text
```

Remaining

```text
Chunking

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

# Common Beginner Mistakes

### Mistake 1

Reading only the first page.

Always loop through all pages unless you specifically need one page.

---

### Mistake 2

Assuming `extract_text()` always returns text.

Some pages (such as scanned PDFs) may return `None`.

Check before appending.

---

### Mistake 3

Putting PDF reading logic inside `app.py`.

Keep it inside `loader.py` so it can be reused by other projects.

---

# Key Takeaways

- `pypdf` is a simple library for reading text-based PDFs.
- `PdfReader` loads a PDF into memory.
- `extract_text()` extracts text from a page.
- A reusable `load_pdf()` function keeps the code clean.
- The output of this module becomes the input for the Chunking module.