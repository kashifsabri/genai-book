# 12. Encoder Block

## Introduction

So far, we have learned all the major parts of a Transformer.

We learned:

- Embeddings
- Positional Encoding
- Multi-Head Attention
- Feed Forward Network (FFN)
- Residual Connections
- Layer Normalization

Until now, we studied each part separately.

Now it's time to put everything together.

When these components are connected in the correct order, they form an **Encoder Block**.

The Encoder Block is one of the main building blocks of the original Transformer architecture.

Every Encoder in the Transformer is made by stacking multiple Encoder Blocks.

---

# 12.1 What is an Encoder Block?

An **Encoder Block** is a group of layers that work together to understand the input sentence.

Think of it as one worker in a factory.

The worker receives some information.

Processes it.

Improves it.

Then passes it to the next worker.

The next worker repeats the same process.

---

# 12.2 Where Does the Encoder Block Fit?

A Transformer does not have only one Encoder Block.

It has several Encoder Blocks stacked on top of each other.

```text
Input
   │
   ▼
Encoder Block 1
   │
   ▼
Encoder Block 2
   │
   ▼
Encoder Block 3
   │
   ▼
Encoder Block 4
   │
   ▼
Final Encoder Output
```

Each block improves the representation created by the previous block.

---

# 12.3 Inside an Encoder Block

An Encoder Block contains two main parts.

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

Let's understand each step.

---

# 12.4 Step 1 – Multi-Head Attention

The input first enters the Multi-Head Attention layer.

Here, every word looks at every other word.

Example:

```text
The cat drinks milk.
```

While understanding:

```text
drinks
```

the model can pay attention to:

```text
cat

milk
```

Instead of looking at only one relationship, multiple attention heads learn different relationships at the same time.

The result is a richer understanding of the sentence.

---

# 12.5 Step 2 – Residual Connection

After attention finishes, the original input is added back.

```text
Attention Output
        │
        ▼
      Add
      ▲
      │
Original Input
```

This helps preserve important information from the earlier layers.

Without this shortcut, some information could gradually disappear as the model becomes deeper.

---

# 12.6 Step 3 – Layer Normalization

The output is then passed through Layer Normalization.

```text
Values
   │
   ▼
Layer Normalization
   │
   ▼
Balanced Values
```

This keeps the values stable.

Stable values make training faster and more reliable.

---

# 12.7 Step 4 – Feed Forward Network

Next, every token is processed independently by the Feed Forward Network.

```text
Token
   │
   ▼
Linear Layer
   │
   ▼
GELU
   │
   ▼
Linear Layer
```

Remember:

The FFN does **not** compare different words.

That job was already completed by the attention layer.

The FFN simply improves each token's representation.

---

# 12.8 Step 5 – Another Residual Connection

After the Feed Forward Network, another shortcut is added.

```text
FFN Output
     │
     ▼
    Add
    ▲
    │
Previous Input
```

Again, this preserves important information.

---

# 12.9 Step 6 – Another Layer Normalization

Finally, another Layer Normalization is applied.

```text
Values
   │
   ▼
Layer Normalization
   │
   ▼
Encoder Output
```

The Encoder Block is now complete.

The output is sent to the next Encoder Block.

---

# 12.10 Complete Encoder Block

Putting everything together:

```text
                 Input
                   │
                   ▼
        Multi-Head Attention
                   │
                   ▼
          Add (Residual)
                   │
                   ▼
         Layer Normalization
                   │
                   ▼
      Feed Forward Network
                   │
                   ▼
          Add (Residual)
                   │
                   ▼
         Layer Normalization
                   │
                   ▼
                 Output
```

This is the complete Encoder Block used in the original Transformer.

---

# 12.11 Why Do We Stack Encoder Blocks?

One Encoder Block can understand the sentence.

But multiple Encoder Blocks understand it much better.

Think of reading a difficult paragraph.

The first time you read it, you understand the basic idea.

The second time, you notice more details.

The third time, you understand it even better.

Stacking Encoder Blocks works in a similar way.

Each block improves the understanding produced by the previous block.

---

# 12.12 Python Example

PyTorch provides a built-in Encoder Layer.

Import the required modules.

```python
import torch
import torch.nn as nn
```

Create an Encoder Layer.

```python
encoder = nn.TransformerEncoderLayer(
    d_model=8,
    nhead=2,
    batch_first=True
)
```

Create sample input.

```python
x = torch.rand(1, 4, 8)
```

Pass the input through the Encoder Layer.

```python
output = encoder(x)

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

The Encoder processes every token and returns improved representations.

---

# 12.13 Real-World Example

Imagine a team reviewing a document.

The first reviewer fixes grammar.

The second reviewer improves clarity.

The third reviewer corrects technical mistakes.

Each reviewer makes the document better.

Encoder Blocks work in the same way.

Each block improves the representation produced by the previous block.

---

# 12.14 Where Is the Encoder Used?

The Encoder is used in many Transformer models.

Examples:

- BERT
- T5
- BART

These models use the Encoder to understand the input text before performing tasks such as classification, question answering, or translation.

GPT models are different.

They do **not** use the Encoder.

We'll learn why in a later chapter.

---

# Transformer Connection

The complete Encoder now looks like this.

```text
Input Sentence
        │
        ▼
Tokenization
        │
        ▼
Embeddings
        │
        ▼
Positional Encoding
        │
        ▼
Encoder Block 1
        │
        ▼
Encoder Block 2
        │
        ▼
Encoder Block 3
        │
        ▼
Encoder Output
```

The Encoder's job is simple:

> **Read the input sentence and create a rich representation of its meaning.**

---

# Common Beginner Mistakes

### Mistake 1

Thinking an Encoder has only one block.

It doesn't.

Most Transformers stack many Encoder Blocks.

---

### Mistake 2

Thinking the Feed Forward Network compares words.

It doesn't.

Only the attention layer compares words.

---

### Mistake 3

Thinking every Encoder Block is different.

They all have the same structure.

Each block has its own learned weights, but the architecture is the same.

---

# Key Takeaways

- An Encoder Block is made of several layers working together.
- It contains Multi-Head Attention, Feed Forward Network, Residual Connections, and Layer Normalization.
- Multi-Head Attention allows words to interact with each other.
- The Feed Forward Network improves each token independently.
- Residual Connections preserve important information.
- Layer Normalization keeps training stable.
- Multiple Encoder Blocks are stacked to build a complete Encoder.