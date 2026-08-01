# 9. Feed Forward Network (FFN)

## Introduction

In the previous chapter, we learned about **Multi-Head Attention**.

Multi-Head Attention helps every word collect useful information from the other words in the sentence.

For example,

```text
The cat drinks milk.
```

The word:

```text
drinks
```

can learn information from:

- cat
- milk

After this step, every word has a **better understanding** of the sentence.

But the Transformer is not finished yet.

It now needs another component to **process and improve** this information.

This component is called the **Feed Forward Network (FFN)**.

Every Transformer block contains one Feed Forward Network.

Without it, the Transformer would not be able to learn complex patterns.

---

# 9.1 What is a Feed Forward Network?

A **Feed Forward Network (FFN)** is a small neural network inside every Transformer layer.

Its job is simple.

It takes the output from the attention layer and processes it further.

Think of it as a **refinement step**.

```text
Attention Output
        │
        ▼
Feed Forward Network
        │
        ▼
Better Representation
```

---

# 9.2 Why Do We Need It?

Imagine you're writing an exam.

First, you collect information from different books.

```text
Book 1

Book 2

Book 3
```

Now you have lots of information.

But before writing your answer, you organize everything in your mind.

You remove unnecessary details.

You connect related ideas.

You prepare a better answer.

The Feed Forward Network does the same thing.

After attention gathers information, the FFN processes it and creates a better representation.

---

# 9.3 Where Does the FFN Fit?

Inside every Transformer block, the order is:

```text
Input
   │
   ▼
Multi-Head Attention
   │
   ▼
Feed Forward Network
   │
   ▼
Output
```

So, attention comes first.

The Feed Forward Network comes next.

---

# 9.4 Does Every Word Go Through the FFN?

Yes.

This is an important point.

Suppose we have this sentence.

```text
The

cat

drinks

milk
```

Each word has its own vector after attention.

```text
The
   │
   ▼
FFN

Cat
   │
   ▼
FFN

Drinks
   │
   ▼
FFN

Milk
   │
   ▼
FFN
```

The **same FFN** is applied to every word independently.

The words do not interact with each other inside the FFN.

The interaction already happened in the attention layer.

---

# 9.5 What Happens Inside the FFN?

A Feed Forward Network usually contains:

- One Linear layer
- An activation function (usually GELU or ReLU)
- Another Linear layer

Simple diagram:

```text
Input
   │
   ▼
Linear Layer
   │
   ▼
Activation Function
   │
   ▼
Linear Layer
   │
   ▼
Output
```

This allows the model to learn more complex patterns.

---

# 9.6 Why Two Linear Layers?

A common question is:

> Why not use only one Linear layer?

Using two Linear layers with an activation function in between allows the model to learn more complex relationships.

Think of it like this.

One Linear layer is like drawing only straight lines.

Adding an activation function lets the model learn curves and more complicated patterns.

This makes the Transformer much more powerful.

---

# 9.7 Python Example

PyTorch provides the `Linear` layer in the `torch.nn` module.

```python
import torch
import torch.nn as nn

ffn = nn.Sequential(
    nn.Linear(8, 32),
    nn.GELU(),
    nn.Linear(32, 8)
)

x = torch.rand(1, 4, 8)

output = ffn(x)

print(output.shape)
```

Output

```text
torch.Size([1, 4, 8])
```

### Understanding the Output

- **1** → Batch size
- **4** → Number of tokens
- **8** → Embedding size

Notice that the output shape is the same as the input shape.

The values inside the vectors have changed, but the overall size remains the same.

---

# 9.8 Why Does the Hidden Size Become Larger?

You may have noticed this line:

```python
nn.Linear(8, 32)
```

Why did we increase the size from **8** to **32**?

This gives the model more space to learn useful patterns.

After processing the information, the second Linear layer reduces it back to the original size.

In real Transformer models, this hidden size is often **4 times larger** than the embedding size.

Example:

```text
Embedding Size

768
```

```text
Hidden Size

3072
```

The exact numbers depend on the model.

---

# 9.9 Real-World Example

Imagine a chef preparing a meal.

First, the chef collects ingredients.

```text
Vegetables

Rice

Spices

Oil
```

This is similar to **Multi-Head Attention**, which gathers information.

Next, the chef cooks everything together.

```text
Ingredients

↓

Cooking

↓

Delicious Meal
```

This is similar to the **Feed Forward Network**.

It processes the collected information and produces a better result.

---

# 9.10 Is the FFN Shared?

Yes.

Every token uses the **same Feed Forward Network**.

For example:

```text
The

↓

Same FFN
```

```text
Cat

↓

Same FFN
```

```text
Milk

↓

Same FFN
```

The weights are shared across all tokens.

This makes the model efficient.

---

# Transformer Connection

A Transformer block now looks like this.

```text
Input
      │
      ▼
Multi-Head Attention
      │
      ▼
Feed Forward Network
      │
      ▼
Output
```

We've now learned the two biggest parts of a Transformer block.

However, another question remains.

> **How does the Transformer avoid losing information as it passes through many layers?**

The answer is **Residual Connections**, which we'll study in the next chapter.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the FFN compares different words.

It doesn't.

Attention compares words.

The FFN processes each word separately.

---

### Mistake 2

Thinking every token has its own FFN.

All tokens use the same Feed Forward Network.

---

### Mistake 3

Thinking the FFN replaces attention.

It doesn't.

Attention gathers information.

The FFN processes that information.

Both are necessary.

---

# Key Takeaways

- A Feed Forward Network (FFN) is a small neural network inside every Transformer block.
- It comes after the Multi-Head Attention layer.
- It processes each token independently.
- A typical FFN contains two Linear layers with an activation function in between.
- Most modern Transformers use **GELU** as the activation function.
- The FFN helps the model learn more complex patterns.
- Every token shares the same FFN.