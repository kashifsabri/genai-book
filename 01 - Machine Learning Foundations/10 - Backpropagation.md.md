# 10 - Backpropagation

## Introduction

In the previous chapter, we learned about **Gradient Descent**.

Gradient Descent knows **how** to update the weights.

But it still needs one important piece of information:

> **What is the gradient of every weight?**

A neural network may contain:

- Thousands of weights
- Millions of weights
- Billions of weights

Calculating the derivative of every parameter manually would be impossible.

This is where **Backpropagation** comes in.

Backpropagation is the algorithm that efficiently computes the gradients of all trainable parameters in a neural network.

Once these gradients are computed, Gradient Descent uses them to update the weights.

In simple words,

> **Backpropagation calculates the gradients. Gradient Descent uses those gradients to improve the model.**

---

## 10.1 What is Backpropagation?

Backpropagation is an algorithm that computes how much each weight contributed to the final prediction error.

Instead of calculating derivatives separately for every parameter, it efficiently applies the **Chain Rule** through the entire network.

Its job is simple:

1. Calculate the loss.
2. Work backward through the network.
3. Compute gradients for every weight.
4. Pass those gradients to the optimizer.

Backpropagation itself **does not update** the weights.

It only computes the gradients.

---

## 10.2 Forward Pass vs Backward Pass

Training a neural network always consists of two phases.

### Forward Pass

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
Prediction
   │
   ▼
Loss
```

During the forward pass:

- Inputs move forward.
- Predictions are produced.
- The loss is calculated.

---

### Backward Pass

```
Loss
  ▲
  │
Gradients
  ▲
  │
Layer 2
  ▲
  │
Layer 1
  ▲
  │
Input
```

During the backward pass:

- The loss moves backward.
- Gradients are computed.
- Every parameter receives its gradient.

This reverse flow gives the algorithm its name:

> **Backpropagation = Backward Propagation of Gradients**

---

## 10.3 Why Work Backward?

Imagine a chain of dominoes.

```
A → B → C → D
```

Suppose the last domino falls.

To understand why,

we trace backward.

```
D ← C ← B ← A
```

Similarly,

a neural network prediction depends on many previous calculations.

To determine which weight caused the error,

we must move backward through the computational steps.

This is exactly what Backpropagation does.

---

## 10.4 A Simple Neural Network

Consider a tiny neural network.

```
Input

↓

Weight

↓

Prediction

↓

Loss
```

Suppose

```
Input = 2
Weight = 3
```

Prediction:

```
2 × 3

=

6
```

Actual value:

```
8
```

Loss:

```
(8 − 6)²

=

4
```

Now the model asks:

> "How should the weight change to reduce this loss?"

Backpropagation answers this question by computing:

```
dLoss/dWeight
```

Once this gradient is known,

Gradient Descent updates the weight.

---

## 10.5 Backpropagation Uses the Chain Rule

Recall the simple pipeline.

```
Input

↓

Prediction

↓

Loss
```

The loss depends on the prediction.

The prediction depends on the weight.

Therefore,

the loss depends on the weight.

The Chain Rule connects these relationships.

```
Loss

↓

Prediction

↓

Weight
```

Instead of treating the entire network as one huge equation,

Backpropagation computes derivatives one operation at a time and combines them.

This makes training deep neural networks computationally efficient.

---

## 10.6 Intuition Behind Gradient Flow

Imagine pouring water through a series of pipes.

```
Water

↓

Pipe 1

↓

Pipe 2

↓

Pipe 3

↓

Bucket
```

Now imagine sending pressure backward through the pipes.

Every pipe feels part of that pressure.

Backpropagation works similarly.

The loss sends gradient information backward.

Every layer receives exactly the amount of information it needs to update its parameters.

---

## 10.7 Step-by-Step Training Process

Training follows the same cycle repeatedly.

```
Input

↓

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

Then the entire process starts again.

Every training iteration makes the model slightly better.

After millions or billions of iterations,

the model learns meaningful patterns from data.

---

## 10.8 Example of Gradient Flow

Consider a small network.

```
Input

↓

Layer 1

↓

Layer 2

↓

Output

↓

Loss
```

Suppose the prediction is incorrect.

The loss is high.

Backpropagation first computes the gradient of the output layer.

```
Loss

↓

Output Gradient
```

Then it moves to Layer 2.

```
Loss

↓

Output

↓

Layer 2 Gradient
```

Finally,

it reaches Layer 1.

```
Loss

↓

Output

↓

Layer 2

↓

Layer 1 Gradient
```

Every layer receives gradients based on its contribution to the final loss.

---

## 10.9 Python Example with PyTorch

One of the biggest advantages of modern deep learning frameworks is that Backpropagation happens automatically.

```python
import torch

x = torch.tensor(2.0, requires_grad=True)

w = torch.tensor(3.0, requires_grad=True)

prediction = x * w

loss = (prediction - 8) ** 2

loss.backward()

print(w.grad)
```

Output

```
tensor(-8.)
```

Let's understand what happened.

Forward pass:

```
Prediction = 6
```

Loss:

```
(6 − 8)²

=

4
```

Backward pass:

PyTorch automatically computed

```
dLoss/dWeight
```

No manual derivative calculations were needed.

The line

```python
loss.backward()
```

triggered the entire Backpropagation process.

---

## 10.10 Computational Graph

Deep learning libraries internally build a **computational graph**.

Consider

```python
y = x * w

loss = (y - 8) ** 2
```

The graph looks like this.

```
x

 \
  ×

 /

w

↓

Prediction

↓

Loss
```

Each node stores:

- The operation performed
- The inputs
- The output
- How to compute its derivative

When

```python
loss.backward()
```

is executed,

PyTorch walks backward through this graph,

applying the Chain Rule at every node.

This is why automatic differentiation is so powerful.

---

## 10.11 Why Backpropagation is Efficient

Suppose a network has

```
1 billion parameters
```

Naively computing derivatives one parameter at a time would be extremely slow.

Backpropagation avoids repeating calculations.

Instead,

it reuses intermediate derivatives while moving backward through the computational graph.

Its computational cost stays in the same order of magnitude as a single forward pass — typically around two to three times the work, rather than scaling separately with each individual parameter.

Without Backpropagation,

modern deep learning would not be feasible.

---

## 10.12 Transformer Connection

Now let's connect everything we've learned.

A Transformer training iteration looks like this.

```
Input Tokens
      │
      ▼
Embeddings
      │
      ▼
Multi-Head Attention
      │
      ▼
Feed Forward Network
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
Cross Entropy Loss
      │
      ▼
Backpropagation
      │
      ▼
Gradients
      │
      ▼
Adam / AdamW Optimizer
      │
      ▼
Updated Parameters
```

During Backpropagation:

- The output layer receives the loss first.
- Gradients flow backward through the feed-forward network.
- Then through every attention layer.
- Then through the embedding layer.
- Every parameter receives its gradient.
- The optimizer updates all parameters.

This process happens for **every training batch** throughout the training of a Large Language Model.

For models like GPT, this cycle is repeated billions of times over trillions of tokens.

---

## 10.13 Putting It All Together

Let's review the learning journey from the previous chapters.

```
Input

↓

Model Prediction

↓

Softmax

↓

Cross Entropy Loss

↓

Derivatives

↓

Chain Rule

↓

Backpropagation

↓

Gradients

↓

Gradient Descent

↓

Updated Weights

↓

Better Predictions
```

Every concept we've studied builds directly on the previous one.

Together, they form the complete learning mechanism of modern neural networks.

---

## 10.14 Key Takeaways

- Backpropagation computes the gradients of every trainable parameter in a neural network.
- It works by propagating gradients backward from the loss to the input layers.
- Backpropagation relies on the Chain Rule to efficiently calculate derivatives across many interconnected operations.
- The forward pass computes predictions and the loss, while the backward pass computes gradients.
- Backpropagation computes gradients but does **not** update the weights.
- Gradient Descent (or optimizers like Adam and AdamW) uses these gradients to update the parameters.
- PyTorch performs Backpropagation automatically using `loss.backward()`.
- Modern Transformers rely on Backpropagation during every training iteration to learn from data.