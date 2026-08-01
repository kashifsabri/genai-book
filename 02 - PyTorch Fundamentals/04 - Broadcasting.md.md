# 4. Broadcasting

## Introduction

In the previous chapter, we learned how to perform mathematical operations on tensors.

But what happens if two tensors have **different shapes**?

For example:

```python
import torch

A = torch.tensor([
    [1, 2, 3],
    [4, 5, 6]
])

B = torch.tensor([10, 20, 30])

print(A + B)
```

Even though the shapes are different, PyTorch successfully performs the addition.

How?

The answer is **broadcasting**.

Broadcasting is one of PyTorch's most useful features. It allows tensors with compatible shapes to participate in mathematical operations without manually copying data.

---

## What is Broadcasting?

**Broadcasting** is the process of automatically expanding a smaller tensor so it can match the shape of a larger tensor during an operation.

Think of it like using the same value repeatedly wherever it is needed.

For example:

```text
A

1 2 3
4 5 6
```

and

```text
B

10 20 30
```

PyTorch automatically treats **B** as:

```text
10 20 30
10 20 30
```

Now both tensors have the same shape.

Then it performs the addition.

```text
1+10   2+20   3+30
4+10   5+20   6+30
```

Result:

```text
11 22 33
14 25 36
```

Notice that **PyTorch does not actually copy the data**. It behaves _as if_ it expanded the tensor, making operations efficient in both memory and speed.

---

## Python Example

```python
import torch

A = torch.tensor([
    [1, 2, 3],
    [4, 5, 6]
])

B = torch.tensor([10, 20, 30])

print(A + B)
```

Output:

```text
tensor([[11, 22, 33],
        [14, 25, 36]])
```

---

## Another Example

Suppose we have:

```text
A

5
10
15
```

and

```text
B

100
```

PyTorch automatically expands **100**.

```text
100
100
100
```

Result:

```text
105
110
115
```

Python:

```python
import torch

A = torch.tensor([5, 10, 15])

B = torch.tensor(100)

print(A + B)
```

Output:

```text
tensor([105, 110, 115])
```

---

## Broadcasting Rules

Broadcasting follows three simple rules.

### Rule 1

If both dimensions are equal, broadcasting works.

Example:

```text
(2,3)

+

(2,3)

✅ Works
```

---

### Rule 2

If one dimension is **1**, PyTorch stretches it to match the other tensor.

Example:

```text
(2,3)

+

(1,3)

✅ Works
```

The first dimension is expanded from **1** to **2**.

---

### Rule 3

If neither dimension matches and neither is **1**, broadcasting fails.

Example:

```text
(2,3)

+

(2,2)

❌ Error
```

Because:

```text
3 ≠ 2
```

and neither dimension is **1**.

---

## Comparing Shapes

Example 1

```text
A Shape = (2,3)

B Shape = (3,)
```

PyTorch interprets `(3,)` as:

```text
(1,3)
```

Then broadcasts it to:

```text
(2,3)
```

So the operation succeeds.

---

Example 2

```text
A Shape = (4,5)

B Shape = (5,)
```

PyTorch treats `(5,)` as:

```text
(1,5)
```

Then expands it to:

```text
(4,5)
```

Again, the operation succeeds.

---

Example 3

```text
A Shape = (3,4)

B Shape = (2,4)
```

Neither first dimension matches.

Neither is **1**.

Broadcasting fails.

---

## Visualizing Broadcasting

Without broadcasting, we would have to manually create:

```text
10 20 30
10 20 30
```

Broadcasting performs this expansion automatically.

```text
      10 20 30
          │
          ▼

10 20 30
10 20 30
```

Then performs the operation.

---

## Why is Broadcasting Useful?

Broadcasting removes the need to manually duplicate data.

For example, suppose every student's marks need **5 bonus points**.

Without broadcasting:

```text
85 90 78
75 88 91
92 80 86
```

We would have to create another matrix filled with **5s**.

With broadcasting:

```python
marks + 5
```

PyTorch automatically adds **5** to every value.

---

## Broadcasting in Deep Learning

Broadcasting is used constantly inside neural networks.

Examples include:

- Adding bias values
- Normalizing data
- Scaling tensors
- Applying activation functions
- Image preprocessing

Although you may not notice it, many PyTorch operations rely on broadcasting behind the scenes.

---

## Broadcasting with Higher-Dimensional Tensors

The examples so far only used 1D and 2D tensors, but the same rules apply no matter how many dimensions a tensor has — the shapes are simply compared starting from the **rightmost (trailing) dimension** and moving left.

Recall from the Tensors chapter that PyTorch represents a batch of images with shape `[batch, channels, height, width]` — for example, `[32, 3, 224, 224]`. Suppose we want to add a different bias value to each color channel:

```python
import torch

images = torch.randn(32, 3, 224, 224)          # a batch of 32 RGB images

channel_bias = torch.tensor([0.1, 0.2, 0.3]).reshape(3, 1, 1)   # shape: (3, 1, 1)

result = images + channel_bias

print(result.shape)
```

Output:

```text
torch.Size([32, 3, 224, 224])
```

Here's why this works, comparing shapes from right to left:

```text
images:        (32, 3, 224, 224)
channel_bias:        (3,   1,   1)
                       │    │    │
                     equal  │    │
                          stretched (1 → 224)
                               stretched (1 → 224)
```

The `channel_bias` tensor has no `batch` dimension at all — PyTorch implicitly treats the missing leftmost dimension as `1` and broadcasts it across all 32 images. This is exactly how per-channel normalization (subtracting a mean and dividing by a standard deviation for each color channel) is implemented in real image preprocessing pipelines.

---

## Transformer Connection

Transformers perform billions of tensor operations.

Many of these involve broadcasting.

Examples include:

- Adding positional embeddings to token embeddings.
- Adding bias terms in linear layers.
- Applying attention masks.
- Normalizing activations.

Without broadcasting, these operations would require much more code and memory.

---

## Common Beginner Mistakes

### Mistake 1

Assuming broadcasting always works.

It doesn't.

Shapes must follow the broadcasting rules.

---

### Mistake 2

Confusing broadcasting with matrix multiplication.

Broadcasting is used for **element-wise operations**.

Matrix multiplication follows completely different shape rules.

---

### Mistake 3

Ignoring tensor shapes.

Whenever you get an error like:

```text
RuntimeError:
The size of tensor a (...) must match the size of tensor b (...)
```

The first thing to check is:

```python
print(A.shape)
print(B.shape)
```

Most broadcasting errors can be solved by inspecting the tensor shapes.

---

## Key Takeaways

- Broadcasting automatically expands smaller tensors during element-wise operations.
- PyTorch behaves as if it stretches a tensor without actually copying the data.
- A dimension can be broadcast if it is equal to the corresponding dimension or if it is **1**.
- Shapes are compared starting from the rightmost dimension; missing leading dimensions are treated as **1**.
- Broadcasting simplifies code and improves performance.
- Many neural-network and Transformer operations rely on broadcasting internally, including higher-dimensional cases like per-channel image normalization.