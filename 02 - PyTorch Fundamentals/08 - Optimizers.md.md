# 8. Optimizers

## Introduction

In the previous chapter, we learned how a **loss function** measures how wrong a model's predictions are.

After computing the loss, PyTorch's **Autograd** calculates the gradients for every trainable parameter.

But one important question remains:

> **Who actually updates the model's weights?**

The answer is an **optimizer**.

An optimizer uses the gradients computed by Autograd to adjust the model's parameters, helping the model make better predictions over time.

Without an optimizer, a neural network would never learn.

---

## 8.1 What is an Optimizer?

An **optimizer** is an algorithm that updates a model's parameters (weights and biases) to reduce the loss.

The learning process follows these steps:

```text
Input Data
      │
      ▼
Neural Network
      │
      ▼
Prediction
      │
      ▼
Loss Function
      │
      ▼
Autograd
      │
      ▼
Optimizer
      │
      ▼
Updated Weights
```

This cycle repeats thousands or even millions of times during training.

---

## 8.2 Why Do We Need an Optimizer?

Imagine you're climbing a mountain while blindfolded.

Your goal is to reach the **lowest point** in the valley.

After every step, someone tells you:

> "Move a little to the left."

or

> "Take a small step forward."

By repeatedly following these directions, you eventually reach the bottom.

Training a neural network works in a similar way.

- The **loss function** tells us how wrong the prediction is.
- **Autograd** tells us which direction reduces the error.
- The **optimizer** takes a step in that direction.

Without an optimizer, the model would never improve.

---

## 8.3 Stochastic Gradient Descent (SGD)

The simplest optimizer in PyTorch is **Stochastic Gradient Descent (SGD)**.

```python
import torch.optim as optim

optimizer = optim.SGD(model.parameters(), lr=0.01)
```

Here:

- `model.parameters()` tells the optimizer which parameters to update.
- `lr` stands for **learning rate**, which controls how large each update step should be.

A learning rate of `0.01` means the optimizer makes relatively small updates to the model's parameters.

---

## 8.4 Understanding the Learning Rate

The **learning rate** is one of the most important hyperparameters in Deep Learning.

If it's **too small**, training becomes very slow.

```text
Loss

│\
│ \
│  \
│   \
└──────────────► Epochs
```

If it's **too large**, the optimizer may overshoot the best solution.

```text
Target
   X

←────→
```

A good learning rate helps the model converge efficiently.

Common starting values include:

```text
0.1
0.01
0.001
```

For many deep learning models, `0.001` is a common default.

---

## 8.5 Adam Optimizer

Although SGD is simple, modern deep learning models often use **Adam**.

Creating an Adam optimizer is almost identical:

```python
import torch.optim as optim

optimizer = optim.Adam(model.parameters(), lr=0.001)
```

Adam automatically adjusts parameter updates during training, often allowing models to converge faster than SGD.

Because of its reliability and performance, Adam is one of the most widely used optimizers in Machine Learning.

---

## 8.6 SGD vs Adam

| SGD | Adam |
|------|------|
| Simple | More advanced |
| Usually slower | Usually faster |
| Fewer parameters | Adaptive learning rates |
| Good for simple models | Common choice for deep learning |

Both optimizers are useful, but **Adam** is the default choice for many modern neural networks.

---

## 8.7 Updating the Model

Once gradients have been computed, updating the model requires only one line of code.

```python
optimizer.step()
```

This tells the optimizer to update every trainable parameter using the gradients calculated by Autograd.

After updating the parameters, we clear the stored gradients.

```python
optimizer.zero_grad()
```

This prevents gradients from accumulating across training iterations.

A typical training step looks like this:

```python
loss.backward()

optimizer.step()

optimizer.zero_grad()
```

These three lines appear in almost every PyTorch training loop.

---

## 8.8 Real-World Example

Imagine you're learning to play chess.

After every game:

- You review your mistakes.
- You learn from them.
- You adjust your strategy.
- You play again.

Similarly, during training:

- The model makes a prediction.
- The loss measures the mistake.
- Autograd computes the gradients.
- The optimizer updates the weights.
- The model makes a better prediction next time.

This process repeats until the model performs well.

---

## 8.9 Transformer Connection

Every Transformer model—including GPT, Llama, Gemma, Qwen, and Mistral—is trained using an optimizer.

The training pipeline looks like this:

```text
Training Data
      │
      ▼
Transformer
      │
      ▼
Prediction
      │
      ▼
CrossEntropyLoss
      │
      ▼
Autograd
      │
      ▼
Adam Optimizer
      │
      ▼
Updated Weights
```

While many optimizers exist, modern Transformer models commonly use **AdamW**, which is a variant of Adam designed for better regularization.

For now, understanding **SGD** and **Adam** provides the foundation needed to understand how neural networks learn.

---

## 8.10 Common Beginner Mistakes

### Forgetting `optimizer.zero_grad()`

Gradients accumulate by default.

Always clear them before the next training iteration.

---

### Choosing a Learning Rate That Is Too High

A large learning rate can make training unstable or prevent the model from converging.

---

### Choosing a Learning Rate That Is Too Low

A very small learning rate may eventually work, but training can become extremely slow.

---

### Calling `optimizer.step()` Before `loss.backward()`

The correct order is:

```python
loss.backward()

optimizer.step()

optimizer.zero_grad()
```

---

## 8.11 Key Takeaways

- An optimizer updates a model's weights to reduce the loss.
- Autograd computes the gradients, while the optimizer applies them.
- `optim.SGD()` implements Stochastic Gradient Descent.
- `optim.Adam()` is one of the most popular optimizers in Deep Learning.
- The learning rate controls the size of each update.
- `optimizer.step()` updates the model parameters.
- `optimizer.zero_grad()` clears accumulated gradients.
- Modern LLMs are commonly trained using **AdamW**, which builds upon the ideas introduced by Adam.

In the next chapter, we'll combine everything we've learned so far and write our **first complete PyTorch training loop**.