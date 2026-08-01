# 3. Tensor Operations

## Introduction

In the previous chapter, we learned what tensors are and how to create them.

Now it's time to perform operations on tensors.

Just as we perform addition, subtraction, and multiplication on numbers, PyTorch allows us to perform these operations on tensors.

These operations are used everywhere in Deep Learning, from simple neural networks to Large Language Models.

---

## Addition

Two tensors with the same shape can be added together.

```python
import torch

A = torch.tensor([1, 2, 3])
B = torch.tensor([4, 5, 6])

C = A + B

print(C)
```

Output:

```text
tensor([5, 7, 9])
```

Each element is added to its corresponding element.

```text
1 + 4 = 5
2 + 5 = 7
3 + 6 = 9
```

---

## Subtraction

Subtraction works the same way.

```python
import torch

A = torch.tensor([10, 20, 30])
B = torch.tensor([2, 5, 8])

C = A - B

print(C)
```

Output:

```text
tensor([ 8, 15, 22])
```

---

## Multiplication

The `*` operator performs **element-wise multiplication**.

```python
import torch

A = torch.tensor([2, 3, 4])
B = torch.tensor([5, 6, 7])

C = A * B

print(C)
```

Output:

```text
tensor([10, 18, 28])
```

Calculation:

```text
2 × 5 = 10
3 × 6 = 18
4 × 7 = 28
```

> **Important:** `*` is **not** matrix multiplication. It multiplies matching elements.

---

## Division

Division is also performed element by element.

```python
import torch

A = torch.tensor([10.0, 20.0, 30.0])
B = torch.tensor([2.0, 5.0, 3.0])

print(A / B)
```

Output:

```text
tensor([5., 4., 10.])
```

---

## Broadcasting

So far, every operation used two tensors with the **exact same shape**. But PyTorch can also combine tensors of _different_ shapes, as long as those shapes are "compatible." This is called **broadcasting** — PyTorch automatically stretches the smaller tensor across the larger one so their shapes line up.

### Example 1 — Scalar Broadcasting

```python
import torch

x = torch.tensor([1, 2, 3])

y = x + 10

print(y)
```

Output:

```text
tensor([11, 12, 13])
```

Here, the single value `10` is applied to every element of `x`, even though `10` isn't a tensor of shape `[3]`.

---

### Example 2 — Vector + Matrix Broadcasting

```python
import torch

A = torch.tensor([
    [1, 2, 3],
    [4, 5, 6]
])

b = torch.tensor([10, 20, 30])

print(A + b)
```

Output:

```text
tensor([[11, 22, 33],
        [14, 25, 36]])
```

`A` has shape `[2, 3]` and `b` has shape `[3]`. Since `b`'s shape matches `A`'s last dimension, PyTorch "broadcasts" `b` across every row of `A`:

```text
Row 1: 1+10=11, 2+20=22, 3+30=33
Row 2: 4+10=14, 5+20=25, 6+30=36
```

> **Rule of thumb:** Two dimensions are compatible for broadcasting when they're either **equal**, or **one of them is 1**. This is exactly what let us write `image = image * 2` in the real-world example later in this chapter — a single scalar was broadcast across every pixel of the image tensor.

---

## Matrix Multiplication

To perform matrix multiplication, use the `@` operator or `torch.matmul()`.

```python
import torch

A = torch.tensor([
    [1, 2],
    [3, 4]
])

B = torch.tensor([
    [5],
    [6]
])

print(A @ B)
```

Output:

```text
tensor([[17],
        [39]])
```

Calculation:

```text
1×5 + 2×6 = 17
3×5 + 4×6 = 39
```

This is the same matrix multiplication you learned earlier using NumPy.

---

## Reshaping Tensors

Sometimes we need to change a tensor's shape without changing its data.

```python
import torch

x = torch.arange(12)

print(x)
```

Output:

```text
tensor([0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11])
```

Reshape it:

```python
x = x.reshape(3, 4)

print(x)
```

Output:

```text
tensor([[ 0,  1,  2,  3],
        [ 4,  5,  6,  7],
        [ 8,  9, 10, 11]])
```

Notice that only the shape changed.

The values remained the same.

---

## Flattening a Tensor

Sometimes we want to convert a multi-dimensional tensor into one dimension.

```python
import torch

x = torch.tensor([
    [1,2],
    [3,4]
])

print(torch.flatten(x))
```

Output:

```text
tensor([1, 2, 3, 4])
```

Flattening is commonly used before passing data into a neural network.

---

## Transpose

Transpose swaps rows and columns.

```python
import torch

A = torch.tensor([
    [1,2,3],
    [4,5,6]
])

print(A.T)
```

Output:

```text
tensor([[1,4],
        [2,5],
        [3,6]])
```

---

## Indexing

Access a single element.

```python
import torch

x = torch.tensor([10,20,30])

print(x[1])
```

Output:

```text
tensor(20)
```

---

## Slicing

Extract multiple elements.

```python
import torch

x = torch.tensor([10,20,30,40,50])

print(x[1:4])
```

Output:

```text
tensor([20, 30, 40])
```

The syntax is the same as Python lists.

---

## Useful Operations

Find the sum:

```python
print(torch.sum(x))
```

Output:

```text
tensor(150)
```

Find the mean:

```python
print(torch.mean(x.float()))
```

Output:

```text
tensor(30.)
```

Find the maximum:

```python
print(torch.max(x))
```

Output:

```text
tensor(50)
```

Find the minimum:

```python
print(torch.min(x))
```

Output:

```text
tensor(10)
```

These operations are frequently used when processing data.

---

## Real-World Example

Suppose a grayscale image is represented as a tensor.

```text
[
 [12, 45, 78],
 [91, 62, 30],
 [15, 80, 42]
]
```

If we multiply every value by 2:

```python
image = image * 2
```

Every pixel becomes brighter.

This works because of **broadcasting** — the scalar `2` is applied across every element of the image tensor, the same way it worked in the earlier broadcasting examples.

Similarly, neural networks continuously perform mathematical operations on tensors representing images, text, audio, and embeddings.

---

## Transformer Connection

Every operation inside a Transformer is a tensor operation.

Examples include:

- Creating embeddings
- Matrix multiplication
- Computing attention scores
- Applying activation functions
- Calculating probabilities

Modern GPUs perform billions of tensor operations every second while training or running Large Language Models.

---

## Key Takeaways

- Tensor operations are similar to normal mathematical operations.
- `+`, `-`, `*`, and `/` work element by element.
- Broadcasting lets PyTorch combine tensors of different but compatible shapes, such as adding a scalar to a vector or a vector to every row of a matrix.
- Matrix multiplication uses `@` or `torch.matmul()`.
- `reshape()` changes the shape without changing the data.
- `flatten()` converts tensors into one dimension.
- Transpose swaps rows and columns.
- Indexing and slicing work similarly to Python lists.
- Tensor operations are the foundation of every Deep Learning model.