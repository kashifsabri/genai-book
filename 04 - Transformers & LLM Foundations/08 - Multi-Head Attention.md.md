# 8. Multi-Head Attention

## Introduction

In the previous chapter, we learned how **Scaled Dot-Product Attention** works.

Each word compares itself with every other word and decides which words are important.

This works well.

But imagine reading a sentence only once.

Would you notice **every detail**?

Probably not.

You might first focus on:

- The people in the sentence.

Then read it again to understand:

- The action.

Then read it again to understand:

- The place.

Humans often look at the same sentence from different points of view.

Transformers do the same thing.

Instead of using **one attention mechanism**, they use **multiple attention mechanisms**.

This is called **Multi-Head Attention**.

---

# 8.1 Why Isn't One Attention Head Enough?

Consider this sentence.

```text
The little boy kicked the football because he was excited.
```

Suppose we want to understand the word:

```text
he
```

One attention head may focus on:

```text
boy
```

because it wants to know who **he** refers to.

Another attention head may focus on:

```text
kicked
```

because it wants to understand the action.

Another attention head may focus on:

```text
football
```

because it wants to understand the object.

Every attention head learns something different.

---

# 8.2 What is an Attention Head?

An **Attention Head** is simply **one Self-Attention calculation**.

If we use:

```text
One Head
```

the model looks at the sentence from one perspective.

If we use:

```text
Eight Heads
```

the model looks at the same sentence in eight different ways.

Each head learns different relationships between words.

---

# 8.3 Imagine a Classroom

Imagine a classroom where students are reading the same story.

Teacher asks:

> "Tell me what you noticed."

Student 1 says:

```text
I noticed the main character.
```

Student 2 says:

```text
I noticed the place.
```

Student 3 says:

```text
I noticed the action.
```

Student 4 says:

```text
I noticed the emotions.
```

Everyone read the same story.

But everyone focused on different details.

Multi-Head Attention works in exactly the same way.

---

# 8.4 Single Head vs Multi-Head

### Single Head

```text
Sentence
     │
     ▼
Attention
     │
     ▼
Output
```

The model gets only one view of the sentence.

---

### Multi-Head

```text
Sentence
      │
      ▼
 ┌──────────────┐
 │ Head 1       │
 ├──────────────┤
 │ Head 2       │
 ├──────────────┤
 │ Head 3       │
 ├──────────────┤
 │ Head 4       │
 └──────────────┘
      │
      ▼
Combine Results
      │
      ▼
Final Output
```

Now the model learns multiple relationships at the same time.

---

# 8.5 How Does Multi-Head Attention Work?

The input embeddings are copied into several attention heads.

Each head creates its own:

- Query (Q)
- Key (K)
- Value (V)

Example:

```text
Embedding
      │
      ├────────► Head 1
      │
      ├────────► Head 2
      │
      ├────────► Head 3
      │
      └────────► Head 4
```

Each head performs **Scaled Dot-Product Attention** independently.

This means every head learns something different.

---

# 8.6 Combining the Heads

After every head finishes its work, their outputs are joined together.

```text
Head 1 Output
        │
Head 2 Output
        │
Head 3 Output
        │
Head 4 Output
        │
        ▼
Concatenate
        │
        ▼
Linear Layer
        │
        ▼
Final Output
```

This final output contains information gathered from all attention heads.

---

# 8.7 Why Is This Better?

Imagine trying to understand a football match.

One camera shows only the goalkeeper.

You miss everything else.

Now imagine eight cameras.

One camera watches:

- Goalkeeper

Another watches:

- Striker

Another watches:

- Midfield

Another watches:

- Coach

Together, they provide a much better understanding of the match.

Multi-Head Attention works the same way.

Each head focuses on different information.

---

# 8.8 Python Example

PyTorch provides a built-in class for Multi-Head Attention.

Import the class.

```python
import torch
import torch.nn as nn
```

Create a Multi-Head Attention layer.

```python
attention = nn.MultiheadAttention(
    embed_dim=8,
    num_heads=2,
    batch_first=True
)
```

Create a sample input.

```python
x = torch.rand(1, 4, 8)
```

Run the attention layer.

```python
output, weights = attention(x, x, x)

print(output.shape)
```

Output

```text
torch.Size([1, 4, 8])
```

Explanation:

- **1** → Batch size
- **4** → Number of tokens
- **8** → Embedding size

Even though two attention heads were used, the final output size remains the same.

---

# 8.9 How Many Attention Heads Do Models Use?

Different models use different numbers of attention heads.

Examples:

| Model | Approximate Number of Heads |
|--------|----------------------------:|
| BERT Base | 12 |
| GPT-2 Small | 12 |
| GPT-3 | 96 |
| Llama 2 7B | 32 |

Larger models usually use more attention heads.

More heads allow the model to learn more complex relationships.

---

# 8.10 Does Every Head Learn the Same Thing?

No.

Each head learns different patterns during training.

One head may learn:

- Grammar

Another may learn:

- Sentence structure

Another may learn:

- Relationships between nouns and verbs

Another may learn:

- Long-distance dependencies

The model learns these patterns automatically.

We do not manually assign tasks to each head.

---

# 8.11 Is More Always Better?

Not always.

Using more attention heads increases:

- Memory usage
- Computation time

Model designers choose the number of heads based on the model size and the task.

The goal is to balance accuracy and efficiency.

---

# Transformer Connection

Inside every Transformer block, Multi-Head Attention is one of the first operations.

The complete flow now looks like this.

```text
Input
      │
      ▼
Embeddings
      │
      ▼
Positional Encoding
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

We've now learned how the Transformer understands relationships between words.

The next question is:

> **What happens after Multi-Head Attention?**

The answer is the **Feed Forward Network**, which we'll study in the next chapter.

---

# Common Beginner Mistakes

### Mistake 1

Thinking each attention head receives different input.

All heads receive the same input.

They learn different patterns using different learned weights.

---

### Mistake 2

Thinking more heads always mean better performance.

More heads increase computation and memory.

The number of heads is a design choice.

---

### Mistake 3

Thinking the outputs of the heads are averaged.

They are first **concatenated** (joined together) and then passed through another Linear layer.

---

# Key Takeaways

- Multi-Head Attention uses multiple Self-Attention mechanisms in parallel.
- Every head creates its own Query, Key, and Value vectors.
- Each head learns different relationships between words.
- The outputs of all heads are combined to create the final representation.
- Multi-Head Attention allows Transformers to understand language from multiple perspectives.
- It is one of the core building blocks of every modern Large Language Model.