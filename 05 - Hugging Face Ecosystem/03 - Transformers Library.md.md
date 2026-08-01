# 3. Transformers Library

## Introduction

In the previous chapter, we learned about the Hugging Face Hub.

The Hub stores thousands of pretrained AI models.

But how do we use these models in Python?

The answer is the **Transformers** library.

It is the official Python library developed by Hugging Face.

With just a few lines of code, you can download and use powerful AI models like:

- BERT
- GPT-2
- T5
- Llama (supported versions)
- DistilBERT
- RoBERTa

Instead of building everything from scratch, the library provides ready-to-use classes and functions.

---

# 3.1 What is the Transformers Library?

The **Transformers** library is a Python package that makes it easy to work with pretrained Transformer models.

It helps you:

- Download models
- Load tokenizers
- Generate text
- Classify text
- Translate languages
- Summarize documents
- Answer questions

You don't need to build the Transformer architecture yourself.

---

# 3.2 Installing the Library

Install the library using pip.

```bash
pip install transformers
```

If you're using PyTorch, install it as well.

```bash
pip install torch
```

---

# 3.3 Importing the Library

```python
import transformers

print(transformers.__version__)
```

Example Output

```text
4.xx.x
```

This confirms that the library is installed correctly.

---

# 3.4 What Does the Library Provide?

The Transformers library contains many useful classes.

Some of the most common ones are:

- AutoTokenizer
- AutoModel
- AutoModelForCausalLM
- AutoModelForSequenceClassification
- pipeline

We'll learn each of these in the next chapters.

---

# 3.5 How Does It Work?

The workflow is very simple.

```text
Choose a Model
        │
        ▼
Download from Hugging Face Hub
        │
        ▼
Load in Python
        │
        ▼
Use the Model
```

The library handles everything automatically.

---

# 3.6 Python Example

Load a tokenizer.

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

print("Tokenizer Loaded Successfully!")
```

The first time you run this code, the tokenizer is downloaded from the Hugging Face Hub.

After that, it is loaded from your local computer.

---

# 3.7 Real-World Example

Imagine buying a new mobile phone.

The phone already comes with:

- Camera
- Calculator
- Calendar
- Browser

You don't need to build these apps yourself.

The Transformers library works in a similar way.

It already provides everything needed to work with pretrained AI models.

You simply import the class you need and start building.

---

# Transformer Connection

The Transformer models we studied earlier are already implemented inside this library.

Instead of writing hundreds of lines of code to build GPT or BERT, you can load them with a few lines of Python.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the Transformers library contains only GPT models.

It supports many different Transformer models.

---

### Mistake 2

Thinking the library trains models automatically.

It provides tools to load, use, and fine-tune models, but you still decide how to use them.

---

### Mistake 3

Thinking you need to implement the Transformer architecture yourself.

Most of the time, the library already provides the implementation.

---

# Key Takeaways

- The Transformers library is the official Hugging Face Python library.
- It makes working with pretrained Transformer models easy.
- It can download models directly from the Hugging Face Hub.
- It provides classes for tokenization, inference, text generation, and many other NLP tasks.
- It is one of the most widely used libraries in modern AI development.