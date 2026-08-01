# 13. Decoder Block

## Introduction

In the previous chapter, we learned about the **Encoder Block**.

The Encoder reads the input sentence and builds a rich understanding of its meaning.

But understanding the sentence is only half the job.

If we are translating a sentence, writing a summary, or generating text, we also need a component that **creates the output**.

This component is called the **Decoder**.

Think of it like this.

```text
Encoder

↓

Understands the input
```

```text
Decoder

↓

Generates the output
```

The Decoder is responsible for producing the final text, one token at a time.

---

# 13.1 What is a Decoder Block?

A **Decoder Block** is a group of layers that generates the output sequence.

For example,

Input:

```text
English

Hello
```

Output:

```text
Spanish

Hola
```

The Encoder understands **Hello**.

The Decoder generates **Hola**.

---

# 13.2 How is the Decoder Different?

The Encoder only reads the input.

The Decoder must do two jobs.

First,

it understands the words it has already generated.

Second,

it looks at the Encoder's output to understand the input sentence.

Because of these two jobs, the Decoder is slightly more complex than the Encoder.

---

# 13.3 Inside a Decoder Block

A Decoder Block contains three main parts.

```text
Input
   │
   ▼
Masked Multi-Head Attention
   │
   ▼
Residual Connection
   │
   ▼
Layer Normalization
   │
   ▼
Cross Attention
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

Notice that the Decoder has **one extra attention layer** compared to the Encoder.

---

# 13.4 Step 1 – Masked Multi-Head Attention

The Decoder first looks at the words it has already generated.

Suppose the model is writing this sentence.

```text
I love AI
```

When predicting:

```text
love
```

the model is allowed to see:

```text
I
```

But it is **not allowed** to see:

```text
AI
```

because that word has not been generated yet.

This rule is called **Masking**.

We'll study masking in detail in a later chapter.

For now, remember:

> The Decoder cannot look into the future.

---

# 13.5 Step 2 – Cross Attention

After looking at the words it has already generated, the Decoder looks at the Encoder's output.

This layer is called **Cross Attention**.

Example:

Input:

```text
The cat is sleeping.
```

The Encoder has already understood this sentence.

Now the Decoder uses that information while generating the translated sentence.

Simple diagram:

```text
Encoder Output
        │
        ▼
Cross Attention
        ▲
        │
Decoder
```

Cross Attention connects the Decoder with the Encoder.

---

# 13.6 Step 3 – Feed Forward Network

Just like the Encoder, the Decoder also contains a Feed Forward Network.

Its job is to improve the representation of every token.

```text
Input
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

Each token is processed independently.

---

# 13.7 Why Does the Decoder Need More Layers?

The Decoder has more work to do.

It must:

- Remember the words it has already generated.
- Understand the original input sentence.
- Predict the next word.

Because of these extra responsibilities, the Decoder contains one additional attention layer.

---

# 13.8 Encoder Block vs Decoder Block

Let's compare them.

### Encoder Block

```text
Multi-Head Attention
        │
        ▼
Feed Forward Network
```

### Decoder Block

```text
Masked Multi-Head Attention
            │
            ▼
Cross Attention
            │
            ▼
Feed Forward Network
```

The Decoder contains one extra step:

```text
Cross Attention
```

---

# 13.9 Python Example

PyTorch provides a built-in Decoder Layer.

Import the required modules.

```python
import torch
import torch.nn as nn
```

Create a Decoder Layer.

```python
decoder = nn.TransformerDecoderLayer(
    d_model=8,
    nhead=2,
    batch_first=True
)
```

Create sample data.

```python
target = torch.rand(1, 4, 8)

memory = torch.rand(1, 4, 8)
```

Run the Decoder.

```python
output = decoder(target, memory)

print(output.shape)
```

Output

```text
torch.Size([1, 4, 8])
```

### Understanding the Output

- **target** represents the tokens generated so far.
- **memory** is the output from the Encoder.
- The Decoder combines both to produce the next representation.

---

# 13.10 Real-World Example

Imagine a translator.

The translator first reads the entire English sentence.

```text
Encoder

↓

Understands English
```

Then the translator begins speaking in Spanish.

While speaking,

the translator:

- Remembers what has already been spoken.
- Looks back at the original English sentence whenever needed.

This is exactly how the Decoder works.

---

# 13.11 Where Is the Decoder Used?

The Decoder is used in models that generate text.

Examples include:

- GPT
- T5
- BART

These models use the Decoder to produce one token after another.

Some models, like GPT, use only the Decoder.

We'll learn why in later chapters.

---

# Transformer Connection

The complete Transformer now looks like this.

```text
Input
   │
   ▼
Encoder
   │
   ▼
Encoder Output
   │
   ▼
Decoder
   │
   ▼
Next Token
```

The Encoder understands the input.

The Decoder generates the output.

Together, they form the original Transformer architecture.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the Decoder works alone.

In the original Transformer, the Decoder receives information from the Encoder through Cross Attention.

---

### Mistake 2

Thinking the Decoder can see future words.

It cannot.

Masking prevents the Decoder from looking ahead.

---

### Mistake 3

Thinking the Encoder and Decoder are identical.

They are similar, but the Decoder has an extra Cross Attention layer.

---

# Key Takeaways

- The Decoder generates the output sequence.
- It contains Masked Multi-Head Attention, Cross Attention, and a Feed Forward Network.
- Cross Attention allows the Decoder to use information from the Encoder.
- Masking prevents the Decoder from seeing future words.
- The Decoder is used in text generation tasks such as translation and text generation.
- Together, the Encoder and Decoder form the original Transformer architecture.