# 3. Vector Norms

## Introduction

In the previous chapter, we learned that the **dot product** measures the relationship between two vectors. However, the dot product has one limitation—it depends on both the **direction** and the **length** of the vectors. Before learning **Cosine Similarity**, we first need to understand how to measure the **length of a vector**. In mathematics, the length of a vector is called its **norm**.

---

## 3.1 What is a Vector Norm?

A **vector norm** is a single number that represents the **magnitude (length)** of a vector.
Imagine a vector as an arrow starting from the origin.

```text
Origin ------------------------>
```

The norm tells us **how long the arrow is**, regardless of the direction it points.
Example:

```text
A = [3, 4]
```

This vector starts at `(0,0)` and ends at `(3,4)`. The distance between these two points is the vector's norm.

---

## 3.2 L2 Norm (Euclidean Norm)

The **L2 norm**, also called the **Euclidean norm**, is the most commonly used norm in Machine Learning.

For a vector:

```text
[x₁, x₂, ..., xₙ]
```

the L2 norm is:

```text
√(x₁² + x₂² + ... + xₙ²)
```

Example:

```text
A = [3, 4]
```

Calculation:

```text
√(3² + 4²)
= √(9 + 16)
= √25
= 5
```

Therefore,

```text
||A|| = 5
```

The notation `||A||` means **the norm (length) of vector A**. This is the same calculation used in the Pythagorean theorem.

---

## 3.3 Python Example

NumPy provides a built-in function for calculating vector norms.

```python
import numpy as np

A = np.array([3, 4])

print(np.linalg.norm(A))
```

Output:

```text
5.0
```

By default, `np.linalg.norm()` computes the **L2 norm**.

Example 2:

```text
B = [2, 6]
```

Calculation:

```text
√(2² + 6²)
= √40
≈ 6.32
```

Python:

```python
import numpy as np

B = np.array([2, 6])

print(np.linalg.norm(B))
```

Output:

```text
6.324555320336759
```

---

## 3.4 Why Do We Need Norms?

Consider these two vectors.

```text
A = [2, 2]

B = [20, 20]
```

Although **B** is much larger, both vectors point in exactly the same direction.

Their lengths are:

```text
||A|| ≈ 2.83

||B|| ≈ 28.28
```

The **dot product** becomes much larger simply because **B** is longer, not because it represents a different direction.

To compare vectors fairly, we need a method that ignores their length. That method is **Cosine Similarity**, which we'll learn in the next chapter.

---

## 3.5 L1 Norm vs L2 Norm

Another commonly used norm is the **L1 norm**.

For the vector:

```text
A = [3, -4]
```

**L1 Norm**

```text
|3| + |-4|
= 3 + 4
= 7
```

Python:

```python
import numpy as np

A = np.array([3, -4])

print(np.linalg.norm(A, ord=1))
```

Output:

```text
7.0
```

**L2 Norm**

```text
√(3² + (-4)²)
= √25
= 5
```

Python:

```python
print(np.linalg.norm(A))
```

Output:

```text
5.0
```

In Machine Learning, the **L2 norm** is used much more frequently than the L1 norm.

---

## 3.6 Transformer Connection

Every token inside a Transformer is converted into an **embedding vector**. Before comparing two embeddings, we often measure or normalize their lengths. Cosine Similarity uses the **L2 norm** of both vectors before computing similarity.

This is why vector norms are an important foundation for:

- Embeddings
- Semantic Search
- RAG
- Vector Databases
- Transformers

---

## 3.7 Key Takeaways

- A norm measures the **length (magnitude)** of a vector.
- The **L2 (Euclidean) norm** is the most common norm in Machine Learning.
- The **L1 norm** is the sum of the absolute values of a vector's elements.
- Two vectors can point in the same direction while having different lengths.
- Dot products are affected by vector length.
- Cosine Similarity removes the effect of vector length using vector norms.

In the next chapter, we'll combine **Dot Product** and **Vector Norms** to understand **Cosine Similarity**,one of the most important concepts in semantic search, RAG, vector databases, and modern LLMs.