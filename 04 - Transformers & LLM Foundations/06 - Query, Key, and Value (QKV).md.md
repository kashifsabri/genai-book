# 6. Query, Key, and Value (QKV)

## Introduction

In the previous chapter, we learned about **Self-Attention**.

We said that every word looks at every other word in the sentence and decides which words are important.

But an important question remains.

> **How does the Transformer decide which words are important?**

The answer is **Query, Key, and Value**, often written as:

- Query (Q)
- Key (K)
- Value (V)

These three vectors help the Transformer decide **where to pay attention** and **what information to collect**.

Don't worry if these names sound confusing.

By the end of this chapter, you'll understand them using simple examples.

---

# 6.1 Imagine a Library

Suppose you go to a library.

You want a book about **Artificial Intelligence**.

What do you do?

You don't open every book one by one.

Instead, you:

- Search the library catalog.
- Find the matching book.
- Read the information inside the book.

This process is very similar to how Self-Attention works.

```text
Your Search
      │
      ▼
Find Matching Book
      │
      ▼
Read the Book
```

In Transformers:

- Your search is the **Query (Q)**.
- The book title is the **Key (K)**.
- The book's content is the **Value (V)**.

---

# 6.2 What is a Query (Q)?

A **Query** represents what the current word is looking for.

Think of it as asking a question.

Example sentence:

```text
The cat drinks milk.
```

Suppose the model is looking at the word:

```text
drinks
```

The Query asks:

> **Which other words can help me understand "drinks"?**

So, the Query is simply a search request.

---

# 6.3 What is a Key (K)?

Every word also has a **Key**.

Think of a Key as a label or identity.

When a Query looks at another word, it compares itself with that word's Key.

Example:

```text
Word

cat

↓

Key
```

```text
Word

milk

↓

Key
```

The Query checks:

> "Is this Key useful for me?"

The better the match, the more attention that word receives.

---

# 6.4 What is a Value (V)?

The **Value** contains the actual information of the word.

Imagine you're reading a book.

The title helps you find the book.

But the title is not what you want.

You want the information inside the book.

That's the Value.

Example:

```text
Book Title

↓

Key
```

```text
Book Content

↓

Value
```

After finding the right word, the Transformer uses its **Value** to understand the sentence.

---

# 6.5 Putting Everything Together

Every word creates three different vectors.

```text
Word

↓

Query (Q)

↓

Key (K)

↓

Value (V)
```

Every token in the sentence has its own:

- Query
- Key
- Value

---

# 6.6 Example

Sentence:

```text
The cat drinks milk.
```

Suppose we're understanding:

```text
drinks
```

The Query from **drinks** compares itself with the Keys of every word.

```text
The

↓

Key
```

```text
Cat

↓

Key
```

```text
Drinks

↓

Key
```

```text
Milk

↓

Key
```

The Transformer asks:

> Which Key matches my Query the best?

Suppose the result is:

| Word | Match |
|------|-------:|
| The | Low |
| Cat | High |
| Drinks | Medium |
| Milk | High |

This means:

- **Cat** is important.
- **Milk** is important.
- **The** is less important.

Finally, the model collects the **Values** of the important words.

---

# 6.7 Why Do We Need Three Different Vectors?

A common question is:

> Why not use just one vector?

Because each vector has a different job.

| Vector | Job |
|---------|-----|
| Query | What am I looking for? |
| Key | What information do I have? |
| Value | What information should I share? |

Think of a classroom.

A student asks a question.

```text
Question

↓

Query
```

Another student knows the answer.

```text
Knowledge

↓

Key
```

The teacher explains the answer.

```text
Explanation

↓

Value
```

Each role is different.

The Transformer works in a similar way.

---

# 6.8 Where Do Q, K, and V Come From?

The Transformer does not create Q, K, and V manually.

They are learned during training.

The input embedding is passed through three different Linear layers.

```text
Embedding
      │
      ├────────► Linear Layer ─────► Query
      │
      ├────────► Linear Layer ─────► Key
      │
      └────────► Linear Layer ─────► Value
```

Each Linear layer learns a different transformation.

This is why every word has three different vectors.

---

# 6.9 Python Example

PyTorch provides a Linear layer that can create these vectors.

```python
import torch
import torch.nn as nn

embedding = torch.randn(4, 8)

linear = nn.Linear(8, 8)

query = linear(embedding)

print(query.shape)
```

Output

```text
torch.Size([4, 8])
```

Explanation:

- **4** represents four tokens.
- **8** represents the embedding size.

In a real Transformer, three separate Linear layers are used.

```python
query_layer = nn.Linear(8, 8)
key_layer = nn.Linear(8, 8)
value_layer = nn.Linear(8, 8)

Q = query_layer(embedding)
K = key_layer(embedding)
V = value_layer(embedding)
```

Now every token has:

- Query
- Key
- Value

---

# 6.10 Real-World Example

Imagine you're shopping online.

You search for:

```text
Wireless Mouse
```

Your search text is the **Query**.

Every product has:

- Product title
- Product description

The product title helps the website find matching products.

This is like the **Key**.

Once the product is selected, you read the product description.

This is like the **Value**.

```text
Search

↓

Query

↓

Compare Product Titles

↓

Keys

↓

Read Product Description

↓

Value
```

---

# 6.11 Are Q, K, and V Different for Every Word?

Yes.

Every word creates its own Query, Key, and Value.

Example:

```text
Sentence

I love AI
```

The word:

```text
I
```

has its own:

- Query
- Key
- Value

The word:

```text
love
```

also has its own:

- Query
- Key
- Value

The word:

```text
AI
```

also has its own:

- Query
- Key
- Value

This allows every word to understand the sentence from its own point of view.

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
Embeddings
      │
      ▼
Positional Encoding
      │
      ▼
Linear Layers
      │
      ▼
Query, Key, Value
      │
      ▼
Attention Scores
      │
      ▼
Output
```

We've now learned what **Query**, **Key**, and **Value** are.

But one important question remains.

> **How does the Transformer compare Queries and Keys to calculate attention scores?**

The answer is **Scaled Dot-Product Attention**, which we'll learn in the next chapter.

---

# Common Beginner Mistakes

### Mistake 1

Thinking Query, Key, and Value are separate inputs.

They are not.

They are all created from the same input embedding.

---

### Mistake 2

Thinking every word shares the same Query.

Every word creates its own Query, Key, and Value.

---

### Mistake 3

Thinking Q, K, and V are manually designed.

They are learned automatically during training.

---

# Key Takeaways

- Every token creates three vectors: Query, Key, and Value.
- The Query represents what a word is looking for.
- The Key helps determine whether another word is important.
- The Value contains the information that will be used.
- Query, Key, and Value are created using Linear layers.
- These vectors are the foundation of Self-Attention.