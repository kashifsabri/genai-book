# 20. Mini Project – Build a Transformer Block in PyTorch

## Introduction

Congratulations!

You have now learned all the important parts of a Transformer.

You know about:

- Embeddings
- Positional Encoding
- Multi-Head Attention
- Feed Forward Network (FFN)
- Residual Connections
- Layer Normalization

In this chapter, we'll put everything together and build a simple Transformer Block using PyTorch.

Don't worry if the code looks long.

You've already learned every piece separately.

Now we're simply combining them.

---

# 20.1 Project Goal

Our Transformer Block will contain:

```text
Input
   │
   ▼
Embedding
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

This is the same high-level structure used inside modern Transformer models.

---

# 20.2 Import the Required Libraries

```python
import torch
import torch.nn as nn
```

---

# 20.3 Create the Transformer Block

```python
class TransformerBlock(nn.Module):

    def __init__(self, embed_dim, num_heads, hidden_dim):
        super().__init__()

        self.attention = nn.MultiheadAttention(
            embed_dim=embed_dim,
            num_heads=num_heads,
            batch_first=True
        )

        self.norm1 = nn.LayerNorm(embed_dim)

        self.ffn = nn.Sequential(
            nn.Linear(embed_dim, hidden_dim),
            nn.GELU(),
            nn.Linear(hidden_dim, embed_dim)
        )

        self.norm2 = nn.LayerNorm(embed_dim)

    def forward(self, x):

        attention_output, _ = self.attention(x, x, x)

        x = self.norm1(x + attention_output)

        ffn_output = self.ffn(x)

        x = self.norm2(x + ffn_output)

        return x
```

---

# 20.4 Create a Sample Input

```python
x = torch.rand(2, 5, 16)
```

What do these numbers mean?

- **2** → Batch size
- **5** → Number of tokens
- **16** → Embedding size

---

# 20.5 Create the Model

```python
model = TransformerBlock(
    embed_dim=16,
    num_heads=4,
    hidden_dim=64
)
```

Here,

- Embedding Size = **16**
- Number of Attention Heads = **4**
- Hidden Size = **64**

---

# 20.6 Run the Model

```python
output = model(x)

print(output.shape)
```

Example Output

```text
torch.Size([2, 5, 16])
```

---

# 20.7 Understanding the Output

The output shape is the same as the input shape.

```text
Input

torch.Size([2,5,16])
```

↓

```text
Transformer Block
```

↓

```text
Output

torch.Size([2,5,16])
```

The **shape** stays the same.

What changes are the **values inside the tensor**.

Those new values contain richer information learned through attention and the Feed Forward Network.

---

# 20.8 Let's Understand the Flow

Suppose the input is:

```text
I love AI
```

Step 1

The words become embeddings.

```text
I

↓

Vector
```

```text
love

↓

Vector
```

```text
AI

↓

Vector
```

---

Step 2

Multi-Head Attention allows every word to look at every other word.

```text
I

↔

love

↔

AI
```

Each word gathers useful information.

---

Step 3

Residual Connection adds the original information back.

```text
Original Input

+

Attention Output
```

---

Step 4

Layer Normalization keeps the values balanced.

---

Step 5

The Feed Forward Network improves each token independently.

---

Step 6

Another Residual Connection preserves important information.

---

Step 7

Another Layer Normalization produces the final output.

The Transformer Block is now complete.

---

# 20.9 What Did We Build?

We built one Transformer Block containing:

```text
✓ Multi-Head Attention

✓ Residual Connection

✓ Layer Normalization

✓ Feed Forward Network

✓ Residual Connection

✓ Layer Normalization
```

This is the same pattern repeated many times inside real Transformer models.

---

# 20.10 How Real Models Become Large

A single Transformer Block is useful.

Modern LLMs stack many Transformer Blocks together.

Example:

```text
Input
   │
   ▼
Transformer Block 1
   │
   ▼
Transformer Block 2
   │
   ▼
Transformer Block 3
   │
   ▼
Transformer Block 4
   │
   ▼
...
   │
   ▼
Output
```

Different models use different numbers of blocks.

| Model | Approximate Number of Transformer Blocks |
|--------|------------------------------------------:|
| BERT Base | 12 |
| GPT-2 Small | 12 |
| GPT-3 | 96 |
| Llama 2 7B | 32 |

Larger models usually contain more Transformer Blocks.

---

# 20.11 Real-World Example

Imagine building a car.

First, you learn about:

- Engine
- Wheels
- Steering
- Brakes

Then you assemble all the parts to build the complete car.

That's exactly what we've done.

We first learned every Transformer component separately.

Now we've assembled them into one working Transformer Block.

---

# What's Next?

You now understand the foundation of every modern Large Language Model.

The next part of the book moves from **how Transformers work** to **how we use pretrained models in practice**.

We'll start with the Hugging Face ecosystem, which is the standard library for working with Transformer models.

---

# Key Takeaways

- A Transformer Block combines all the core components of a Transformer.
- It contains Multi-Head Attention, Feed Forward Network, Residual Connections, and Layer Normalization.
- The input and output shapes remain the same.
- Modern LLMs are built by stacking many Transformer Blocks.
- Understanding one Transformer Block makes it much easier to understand models like BERT, GPT, Llama, and T5.