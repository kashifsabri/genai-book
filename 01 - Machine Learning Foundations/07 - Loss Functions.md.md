# 07 - Loss Functions

## Introduction

Imagine teaching a child to solve math problems.

The child gives an answer.

You compare it with the correct answer.

If the answer is wrong, you explain the mistake.

The child learns and improves.

Machine learning works in exactly the same way.

An AI model makes a prediction.

Something must measure **how wrong** that prediction is.

That measurement is called the **loss function**.

Without a loss function, an AI model has no idea whether it is improving or getting worse.

Loss functions are one of the most important concepts in machine learning because they provide the learning signal that guides the model toward better predictions.

---

## What is a Loss Function?

A **loss function** is a mathematical function that measures the difference between:

- The model's prediction
- The correct answer (ground truth)

Simply put,

> **Loss tells the model how bad its prediction was.**

A small loss means the prediction was close to the correct answer.

A large loss means the prediction was far from the correct answer.

The objective of training is simple:

> **Minimize the loss.**

---

## Why AI Needs Loss Functions

Consider a student answering an exam.

Question:

> What is 5 + 5?

Student answers:

```
11
```

Teacher says:

```
Wrong by 1
```

Next attempt:

```
10
```

Teacher says:

```
Correct
```

The teacher is acting like a loss function.

The student improves because someone tells them **how wrong** they are.

AI models learn exactly the same way.

Training follows this cycle:

```
Input
   │
   ▼
Model Prediction
   │
   ▼
Loss Function
   │
   ▼
Error Value
   │
   ▼
Optimizer Updates Weights
   │
   ▼
Better Prediction
```

No loss function means:

- No error measurement
- No feedback
- No learning

---

## Intuition with Prediction Examples

### Example 1 — House Price Prediction

Actual house price:

```
₹50,00,000
```

Model predicts:

```
₹48,00,000
```

Difference:

```
₹2,00,000
```

Loss is relatively small.

---

Another prediction:

```
₹80,00,000
```

Difference:

```
₹30,00,000
```

Loss becomes much larger.

The model now knows this prediction is much worse.

---

### Example 2 — Cat vs Dog Classification

Correct label:

```
Dog
```

Model predicts:

```
Dog = 99%
Cat = 1%
```

Very confident.

Loss is tiny.

---

Another prediction:

```
Dog = 51%
Cat = 49%
```

Prediction is correct but uncertain.

Loss increases.

---

Another prediction:

```
Dog = 2%
Cat = 98%
```

Prediction is completely wrong.

Loss becomes very large.

Notice that classification is not only about being right or wrong.

Confidence also matters.

---

## Types of Loss Functions

Different problems require different loss functions.

|Problem|Common Loss Function|
|---|---|
|Regression|Mean Squared Error (MSE)|
|Binary Classification|Binary Cross Entropy|
|Multi-Class Classification|Cross Entropy Loss|
|Object Detection|Combination of Multiple Losses|
|Language Models|Cross Entropy Loss|

The two most important ones are:

- Mean Squared Error (MSE)
- Cross Entropy Loss

---

## Mean Squared Error (MSE)

Mean Squared Error is mainly used for **regression problems**, where the output is a continuous number.

Examples include:

- House price prediction
- Stock price prediction
- Temperature forecasting
- Sales prediction

---

### Formula

$$ MSE = \frac{1}{N}\sum_{i=1}^{N}(y_i-\hat{y_i})^2 $$

Where:

- $y$ = actual value
- $\hat{y}$ = predicted value
- $N$ = number of samples

---

### Why Square the Error?

Suppose errors are:

```
+5
-5
```

Adding them gives:

```
0
```

That is misleading because both predictions are wrong.

Squaring fixes this.

```
5² = 25
(-5)² = 25
```

Now large mistakes are penalized more heavily.

This makes the model focus on reducing big errors.

---

### Manual Example

Actual values:

```
[2, 4, 6]
```

Predictions:

```
[3, 5, 4]
```

Errors:

```
1
1
-2
```

Squared errors:

```
1
1
4
```

Average:

```
(1 + 1 + 4) / 3

= 2
```

Therefore,

```
MSE = 2
```

---

### Python Example

```python
import numpy as np

actual = np.array([2, 4, 6])
predicted = np.array([3, 5, 4])

mse = np.mean((actual - predicted) ** 2)

print(mse)
```

Output

```
2.0
```

---

### Advantages of MSE

- Simple
- Easy to differentiate
- Strong mathematical properties
- Penalizes large errors

---

### Limitations

Because errors are squared,

Large mistakes dominate the loss.

One bad prediction can significantly increase the overall loss.

---

## Cross Entropy Loss

Cross Entropy is the most important loss function in modern AI.

Almost every large language model uses it during training.

Unlike MSE, Cross Entropy is designed for **classification**.

Instead of predicting numbers, the model predicts **probabilities**.

---

### Intuition

Suppose a model predicts:

```
Cat : 90%
Dog : 10%
```

If the correct answer is:

```
Cat
```

The model should receive a very small loss.

Now consider:

```
Cat : 40%
Dog : 60%
```

The model is uncertain.

Loss increases.

Now:

```
Cat : 1%
Dog : 99%
```

The model is confidently wrong.

Loss becomes extremely large.

Cross Entropy rewards high confidence only when that confidence is correct.

---

### Formula

For a single correct class,

$$ Loss = -\log(p) $$

Where:

- $p$ is the predicted probability of the correct class.
- $\log$ here is the **natural logarithm** (base _e_), the same one used by `np.log()` in Python — this matters because the loss values below only match up if the same base is used consistently.

The logarithm has an interesting behavior.

|Correct Probability|Loss|
|---|---|
|1.0|0|
|0.9|0.105|
|0.5|0.693|
|0.1|2.30|
|0.01|4.60|

Notice something important.

As confidence in the correct answer decreases, the loss grows rapidly.

This strongly encourages the model to assign higher probabilities to the correct class.

---

### Example

Correct word:

```
apple
```

Model prediction:

|Word|Probability|
|---|---|
|apple|0.92|
|mango|0.05|
|orange|0.03|

Loss:

```
-log(0.92)

≈ 0.083
```

Very small.

---

Another prediction:

|Word|Probability|
|---|---|
|apple|0.20|
|mango|0.50|
|orange|0.30|

Loss:

```
-log(0.20)

≈ 1.609
```

Much larger.

The model learns that it should assign more probability to the correct word.

---

### Python Example

```python
import numpy as np

p = 0.92

loss = -np.log(p)

print(loss)
```

Output

```
0.083
```

---

### Cross Entropy with Multiple Classes

Suppose a classifier predicts:

```
[0.05, 0.90, 0.05]
```

Correct class:

```
Class 2
```

Loss becomes:

```
-log(0.90)
```

Only the probability of the correct class matters.

The optimizer then adjusts the weights to increase that probability in future predictions.

---

## Binary Cross Entropy

**Binary Cross Entropy (BCE)** is a special case of Cross Entropy used specifically when there are only **two** possible classes — for example, "spam vs. not spam" or "fraud vs. not fraud."

Its formula accounts for both possible outcomes:

$$ Loss = -\big[y\log(p) + (1-y)\log(1-p)\big] $$

Where:

- $y$ is the true label (**0** or **1**)
- $p$ is the model's predicted probability that the label is **1**

If the true label is **1**, the second term disappears and the formula reduces to `-log(p)` — the same idea as the single-class Cross Entropy example above. If the true label is **0**, the first term disappears instead, and the loss is based on how close $p$ was to 0.

Multi-class Cross Entropy (used for tasks like next-token prediction, where there can be thousands of possible classes) is the generalized version of this same idea.

---

## Why Cross Entropy is Used in LLMs

Large Language Models perform **next-token prediction**.

Suppose the sentence is:

```
The sky is _____
```

The model predicts:

|Token|Probability|
|---|---|
|blue|0.92|
|green|0.03|
|red|0.02|
|black|0.01|
|other|0.02|

The correct token is:

```
blue
```

Cross Entropy computes the loss using only the probability assigned to **blue**.

If the probability is high,

Loss is low.

If the probability is low,

Loss is high.

The optimizer (the algorithm — such as **Gradient Descent** — that decides how to adjust each of the model's weights based on the loss) then updates millions or even billions of parameters so that "blue" receives a higher probability next time.

This process is repeated for **every token in every sentence** across trillions of training tokens.

---

### Why Not Use MSE?

Suppose the vocabulary contains:

```
100,000 words
```

The model outputs a probability distribution over all 100,000 tokens.

Cross Entropy is specifically designed to compare probability distributions.

MSE treats probabilities like ordinary numbers and does not strongly penalize confident wrong predictions.

Cross Entropy provides:

- Faster convergence
- Better gradients
- Better probability learning
- Superior performance on classification tasks

That is why nearly all modern language models use Cross Entropy instead of MSE.

---

## Transformer Connection

Recall the Transformer pipeline.

```
Input Tokens
      │
      ▼
Embeddings
      │
      ▼
Positional Encoding
      │
      ▼
Transformer Layers
      │
      ▼
Linear Layer
      │
      ▼
Logits
      │
      ▼
Softmax
      │
      ▼
Probabilities
      │
      ▼
Cross Entropy Loss
      │
      ▼
Backpropagation
      │
      ▼
Gradient Descent
      │
      ▼
Updated Weights
```

The Transformer itself only generates **logits**.

Softmax converts those logits into probabilities.

Cross Entropy compares those probabilities with the correct token.

The resulting loss is propagated backward through the entire network using **backpropagation** (the process of working backward through every layer of the network to calculate exactly how much each individual weight contributed to the final error).

Those calculations are then used by **gradient descent** (the optimization method that nudges each weight slightly in the direction that reduces the loss) to update the weights.

This is how every attention layer, feed-forward layer, embedding matrix, and linear layer gradually learns.

Without Cross Entropy, the Transformer would have no learning signal.

---

## Summary

|Loss Function|Used For|
|---|---|
|Mean Squared Error|Regression|
|Binary Cross Entropy|Binary Classification|
|Cross Entropy|Multi-Class Classification|
|Cross Entropy|Language Models|
|Cross Entropy|Transformers|

---

## Key Takeaways

- A loss function measures how wrong a model's prediction is.
- Training aims to minimize the loss over time.
- Loss provides the feedback signal that enables learning.
- Mean Squared Error is commonly used for regression problems.
- Cross Entropy is used for classification tasks where outputs are probabilities.
- Binary Cross Entropy is the two-class special case of Cross Entropy; multi-class Cross Entropy generalizes it to many possible classes.
- Large Language Models use Cross Entropy because they predict probability distributions over vocabulary tokens.
- In Transformers, logits are converted into probabilities using Softmax, and Cross Entropy measures how well those probabilities match the correct next token.
- Loss is minimized through backpropagation (computing each weight's contribution to the error) and gradient descent (updating weights to reduce that error), gradually improving the model's predictions.