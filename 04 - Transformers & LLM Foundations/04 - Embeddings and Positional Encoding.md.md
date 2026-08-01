# 4. Embeddings and Positional Encoding

## Introduction

In the previous chapter, we learned that a Transformer cannot understand words.

It only understands **numbers**.

So we converted words into **Token IDs**.

Example:

```text
I love AI

↓

[125, 842, 315]
```

But there is a problem.

Do these numbers have any meaning?

```text
125

842

315
```

No.

They are just IDs.

They are like student roll numbers in a classroom.

For example:

```text
Rahul → Roll No. 15

Aisha → Roll No. 28

John → Roll No. 41
```

The roll number only identifies the student.

It does not tell us anything about the student.

Token IDs work the same way.

The model needs something that contains the **meaning** of each word.

That is where **Embeddings** come in.

---

# 4.1 What is an Embedding?

An **embedding** is a list of numbers that represents the meaning of a word.

Example:

Word:

```text
Cat
```

Embedding:

```text
[0.42, -0.18, 0.91, 0.33]
```

Another word:

```text
Dog
```

Embedding:

```text
[0.40, -0.21, 0.88, 0.31]
```

Notice that the numbers are similar.

This tells the model that **cat** and **dog** have similar meanings.

You don't need to understand what each number means.

The important thing is:

> **Words with similar meanings usually have similar embeddings.**

---

# 4.2 Token ID vs Embedding

It is very important to understand the difference.

A Token ID is just a number.

Example:

```text
Cat

↓

2054
```

This number has no meaning.

Now look at the embedding.

```text
Cat

↓

[0.42, -0.18, 0.91, 0.33]
```

This vector contains information that helps the model understand the word.

Think of it like this.

```text
Token ID

↓

House Number
```

```text
Embedding

↓

Everything about the house
```

The house number only identifies the house.

The embedding describes it.

---

# 4.3 How Are Embeddings Created?

Transformers have a large table called the **Embedding Matrix**.

Think of it like a dictionary.

```text
Token ID      Embedding

125      →   [0.42, -0.18, ...]

842      →   [0.11, 0.67, ...]

315      →   [-0.52, 0.31, ...]
```

When the model sees a Token ID, it looks up the matching embedding.

---

# 4.4 Python Example

PyTorch provides an embedding layer.

```python
import torch
import torch.nn as nn

embedding = nn.Embedding(
    num_embeddings=100,
    embedding_dim=4
)

token = torch.tensor([5])

result = embedding(token)

print(result)
```

Example Output

```text
tensor([[ 0.43, -1.12, 0.56, 0.90]])
```

The exact numbers will be different every time because the embeddings are learned during training.

---

# 4.5 Why Are Embeddings Useful?

Imagine these words:

```text
King

Queen

Prince

Princess
```

Their embeddings will be close to each other because they have related meanings.

Another group:

```text
Apple

Banana

Orange
```

These embeddings will also be close together.

This helps the model understand relationships between words.

---

# 4.6 One Big Problem

Embeddings understand the meaning of words.

But they still don't know the **order** of the words.

Look at these two sentences.

```text
Dog bites man.
```

```text
Man bites dog.
```

The words are the same.

The embeddings are also the same.

But the meaning is completely different.

So how does the Transformer know which word comes first?

The answer is **Positional Encoding**.

---

# 4.7 What is Positional Encoding?

A Transformer reads all words at the same time.

Because of this, it does not automatically know the order of the words.

Positional Encoding tells the model where each word appears in the sentence.

Example:

```text
Sentence

I love AI
```

Position:

```text
I      → Position 1

love   → Position 2

AI     → Position 3
```

Now the model knows both:

- The meaning of each word.
- The position of each word.

---

# 4.8 Why Do We Need Positional Encoding?

Imagine you receive these words.

```text
eat

I

pizza
```

Can you understand the sentence?

Probably not.

Now add the correct order.

```text
I

eat

pizza
```

Now the sentence makes sense.

Word order changes the meaning.

That is why positional information is so important.

---

# 4.9 Embedding + Position

The Transformer combines both pieces of information.

```text
Token ID
      │
      ▼
Embedding
      │
      ▼
Add Position
      │
      ▼
Final Input to Transformer
```

Now every word has:

- Its meaning.
- Its position.

Only after this step does the data enter the Transformer layers.

---

# 4.10 Real-World Example

Imagine students sitting in a classroom.

Each student has:

- A name
- A seat number

Example:

```text
Ali

Seat 1
```

```text
Sara

Seat 2
```

The **name** tells you who the student is.

The **seat number** tells you where the student is sitting.

A Transformer works in the same way.

- Embedding = Meaning
- Position = Location in the sentence

Both are needed.

---

# Transformer Connection

The Transformer pipeline now looks like this.

```text
Input Text
      │
      ▼
Tokenization
      │
      ▼
Token IDs
      │
      ▼
Embeddings
      │
      ▼
Positional Encoding
      │
      ▼
Transformer Layers
```

The next step is the most important part of the Transformer.

The model must decide:

> **Which words should pay attention to each other?**

That is called **Self-Attention**, and we'll learn it in the next chapter.

---

# Common Beginner Mistakes

### Mistake 1

Thinking a Token ID contains meaning.

It doesn't.

It is only an identifier.

---

### Mistake 2

Thinking embeddings are created manually.

They are **learned automatically** during training.

---

### Mistake 3

Thinking embeddings know word order.

They don't.

Word order comes from **Positional Encoding**.

---

# Key Takeaways

- A Token ID is just a unique number.
- An embedding is a vector that represents the meaning of a word.
- Similar words usually have similar embeddings.
- Transformers use an embedding layer to convert Token IDs into vectors.
- Embeddings do not know the order of words.
- Positional Encoding tells the Transformer where each word appears in the sentence.
- The Transformer uses both embeddings and positional information before processing the input.