# 5. Probability & Probability Distributions

## Introduction

So far, we've learned how vectors represent information and how cosine similarity measures the similarity between vectors.

The next question is:

> **How does an AI model decide what to predict next?**

The answer lies in **probability**.

Every time an LLM generates a word, it doesn't simply choose one word. Instead, it predicts the probability of **every possible next word** and then selects one based on those probabilities.

Understanding probability is the foundation for learning **Softmax**, **Sampling**, and ultimately how LLMs generate text.

---

## 5.1 What is Probability?

**Probability** measures how likely an event is to happen.

A probability is always between **0** and **1**.

- **0** → Impossible
- **1** → Certain
- **0.5** → Equally likely to happen or not happen

For example, when flipping a fair coin:

```text
P(Heads) = 0.5
P(Tails) = 0.5
```

Since one of these outcomes must occur:

```text
0.5 + 0.5 = 1
```

Probability tells us **how likely** an event is—not whether it will definitely happen.

---

## 5.2 What is a Probability Distribution?

A **probability distribution** is simply a collection of probabilities for all possible outcomes.

The most important rule is:

> **The probabilities of all possible outcomes must add up to 1.**

Example:

```text
Word      Probability
---------------------
cat          0.50
dog          0.30
bird         0.15
fish         0.05
---------------------
Total         1.00
```

This table represents a probability distribution.

It tells us how likely each word is to be selected.

---

## 5.3 Why Must the Total Be 1?

Imagine you're predicting the next word in a sentence.

Only **one** word can be chosen.

Therefore, the probabilities of **all possible words together** must equal **100%**, or **1.0**.

If they don't, the numbers cannot represent valid probabilities.

For example:

```text
Apple      0.40
Banana     0.35
Orange     0.25
----------------
Total      1.00
```

This is a valid probability distribution.

However:

```text
Apple      0.40
Banana     0.35
Orange     0.50
----------------
Total      1.25 ❌
```

This is **not** a valid probability distribution because the total exceeds 1.

---

## 5.4 Sampling from a Probability Distribution

Many beginners think an LLM always chooses the word with the highest probability.

That isn't always true.

Instead, the model usually **samples** from the probability distribution.

Suppose the model predicts:

```text
Word      Probability
---------------------
cat          0.70
dog          0.20
bird         0.10
```

Most of the time, the model will choose **cat** because it has the highest probability.

However, it may occasionally choose **dog** or **bird**.

This randomness helps LLMs generate more natural and varied responses instead of repeating the same sentence every time.

---

## 5.5 Python Example

Python can randomly choose an item using a probability distribution.

```python
import numpy as np

words = ["cat", "dog", "bird"]
probabilities = [0.7, 0.2, 0.1]

choice = np.random.choice(words, p=probabilities)

print(choice)
```

Possible outputs:

```text
cat
```

or

```text
dog
```

or

```text
bird
```

If you run the program many times, **cat** will appear most often because it has the highest probability.

---

## 5.6 Probability in Large Language Models

Suppose the input sentence is:

```text
The cat sat on the ______
```

The model predicts probabilities for many possible next words.

Example:

```text
Word      Probability
---------------------
mat         0.62
chair       0.18
floor       0.11
roof        0.06
table       0.03
```

The probabilities add up to:

```text
1.00
```

The model then samples one of these words.

Usually it selects **mat**, but sometimes it may choose another word depending on the sampling strategy.

This is one reason LLM responses are not always identical.

---

## 5.7 Common Sampling Strategies

The phrase "sampling strategy" refers to the specific method used to pick a word from the probability distribution. A few of the most common strategies are:

**Greedy Decoding** Always pick the single highest-probability word. This is deterministic — running it twice on the same input gives the same output every time. It's simple, but can produce repetitive or overly predictable text.

**Temperature** A setting that reshapes the probability distribution before sampling.

- **Low temperature** (e.g. 0.2) sharpens the distribution, making high-probability words even more likely — output becomes more focused and predictable.
- **High temperature** (e.g. 1.2) flattens the distribution, giving lower-probability words a better chance — output becomes more random and creative.

**Top-k Sampling** Only consider the **k** most probable words, and sample from that shortened list. For example, with `k=3`, the model would only choose among _mat_, _chair_, and _floor_ from the table above — ignoring _roof_ and _table_ entirely.

**Top-p (Nucleus) Sampling** Instead of a fixed number of words, keep adding words (starting from the most probable) until their combined probability reaches a threshold **p** (e.g. 0.9), then sample from that dynamically sized set.

These strategies are why the same prompt can produce different — yet still fluent — responses across different runs, and why tools that expose a "temperature" setting let users control how predictable or creative the output feels.

---

## 5.8 Where Do These Probabilities Come From?

The Transformer itself does **not** directly produce probabilities.

Instead, it produces **raw scores** called **logits**.

For example:

```text
Word      Logit
----------------
mat         8.7
chair       6.9
floor       5.8
roof        4.2
```

These numbers are **not probabilities** because:

- They don't lie between 0 and 1.
- They don't add up to 1.

The next chapter introduces **Softmax**, which converts these raw scores into a valid probability distribution.

---

## 5.9 Transformer Connection

Every time a Transformer predicts the next token, it follows this process:

```text
Input Sentence
       │
       ▼
Transformer
       │
       ▼
Logits (Raw Scores)
       │
       ▼
Softmax
       │
       ▼
Probability Distribution
       │
       ▼
Sample Next Token
```

This process repeats for every token generated by the model.

Whether you're using ChatGPT, Claude, Gemini, or Llama, they all follow this same high-level idea.

---

## 5.10 Key Takeaways

- Probability measures how likely an event is to occur.
- Every probability lies between **0** and **1**.
- A probability distribution is a collection of probabilities whose total equals **1**.
- LLMs predict a probability distribution for every possible next token.
- The next token is usually sampled from this distribution rather than always choosing the highest probability.
- Common sampling strategies include greedy decoding, temperature, top-k, and top-p (nucleus) sampling.
- Transformers produce **logits**, and **Softmax** converts those logits into probabilities.

In the next chapter, we'll learn how **Softmax** transforms raw model scores into the probability distributions used by every modern Large Language Model.