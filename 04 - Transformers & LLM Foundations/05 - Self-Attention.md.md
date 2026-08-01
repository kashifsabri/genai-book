# 5. Self-Attention

## Introduction

Imagine you are reading this sentence:

```text
The animal didn't cross the road because it was tired.
```

When you read the word:

```text
it
```

how do you know what **it** refers to?

Does **it** mean:

- the animal?
- the road?

As humans, we immediately understand that **it** refers to **the animal**.

Why?

Because while reading the word **it**, our brain automatically looks at the other words in the sentence.

We pay more attention to the important words.

Transformers do exactly the same thing.

This process is called **Self-Attention**.

Self-Attention is the most important idea behind the Transformer architecture.

Without Self-Attention, modern Large Language Models like ChatGPT, Gemini, Claude, and Llama would not exist.

---

# 5.1 What is Self-Attention?

Self-Attention is a mechanism that allows every word in a sentence to look at every other word in the same sentence.

It helps the model decide:

> **Which words are important for understanding the current word?**

For example,

Sentence:

```text
The cat sat on the mat.
```

When understanding:

```text
sat
```

the model may pay attention to:

```text
cat
```

because the cat is the one performing the action.

It may pay less attention to:

```text
the
```

because the word **the** doesn't add much meaning.

---

# 5.2 Why Do We Need Self-Attention?

Earlier models like RNNs read words one at a time.

Example:

```text
The

↓

cat

↓

sat

↓

on

↓

the

↓

mat
```

This worked, but it had two problems.

### Problem 1

It was slow.

The model had to wait until one word was processed before moving to the next.

---

### Problem 2

It forgot information from the beginning of long sentences.

Example:

```text
The boy who was wearing a blue jacket and carrying a heavy backpack walked into the classroom because he...
```

By the time the model reached:

```text
he
```

it might already have forgotten important information from the beginning.

Transformers solve this problem.

Instead of reading one word at a time,

they read the **entire sentence together**.

---

# 5.3 How Self-Attention Works

Imagine this sentence.

```text
The cat drinks milk.
```

Instead of reading one word at a time,

every word looks at every other word.

Like this:

```text
The  ←→ cat

The  ←→ drinks

The  ←→ milk

cat  ←→ drinks

cat  ←→ milk

drinks ←→ milk
```

Every word checks whether the other words are important.

This happens at the same time.

---

# 5.4 Real-Life Example

Imagine five friends are discussing a movie.

```text
Ali

Sara

John

Fatima

Omar
```

When Ali speaks,

everyone listens.

When Sara speaks,

everyone listens.

Every person hears every other person.

At the end,

everyone has a better understanding of the discussion.

Self-Attention works in a similar way.

Every word "listens" to every other word before deciding its final meaning.

---

# 5.5 Example

Sentence:

```text
The cat chased the mouse because it was hungry.
```

When the model reaches:

```text
it
```

it looks at all the other words.

```text
The

cat

chased

the

mouse

because

it

was

hungry
```

Some words are more important than others.

The model may pay more attention to:

```text
cat
```

than to:

```text
the
```

because **cat** carries much more meaning.

---

# 5.6 Attention Scores

Self-Attention gives every word an **attention score**.

Think of the score as:

> **How important is this word?**

Example:

```text
Sentence

The cat drinks milk.
```

Suppose we are understanding the word:

```text
drinks
```

The model might assign scores like this.

| Word | Attention Score |
|------|----------------:|
| The | 0.05 |
| Cat | 0.55 |
| Drinks | 0.25 |
| Milk | 0.15 |

Notice that:

- **Cat** receives the highest score.
- **The** receives the lowest score.

This is because **cat** is much more useful for understanding **drinks**.

The numbers are only examples.

The model learns these scores automatically during training.

---

# 5.7 Every Word Gets Its Own Attention

Now let's look at another word.

Suppose we are understanding:

```text
milk
```

The scores will be different.

| Word | Attention Score |
|------|----------------:|
| The | 0.05 |
| Cat | 0.20 |
| Drinks | 0.60 |
| Milk | 0.15 |

Notice something interesting.

The attention scores changed.

Why?

Because now the model is trying to understand **milk**, not **drinks**.

This is why Self-Attention is so powerful.

Every word gets its **own unique view** of the sentence.

---

# 5.8 Self-Attention Happens for Every Word

Suppose our sentence has four words.

```text
The

cat

drinks

milk
```

The model performs Self-Attention for:

```text
The

↓

cat

↓

drinks

↓

milk
```

Every word looks at every other word.

This happens in parallel.

That is one reason Transformers are much faster than RNNs.

---

# 5.9 Python Example

Let's create a small sentence using PyTorch.

```python
import torch

sentence = torch.tensor([
    [1.2, 0.5],
    [0.8, 1.4],
    [2.0, 0.3],
    [1.1, 2.2]
])

print(sentence.shape)
```

Output

```text
torch.Size([4, 2])
```

Explanation:

- **4** represents four tokens.
- **2** represents two features for each token.

In a real Transformer, each token has hundreds or even thousands of features.

Later, Self-Attention will compare these token vectors to understand how they are related.

Don't worry about the calculations yet.

We'll build them step by step in the next chapters.

---

# 5.10 Why Self-Attention Changed AI

Before Transformers,

models processed text one word after another.

After Self-Attention,

models could:

- Read the whole sentence at once.
- Understand relationships between distant words.
- Train much faster on GPUs.
- Handle much longer documents.

This single idea changed Natural Language Processing forever.

---

# Transformer Connection

The Transformer now looks like this.

```text
Input Text
      │
      ▼
Tokenization
      │
      ▼
Embeddings
      │
      ▼
Positional Encoding
      │
      ▼
Self-Attention
      │
      ▼
Feed Forward Network
      │
      ▼
Output
```

Self-Attention is the first major computation inside every Transformer layer.

But one important question still remains.

> **How does the Transformer calculate these attention scores?**

To answer that, we first need to understand three new concepts:

- Query (Q)
- Key (K)
- Value (V)

These three vectors are the foundation of Self-Attention.

We'll learn them in the next chapter.

---

# Common Beginner Mistakes

### Mistake 1

Thinking Self-Attention only looks at the previous word.

It doesn't.

It can look at **every word** in the sentence.

---

### Mistake 2

Thinking every word receives the same attention.

It doesn't.

Each word calculates its own attention scores.

---

### Mistake 3

Thinking attention scores are written by humans.

They are not.

The Transformer learns them automatically during training.

---

# Key Takeaways

- Self-Attention allows every word to look at every other word in the same sentence.
- It helps the model decide which words are most important.
- Every word gets its own set of attention scores.
- Self-Attention processes all words in parallel.
- This makes Transformers faster and better than RNNs for many language tasks.
- Self-Attention is the core idea behind modern Large Language Models.