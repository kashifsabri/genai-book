# 11. Layer Normalization

## Introduction

In the previous chapter, we learned about **Residual Connections**.

Residual Connections help preserve information as it passes through many Transformer layers.

But another problem still exists.

As data flows through the network, some values can become **very large**, while others become **very small**.

When this happens, training becomes slower and less stable.

To solve this problem, Transformers use **Layer Normalization**.

Think of Layer Normalization as a way of **keeping the data balanced** before it moves to the next layer.

---

# 11.1 What is Layer Normalization?

**Layer Normalization** is a technique that adjusts the values inside a layer so they stay within a reasonable range.

It does **not** change the meaning of the data.

Instead, it makes the values more stable.

Simple diagram:

```text
Input Values
      │
      ▼
Layer Normalization
      │
      ▼
Balanced Values
```

This helps the Transformer learn more efficiently.

---

# 11.2 Why Do We Need Layer Normalization?

Imagine a classroom where students are speaking.

One student is shouting.

Another is whispering.

Some are speaking normally.

```text
Student 1 → Very Loud

Student 2 → Very Quiet

Student 3 → Normal
```

Can the teacher understand everyone easily?

Not really.

Now imagine everyone speaks at a similar volume.

The teacher can understand the discussion much better.

Layer Normalization works in a similar way.

It keeps the values balanced so that no value becomes too large or too small.

---

# 11.3 A Simple Example

Suppose a layer produces these values.

```text
[2, 5, 100, 8]
```

Notice that:

```text
100
```

is much larger than the other values.

Large differences like this can make training difficult.

After Layer Normalization, the values become more balanced.

Example:

```text
[-0.6, -0.2, 1.7, -0.1]
```

The exact numbers are not important.

The important idea is that the values are now on a similar scale.

---

# 11.4 Does Layer Normalization Change the Meaning?

No.

This is a common misunderstanding.

Layer Normalization does **not** remove information.

It only changes the scale of the values.

Think about measuring height.

These two measurements represent the same height.

```text
180 centimeters
```

```text
1.8 meters
```

The numbers look different.

The meaning is the same.

Layer Normalization works in a similar way.

---

# 11.5 Where Is Layer Normalization Used?

Every Transformer block uses Layer Normalization.

It appears:

- After the Multi-Head Attention block.
- After the Feed Forward Network.

A simplified Transformer block looks like this.

```text
Input
   │
   ▼
Multi-Head Attention
   │
   ▼
Residual Connection
   │
   ▼
Layer Normalization
   │
   ▼
Feed Forward Network
   │
   ▼
Residual Connection
   │
   ▼
Layer Normalization
   │
   ▼
Output
```

Notice that Layer Normalization is used twice in every Transformer block.

---

# 11.6 Python Example

PyTorch provides a built-in `LayerNorm` class.

Import it.

```python
import torch
import torch.nn as nn
```

Create a Layer Normalization layer.

```python
layer_norm = nn.LayerNorm(4)
```

Create sample data.

```python
x = torch.tensor([
    [2.0, 5.0, 100.0, 8.0]
])
```

Apply Layer Normalization.

```python
output = layer_norm(x)

print(output)
```

Example Output

```text
tensor([[-0.63, -0.54,  1.73, -0.56]])
```

The exact values may be different.

The important thing is that the values are now balanced.

---

# 11.7 Understanding the Output

Before normalization:

```text
[2, 5, 100, 8]
```

The value:

```text
100
```

is much larger than the others.

After normalization:

```text
[-0.63, -0.54, 1.73, -0.56]
```

The numbers are much closer in scale.

This makes learning more stable.

---

# 11.8 Why Is Layer Normalization Better for Transformers?

Earlier neural networks often used **Batch Normalization**.

Batch Normalization works well for images.

However, Transformers process sequences of different lengths.

Layer Normalization works directly on each token's features.

Because of this, it is a much better choice for Transformer models.

You don't need to learn Batch Normalization right now.

Just remember:

- CNNs often use **Batch Normalization**.
- Transformers use **Layer Normalization**.

---

# 11.9 Real-World Example

Imagine a teacher grading exams.

One student writes with a tiny pencil.

Another writes with a thick marker.

Another writes with blue ink.

Another writes with black ink.

Before checking the answers, the teacher photocopies every paper using the same settings.

Now every paper looks consistent and is easier to evaluate.

Layer Normalization works in a similar way.

It makes the data more consistent before sending it to the next layer.

---

# Transformer Connection

Our Transformer block is almost complete.

```text
Input
   │
   ▼
Multi-Head Attention
   │
   ▼
Residual Connection
   │
   ▼
Layer Normalization
   │
   ▼
Feed Forward Network
   │
   ▼
Residual Connection
   │
   ▼
Layer Normalization
   │
   ▼
Output
```

We've now learned every major component inside a Transformer block.

In the next chapter, we'll combine everything we've learned and build the complete **Encoder Block**.

---

# Common Beginner Mistakes

### Mistake 1

Thinking Layer Normalization changes the meaning of the data.

It doesn't.

It only changes the scale of the values.

---

### Mistake 2

Thinking Layer Normalization is used only once.

A Transformer block usually uses it twice.

---

### Mistake 3

Thinking Layer Normalization and Residual Connections do the same job.

They don't.

- Residual Connections preserve information.
- Layer Normalization keeps the values stable.

Both are important.

---

# Key Takeaways

- Layer Normalization keeps values balanced during training.
- It makes training faster and more stable.
- It does not change the meaning of the data.
- Every Transformer block uses Layer Normalization.
- Transformers use Layer Normalization instead of Batch Normalization.
- Layer Normalization works together with Residual Connections to build deep Transformer models.