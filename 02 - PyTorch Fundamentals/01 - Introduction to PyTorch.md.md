# 1. Introduction to PyTorch

## Introduction

So far, we have learned the mathematical foundations of Machine Learning, including vectors, matrices, probability, softmax, loss functions, gradient descent, and backpropagation. These concepts explain **how Machine Learning models learn**.

Now it's time to put those ideas into practice.

This is where **PyTorch** comes in.

PyTorch is one of the world's most popular Deep Learning frameworks. It provides the tools needed to build, train, and deploy Machine Learning models without implementing every mathematical operation from scratch.

A simple way to think about it is:

> **Mathematics explains how a model works. PyTorch helps us build that model in code.**

In this chapter, we'll learn what PyTorch is, why it is widely used, how to install it, and write our first PyTorch program.

---

## What Is PyTorch?

**PyTorch** is an open-source Machine Learning and Deep Learning framework developed by **Facebook AI Research (FAIR)** and now maintained by the **PyTorch Foundation**.

It is mainly used with Python and provides powerful tools for:

- Working with tensors
- Performing mathematical operations
- Building neural networks
- Training Machine Learning models
- Running computations efficiently on CPUs and GPUs

Instead of writing hundreds of lines of mathematical code, PyTorch allows us to build models using simple and readable Python code.

For example:

```python
import torch

x = torch.tensor([1.0, 2.0, 3.0])

print(x)
```

Output:

```text
tensor([1., 2., 3.])
```

The object stored in `x` is called a **tensor**, which is the fundamental data structure in PyTorch. We will study tensors in detail in the next chapter.

---

## Why Do We Need PyTorch?

Building a Machine Learning model involves much more than writing equations.

A model must:

1. Accept input data
2. Perform mathematical operations
3. Make predictions
4. Measure prediction errors
5. Calculate gradients
6. Update its parameters
7. Repeat this process thousands of times

Writing all of this from scratch would be difficult and error-prone.

PyTorch provides reliable and optimized tools for these tasks, allowing us to focus on designing and improving models rather than implementing low-level mathematics.

> **PyTorch handles the computations. We focus on building the model.**

---

## Why Is PyTorch Popular?

PyTorch has become one of the most widely used Deep Learning frameworks because it is:

- Easy to learn and use
- Python-friendly
- Flexible for research and experimentation
- Fast and efficient
- GPU accelerated
- Supported by a large open-source community

Many popular AI libraries are built on the PyTorch ecosystem, including:

- Hugging Face Transformers
- Sentence Transformers
- PEFT
- TRL
- TorchVision
- TorchAudio
- PyTorch Lightning

Learning PyTorch gives you the foundation needed to work with these tools.

---

## Why Are We Learning PyTorch?

Throughout the rest of this book, we'll use PyTorch to build increasingly advanced AI systems.

Using PyTorch, you'll learn how to:

- Work with tensors
- Build neural networks
- Train Machine Learning models
- Understand embeddings
- Implement attention mechanisms
- Build Transformers
- Fine-tune Large Language Models (LLMs)

Almost every practical Deep Learning project you'll build later in this book will use PyTorch.

---

## Where Is PyTorch Used?

PyTorch is widely used across many areas of Artificial Intelligence, including:

- Computer Vision
- Natural Language Processing (NLP)
- Recommendation Systems
- Speech Recognition
- Large Language Models (LLMs)

Many modern AI models and frameworks rely on the PyTorch ecosystem, making it one of the most valuable tools for AI engineers.

---

## Installing PyTorch

For this book, a basic installation is enough.

```bash
pip install torch
```

To verify the installation:

```python
import torch

print(torch.__version__)
```

If a version number is displayed, PyTorch has been installed successfully.

> **Note:** If you're using a GPU, visit the official PyTorch website for the appropriate installation command for your operating system and CUDA version.

---

## Your First PyTorch Program

Create a new Python file and write the following code:

```python
import torch

numbers = torch.tensor([10, 20, 30])

print(numbers)
```

Output:

```text
tensor([10, 20, 30])
```

This simple program creates a tensor containing three numbers and prints it to the screen.

Let's perform a simple mathematical operation.

```python
import torch

numbers = torch.tensor([1, 2, 3])

result = numbers * 2

print(result)
```

Output:

```text
tensor([2, 4, 6])
```

Each value inside the tensor is multiplied by `2`.

Although this example is simple, the same idea is used when training large neural networks.

---

## What We'll Learn Next

In the next chapter, we'll study **tensors**, the most important data structure in PyTorch.

We'll learn:

- What tensors are
- Tensor dimensions
- Tensor shapes
- Data types
- Creating tensors
- Inspecting tensors

Every image, sentence, embedding, model weight, and prediction in PyTorch is represented using tensors.

Understanding tensors is the first practical step toward building Deep Learning models.

---

## Key Takeaways

- PyTorch is an open-source Deep Learning framework.
- It allows us to build and train Machine Learning models using Python.
- PyTorch simplifies mathematical computations required for Deep Learning.
- The fundamental data structure in PyTorch is the **tensor**.
- PyTorch is widely used for neural networks, Transformers, and Large Language Models.
- In the next chapter, we'll explore tensors in detail.