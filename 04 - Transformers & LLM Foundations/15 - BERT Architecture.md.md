# 15. BERT Architecture

## Introduction

In the previous chapter, we learned that different Transformer models use different architectures.

For example:

- BERT uses only the **Encoder**.
- GPT uses only the **Decoder**.
- T5 uses both the **Encoder** and the **Decoder**.

In this chapter, we'll study **BERT**, one of the most influential language models ever created.

BERT changed the field of Natural Language Processing (NLP) by showing that understanding the **entire sentence** leads to much better results.

Although ChatGPT is based on GPT and not BERT, many modern AI systems still use BERT for language understanding tasks.

---

# 15.1 What is BERT?

**BERT** stands for:

**Bidirectional Encoder Representations from Transformers**

The important word here is:

> **Bidirectional**

This means BERT reads a sentence from **both directions**.

It looks at:

- The words before the current word.
- The words after the current word.

This helps BERT understand the meaning of a word much better.

---

# 15.2 Why Was BERT Created?

Before BERT, many language models processed text in only one direction.

For example,

Left to Right

```text
The

↓

cat

↓

is

↓

sleeping
```

or

Right to Left

```text
sleeping

↓

is

↓

cat

↓

The
```

This means the model only had part of the sentence while understanding a word.

Researchers wanted a model that could look at the **entire sentence at once**.

That is exactly what BERT does.

---

# 15.3 Bidirectional Understanding

Consider this sentence.

```text
The bank approved my loan.
```

When BERT reads the word:

```text
bank
```

it also sees:

```text
approved

loan
```

So it understands that:

```text
bank

↓

Financial Institution
```

Now look at another sentence.

```text
The children played near the river bank.
```

This time, BERT sees:

```text
river
```

So it understands:

```text
bank

↓

Side of a River
```

This is one of BERT's biggest strengths.

It understands words using the **entire context**.

---

# 15.4 BERT Uses Only the Encoder

Unlike the original Transformer, BERT removes the Decoder completely.

The architecture looks like this.

```text
Input Sentence
       │
       ▼
Tokenization
       │
       ▼
Embeddings
       │
       ▼
Encoder Block
       │
       ▼
Encoder Block
       │
       ▼
Encoder Block
       │
       ▼
Understanding
```

There is **no Decoder**.

Why?

Because BERT is not designed to generate text.

Its job is to understand text.

---

# 15.5 What Kind of Tasks Does BERT Solve?

BERT is excellent for tasks where the model needs to understand text.

Examples include:

- Sentiment Analysis
- Spam Detection
- Text Classification
- Named Entity Recognition (NER)
- Question Answering
- Search Ranking

Example:

```text
Review

↓

"This movie was amazing."
```

Output:

```text
Positive
```

Another example:

```text
Email

↓

"You have won a free iPhone!"
```

Output:

```text
Spam
```

Notice that BERT is making a decision.

It is **not writing a paragraph**.

---

# 15.6 How Does BERT Learn?

BERT is trained using a technique called **Masked Language Modeling (MLM)**.

During training, some words are hidden.

Example:

```text
The cat is [MASK] on the sofa.
```

BERT tries to predict the missing word.

Possible answer:

```text
sleeping
```

By solving millions of these small puzzles, BERT learns grammar, meaning, and relationships between words.

---

# 15.7 Python Example

The Hugging Face Transformers library provides pretrained BERT models.

Import the required classes.

```python
from transformers import AutoTokenizer, AutoModel
```

Load the tokenizer and model.

```python
from transformers import AutoTokenizer, AutoModel

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

model = AutoModel.from_pretrained("bert-base-uncased")
```

Tokenize a sentence.

```python
text = "I love Artificial Intelligence."

inputs = tokenizer(text, return_tensors="pt")
```

Pass the input to BERT.

```python
outputs = model(**inputs)

print(outputs.last_hidden_state.shape)
```

Example Output

```text
torch.Size([1, 6, 768])
```

---

# 15.8 Understanding the Output

The output shape is:

```text
[1, 6, 768]
```

What does it mean?

- **1** → One sentence (batch size)
- **6** → Six tokens
- **768** → Size of each token's embedding

Notice something important.

BERT returns an **embedding for every token**.

These embeddings contain rich contextual information.

---

# 15.9 Real-World Example

Imagine you're a teacher checking an exam paper.

You read the entire answer before giving marks.

You don't stop after reading the first few words.

BERT works in the same way.

It looks at the **whole sentence** before making a decision.

---

# 15.10 Advantages of BERT

BERT has many advantages.

- Understands both left and right context.
- Produces rich contextual embeddings.
- Excellent for language understanding tasks.
- Performs well on many NLP benchmarks.

---

# 15.11 Limitations of BERT

Although BERT is powerful, it has one important limitation.

It is **not designed to generate text**.

For example, if you ask:

```text
Write a story about space.
```

BERT cannot generate a complete story like ChatGPT.

Its main job is understanding, not writing.

---

# Transformer Connection

BERT uses only the **Encoder** part of the Transformer.

```text
Input Text
      │
      ▼
Encoder
      │
      ▼
Understanding
```

In the next chapter, we'll study **GPT**.

GPT takes the opposite approach.

Instead of focusing on understanding, GPT focuses on **generating text**.

---

# Common Beginner Mistakes

### Mistake 1

Thinking BERT can generate long answers like ChatGPT.

It can't.

BERT is mainly designed for understanding text.

---

### Mistake 2

Thinking BERT uses both the Encoder and Decoder.

It doesn't.

BERT uses only the Encoder.

---

### Mistake 3

Thinking BERT reads only from left to right.

It doesn't.

BERT reads the sentence in both directions to understand the context.

---

# Key Takeaways

- BERT stands for **Bidirectional Encoder Representations from Transformers**.
- BERT uses only the Encoder part of the Transformer.
- It reads the entire sentence to understand context.
- BERT is trained using Masked Language Modeling (MLM).
- It is widely used for text understanding tasks.
- BERT is not designed for text generation.