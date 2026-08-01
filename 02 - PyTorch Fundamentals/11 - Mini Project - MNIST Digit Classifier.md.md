# 11. Mini Project – Handwritten Digit Classifier (MNIST)

## Introduction

Congratulations! 🎉

You've now learned the core concepts of PyTorch:

- Tensors
- Tensor Operations
- Broadcasting
- Autograd
- Neural Networks
- Loss Functions
- Optimizers
- Training Loops
- CPU vs GPU

Now it's time to combine everything into a real Deep Learning project.

In this chapter, we'll build a neural network that recognizes handwritten digits from **0 to 9**.

Although this is a small project, it follows the same workflow used to train much larger AI models.

---

## 11.1 What is the MNIST Dataset?

The **MNIST** dataset is one of the most popular datasets for learning Deep Learning.

It contains:

- 70,000 handwritten digit images
- Digits from **0** to **9**
- Grayscale images
- Image size: **28 × 28 pixels**

Example:

```text
Image          Label

[ 28 × 28 ]      7
[ 28 × 28 ]      3
[ 28 × 28 ]      0
```

The goal is simple:

Given an image, predict which digit it represents.

---

## 11.2 Importing the Required Libraries

```python
import torch
import torch.nn as nn
import torch.optim as optim

from torchvision import datasets
from torchvision import transforms
from torch.utils.data import DataLoader
```

---

## 11.3 Loading the Dataset

```python
transform = transforms.ToTensor()

train_dataset = datasets.MNIST(
    root="./data",
    train=True,
    download=True,
    transform=transform
)

train_loader = DataLoader(
    train_dataset,
    batch_size=64,
    shuffle=True
)
```

The DataLoader loads the dataset in small batches, making training more efficient.

---

## 11.4 Building the Neural Network

```python
class DigitClassifier(nn.Module):

    def __init__(self):
        super().__init__()

        self.model = nn.Sequential(

            nn.Flatten(),

            nn.Linear(28 * 28, 128),

            nn.ReLU(),

            nn.Linear(128, 64),

            nn.ReLU(),

            nn.Linear(64, 10)

        )

    def forward(self, x):
        return self.model(x)
```

This network contains:

- Input Layer → 784 neurons
- Hidden Layer → 128 neurons
- Hidden Layer → 64 neurons
- Output Layer → 10 neurons

The output represents the ten possible digits (0–9).

---

## 11.5 Creating the Model

```python
model = DigitClassifier()
```

---

## 11.6 Defining the Loss Function

Since this is a classification problem, we'll use:

```python
criterion = nn.CrossEntropyLoss()
```

---

## 11.7 Creating the Optimizer

```python
optimizer = optim.Adam(
    model.parameters(),
    lr=0.001
)
```

---

## 11.8 Training the Model

```python
epochs = 5

for epoch in range(epochs):

    for images, labels in train_loader:

        predictions = model(images)

        loss = criterion(predictions, labels)

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

    print(
        f"Epoch {epoch+1} Loss: {loss.item():.4f}"
    )
```

Notice how every concept you've learned comes together in just a few lines of code.

---

## 11.9 The Complete Training Pipeline

```text
MNIST Images
      │
      ▼
Neural Network
      │
      ▼
Predictions
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
      │
      ▼
Repeat
```

This is exactly the same learning cycle used in much larger Deep Learning models.

---

## 11.10 How Does the Model Improve?

Initially, the model's weights are random.

```text
Random Weights
      │
      ▼
Poor Predictions
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

The model gradually learns patterns that help it recognize handwritten digits more accurately.

---

## 11.11 Transformer Connection

Although MNIST is an image classification task, the overall training process is identical to training a Transformer.

```text
Training Data
      │
      ▼
Neural Network
      │
      ▼
Predictions
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
Updated Parameters
```

For an LLM:

- Images become text tokens.
- Digit labels become the correct next token.
- The neural network becomes a Transformer.
- The training loop remains fundamentally the same.

The scale changes, but the learning process does not.

---

## 11.12 What You've Learned

By completing this project, you've successfully used:

- Tensors
- Tensor Operations
- Neural Networks
- `nn.Module`
- `nn.Sequential`
- `CrossEntropyLoss`
- Adam Optimizer
- Autograd
- Training Loops

These are the core building blocks of almost every modern Deep Learning model.

---

## Key Takeaways

- MNIST is one of the most widely used datasets for learning Deep Learning.
- A neural network can classify handwritten digits by learning from labeled examples.
- Building a PyTorch model involves creating the model, choosing a loss function, selecting an optimizer, and writing a training loop.
- The same workflow used in this project is also used to train modern Transformer models.
- Although LLMs are much larger, they rely on the same fundamental concepts you've learned throughout the PyTorch Fundamentals section.

Congratulations! 🎉

You have completed the **PyTorch Fundamentals** section.

You now have the knowledge required to begin studying **Deep Learning**, where you'll learn how neural networks work internally before moving on to Transformers and Large Language Models.