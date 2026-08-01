# 1. Vectors, Matrices, and Matrix Multiplication

Vectors, matrices, and matrix multiplication are the building blocks of Machine Learning and Deep Learning.

Almost every modern AI model—including recommendation systems, computer vision models, and Transformers—uses these concepts repeatedly.

Before learning neural networks or attention mechanisms, it's important to understand these fundamentals.

---

## What is a Vector?

A **vector** is simply a **list of numbers**.

Example:

```text
[2, 5, 8]
```

In Python:

```python
vector = [2, 5, 8]

print(vector)

# Output
[2, 5, 8]
```

Think of a vector as a way to describe something using numbers.

For example, suppose we want to describe a person.

```text
Age      = 25
Height   = 175 cm
Weight   = 70 kg
```

Instead of storing these separately, we can combine them into one vector.

```text
[25, 175, 70]
```

Vectors are used everywhere in Machine Learning because computers understand numbers—not text, images, or sounds directly.

For example, in Generative AI, every word is converted into a vector.

```text
"cat" → [0.12, -0.55, 0.89, ...]
```

These numbers do **not** represent ASCII values or letters.

Instead, they capture the **meaning** of the word.

Words with similar meanings often have similar vectors.

For example:

```text
"cat"  → [0.12, -0.55, 0.89, ...]
"kitten" → [0.10, -0.58, 0.91, ...]
```

Since these vectors are close together, the model understands that **cat** and **kitten** are semantically similar.

---

## What is a Matrix?

A **matrix** is simply a **grid (table) of numbers**.

Example:

```text
2  5  8
1  4  6
7  3  9
```

In Python:

```python
matrix = [
    [2, 5, 8],
    [1, 4, 6],
    [7, 3, 9]
]

print(matrix)
```

A simple way to think about a matrix is:

> **A matrix is multiple vectors stacked together.**

For example:

```text
Person A → [2, 5, 8]
Person B → [1, 4, 6]
Person C → [7, 3, 9]
```

When we stack these vectors vertically, we get:

```text
2  5  8
1  4  6
7  3  9
```

This is a matrix.

Matrices are useful because they allow computers to process many vectors simultaneously instead of one at a time.

For example:

- One image can be represented as a matrix.
- A batch of images can be represented as multiple matrices.
- A sentence can be represented as a matrix of word embeddings.
- A dataset is often represented as a matrix where each row is one sample.

This is why matrices appear everywhere in Machine Learning.

---

## What is Matrix Multiplication?

Matrix multiplication is one of the most important operations in Machine Learning.

A matrix is often used to **transform** one vector into another vector.

Suppose we have the following vector.

```text
2
3
```

Now suppose we have a transformation matrix.

```text
1  2
3  4
```

To multiply them:

```text
1  2     2
3  4  ×  3
```

We calculate one row at a time.

### First Row

Take the first row of the matrix.

```text
1  2
```

Multiply it with the vector.

```text
(1 × 2) + (2 × 3)

= 2 + 6

= 8
```

### Second Row

Take the second row.

```text
3  4
```

Multiply it with the vector.

```text
(3 × 2) + (4 × 3)

= 6 + 12

= 18
```

The final result becomes:

```text
8
18
```

So,

```text
2
3
```

has been transformed into

```text
8
18
```

This is the core idea behind matrix multiplication.

The input vector is transformed into a new vector by applying the matrix.

---

## Matrix Multiplication in Python

Using NumPy, matrix multiplication is very simple.

```python
import numpy as np

matrix = np.array([
    [1, 2],
    [3, 4]
])

vector = np.array([
    [2],
    [3]
])

result = matrix @ vector

print(result)
```

Output:

```text
[[ 8]
 [18]]
```

The `@` operator performs **matrix multiplication**.

Although this example is very small, the same operation is performed on matrices containing millions or even billions of numbers inside modern AI systems.

## 1.5 Matrix Multiplication = Many Dot Products

One of the easiest ways to understand matrix multiplication is to think of it as performing **many dot products at once**.

Suppose we want to compare one user against three different movies.

Without matrix multiplication, we would calculate each similarity separately.

```text
Movie A • User

Movie B • User

Movie C • User
```

Each comparison produces a single similarity score.

Instead of writing three separate calculations, we place all movie vectors into a matrix.

```text
Movies Matrix

Movie A
Movie B
Movie C
```

Now we simply write:

```python
scores = movies @ user
```

Python automatically computes:

```text
Movie A • User

Movie B • User

Movie C • User
```

all in one operation.

Instead of getting one score, we receive a score for every movie.

Example:

```text
Movie A → 41

Movie B → 9

Movie C → 41
```

This is why matrix multiplication is so powerful.

Rather than processing one comparison at a time, computers can compare **thousands or even millions of vectors simultaneously**.

This same idea is used in:

- Recommendation Systems
- Search Engines
- Neural Networks
- Transformers
- Computer Vision

---

## 1.6 Shape Rule (Very Important)

Matrix multiplication only works when the dimensions are compatible.

The rule is simple:

> **The number of columns in the first matrix must equal the number of rows in the second matrix.**

### Example That Works

```text
(3 × 4) @ (4 × 1) ✅
```

Both matrices share the number **4**, so multiplication is possible.

The result will have the outer dimensions:

```text
(3 × 1)
```

### Example That Doesn't Work

```text
(3 × 4) @ (5 × 1) ❌
```

This fails because:

```text
4 ≠ 5
```

NumPy will throw an error similar to:

```text
ValueError: shapes (3,4) and (5,1) not aligned
```

Don't be afraid of this error.

It simply means the dimensions don't match.

### Easy Trick to Remember

Whenever you see:

```text
(A × B) @ (B × C)
```

Look only at the **inside numbers**.

```text
(A × B) @ (B × C)
        ↑     ↑
     Must Match
```

If they match, multiplication is valid.

The answer will always have the **outside dimensions**.

```text
(A × C)
```

For example:

```text
(5 × 10) @ (10 × 8)

↓

Result = (5 × 8)
```

Once you remember this rule, dimension errors become much easier to understand.

---

## 1.7 Transformer Intuition

Everything you've learned so far directly applies to Transformers.

Inside a Transformer:

- Every word is converted into a **vector**.
- A sentence becomes a **matrix** because it contains many word vectors.
- Weight matrices transform these vectors into new representations.

For example, consider the sentence:

```text
"I love AI"
```

After tokenization:

```text
["I", "love", "AI"]
```

Each word is converted into an embedding vector.

```text
"I"    → [0.21, -0.43, ...]
"love" → [0.85,  0.12, ...]
"AI"   → [0.67, -0.30, ...]
```

Stacking these vectors forms a matrix.

```text
Sentence Matrix

Word 1 Embedding

Word 2 Embedding

Word 3 Embedding
```

The Transformer repeatedly performs operations such as:

```python
output = weights @ embeddings
```

or

```text
Weight Matrix × Embedding Matrix
```

Each multiplication transforms the embeddings into richer representations that capture more context and meaning.

This process happens repeatedly across multiple Transformer layers.

Almost every major computation inside a Transformer—including embeddings, self-attention, and feed-forward neural networks—is built on **matrix multiplication**.

This is why vectors, matrices, and matrix multiplication are considered the mathematical foundation of modern AI.

---

## 1.8 Key Takeaways

By now, you should understand the following ideas:

- A **vector** is a list of numbers representing an object or concept.
- A **matrix** is a collection of vectors arranged in rows and columns.
- Matrix multiplication transforms one vector or matrix into another.
- Matrix multiplication is essentially **many dot products performed at once**.
- The **shape rule** determines whether two matrices can be multiplied.
- Recommendation systems, neural networks, and Transformers all rely heavily on matrix multiplication.

These concepts may seem simple, but they form the mathematical backbone of almost every modern Machine Learning and Generative AI model you'll study in the chapters ahead.