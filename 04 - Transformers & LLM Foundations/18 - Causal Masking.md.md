# 18. Causal Masking

## Introduction

In the previous chapter, we learned that GPT generates text **one token at a time**.

For example,

```text
I love
```

GPT predicts:

```text
AI
```

Now the sentence becomes:

```text
I love AI
```

Then GPT predicts the next token.

```text
I love AI because
```

Then another.

This process continues until the answer is complete.

But an important question arises.

> **During training, GPT already has the complete sentence. So why doesn't it simply look at the future words and cheat?**

The answer is **Causal Masking**.

---

# 18.1 What is Causal Masking?

**Causal Masking** is a rule that prevents GPT from looking at future tokens.

In simple words,

> **A token can only see itself and the tokens before it.**

It cannot see words that come later in the sentence.

---

# 18.2 Why Do We Need It?

Imagine you're taking an exam.

The answer sheet is already on your desk.

If you're allowed to look at the answers before solving the questions,

would the exam be fair?

Of course not.

You would simply copy the answers.

GPT would do exactly the same thing.

If it could see future words,

it wouldn't actually learn how to predict them.

It would simply copy them.

Causal Masking prevents this.

---

# 18.3 Example

Suppose the sentence is:

```text
I love Artificial Intelligence.
```

While predicting:

```text
love
```

GPT is allowed to see:

```text
I
```

It is **not allowed** to see:

```text
Artificial

Intelligence

.
```

When predicting:

```text
Artificial
```

GPT can now see:

```text
I

love
```

But it still cannot see:

```text
Intelligence

.
```

Each token only sees the past.

---

# 18.4 Visual Example

Suppose we have four tokens.

```text
I

love

AI

today
```

The first token can see:

```text
I
```

The second token can see:

```text
I

love
```

The third token can see:

```text
I

love

AI
```

The fourth token can see:

```text
I

love

AI

today
```

Notice something important.

No token can look ahead.

---

# 18.5 The Mask

Inside the Transformer, a mask is created.

Imagine this table.

```text
          I   Love   AI   Today

I         ✓    ✗     ✗      ✗

Love      ✓    ✓     ✗      ✗

AI        ✓    ✓     ✓      ✗

Today     ✓    ✓     ✓      ✓
```

Meaning:

✓ = Can see

✗ = Cannot see

This triangular pattern is called the **Causal Mask**.

---

# 18.6 Why Is It Called "Causal"?

The word **causal** comes from **cause and effect**.

The future should not affect the past.

When predicting the next token,

only the previous tokens should influence the prediction.

That is why it is called **Causal Masking**.

---

# 18.7 During Training

Suppose GPT sees this sentence.

```text
The cat sat on the mat.
```

While predicting:

```text
sat
```

The model can only use:

```text
The

cat
```

It cannot look at:

```text
on

the

mat
```

Even though the full sentence is available,

the mask hides the future words.

---

# 18.8 During Inference

Inference means using the trained model to generate text.

Suppose the user types:

```text
The cat
```

GPT has never generated the remaining words yet.

So there are no future words to look at.

It simply predicts:

```text
sat
```

Then:

```text
on
```

Then:

```text
the
```

Then:

```text
mat
```

This is exactly how ChatGPT generates responses.

---

# 18.9 Python Example

PyTorch provides a function to create a causal mask.

```python
import torch
import torch.nn as nn

mask = nn.Transformer.generate_square_subsequent_mask(4)

print(mask)
```

Example Output

```text
tensor([[0., -inf, -inf, -inf],
        [0.,   0., -inf, -inf],
        [0.,   0.,   0., -inf],
        [0.,   0.,   0.,   0.]])
```

### Understanding the Output

The value:

```text
0
```

means the token is visible.

The value:

```text
-inf
```

means the token is hidden.

The upper-right part of the matrix is blocked.

This prevents the model from seeing future tokens.

---

# 18.10 Encoder vs Decoder Attention

The Encoder uses **Self-Attention**.

Every token can see every other token.

```text
✓ ✓ ✓ ✓

✓ ✓ ✓ ✓

✓ ✓ ✓ ✓

✓ ✓ ✓ ✓
```

The Decoder uses **Masked Self-Attention**.

Future tokens are hidden.

```text
✓ ✗ ✗ ✗

✓ ✓ ✗ ✗

✓ ✓ ✓ ✗

✓ ✓ ✓ ✓
```

This is the biggest difference.

---

# 18.11 Real-World Example

Imagine watching a movie for the first time.

You don't know the ending.

You understand the story one scene at a time.

Now imagine someone tells you the ending before you start watching.

The experience changes completely.

GPT learns the same way.

It is not allowed to know the ending while predicting the next token.

---

# Transformer Connection

The GPT Decoder now looks like this.

```text
Input
   │
   ▼
Masked Multi-Head Attention
   │
   ▼
Feed Forward Network
   │
   ▼
Next Token
```

The mask ensures that GPT generates text naturally,

one token at a time.

---

# Common Beginner Mistakes

### Mistake 1

Thinking GPT can see future words.

It cannot.

Future tokens are hidden using Causal Masking.

---

### Mistake 2

Thinking the Encoder also uses Causal Masking.

It doesn't.

The Encoder can see the entire input sentence.

---

### Mistake 3

Thinking Causal Masking is used only during inference.

It is mainly important during **training** to prevent the model from cheating.

---

# Key Takeaways

- Causal Masking prevents GPT from seeing future tokens.
- Every token can see only itself and the previous tokens.
- This forces GPT to learn Next Token Prediction correctly.
- The Encoder does not use Causal Masking.
- The Decoder uses Masked Self-Attention.
- Causal Masking is one of the key ideas behind GPT and ChatGPT.