

## Introduction

So far, we've built almost the entire RAG pipeline.

```text
PDF

↓

Loader

↓

Chunker

↓

Embeddings

↓

FAISS

↓

Retriever

↓

Prompt Builder
```

Only one step remains.

We need to send the final prompt to an LLM and receive an answer.

---

# Which LLM Should We Use?

Our RAG pipeline works with any LLM.

Examples:

- OpenAI GPT
- Google Gemini
- Anthropic Claude
- Ollama
- Hugging Face Models

To keep this project simple,

we'll use the **OpenAI API**.

Later, you can replace it with any other model.

---

# Step 1 — Install the Library

```bash
pip install openai
```

---

# Step 2 — Store Your API Key

Never write your API key directly inside your code.

Instead,

create a file named:

```text
.env
```

Add your key.

```text
OPENAI_API_KEY=your_api_key_here
```

Install python-dotenv.

```bash
pip install python-dotenv
```

---

# Step 3 — Create llm.py

Create

```text
llm.py
```

---

# Step 4 — Load Environment Variables

```python
import os

from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()

client = OpenAI(
    api_key=os.getenv("OPENAI_API_KEY")
)
```

---

# Step 5 — Create a Function

```python
def generate_answer(prompt):

    response = client.chat.completions.create(

        model="gpt-4.1-mini",

        messages=[

            {
                "role": "user",
                "content": prompt
            }

        ]

    )

    return response.choices[0].message.content
```

The function accepts a prompt,

sends it to the LLM,

and returns the generated answer.

---

# Step 6 — Test the LLM

Open

```python
app.py
```

```python
from llm import generate_answer

answer = generate_answer(prompt)

print(answer)
```

Example Output

```text
Employees receive 24 paid leave days every year according to the Employee Handbook.
```

Congratulations!

Your LLM is now connected.

---

# Complete llm.py

```python
import os

from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()

client = OpenAI(
    api_key=os.getenv("OPENAI_API_KEY")
)


def generate_answer(prompt):

    response = client.chat.completions.create(

        model="gpt-4.1-mini",

        messages=[
            {
                "role": "user",
                "content": prompt
            }
        ]

    )

    return response.choices[0].message.content
```

---

# Project Architecture

Completed

```text
PDF

↓

Loader

↓

Chunker

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

Only one final step remains.

We need to connect every module together.

---

# Real-World Example

Imagine a librarian.

The librarian first finds the correct pages from a book.

Then they hand those pages to an expert.

The expert reads them and answers your question.

The Retriever is the librarian.

The LLM is the expert.

---

# Common Beginner Mistakes

### Mistake 1

Hardcoding the API key.

Always use environment variables.

---

### Mistake 2

Sending only the user's question.

Always send the prompt built by the Prompt Builder.

---

### Mistake 3

Assuming the LLM knows your documents.

The LLM only knows the context you provide in the prompt.

---

# Key Takeaways

- The LLM generates the final answer.
- The Prompt Builder prepares the information sent to the LLM.
- Store API keys securely using environment variables.
- The LLM should answer using the retrieved context, not its own assumptions.