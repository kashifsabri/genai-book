# 5. Neural Networks

## Introduction

In the previous chapter, we learned about the **Perceptron**, the simplest artificial neuron.

A single perceptron can solve only simple problems.

For more complex tasks like recognizing faces, translating languages, or generating text, we need many perceptrons working together.

A collection of connected perceptrons is called a **Neural Network**.

Modern AI systems such as ChatGPT, Gemini, Llama, and Claude are all built using very large neural networks.

---

## 5.1 What is a Neural Network?

A **Neural Network** is a collection of artificial neurons connected together.

Each neuron receives information, performs a calculation, and passes its output to the next layer.

Instead of one neuron making a decision, thousands or even billions of neurons work together.

```text
Input
   │
   ▼
Neural Network
   │
   ▼
Output
```

The more neurons and layers a network has, the more complex patterns it can learn.

---

## 5.2 Layers in a Neural Network

A neural network is usually divided into three types of layers.

- Input Layer
- Hidden Layer(s)
- Output Layer

```text
Input Layer
      │
      ▼
Hidden Layer
      │
      ▼
Output Layer
```

Each layer has a different purpose.

---

## 5.3 Input Layer

The **Input Layer** receives the data.

For example, suppose we're predicting house prices.

Our inputs might be:

- House Size
- Number of Bedrooms
- Age of the House

```text
House Size
Bedrooms
House Age
      │
      ▼
Input Layer
```

The input layer simply passes this information to the next layer.

It does not perform any learning.

---

## 5.4 Hidden Layers

The **Hidden Layer** is where learning happens.

Each neuron performs the same steps:

1. Receive inputs.
2. Multiply them by weights.
3. Add a bias.
4. Apply an activation function.
5. Pass the result to the next layer.

```text
Inputs
     │
     ▼
Weights
     │
     ▼
Bias
     │
     ▼
Activation Function
     │
     ▼
Output
```

Most modern neural networks contain multiple hidden layers.

This is why they are called **Deep Neural Networks**.

---

## 5.5 Output Layer

The **Output Layer** produces the final prediction.

Examples:

Spam Detection

```text
Spam

or

Not Spam
```

House Price Prediction

```text
₹52,00,000
```

Digit Recognition

```text
7
```

The type of output depends on the problem being solved.

---

## 5.6 A Simple Neural Network

A small neural network might look like this.

```text
Input Layer

 ○   ○   ○
  \  |  /
   \ | /
 Hidden Layer

 ○   ○   ○
   \ | /
    \|/

Output Layer

    ○
```

Each circle represents a neuron.

Each line represents a connection between neurons.

---

## 5.7 Python Example

Let's create a small neural network using PyTorch.

```python
import torch
import torch.nn as nn

model = nn.Sequential(
    nn.Linear(3, 4),
    nn.ReLU(),
    nn.Linear(4, 2)
)

print(model)
```

Output

```text
Sequential(
  (0): Linear(in_features=3, out_features=4)
  (1): ReLU()
  (2): Linear(in_features=4, out_features=2)
)
```

This network has:

- 3 input features
- 1 hidden layer with 4 neurons
- 2 output neurons

---

## 5.8 Real-World Example

Imagine a hospital building a system to detect heart disease.

The inputs might include:

- Age
- Blood Pressure
- Cholesterol
- Heart Rate

```text
Patient Data
      │
      ▼
Neural Network
      │
      ▼
Disease Risk
```

The neural network learns relationships between these values and predicts whether the patient is at risk.

---

## 5.9 Why Do We Need Multiple Layers?

A single perceptron can learn only simple relationships.

Adding hidden layers allows the network to learn much more complex patterns.

For example:

- Recognizing faces
- Understanding speech
- Translating languages
- Writing text
- Detecting objects in images

This is why modern AI models contain many layers.

---

## Transformer Connection

Although Transformers have a different architecture from traditional neural networks, they are still built from layers of neurons.

Inside every Transformer, you'll find:

- Linear Layers
- Activation Functions
- Neural Network Blocks

These components work together to process language and generate text.

Understanding simple neural networks makes it much easier to understand Transformers later in this book.

---

## Key Takeaways

- A neural network is a collection of connected artificial neurons.
- Neural networks contain input, hidden, and output layers.
- The hidden layers learn patterns from the data.
- Multiple layers allow the network to solve complex problems.
- Modern AI systems, including Large Language Models, are built using deep neural networks.