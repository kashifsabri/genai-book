# 2. Tensors

## Introduction

In the previous chapter, we learned that **PyTorch uses tensors as its fundamental data structure**.

Every image, sentence, embedding, model weight, and prediction inside a Deep Learning model is stored as a tensor.

If vectors and matrices were the building blocks of Machine Learning mathematics, then tensors are the building blocks of PyTorch.

In this chapter, we'll learn what tensors are, why they are used, how to create them, and how to inspect their properties.

---

## What Is a Tensor?

A **tensor** is a multi-dimensional collection of numbers.

You can think of a tensor as a generalization of the mathematical concepts you've already learned.

|Mathematical Object|PyTorch Representation|
|---|---|
|Scalar|0D Tensor|
|Vector|1D Tensor|
|Matrix|2D Tensor|
|3D Data|3D Tensor|
|Higher-dimensional Data|nD Tensor|

In simple words:

- A scalar is a tensor.
- A vector is a tensor.
- A matrix is a tensor.
- A collection of matrices is also a tensor.

This is why PyTorch uses the single word **tensor** instead of separate terms like vector and matrix.


---

## Visualizing Tensors

Before learning tensor operations, let's visualize what tensors look like.

### Scalar (0D Tensor)

A scalar is a single value.

```text
5
```

Examples:

- Temperature = 28°C
- Age = 25
- Price = ₹499

Python:

```python
import torch

scalar = torch.tensor(5)

print(scalar)
```

---

### Vector (1D Tensor)

A vector is a list of numbers.

```text
[2, 4, 6]
```

Examples:

- Student marks
- Daily temperatures
- Word embedding

Python:

```python
vector = torch.tensor([2, 4, 6])

print(vector)
```

---

### Matrix (2D Tensor)

A matrix is a table of numbers.

```text
1  2  3
4  5  6
```

Examples:

- Excel spreadsheet
- Database table
- Grayscale image

Python:

```python
matrix = torch.tensor([
    [1, 2, 3],
    [4, 5, 6]
])

print(matrix)
```

---

### 3D Tensor

A 3D tensor is a collection of matrices.

```text
Matrix 1

1 2
3 4

Matrix 2

5 6
7 8
```

Examples:

- RGB image (Height × Width × Channels)
- Video frame
- Batch of matrices

Python:

```python
tensor3d = torch.tensor([
    [[1, 2],
     [3, 4]],

    [[5, 6],
     [7, 8]]
])

print(tensor3d)
```

---

### Higher-Dimensional Tensor

Deep Learning often works with tensors having four or more dimensions.

For example, a batch of RGB images may have the shape:

```text
[32, 224, 224, 3]
```

Meaning:

- 32 images
- 224 pixels high
- 224 pixels wide
- 3 color channels (Red, Green, Blue)

You don't need to visualize every higher-dimensional tensor. Think of them as collections of lower-dimensional tensors stacked together.

---

## Tensor Hierarchy

You can think of tensors as building on one another.

```text
Scalar (0D)
      │
      ▼
Vector (1D)
      │
      ▼
Matrix (2D)
      │
      ▼
3D Tensor
      │
      ▼
nD Tensor
```

Every scalar, vector, and matrix is a tensor. A tensor is simply a way of representing numerical data with one or more dimensions.

---

## Why Do We Need Tensors?

Machine Learning models work with large amounts of numerical data.

Examples include:

- Images
- Audio
- Videos
- Word embeddings
- Model weights
- Predictions

All of this data needs to be stored efficiently.

PyTorch stores it using tensors.

A tensor can also perform mathematical operations much faster than a normal Python list and can be moved to a GPU for faster computation.

---

## Scalars, Vectors, Matrices and Tensors

### Scalar (0D Tensor)

A scalar is a single value.

```text
5
```

Python:

```python
import torch

x = torch.tensor(5)

print(x)
```

Output:

```text
tensor(5)
```

---

### Vector (1D Tensor)

A vector is a list of numbers.

```text
[2, 4, 6]
```

Python:

```python
vector = torch.tensor([2, 4, 6])

print(vector)
```

Output:

```text
tensor([2, 4, 6])
```

---

### Matrix (2D Tensor)

A matrix is a table of numbers.

```text
1 2 3
4 5 6
```

Python:

```python
matrix = torch.tensor([
    [1, 2, 3],
    [4, 5, 6]
])

print(matrix)
```

Output:

```text
tensor([[1, 2, 3],
        [4, 5, 6]])
```

---

### 3D Tensor

A 3D tensor is a collection of matrices.

Example:

```text
[
 [
  [1,2],
  [3,4]
 ],

 [
  [5,6],
  [7,8]
 ]
]
```

Python:

```python
tensor3d = torch.tensor([
    [[1,2],[3,4]],
    [[5,6],[7,8]]
])

print(tensor3d)
```

---

## Real-World Examples

Different types of data are stored as tensors.

|Data|Tensor Shape|
|---|---|
|Temperature|`[]`|
|Student Marks|`[100]`|
|Excel Table|`[500, 8]`|
|RGB Image|`[3, 224, 224]`|
|Batch of Images|`[32, 3, 224, 224]`|
|Sentence Embeddings|`[768]`|
|Batch of Embeddings|`[64, 768]`|

Notice that everything can be represented as numbers.

> **Note:** PyTorch represents images in **channel-first** format — `[channels, height, width]` — as shown above. This is different from NumPy, PIL, and TensorFlow/Keras, which typically use **channel-last** format (`[height, width, channels]`). If you load an image with a library like PIL and convert it directly to a tensor, double-check its shape — mismatched channel ordering is one of the most common shape errors beginners run into when feeding images into a PyTorch model.

---

## Creating Tensors

PyTorch provides many ways to create tensors.

From a list:

```python
x = torch.tensor([1,2,3])
```

All zeros:

```python
torch.zeros(3,4)
```

All ones:

```python
torch.ones(2,5)
```

Random numbers:

```python
torch.rand(3,3)
```

Random integers:

```python
torch.randint(0,10,(2,4))
```

Identity matrix:

```python
torch.eye(3)
```

These functions are used constantly in Deep Learning.

---

## Tensor Properties

Every tensor has four important properties.

### Shape

The shape tells us the size of each dimension.

```python
x = torch.tensor([
    [1,2,3],
    [4,5,6]
])

print(x.shape)
```

Output:

```text
torch.Size([2, 3])
```

Meaning:

- 2 rows
- 3 columns

---

### Number of Dimensions

```python
print(x.ndim)
```

Output:

```text
2
```

---

### Data Type

```python
print(x.dtype)
```

Output:

```text
torch.int64
```

---

### Device

```python
print(x.device)
```

Output:

```text
cpu
```

Later we'll move tensors to GPUs.

---

## Why Shape Is So Important

Shape is one of the most important concepts in Deep Learning.

Suppose we have:

```text
(3 × 4)
```

and

```text
(4 × 2)
```

These matrices can be multiplied.

However,

```text
(3 × 4)

@

(5 × 2)
```

will produce an error because the dimensions don't match.

Many PyTorch errors are actually **shape errors**.

Whenever something doesn't work, checking the tensor shape is usually the first debugging step.

---

## Transformer Connection

Every component inside a Transformer is represented using tensors.

Examples include:

- Input IDs
- Token Embeddings
- Position Embeddings
- Attention Matrices
- Query, Key and Value matrices
- Hidden States
- Logits
- Probabilities

During training, billions of tensor operations are performed every second.

Understanding tensors is therefore the first practical step toward understanding Transformers and Large Language Models.

---

## Key Takeaways

- A tensor is a multi-dimensional collection of numbers.
- Scalars, vectors and matrices are all tensors.
- Tensors are the fundamental data structure in PyTorch.
- Every tensor has a shape, data type, number of dimensions and device.
- PyTorch represents images in channel-first format `[C, H, W]`, unlike the channel-last format used by NumPy, PIL, and TensorFlow.
- Images, text, embeddings and model weights are all represented as tensors.
- Understanding tensors is essential before learning tensor operations, Autograd and neural networks.