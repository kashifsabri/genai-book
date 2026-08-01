# 6. Softmax

## Introduction

In the previous chapter, we learned that a Large Language Model predicts a **probability distribution** for the next word.

However, the Transformer doesn't directly produce probabilities.

Instead, it produces **raw scores** called **logits** (the unprocessed output values from the model's final layer, before they are converted into probabilities).

For example:

```text
Word      Logit
----------------
cat         4.2
dog         3.9
bird        1.0
fish        0.5
```

These numbers are simply scores.

They are **not probabilities** because:

- They can be negative.
- They don't lie between 0 and 1.
- They don't add up to 1.

To convert these scores into valid probabilities, we use the **Softmax** function.

---

## 6.1 What is Softmax?

**Softmax** converts a list of raw scores (logits) into a **probability distribution**.

After applying Softmax:

- Every value lies between **0** and **1**.
- All probabilities add up to **1**.
- Larger logits receive higher probabilities.

For example,

Before Softmax:

```text
[4.2, 3.9, 1.0, 0.5]
```

After Softmax:

```text
[0.55, 0.41, 0.02, 0.01]
```

Now the values represent valid probabilities.

---

## 6.2 Why Can't We Use Logits Directly?

Suppose a model predicts:

```text
Word      Logit
----------------
cat         9.5
dog         8.2
bird        5.1
fish       -2.4
```

What does a score of **9.5** mean?

Is it:

- 9.5%?
- 95%?
- 950%?

There is no meaningful interpretation.

Logits only tell us which words score higher than others.

They do **not** tell us how likely each word is.

Softmax converts these scores into probabilities we can understand.

---

## 6.3 How Does Softmax Work?

Softmax performs two steps.

### Step 1 — Exponentiation

Each logit is converted using the exponential function.

```text
exp(logit)
```

This ensures every value becomes positive.

For example:

```text
Logits

[2, 1, 0]
```

becomes approximately:

```text
[7.39, 2.72, 1.00]
```

Notice that every value is now positive.

---

### Step 2 — Normalization

Next, divide every value by the total sum.

```text
Probability =
Value / Sum of All Values
```

Suppose we have:

```text
[7.39, 2.72, 1.00]
```

Total:

```text
11.11
```

The probabilities become:

```text
7.39 / 11.11 = 0.665

2.72 / 11.11 = 0.245

1.00 / 11.11 = 0.090
```

Final output:

```text
[0.665, 0.245, 0.090]
```

These values now form a valid probability distribution.

---

## 6.4 The Softmax Formula

For a vector of logits:

```text
[x₁, x₂, ..., xₙ]
```

Softmax is defined as:

$$ P(x_i) = \frac{e^{x_i}}{\sum_{j=1}^{n} e^{x_j}} $$

The numerator computes the exponential of the current logit.

The denominator is the sum of the exponentials of **all** logits.

This guarantees that every probability is positive and that the total always equals **1**.

---

## 6.4.1 Softmax with Temperature

In the previous chapter, we mentioned that **temperature** controls how random or focused a model's output feels. Temperature works by scaling the logits _before_ they go into Softmax:

$$ P(x_i) = \frac{e^{x_i / T}}{\sum_{j=1}^{n} e^{x_j / T}} $$

Here, **T** is the temperature.

- **T = 1** → standard Softmax, no change.
- **T < 1** (e.g. 0.5) → divides logits by a smaller number, which _widens the gaps_ between them before exponentiation. This makes the distribution sharper and more confident.
- **T > 1** (e.g. 1.5) → divides logits by a larger number, which _narrows the gaps_ between them. This flattens the distribution and makes less likely words more competitive.

This is why lowering temperature makes a model's output more predictable, and raising it makes output more varied — the underlying logits from the Transformer don't change, only how Softmax converts them into probabilities.

---

## 6.5 Python Example

NumPy makes Softmax easy to implement.

```python
import numpy as np

logits = np.array([4.2, 3.9, 1.0, 0.5])

exp_values = np.exp(logits)

probabilities = exp_values / np.sum(exp_values)

print(probabilities)
```

Output (approximately):

```text
[0.554 0.410 0.023 0.014]
```

Notice:

- Every value is between 0 and 1.
- The probabilities add up to 1.

---

## 6.6 Writing Softmax from Scratch

Instead of relying on libraries, we can write our own Softmax function.

```python
import numpy as np

def softmax(logits):
    exp_values = np.exp(logits)
    return exp_values / np.sum(exp_values)

logits = np.array([4.2, 3.9, 1.0, 0.5])

print(softmax(logits))
```

This is the same idea used inside neural networks.

> **Note:** In production libraries such as PyTorch and TensorFlow, Softmax is implemented in a numerically stable way by subtracting the maximum logit before applying `exp`. We'll keep the simple version here to understand the concept.

---

## 6.7 Real-World Example

Suppose a user types:

```text
The cat sat on the ______
```

The Transformer produces the following logits:

```text
Word      Logit
----------------
mat         8.5
chair       6.8
floor       5.7
roof        3.2
```

After Softmax:

```text
Word      Probability
----------------------
mat         0.801
chair       0.146
floor       0.049
roof        0.004
```

Notice how large the gap becomes once these logits pass through Softmax — a logit difference of just 1.7 between **mat** and **chair** turns into a probability more than five times larger. This is a natural effect of the exponential function: it amplifies differences between larger logits far more than it amplifies differences between smaller ones.

The model will most likely choose **mat**, but it can still choose another word depending on the sampling strategy.

---

## 6.8 Why Every LLM Uses Softmax

Every modern Large Language Model follows the same process.

```text
Input Text
      │
      ▼
Transformer
      │
      ▼
Logits
      │
      ▼
Softmax
      │
      ▼
Probability Distribution
      │
      ▼
Sampling
      │
      ▼
Next Token
```

ChatGPT, Claude, Gemini, Llama, DeepSeek, and Mistral all use this idea.

The architecture may differ, but converting logits into probabilities is a fundamental step.

---

## 6.9 Transformer Connection

Inside a Transformer:

- The model predicts a logit for every token in its vocabulary.
- Softmax converts these logits into probabilities.
- A decoding strategy (such as greedy decoding, top-k sampling, or nucleus sampling) selects the next token.

Without Softmax, the model would have no meaningful probability distribution to sample from.

---

## 6.10 Key Takeaways

- Transformers produce **logits**, not probabilities.
- Logits are raw scores with no fixed range.
- Softmax converts logits into a probability distribution.
- Every probability lies between **0** and **1**.
- The probabilities always add up to **1**.
- Temperature scales logits before Softmax — lower values sharpen the distribution, higher values flatten it.
- The exponential function amplifies large logit differences more than small ones, which is why Softmax outputs can be more "confident" than the raw logit gap suggests.
- Every modern LLM uses Softmax before selecting the next token.

In the next chapter, we'll learn **Loss Functions** and understand how the model measures whether its predictions are correct or wrong during training.