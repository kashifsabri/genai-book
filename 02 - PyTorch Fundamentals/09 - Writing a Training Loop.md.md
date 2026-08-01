# 9. Writing a Training Loop

## Introduction

So far, we've learned how to:

- Build a neural network using `nn.Module`
- Measure prediction errors using loss functions
- Compute gradients using Autograd
- Update model parameters using optimizers

Now it's time to combine everything into a **training loop**.

A training loop is the heart of every Machine Learning model.

Whether you're training a simple house-price predictor or a Large Language Model with billions of parameters, the same fundamental process is repeated over and over again.

---

## 9.1 What is a Training Loop?

A training loop repeatedly performs four steps:

1. Make a prediction.
2. Measure the error.
3. Compute the gradients.
4. Update the model's weights.

This cycle repeats until the model learns from the data.

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
      │
      ▼
Repeat
```

---

## 9.2 The Complete Training Process

Every training loop follows the same sequence.

```python
for epoch in range(num_epochs):

    # 1. Make predictions
    predictions = model(X)

    # 2. Calculate the loss
    loss = criterion(predictions, y)

    # 3. Compute gradients
    optimizer.zero_grad()
    loss.backward()

    # 4. Update the weights
    optimizer.step()
```

Although this code is short, it performs everything needed to train a neural network.

Let's understand each step.

---

## 9.3 Step 1 – Forward Pass

The first step is making predictions.

```python
predictions = model(X)
```

This line sends the input through the neural network.

```text
Input
   │
   ▼
Neural Network
   │
   ▼
Prediction
```

At this stage, the model simply makes its best guess.

---

## 9.4 Step 2 – Calculate the Loss

Next, compare the prediction with the correct answer.

```python
loss = criterion(predictions, y)
```

Example:

```text
Prediction = 8.5

Actual = 10.0
```

The loss function measures how wrong the prediction is.

A smaller loss means the model is making better predictions.

---

## 9.5 Step 3 – Clear Old Gradients

Before computing new gradients, always clear the old ones.

```python
optimizer.zero_grad()
```

PyTorch accumulates gradients by default.

Without clearing them, gradients from previous iterations would be added to the new ones, leading to incorrect updates.

---

## 9.6 Step 4 – Backpropagation

Now compute the gradients.

```python
loss.backward()
```

Autograd travels backward through the computational graph and calculates the gradient of every trainable parameter.

```text
Loss
  │
  ▼
Autograd
  │
  ▼
Gradients
```

You don't calculate these gradients manually.

PyTorch does it automatically.

---

## 9.7 Step 5 – Update the Weights

Finally, update the model.

```python
optimizer.step()
```

The optimizer uses the gradients to adjust the weights.

```text
Old Weights
      │
      ▼
Optimizer
      │
      ▼
New Weights
```

The updated model should perform slightly better on the next iteration.

---

## 9.8 Training for Multiple Epochs

Training doesn't happen only once.

The model usually sees the same dataset many times.

Each complete pass through the training data is called an **epoch**.

Example:

```python
num_epochs = 5

for epoch in range(num_epochs):

    predictions = model(X)

    loss = criterion(predictions, y)

    optimizer.zero_grad()

    loss.backward()

    optimizer.step()

    print(f"Epoch {epoch+1}, Loss: {loss.item():.4f}")
```

Example output:

```text
Epoch 1, Loss: 2.4312
Epoch 2, Loss: 1.7085
Epoch 3, Loss: 1.1527
Epoch 4, Loss: 0.8421
Epoch 5, Loss: 0.6034
```

Notice that the loss decreases over time.

This indicates that the model is learning.

---

## 9.9 Putting Everything Together

A complete PyTorch training example looks like this.

```python
import torch
import torch.nn as nn
import torch.optim as optim

X = torch.tensor([[1.0], [2.0], [3.0], [4.0]])
y = torch.tensor([[2.0], [4.0], [6.0], [8.0]])

model = nn.Linear(1, 1)

criterion = nn.MSELoss()

optimizer = optim.SGD(model.parameters(), lr=0.01)

for epoch in range(100):

    predictions = model(X)

    loss = criterion(predictions, y)

    optimizer.zero_grad()

    loss.backward()

    optimizer.step()

print("Training Complete")
```

This example combines everything you've learned in the PyTorch Fundamentals section.

---

## 9.10 Why Does the Loss Decrease?

Initially, the model's weights are random.

```text
Random Weights
      │
      ▼
Bad Predictions
      │
      ▼
High Loss
```

After many training iterations:

```text
Updated Weights
      │
      ▼
Better Predictions
      │
      ▼
Lower Loss
```

The optimizer continuously adjusts the weights until the model performs well.

---

## 9.11 Transformer Connection

Large Language Models use exactly the same training loop.

The only difference is the scale.

```text
Training Text
      │
      ▼
Transformer
      │
      ▼
Predicted Next Token
      │
      ▼
CrossEntropyLoss
      │
      ▼
Autograd
      │
      ▼
AdamW Optimizer
      │
      ▼
Updated Parameters
      │
      ▼
Repeat Billions of Times
```

Whether you're training a tiny neural network or GPT-style models, the overall training process remains the same.

---

## 9.12 Common Beginner Mistakes

### Forgetting `optimizer.zero_grad()`

Gradients accumulate by default.

Always clear them before computing new gradients.

---

### Calling `optimizer.step()` Before `loss.backward()`

Always use this order:

```python
optimizer.zero_grad()

loss.backward()

optimizer.step()
```

---

### Expecting the Loss to Reach Zero

Real-world datasets contain noise.

The goal is to reduce the loss as much as possible, not necessarily to zero.

---

### Using the Wrong Loss Function

Regression models commonly use `MSELoss`.

Classification models commonly use `CrossEntropyLoss`.

Choosing the wrong loss function prevents the model from learning correctly.

---

## 9.13 Key Takeaways

- A training loop is the core of every Machine Learning model.
- Every iteration follows the same sequence:
  1. Forward pass
  2. Calculate loss
  3. Clear gradients
  4. Backpropagation
  5. Update weights
- `optimizer.zero_grad()` clears old gradients.
- `loss.backward()` computes gradients automatically.
- `optimizer.step()` updates the model's parameters.
- Training repeats for multiple epochs until the model learns.
- The same training loop is used for neural networks, CNNs, Transformers, and Large Language Models.

Congratulations! 

You now understand the complete PyTorch training pipeline. In the next chapter, we'll learn how to use the **GPU** to train models much faster.