# 09 - Gradient Descent

## Introduction

In the previous chapter, we learned that **derivatives** tell us how a small change in a parameter affects the loss.

Now the next question is:

> **"Once we know the derivative, how do we actually improve the model?"**

The answer is **Gradient Descent**.

Gradient Descent is the optimization algorithm that allows neural networks to learn.

It uses the gradients computed from derivatives to adjust the model's parameters so that the loss becomes smaller after every training step.

Almost every deep learning model—including Transformers, GPT, BERT, and Llama—uses Gradient Descent (or one of its variants) during training.

---

## 9.1 The Goal of Training

Recall the training process.

```
Input
   │
   ▼
Prediction
   │
   ▼
Loss
```

The model makes a prediction.

The loss function measures how wrong the prediction is.

Now we want to reduce this loss.

Training is simply repeating this process until the loss becomes as small as possible.

The optimization algorithm responsible for this is **Gradient Descent**.

---

## 9.2 What is Gradient Descent?

Gradient Descent is an algorithm that updates the model's parameters in the direction that **reduces the loss**.

In simple words,

> **Gradient tells us which direction increases the loss. Gradient Descent moves in the opposite direction.**

Imagine you're standing on a mountain.

Your goal is to reach the lowest point in the valley.

You don't know the entire landscape.

You simply look around, identify which direction goes downhill, take a small step, and repeat.

Eventually, you reach the bottom.

This is exactly how Gradient Descent works.

---

## 9.3 Mountain Analogy

Imagine the following landscape.

```
            Peak
             ▲
            / \
           /   \
          /     \
         /       \
        /         \
       /           \
      ▼             ▼
   Valley        Valley
```

Your current position is somewhere on the mountain.

The derivative tells you:

```
Slope = Positive
```

This means moving right increases the height.

So you move left.

---

If the slope is negative,

```
Slope = Negative
```

You move right.

---

Eventually,

```
Slope = 0
```

You have reached the lowest point.

This point represents the **minimum loss**.

---

## 9.4 Understanding the Gradient

A **gradient** is simply a collection of derivatives.

For a function with one variable,

```
f(x)
```

the derivative tells us how changing `x` affects the output.

For a neural network,

```
Loss(w₁, w₂, w₃, ..., wₙ)
```

there are millions or even billions of parameters.

The gradient contains one derivative for every parameter.

```
Gradient

↓

[dL/dw₁,
 dL/dw₂,
 dL/dw₃,
 ...
 dL/dwₙ]
```

Each value tells us:

> "If this weight changes slightly, how much will the loss change?"

---

## 9.5 The Gradient Descent Update Rule

Training follows one simple equation.

```
New Weight

=

Old Weight

−

Learning Rate × Gradient
```

Mathematically,

```
w = w − η × ∇L
```

Where:

- `w` = current weight
- `η` (eta) = learning rate
- `∇L` = gradient of the loss

This equation is repeated for every trainable parameter.

---

### Why Subtract the Gradient?

Suppose the gradient is positive.

```
Gradient = +5
```

Increasing the weight increases the loss.

So we should decrease the weight.

```
w = w − something
```

---

Now suppose the gradient is negative.

```
Gradient = -5
```

Decreasing the weight increases the loss.

Therefore,

subtracting a negative value actually increases the weight.

```
w − (-5)

=

w + 5
```

Again, the model moves toward lower loss.

Gradient Descent always moves **opposite** to the gradient.

---

## 9.6 Learning Rate

The **learning rate** determines how large each update should be.

It is one of the most important **hyperparameters** (settings chosen before training begins, rather than values the model learns on its own) in deep learning.

---

### Learning Rate Too Small

```
●
 \
  \
   \
    \
     \
```

Very tiny steps.

Training becomes extremely slow.

The model may require millions of updates.

---

### Learning Rate Too Large

```
      ●
     ↘
       ↘
         ↗
       ↙
     ↗
```

The model jumps across the minimum.

Training becomes unstable.

The loss may even increase.

---

### Good Learning Rate

```
●
 \
  \
   \
    ▼
Minimum
```

The model reaches the minimum efficiently.

Choosing an appropriate learning rate is critical for successful training.

---

## 9.7 Manual Example

Suppose

```
Loss = w²
```

The derivative is

```
Gradient = 2w
```

Start with

```
w = 6
```

Learning rate

```
η = 0.1
```

---

### Step 1

Gradient

```
2 × 6 = 12
```

Update

```
6 − (0.1 × 12)

=

4.8
```

---

### Step 2

Gradient

```
2 × 4.8

=

9.6
```

Update

```
4.8 − 0.96

=

3.84
```

---

### Step 3

Gradient

```
2 × 3.84

=

7.68
```

Update

```
3.84 − 0.768

=

3.072
```

Notice that the weight gradually moves toward

```
0
```

Since

```
Loss = w²
```

the minimum loss occurs when

```
w = 0
```

---

## 9.8 Python Example

```python
w = 6.0
learning_rate = 0.1

for step in range(10):
    gradient = 2 * w
    w = w - learning_rate * gradient
    print(f"Step {step+1}: Weight = {w:.4f}")
```

Output

```
Step 1: Weight = 4.8000
Step 2: Weight = 3.8400
Step 3: Weight = 3.0720
...
```

The weight keeps moving toward zero.

This is Gradient Descent in its simplest form.

---

## 9.9 Gradient Descent in Neural Networks

A neural network does not have one weight.

It has many.

```
Input
   │
   ▼
Layer 1
   │
   ▼
Layer 2
   │
   ▼
Output
```

Every layer contains thousands or millions of parameters.

Training follows these steps.

```
Forward Pass

↓

Prediction

↓

Loss

↓

Backpropagation

↓

Gradients

↓

Gradient Descent

↓

Updated Weights
```

Every parameter is updated using the same update rule.

---

## 9.10 Variants of Gradient Descent

Depending on how much training data is used for one update, Gradient Descent has three common variants.

---

### Batch Gradient Descent

Uses the **entire dataset** before updating the weights.

```
Entire Dataset

↓

One Update
```

Advantages:

- Stable updates
- Accurate gradients

Disadvantages:

- Slow for large datasets
- Requires more memory

---

### Stochastic Gradient Descent (SGD)

Uses **one training example** at a time.

```
One Sample

↓

One Update
```

Advantages:

- Fast updates
- Low memory usage

Disadvantages:

- Noisy gradients
- Training can fluctuate

---

### Mini-Batch Gradient Descent

Uses a small batch of samples.

```
32 Samples

↓

One Update
```

or

```
64 Samples

↓

One Update
```

This combines the advantages of both approaches.

Modern deep learning almost always uses **Mini-Batch Gradient Descent**.

---

## 9.11 Optimizers

Pure Gradient Descent is rarely used in modern deep learning.

Instead, improved optimization algorithms called **optimizers** are used.

Some common optimizers include:

- **SGD** — the basic update rule covered in this chapter: subtract the learning rate times the gradient.
- **Momentum** — keeps a running average of past gradients, so updates build up "speed" in a consistent direction instead of reacting only to the current step. This helps push through flat regions and reduces zig-zagging.
- **RMSProp** — tracks a running average of _squared_ gradients and uses it to scale the learning rate differently for each parameter, taking smaller steps for parameters with large, volatile gradients and larger steps for ones with small, stable gradients.
- **Adam** — short for _Adaptive Moment Estimation_. It combines the ideas behind Momentum and RMSProp, tracking both the average gradient direction and the average gradient magnitude for each parameter individually.
- **AdamW** — a variant of Adam that handles weight decay (a regularization technique) separately from the gradient update, which tends to produce better-generalizing models than standard Adam.

These optimizers still rely on Gradient Descent as their foundation but make learning faster and more stable.

Among them, **Adam** and **AdamW** are the most widely used for training Transformers.

---

## 9.12 Transformer Connection

Let's connect Gradient Descent to the Transformer training pipeline.

```
Input Tokens
      │
      ▼
Embeddings
      │
      ▼
Transformer Layers
      │
      ▼
Logits
      │
      ▼
Softmax
      │
      ▼
Cross Entropy Loss
      │
      ▼
Backpropagation
      │
      ▼
Gradients
      │
      ▼
Gradient Descent
      │
      ▼
Updated Parameters
```

During every training iteration:

1. The Transformer predicts the next token.
2. Cross Entropy computes the loss.
3. Backpropagation calculates the gradients for every parameter.
4. Gradient Descent updates those parameters.
5. The model becomes slightly better at predicting the next token.

This process is repeated billions of times while training large language models.

---

## 9.13 Key Takeaways

- Gradient Descent is the optimization algorithm that minimizes the loss.
- Derivatives tell us how each parameter affects the loss.
- A gradient is a collection of derivatives for all trainable parameters.
- Parameters are updated using the rule: **New Weight = Old Weight − Learning Rate × Gradient**.
- The learning rate controls the size of each update.
- A learning rate that is too small leads to slow learning, while one that is too large can make training unstable.
- Modern neural networks typically use Mini-Batch Gradient Descent.
- Optimizers such as Momentum, RMSProp, Adam, and AdamW build on plain Gradient Descent to make training faster and more stable; Adam and AdamW are the most widely used for Transformers.
- Gradient Descent updates the model's parameters after gradients have been computed through backpropagation.