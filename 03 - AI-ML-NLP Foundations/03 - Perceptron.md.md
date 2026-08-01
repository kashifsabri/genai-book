# 3. Perceptron

## Introduction

So far, we've learned the different types of Machine Learning.

Now we'll study the basic building block of every neural network—the **Perceptron**.

A perceptron is the simplest type of artificial neuron.

Although modern AI models contain billions of neurons, every neural network starts with this simple idea.

Understanding the perceptron makes it much easier to understand neural networks and Transformers later in this book.

---

## 3.1 What is a Perceptron?

A **Perceptron** is the simplest mathematical model of a neuron.

It receives one or more inputs, performs a calculation, and produces a single output.

Think of it as a tiny decision-making unit.

```text
Inputs
 │
 ▼
Perceptron
 │
 ▼
Output
```

A neural network is simply many perceptrons connected together.

---

## 3.2 Real-World Example

Imagine a bank deciding whether to approve a loan.

The bank looks at several pieces of information:

- Monthly salary
- Credit score
- Existing loans

These values become the inputs.

```text
Salary
Credit Score
Existing Loans
      │
      ▼
  Perceptron
      │
      ▼
Approve Loan?
```

Based on these inputs, the perceptron produces an output.

For example:

```text
1 → Approve

0 → Reject
```

---

## 3.3 Inputs

The values given to a perceptron are called **inputs**.

Example:

```text
Salary = 80

Credit Score = 750

Existing Loans = 1
```

Mathematically:

```text
x₁ = 80

x₂ = 750

x₃ = 1
```

The letter **x** is commonly used to represent inputs.

---

## 3.4 Weights

Not every input is equally important.

For example, when approving a loan:

- Credit score may be very important.
- Existing loans may be moderately important.
- Salary may also be important.

A **weight** tells the perceptron how important each input is.

Mathematically:

```text
w₁

w₂

w₃
```

Larger weights mean greater importance.

During training, these weights are automatically adjusted.

---

## 3.5 Weighted Sum

The perceptron multiplies each input by its weight and adds the results together.

```text
Weighted Sum

=

(x₁ × w₁)

+

(x₂ × w₂)

+

(x₃ × w₃)
```

Example:

```text
Input

[2, 3]

Weights

[4, 5]
```

Calculation:

```text
(2 × 4)

+

(3 × 5)

=

8 + 15

=

23
```

The weighted sum is **23**.

---

## 3.6 Bias

A perceptron also has a value called the **bias**.

The bias shifts the weighted sum before producing the final output.

Updated equation:

```text
Weighted Sum

=

(x₁ × w₁)

+

(x₂ × w₂)

+

...

+

b
```

where **b** is the bias.

The bias gives the model extra flexibility when making decisions.

---

## 3.7 Activation Function

The weighted sum is passed to an **activation function**.

```text
Inputs

↓

Weighted Sum

↓

Activation Function

↓

Output
```

The activation function decides what the perceptron should output.

We'll study activation functions in detail in the next chapter.

---

## 3.8 Complete Perceptron

A perceptron works in the following order:

```text
Inputs
      │
      ▼
Multiply by Weights
      │
      ▼
Add Bias
      │
      ▼
Activation Function
      │
      ▼
Output
```

Every neuron inside a neural network follows this same process.

---

## 3.9 Perceptron in PyTorch

A perceptron with two inputs and one output can be created using a linear layer.

```python
import torch
import torch.nn as nn

layer = nn.Linear(2, 1)

print(layer)
```

Output:

```text
Linear(in_features=2, out_features=1)
```

PyTorch automatically creates the weights and bias for this perceptron.

---

## 3.10 Limitations of a Perceptron

A single perceptron can solve only simple problems.

It cannot learn complex patterns such as:

- Face recognition
- Language translation
- Image generation

To solve these problems, we connect many perceptrons together.

This forms a **neural network**, which you'll learn about in the upcoming chapters.

---

## Transformer Connection

Although Transformers are much more advanced than a single perceptron, every neuron inside a Transformer performs the same basic idea:

- Receive inputs
- Multiply by weights
- Add bias
- Produce an output

Modern AI models contain millions or billions of these operations working together.

---

## Key Takeaways

- A perceptron is the simplest artificial neuron.
- It receives inputs and produces one output.
- Every input has a weight that represents its importance.
- The perceptron computes a weighted sum and adds a bias.
- The result is passed through an activation function.
- Many perceptrons connected together form a neural network.