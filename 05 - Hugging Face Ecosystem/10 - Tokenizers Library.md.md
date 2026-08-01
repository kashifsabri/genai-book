# 10. Tokenizers Library

## Introduction

In previous chapters, we used:

```python
from transformers import AutoTokenizer
```

So why does Hugging Face also provide another library called **tokenizers**?

The answer is simple.

- **AutoTokenizer** is used to load and use existing tokenizers.
- **tokenizers** is used to **build and train your own tokenizer**.

Most AI engineers only use `AutoTokenizer`.

The `tokenizers` library is mainly used when creating new language models.

---

# 10.1 What is the Tokenizers Library?

The **tokenizers** library is a fast tokenization library developed by Hugging Face.

It is written in **Rust**, making it much faster than a pure Python implementation.

It is mainly used to:

- Build new tokenizers
- Train custom tokenizers
- Encode text efficiently

---

# 10.2 AutoTokenizer vs Tokenizers

| AutoTokenizer | Tokenizers Library |
|----------------|--------------------|
| Loads pretrained tokenizers | Builds new tokenizers |
| Used in most AI applications | Used while training new models |
| Simple to use | More advanced |
| Recommended for beginners | Used by model developers |

---

# 10.3 Installing the Library

```bash
pip install tokenizers
```

---

# 10.4 Simple Example

Import the library.

```python
from tokenizers import Tokenizer
```

Load a tokenizer from a file.

```python
tokenizer = Tokenizer.from_file("tokenizer.json")
```

This loads a previously trained tokenizer.

In real projects, this file is usually created when training a custom model.

---

# 10.5 When Do You Need It?

You need the `tokenizers` library when you are:

- Building your own LLM
- Training a tokenizer on a custom dataset
- Creating a new vocabulary
- Developing a new NLP model

For most applications like chatbots, RAG, or sentiment analysis, you **do not** need this library directly.

---

# 10.6 Real-World Example

Imagine you're opening a restaurant.

Most people simply use an existing menu.

That's like using **AutoTokenizer**.

Now imagine you're creating a completely new restaurant with your own menu.

That's like using the **tokenizers** library.

You're building everything from scratch.

---

# Transformer Connection

The tokenizer is the first step before any Transformer model.

```text
Raw Text
     │
     ▼
Tokenizer
     │
     ▼
Token IDs
     │
     ▼
Transformer Model
```

Whether you use `AutoTokenizer` or build your own tokenizer, the goal is the same:

Convert text into Token IDs.

---

# Common Beginner Mistakes

### Mistake 1

Thinking `tokenizers` replaces `AutoTokenizer`.

It doesn't.

Most developers continue using `AutoTokenizer`.

---

### Mistake 2

Using the `tokenizers` library when a pretrained tokenizer already exists.

If you're using a pretrained model like BERT or GPT, simply use `AutoTokenizer`.

---

### Mistake 3

Thinking every AI project requires training a tokenizer.

Most projects reuse existing pretrained tokenizers.

---

# Key Takeaways

- The `tokenizers` library is used to build and train custom tokenizers.
- `AutoTokenizer` is used to load pretrained tokenizers.
- Most AI engineers use `AutoTokenizer` in day-to-day projects.
- The `tokenizers` library is mainly useful when creating new language models.