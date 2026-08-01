# 7. Scaled Dot-Product Attention

## Introduction

In the previous chapter, we learned that every word creates three vectors:

- Query (Q)
- Key (K)
- Value (V)

Now an important question arises.

> **How does the Transformer decide which words should receive more attention?**

The answer is **Scaled Dot-Product Attention**.

Don't let the name scare you.

It simply means:

1. Compare the **Query** with every **Key**.
2. Find which words are most relevant.
3. Convert the scores into probabilities.
4. Use those probabilities to combine the **Values**.

This entire process is called **Scaled Dot-Product Attention**.

---

# 7.1 The Four Steps of Attention

Every Transformer performs these four steps.

```text
Query
   │
   ▼
Compare with Keys
   │
   ▼
Attention Scores
   │
   ▼
Softmax
   │
   ▼
Weighted Sum of Values
```

Let's understand each step one by one.

---

# 7.2 Step 1 – Compare Query with Keys

Suppose we have this sentence.

```text
The cat drinks milk.
```

Imagine we are trying to understand the word:

```text
drinks
```

The Query of **drinks** is compared with the Keys of every word.

```text
          Query

        drinks
            │
            ▼

The      Key

Cat      Key

Drinks   Key

Milk     Key
```

The Transformer checks:

> "Which word is most useful for understanding **drinks**?"

This comparison produces a score for every word.

---

# 7.3 Dot Product

The comparison between a Query and a Key is done using the **Dot Product**.

You already learned the Dot Product in the Math Foundations section.

Think of it as a way of measuring **how similar two vectors are**.

Example:

```text
High Dot Product

↓

Words are strongly related.
```

```text
Low Dot Product

↓

Words are less related.
```

Suppose the Transformer calculates:

| Word | Dot Product Score |
|------|------------------:|
| The | 1.2 |
| Cat | 5.8 |
| Drinks | 4.5 |
| Milk | 6.3 |

These scores tell us how important each word is for understanding **drinks**.

At this stage, these are just **raw scores**.

They are not probabilities.

---

# 7.4 Why Do We Scale the Scores?

As models become larger, the Query and Key vectors also become larger.

This makes the dot product values very large.

Very large numbers can make learning unstable.

To solve this problem, the Transformer divides every score by a value called:

```text
√dₖ
```

where:

```text
dₖ
```

is the size of the Key vector.

You don't need to memorize this formula.

Just remember:

> **Scaling keeps the attention scores at a reasonable size, making training more stable.**

---

# 7.5 Step 2 – Apply Softmax

After scaling, we still have raw scores.

Example:

```text
1.2

5.8

4.5

6.3
```

These numbers don't tell us how important each word is.

So the Transformer applies **Softmax**.

Softmax converts the scores into probabilities.

Example:

| Word | Probability |
|------|------------:|
| The | 0.02 |
| Cat | 0.34 |
| Drinks | 0.18 |
| Milk | 0.46 |

Notice:

All the probabilities add up to **1**.

This makes them much easier to interpret.

---

# 7.6 Step 3 – Use the Values

Now the Transformer knows which words are important.

The next step is to collect information from the **Value** vectors.

Words with higher probabilities contribute more information.

Example:

```text
Milk

↓

46%
```

```text
Cat

↓

34%
```

These words influence the final output more than words with smaller probabilities.

---

# 7.7 Step 4 – Create the Final Output

The Transformer combines all the Value vectors using the attention probabilities.

The result is a **new vector**.

This new vector contains information gathered from the entire sentence.

```text
Values

↓

Weighted Combination

↓

New Representation
```

This new representation is much richer than the original embedding.

It now contains information from other related words.

---

# 7.8 Complete Attention Pipeline

Let's put everything together.

```text
Embedding
      │
      ▼
Linear Layers
      │
      ▼
Query   Key   Value
   │      │      │
   └──► Compare ◄─┘
          │
          ▼
     Dot Product
          │
          ▼
       Scaling
          │
          ▼
       Softmax
          │
          ▼
Weighted Sum of Values
          │
          ▼
Final Attention Output
```

This entire process happens for **every token** in the sentence.

---

# 7.9 Python Example

PyTorch provides a built-in implementation of scaled dot-product attention.

```python
import torch
import torch.nn.functional as F

Q = torch.rand(1, 4, 8)
K = torch.rand(1, 4, 8)
V = torch.rand(1, 4, 8)

output = F.scaled_dot_product_attention(Q, K, V)

print(output.shape)
```

Output

```text
torch.Size([1, 4, 8])
```

Explanation:

- **1** → Batch size
- **4** → Number of tokens
- **8** → Embedding size

PyTorch performs all the attention calculations internally.

---

# 7.10 Real-World Example

Imagine you are preparing for an exam.

You have four books.

```text
Math

Science

History

English
```

Today's exam is:

```text
Math
```

Naturally, you spend:

- Most of your time studying Math.
- Some time revising Science.
- Very little time reading History.

You give more attention to the most relevant book.

A Transformer works in the same way.

It gives more attention to the words that are most useful for understanding the current word.

---

# 7.11 Why Is It Called "Scaled Dot-Product Attention"?

The name becomes easy once we break it down.

**Dot Product**

The Query is compared with every Key using the dot product.

↓

**Scaled**

The scores are divided by √dₖ to keep them stable.

↓

**Attention**

The model decides which words deserve more attention.

That's why the complete mechanism is called **Scaled Dot-Product Attention**.

---

# Transformer Connection

Every Transformer layer performs Scaled Dot-Product Attention.

Whether you're using:

- GPT
- BERT
- Llama
- Gemini
- Claude

they all use this mechanism.

It is one of the core computations inside every modern Large Language Model.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the Dot Product gives probabilities.

It doesn't.

Softmax converts the scores into probabilities.

---

### Mistake 2

Thinking every word receives equal attention.

Each word calculates its own attention scores.

---

### Mistake 3

Thinking scaling changes the meaning.

Scaling simply keeps the values stable during training.

---

### Mistake 4

Thinking attention only compares nearby words.

A token can attend to **any other token** in the sentence.

---

# Key Takeaways

- Scaled Dot-Product Attention is the core computation inside a Transformer.
- The Query is compared with every Key using the Dot Product.
- The scores are scaled to keep training stable.
- Softmax converts the scores into probabilities.
- The probabilities are used to combine the Value vectors.
- The final output contains information gathered from the entire sentence.
- Every token performs this process independently.