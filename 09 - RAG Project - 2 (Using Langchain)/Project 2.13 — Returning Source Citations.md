

## Introduction

Our RAG application is now capable of:

- Loading documents
- Splitting documents
- Creating embeddings
- Searching Chroma
- Retrieving relevant documents
- Maintaining conversation history
- Generating answers

But one important question remains.

> **How does the user know where the answer came from?**

Suppose the chatbot answers:

```text
Employees receive 24 paid leave days every year.
```

The user naturally asks:

> **Where did you find that information?**

A trustworthy RAG system should always show its sources.

This feature is called **Source Citation**.

---

# Why Are Source Citations Important?

Imagine asking ChatGPT:

```text
What is our company's leave policy?
```

It answers:

```text
Employees receive 24 paid leave days.
```

How do you verify that?

Without citations,

you cannot.

With citations,

the chatbot can respond:

```text
Employees receive 24 paid leave days.

Source:
employee_handbook.pdf
Page 8
```

Now the user can verify the answer.

---

# What Information Can We Show?

Every LangChain Document already contains metadata.

Example

```python
document.metadata
```

Output

```python
{

    "source":

    "employee_handbook.pdf",

    "page":8

}
```

We simply need to display it.

---

# Why Does This Work?

Remember Project 2.3?

PyPDFLoader created Document objects.

Each Document contained:

```text
page_content

+

metadata
```

Throughout the pipeline,

LangChain preserved this metadata.

Now we can finally use it.

---

# Step 1 — Retrieve Documents

Suppose we already have:

```python
documents = retriever.invoke(

    question
)
```

Each item is a Document object.

---

# Step 2 — Display the Sources

```python
for document in documents:

    print(

        document.metadata
    )
```

Example Output

```text
{

    "source":

    "employee_handbook.pdf",

    "page":8

}
```

---

# Step 3 — Display Only Useful Information

Instead of printing the entire dictionary,

display only the important fields.

```python
for document in documents:

    print(

        f"Source: "

        f"{document.metadata['source']}"

    )

    print(

        f"Page: "

        f"{document.metadata['page'] + 1}"

    )
```

Notice:

```python
+1
```

Internally,

pages start from:

```text
0
```

Humans expect:

```text
1
```

---

# Step 4 — Remove Duplicate Sources

Sometimes,

multiple retrieved chunks come from the same page.

Example

```text
employee_handbook.pdf

Page 8

employee_handbook.pdf

Page 8

employee_handbook.pdf

Page 8
```

Instead,

display it only once.

```python
sources = set()

for document in documents:

    source = (

        document.metadata["source"],

        document.metadata["page"]

    )

    sources.add(source)
```

Now print the unique sources.

```python
for source, page in sources:

    print(

        f"{source}"

        f" (Page {page+1})"
    )
```

---

# Step 5 — Create a Helper Function

Instead of writing this inside:

```text
app.py
```

create a reusable function.

```python
def display_sources(documents):

    sources = set()

    for document in documents:

        sources.add(

            (

                document.metadata["source"],

                document.metadata["page"]

            )

        )

    print("\nSources\n")

    for source, page in sorted(sources):

        print(

            f"- {source}"

            f" (Page {page+1})"

        )
```

---

# Step 6 — Use the Function

```python
results = retriever.invoke(

    question
)

display_sources(results)
```

Example Output

```text
Sources

- employee_handbook.pdf (Page 8)

- leave_policy.pdf (Page 3)
```

Simple.

Readable.

Professional.

---

# Showing Sources with the Answer

A chatbot can now respond like this.

```text
Employees receive 24 paid leave days every year.

Sources

- employee_handbook.pdf (Page 8)
```

This builds trust.

Users can verify every answer.

---

# Complete Example

```python
def display_sources(documents):

    sources = set()

    for document in documents:

        sources.add(

            (

                document.metadata["source"],

                document.metadata["page"]

            )

        )

    print("\nSources\n")

    for source, page in sorted(sources):

        print(

            f"- {source}"

            f" (Page {page+1})"

        )
```

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| Returned text only | Returns text + source |
| No metadata | Metadata available automatically |
| No citations | Source citations supported |
| Hard to verify answers | Easy to verify answers |

Metadata makes this feature possible.

---

# Industry Insight

Source citations are a standard feature in enterprise RAG systems.

Applications such as:

- Microsoft Copilot
- Google Vertex AI Search
- Amazon Q
- Azure AI Studio

display the document source for every generated answer.

This increases user trust and makes the system easier to audit.

In highly regulated industries such as healthcare, finance, and legal services,

source citations are often mandatory.

---

# Best Practice

Always display citations separately from the answer.

Example

```text
Answer

...

----------------------

Sources

- employee_handbook.pdf (Page 8)

- leave_policy.pdf (Page 3)
```

This makes the response easier to read.

---

# Debugging Tip

If citations are missing,

print the metadata.

```python
print(

    document.metadata
)
```

Verify that:

- source exists
- page exists

If metadata is empty,

the problem usually starts with the Document Loader.

---

# Architecture Review

Our RAG pipeline now provides transparent answers.

```text
Question

↓

Retriever

↓

Documents

↓

Prompt

↓

LLM

↓

Answer

+

Source Citations
```

Users now know exactly where every answer came from.

---

# Common Beginner Mistakes

### Mistake 1

Showing duplicate citations.

Use a `set` to remove duplicates before displaying them.

---

### Mistake 2

Displaying raw metadata dictionaries.

Format citations into a clean, readable format.

---

### Mistake 3

Assuming every loader provides identical metadata.

Different document loaders may expose different metadata fields.

Always inspect the metadata before using it.

---

### Mistake 4

Ignoring citations because the answer looks correct.

Even accurate answers should provide references.

Source citations increase user confidence and make the application more trustworthy.

---

# Key Takeaways

- Source citations improve transparency and trust.
- LangChain preserves document metadata throughout the pipeline.
- Metadata can be used to display document names and page numbers.
- Remove duplicate citations for cleaner output.
- Source citations are a standard feature in production RAG applications.