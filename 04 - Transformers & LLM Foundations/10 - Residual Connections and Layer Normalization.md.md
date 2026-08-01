# 10. Residual Connections

## Introduction

So far, we've learned two major parts of a Transformer block:

- Multi-Head Attention
- Feed Forward Network (FFN)

These layers make the Transformer smarter by learning better representations of the input.

But another problem appears.

Modern Transformers can have:

- 12 layers
- 24 layers
- 48 layers
- 96 layers
- Even hundreds of layers

As the network becomes deeper, information has to pass through many layers before reaching the output.

During this journey, some important information can become weaker or even disappear.

Researchers needed a simple way to preserve this information.

The solution was **Residual Connections**.

---

# 10.1 What is a Residual Connection?

A **Residual Connection** is a shortcut.

Instead of passing information through only one path, the Transformer creates an additional shortcut that carries the original information forward.

Think of it like this.

Without a shortcut:

```text
Input
  │
  ▼
Layer 1
  │
  ▼
Layer 2
  │
  ▼
Layer 3
  │
  ▼
Output
```

With a shortcut:

```text
           ┌──────────────┐
           │              │
Input ─────┘              │
  │                       ▼
  ▼                   Add Together
Layer                  │
  │                    ▼
  └────────────────► Output
```

The original input is added back to the output of the layer.

This helps preserve important information.

---

# 10.2 Why Do We Need Residual Connections?

Imagine playing the game:

```text
Chinese Whispers
```

One person whispers a sentence to the next person.

That person whispers it to another.

After many people, the sentence often changes completely.

Example:

Original:

```text
I love learning AI.
```

After passing through many people:

```text
I love eating apples.
```

Some information is lost.

Deep neural networks can have a similar problem.

As information passes through many layers, the original information may become weaker.

Residual Connections help prevent this.

---

# 10.3 The Main Idea

Instead of using only the output from a layer,

the Transformer adds the original input back.

Simple diagram:

```text
Input
   │
   ├───────────────┐
   │               │
   ▼               │
Layer              │
   │               │
   ▼               │
Layer Output       │
   │               │
   └──────► Add ◄──┘
              │
              ▼
           Final Output
```

The layer learns new information.

The shortcut preserves the original information.

The final output contains both.

---

# 10.4 A Simple Example

Imagine you're editing a document.

Original sentence:

```text
The cat drinks milk.
```

You correct one small mistake.

New sentence:

```text
The cat drinks fresh milk.
```

You don't rewrite the whole document.

You keep the original sentence and add only the improvement.

Residual Connections work in a similar way.

The model keeps the original information and adds what it has learned.

---

# 10.5 The Formula

The mathematical formula is very simple.

```text
Output = Input + Layer Output
```

Notice something important.

The Transformer does **not** replace the original input.

It **adds** the new information to it.

---

# 10.6 Python Example

Let's see how this looks in PyTorch.

```python
import torch
import torch.nn as nn

layer = nn.Linear(4, 4)

x = torch.rand(2, 4)

layer_output = layer(x)

output = x + layer_output

print(output.shape)
```

Output

```text
torch.Size([2, 4])
```

### Understanding the Output

- **2** → Number of samples
- **4** → Number of features

Notice that the shape stays the same.

This is important because we can only add tensors with the same shape.

---

# 10.7 Why Is This So Powerful?

Imagine climbing a mountain.

Without shortcuts:

```text
Start

↓

Step 1

↓

Step 2

↓

Step 3

↓

Finish
```

If one step becomes difficult, the journey becomes slow.

Now imagine there are shortcuts.

```text
Start
   │
   ├────────► Step 3
   │
   ▼
Step 1
   │
   ▼
Step 2
```

The shortcuts make the journey easier.

Residual Connections provide similar shortcuts for information inside a neural network.

---

# 10.8 Where Are Residual Connections Used?

Residual Connections are used in many modern deep learning models.

Examples include:

- Transformers
- GPT
- BERT
- Llama
- Claude
- Gemini
- ResNet (Computer Vision)

They are one of the key ideas that made very deep neural networks practical.

---

# 10.9 Does Every Transformer Layer Have One?

Yes.

Every Transformer block contains Residual Connections.

One is used after:

- Multi-Head Attention

Another is used after:

- Feed Forward Network

This means every block preserves important information while learning new patterns.

---

# 10.10 Transformer Block So Far

Our Transformer block now looks like this.

```text
Input
   │
   ▼
Multi-Head Attention
   │
   ▼
Add Residual Connection
   │
   ▼
Feed Forward Network
   │
   ▼
Add Residual Connection
   │
   ▼
Output
```

We're getting very close to understanding the complete Transformer block.

---

# Real-World Example

Imagine you're building a house.

You already have a strong foundation.

When adding a new floor, you don't remove the foundation.

You keep it and build on top of it.

Residual Connections work the same way.

The model keeps the original information while adding new knowledge.

---

# Transformer Connection

Residual Connections help information flow through many Transformer layers.

Without them:

- Training becomes difficult.
- Important information may be lost.
- Deep models become unstable.

With them:

- Information is preserved.
- Training becomes easier.
- Very deep Transformers become possible.

This is one reason why models like GPT and BERT can contain dozens of layers.

---

# Common Beginner Mistakes

### Mistake 1

Thinking Residual Connections replace the layer.

They don't.

The layer still performs its normal computation.

The shortcut simply adds the original input back.

---

### Mistake 2

Thinking the shortcut skips learning.

It doesn't.

The model still learns new information.

The shortcut only helps preserve the original information.

---

### Mistake 3

Thinking Residual Connections are used only in Transformers.

They are also widely used in many other deep learning architectures.

---

# Key Takeaways

- A Residual Connection is a shortcut that carries the original input forward.
- The original input is added to the output of a layer.
- This helps preserve important information.
- Residual Connections make training deep neural networks easier.
- Every Transformer block uses Residual Connections.
- Modern models like GPT, BERT, Llama, and Gemini all rely on Residual Connections.