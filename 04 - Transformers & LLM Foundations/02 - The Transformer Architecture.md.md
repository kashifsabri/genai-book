# 2. The Transformer Architecture

## Introduction

In the previous chapter, we learned **why Transformers were invented**.

Earlier NLP models like **Bag of Words**, **TF-IDF**, **Word2Vec**, **RNNs**, and **LSTMs** all had limitations.

The Transformer solved many of these problems by introducing a completely new architecture.

Before we learn concepts like **Self-Attention**, **Query**, **Key**, and **Value**, we first need to understand the overall structure of a Transformer.

Think of this chapter as learning the blueprint of a house before studying each individual room.

By the end of this chapter, you'll know:

- The main components of a Transformer
- How data flows through the model
- The difference between an Encoder and a Decoder
- Why modern models like GPT and BERT use different parts of the Transformer

---

# 2.1 What is a Transformer?

A **Transformer** is a Deep Learning architecture designed to process sequential data such as text.

Unlike RNNs, which process one word at a time, a Transformer processes **all words in a sentence simultaneously**.

This allows it to:

- Train much faster
- Understand long-range relationships
- Scale to billions of parameters
- Handle very large datasets

Today, almost every modern Large Language Model (LLM) is based on the Transformer architecture.

Examples include:

- ChatGPT
- GPT-4
- Gemini
- Claude
- Llama
- Qwen
- DeepSeek
- Mistral

---

# 2.2 The Original Transformer

The original Transformer was introduced in the 2017 research paper:

> **Attention Is All You Need**

The architecture consists of two main parts:

```text
           Transformer

      ┌───────────────┐
      │   Encoder     │
      └───────────────┘
              │
              ▼
      ┌───────────────┐
      │   Decoder     │
      └───────────────┘
```

The **Encoder** reads and understands the input.

The **Decoder** generates the output.

Together, they form the complete Transformer architecture.

---

# 2.3 High-Level Architecture

A simplified Transformer looks like this.

```text
Input Text
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
Encoder Stack
      │
      ▼
Decoder Stack
      │
      ▼
Linear Layer
      │
      ▼
Softmax
      │
      ▼
Predicted Output
```

Don't worry if some of these terms are unfamiliar.

We'll study every component in the upcoming chapters.

---

# 2.4 Understanding the Flow

Let's understand what happens step by step.

### Step 1 — Input Text

Example:

```text
I love AI.
```

The Transformer cannot understand text directly.

It first converts the sentence into smaller pieces called **tokens**.

---

### Step 2 — Tokenization

Example:

```text
I

love

AI

.
```

Each token is assigned a unique numerical ID.

Example:

```text
I      → 125

love   → 892

AI     → 431

.      → 13
```

The model now works with numbers instead of words.

---

### Step 3 — Embeddings

Token IDs themselves have no meaning.

Each token is converted into a dense vector called an **embedding**.

Example:

```text
125

↓

[0.42, -0.15, 0.81, ...]
```

These embeddings capture the semantic meaning of words.

---

### Step 4 — Positional Encoding

Transformers process all tokens at the same time.

Because of this, they don't automatically know the order of words.

For example:

```text
Dog bites man.
```

and

```text
Man bites dog.
```

contain the same words but have completely different meanings.

To solve this problem, positional information is added to every embedding.

The model now knows:

- Which word comes first
- Which word comes second
- Which word comes third

We'll study positional encoding in detail later.

---

### Step 5 — Encoder

The Encoder receives the embeddings and begins understanding the sentence.

It learns relationships between words.

For example:

```text
The cat sat on the mat.
```

The Encoder learns that:

- "cat" is related to "sat"
- "sat" is related to "mat"

It builds a richer representation of the sentence.

---

### Step 6 — Decoder

The Decoder generates the output one token at a time.

Example:

Input:

```text
Translate:

Hello
```

Output:

```text
Hola
```

The Decoder predicts one token, then the next, until the sentence is complete.

---

### Step 7 — Linear Layer

The Decoder produces a vector.

A Linear Layer converts this vector into scores for every word in the vocabulary.

Example:

```text
cat

↓

2.8

dog

↓

1.7

bird

↓

0.3
```

These scores are called **logits**.

They are not probabilities yet.

---

### Step 8 — Softmax

Softmax converts the logits into probabilities.

Example:

```text
cat

↓

0.71

dog

↓

0.23

bird

↓

0.06
```

The model selects the most likely next token.

This process repeats until the entire sentence is generated.

---

# 2.5 Encoder and Decoder Stacks

The Transformer doesn't use just one Encoder and one Decoder.

Instead, it stacks multiple layers.

```text
Encoder

Layer 6
Layer 5
Layer 4
Layer 3
Layer 2
Layer 1
```

```text
Decoder

Layer 6
Layer 5
Layer 4
Layer 3
Layer 2
Layer 1
```

Each layer gradually improves the understanding of the input.

Modern LLMs often contain dozens of such layers.

---

# 2.6 What Happens Inside Each Layer?

Each Encoder layer contains two main blocks.

```text
Encoder Layer

↓

Self-Attention

↓

Feed Forward Network
```

Each Decoder layer contains three main blocks.

```text
Decoder Layer

↓

Masked Self-Attention

↓

Cross Attention

↓

Feed Forward Network
```

We'll study each block separately in the next chapters.

---

# 2.7 Real-World Example

Imagine you're translating English into French.

Sentence:

```text
The cat is sleeping.
```

The Encoder first understands the entire sentence.

Then the Decoder generates:

```text
Le chat dort.
```

Instead of translating one word at a time, the model first understands the complete meaning before generating the translation.

---

# 2.8 Original Transformer vs Modern LLMs

The original Transformer used both an Encoder and a Decoder.

Modern models use different parts of this architecture.

| Model | Uses |
|--------|------|
| BERT | Encoder Only |
| GPT | Decoder Only |
| T5 | Encoder + Decoder |

We'll study these models later in this section.

---

# 2.9 Why Is This Architecture Powerful?

The Transformer architecture offers several advantages.

- Processes all tokens in parallel
- Learns relationships between distant words
- Scales efficiently on GPUs
- Handles long documents better than RNNs
- Forms the foundation of modern LLMs

These advantages made Transformers the dominant architecture in Natural Language Processing.

---

# What's Coming Next?

In this chapter, we learned the overall structure of a Transformer.

However, one important question remains.

> **How does the Transformer convert text into numbers that it can understand?**

The answer begins with **Tokens and Tokenization**, which we'll study in the next chapter.

---

# Key Takeaways

- A Transformer is a Deep Learning architecture designed for sequential data.
- The original Transformer consists of an Encoder and a Decoder.
- Text is converted into tokens before entering the model.
- Tokens are converted into embeddings.
- Positional encoding tells the model the order of words.
- The Encoder understands the input.
- The Decoder generates the output.
- Modern LLMs like GPT and BERT are based on different parts of the Transformer architecture.