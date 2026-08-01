# 10. CPU vs GPU

## Introduction

So far, we've trained every PyTorch model on the **CPU**, which is perfectly fine for learning and small projects.

However, modern Deep Learning models contain millions—or even billions—of parameters. Training such models on a CPU would take an impractically long time.

To solve this problem, Deep Learning frameworks use **Graphics Processing Units (GPUs)**.

In this chapter, we'll learn the difference between CPUs and GPUs, why GPUs are much faster for AI workloads, and how to move PyTorch models and tensors between devices.

---

## 10.1 What is a CPU?

A **CPU (Central Processing Unit)** is the main processor of a computer.

It is designed to perform many different types of tasks efficiently.

Examples include:

- Running applications
- Browsing the web
- Managing files
- Executing Python programs
- Running operating systems

A CPU has a small number of powerful cores that excel at performing different tasks quickly.

```text
CPU

Core 1
Core 2
Core 3
Core 4
```

CPUs are excellent for general-purpose computing.

---

## 10.2 What is a GPU?

A **GPU (Graphics Processing Unit)** was originally designed to render graphics and videos.

Unlike a CPU, a GPU contains **thousands of smaller cores** that can perform many mathematical operations simultaneously.

```text
GPU

■■■■■■■■■■■■■■■■■■■■
■■■■■■■■■■■■■■■■■■■■
■■■■■■■■■■■■■■■■■■■■
■■■■■■■■■■■■■■■■■■■■
```

This makes GPUs ideal for Deep Learning, where millions of matrix and tensor operations must be performed repeatedly.

---

## 10.3 Why Are GPUs Faster for Deep Learning?

Deep Learning mainly involves operations such as:

- Matrix multiplication
- Tensor addition
- Tensor multiplication
- Gradient computation

These operations can be performed in parallel.

Instead of processing one calculation at a time, a GPU processes thousands of calculations simultaneously.

For this reason, training a neural network on a GPU is often many times faster than training it on a CPU.

---

## 10.4 CPU vs GPU

| CPU | GPU |
|------|------|
| Few powerful cores | Thousands of smaller cores |
| General-purpose computing | Parallel mathematical computation |
| Best for everyday applications | Best for Deep Learning |
| Lower parallelism | Massive parallelism |

For small programs, the CPU is usually sufficient.

For Deep Learning and LLMs, GPUs are the standard choice.

---

## 10.5 Checking for a GPU

PyTorch can check whether a CUDA-compatible GPU is available.

```python
import torch

print(torch.cuda.is_available())
```

Output:

```text
True
```

or

```text
False
```

If the output is `False`, PyTorch will use the CPU.

---

## 10.6 Selecting a Device

A common PyTorch pattern is:

```python
import torch

device = torch.device(
    "cuda" if torch.cuda.is_available() else "cpu"
)

print(device)
```

Output:

```text
cuda
```

or

```text
cpu
```

This allows the same code to run on systems with or without a GPU.

---

## 10.7 Moving Tensors to a GPU

A tensor can be moved using `.to(device)`.

```python
import torch

device = torch.device(
    "cuda" if torch.cuda.is_available() else "cpu"
)

x = torch.tensor([1, 2, 3])

x = x.to(device)

print(x.device)
```

Output:

```text
cuda:0
```

or

```text
cpu
```

---

## 10.8 Moving a Model to a GPU

Models are moved in the same way.

```python
model = model.to(device)
```

Now every parameter inside the model is stored on the selected device.

---

## 10.9 Keeping the Model and Data on the Same Device

One of the most common beginner mistakes is placing the model on the GPU while leaving the data on the CPU.

Incorrect:

```python
model = model.to(device)

prediction = model(X)
```

If `X` is still on the CPU, PyTorch raises an error.

Correct:

```python
X = X.to(device)

model = model.to(device)

prediction = model(X)
```

The model and the input data must always be on the same device.

---

## 10.10 Real-World Example

Training a small neural network on a CPU may take only a few seconds.

Training a modern Large Language Model can require:

- Multiple GPUs
- Hundreds of GPUs
- Sometimes thousands of GPUs working together

This massive computational power allows LLMs to process enormous datasets within a reasonable amount of time.

---

## 10.11 Transformer Connection

Every operation inside a Transformer is a tensor operation.

Examples include:

- Matrix multiplication
- Attention computation
- Feed-forward networks
- Gradient calculation

GPUs execute these tensor operations in parallel, making it practical to train and run modern LLMs.

Without GPUs, training today's large Transformer models would be extremely slow.

---

## 10.12 Common Beginner Mistakes

### Forgetting to Move the Data

```python
X = X.to(device)
```

---

### Forgetting to Move the Model

```python
model = model.to(device)
```

---

### Mixing CPU and GPU Tensors

Both the model and its input tensors must be on the same device.

---

### Assuming a GPU Is Always Available

Always check:

```python
torch.cuda.is_available()
```

before using CUDA.

---

## 10.13 Key Takeaways

- A CPU is designed for general-purpose computing.
- A GPU is designed for highly parallel mathematical computations.
- Deep Learning benefits greatly from GPU acceleration.
- Use `torch.cuda.is_available()` to check for a CUDA-compatible GPU.
- Move tensors and models using `.to(device)`.
- The model and input data must always be on the same device.
- Modern Transformers and LLMs rely heavily on GPUs for training and inference.

Congratulations! 🎉

You have completed the **PyTorch Fundamentals** section.

In the next chapter, we'll build a complete handwritten digit classifier using everything you've learned so far.