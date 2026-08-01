# 2. Dot Product

The **dot product** is one of the most important operations in Machine Learning.

It measures **how similar two vectors are**.

Almost every modern AI model—including recommendation systems, neural networks, and Transformers—uses the dot product repeatedly.

In fact, matrix multiplication is built from many dot products.

---

## 2.1 What is a Dot Product?

A dot product takes **two vectors of the same size** and produces **a single number**.

For example:

```text
A = [2, 3]

B = [4, 5]
```

The dot product is calculated by:

1. Multiplying corresponding elements.
2. Adding the results.

```text
(2 × 4) + (3 × 5)

= 8 + 15

= 23
```

So,

```text
A • B = 23
```

The answer is always a **single number (scalar)**.

---

## 2.2 Python Example

```python
import numpy as np

A = np.array([2, 3])
B = np.array([4, 5])

result = np.dot(A, B)

print(result)
```

Output:

```text
23
```

You can also use the `@` operator.

```python
result = A @ B

print(result)
```

Output:

```text
23
```

---

## 2.3 A Real-World Example

Suppose we want to recommend movies.

We describe movies using four genres:

```text
[Action, Comedy, Romance, Sci-Fi]
```

Movie:

```text
Movie = [5, 1, 0, 4]
```

User preference:

```text
User = [4, 1, 0, 5]
```

Now compute the dot product.

```text
(5 × 4)
+
(1 × 1)
+
(0 × 0)
+
(4 × 5)

=

20 + 1 + 0 + 20

=

41
```

The similarity score is:

```text
41
```

A **higher score** means the movie better matches the user's preferences.

---

## 2.4 What Does the Score Mean?

The dot product itself doesn't have a fixed meaning.

It is simply a **similarity score**.

For example:

```text
Movie A → 41
Movie B → 9
Movie C → 38
```

Since **41** is the highest score, Movie A is the best recommendation.

The larger the score, the more similar the two vectors are.

---

## 2.5 Why Does It Work?

Notice what happens during the calculation.

```text
Movie = [5, 1, 0, 4]

User  = [4, 1, 0, 5]
```

Matching interests produce larger values.

```text
Action

5 × 4 = 20
```

Sci-Fi also matches well.

```text
4 × 5 = 20
```

Romance contributes nothing because both values are zero.

```text
0 × 0 = 0
```

The final score becomes large because the important features align.

---

## 2.6 Dot Product Inside Matrix Multiplication

Earlier we learned:

```python
scores = movies @ user
```

What actually happens?

Suppose the movie matrix is:

```text
Movie A

Movie B

Movie C
```

Python computes:

```text
Movie A • User

Movie B • User

Movie C • User
```

one dot product for each movie.

This is why we say:

> **Matrix multiplication is simply many dot products performed at once.**

---

## 2.7 Dot Product in Transformers

The dot product is one of the most important operations inside a Transformer.

During **Self-Attention**, every word compares itself with every other word using a dot product.

For example:

```text
Query • Key
```

If the score is high:

```text
The words are strongly related.
```

If the score is low:

```text
The words are less related.
```

These scores help the Transformer decide **which words should pay attention to each other**.

We'll study this process in detail when we learn the Attention Mechanism.

---

## 2.8 Key Takeaways

- A dot product compares two vectors.
- The result is always a single number (scalar).
- Larger scores generally indicate greater similarity.
- Matrix multiplication is built from many dot products.
- Recommendation systems use dot products to compare users and items.
- Transformers use dot products to compute attention scores between words.

The dot product is one of the most frequently used mathematical operations in Machine Learning and forms the foundation of the Attention mechanism used in modern Large Language Models.