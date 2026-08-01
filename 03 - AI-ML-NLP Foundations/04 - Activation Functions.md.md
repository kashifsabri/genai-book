# 4. Activation Functions

## Introduction

In the previous chapter, we learned that a perceptron calculates a **weighted sum** of its inputs.

But is that enough to solve complex problems like image recognition or language translation?

The answer is **No**.

If a neural network only performs weighted sums, it behaves like a simple mathematical equation. No matter how many layers we add, it can only learn simple patterns.

To solve complex problems, we need **activation functions**.

An activation function helps a neuron decide **how much information should pass to the next layer**.

Without activation functions, modern AI systems like ChatGPT, Gemini, and Llama would not exist.

---

## 4.1 Why Do We Need Activation Functions?

Imagine you're using a water tap.

```text
Water Pipe
     │
     ▼
 Water Tap
     │
     ▼
Water Flow
```

The tap controls how much water flows through the pipe.

Similarly, an activation function controls how much information flows through a neuron.

Some information is allowed to pass.

Some information is reduced.

Some information is completely blocked.

This allows the neural network to learn complex patterns instead of simple straight-line relationships.

---

## 4.2 Where Does the Activation Function Fit?

Every neuron works in this order.

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

The activation function is always applied **after** the weighted sum.

---

## 4.3 Step Function

The **Step Function** is the simplest activation function.

It has only two outputs.

```text
If Input ≥ 0

Output = 1

If Input < 0

Output = 0
```

Example:

```text
Input = 4

Output = 1
```

```text
Input = -2

Output = 0
```

The Step Function was used in early perceptrons.

Today, it is rarely used because it is not suitable for training deep neural networks.

---

## Step Function in Python

```python
def step(x):
    if x >= 0:
        return 1
    return 0

print(step(5))
print(step(-3))
```

Output

```text
1
0
```

---

## 4.4 Sigmoid Function

The **Sigmoid** function converts any number into a value between **0 and 1**.

```text
Input = -5

Output ≈ 0
```

```text
Input = 0

Output = 0.5
```

```text
Input = 5

Output ≈ 1
```

Because its output looks like a probability, Sigmoid is often used for binary classification problems.

---

## Sigmoid in PyTorch

```python
import torch
import torch.nn as nn

sigmoid = nn.Sigmoid()

x = torch.tensor([-5.0, 0.0, 5.0])

print(sigmoid(x))
```

Output

```text
tensor([0.0067, 0.5000, 0.9933])
```

Notice:

- Large negative numbers become values close to **0**.
- Zero becomes **0.5**.
- Large positive numbers become values close to **1**.

---

## 4.5 Tanh Function

The **Tanh** function is similar to Sigmoid.

The main difference is its output range.

```text
Sigmoid

0 → 1
```

```text
Tanh

-1 → 1
```

Example:

```text
Input = -5

Output ≈ -1
```

```text
Input = 0

Output = 0
```

```text
Input = 5

Output ≈ 1
```

Because the outputs are centered around zero, Tanh often performs better than Sigmoid in some neural networks.

---

## Tanh in PyTorch

```python
import torch
import torch.nn as nn

tanh = nn.Tanh()

x = torch.tensor([-5.0, 0.0, 5.0])

print(tanh(x))
```

Output

```text
tensor([-0.9999, 0.0000, 0.9999])
```

---

## 4.6 ReLU (Rectified Linear Unit)

The **ReLU** activation function is the most widely used activation function in Deep Learning.

Its rule is very simple.

```text
If Input < 0

Output = 0
```

```text
If Input ≥ 0

Output = Input
```

Examples

```text
Input = -3

Output = 0
```

```text
Input = 2

Output = 2
```

```text
Input = 7

Output = 7
```

Unlike Sigmoid and Tanh, ReLU does not compress positive numbers.

This makes it simple and fast.

---

## ReLU in PyTorch

```python
import torch
import torch.nn as nn

relu = nn.ReLU()

x = torch.tensor([-3.0, -1.0, 0.0, 2.0, 5.0])

print(relu(x))
```

Output

```text
tensor([0., 0., 0., 2., 5.])
```

Notice:

- All negative values become **0**.
- Positive values remain unchanged.

This simple behavior is one reason why ReLU became the standard activation function for many Deep Learning models.

---

## 4.7 GELU (Gaussian Error Linear Unit)

Modern Transformer models often use **GELU** instead of ReLU.

Unlike ReLU, GELU changes values more smoothly instead of cutting off every negative value.

You don't need to understand its mathematical formula right now.

For now, remember:

- ReLU is common in many neural networks.
- GELU is commonly used in Transformer models.

---

## GELU in PyTorch

```python
import torch
import torch.nn as nn

gelu = nn.GELU()

x = torch.tensor([-3.0, -1.0, 0.0, 2.0])

print(gelu(x))
```

Example Output

```text
tensor([-0.0040, -0.1587, 0.0000, 1.9545])
```

Notice that GELU does not simply remove negative values. Instead, it changes them smoothly.

---

## 4.8 Comparing Activation Functions

| Activation Function | Output Range | Common Use |
|---------------------|--------------|------------|
| Step | 0 or 1 | Early Perceptrons |
| Sigmoid | 0 to 1 | Binary Classification |
| Tanh | -1 to 1 | Older Neural Networks |
| ReLU | 0 to ∞ | Most Deep Learning Models |
| GELU | Smooth Output | Transformers and LLMs |

---

## 4.9 Real-World Example

Imagine you're applying for a loan.

The bank collects information such as:

- Salary
- Credit Score
- Existing Loans

These values are combined inside the neuron.

The activation function helps decide whether the information is strong enough to influence the final decision.

```text
Inputs
      │
      ▼
Neuron
      │
      ▼
Activation Function
      │
      ▼
Approve Loan?
```

Millions of neurons make similar decisions inside a neural network.

---

## Transformer Connection

Every Transformer contains millions of neurons.

After each linear layer, an activation function is applied.

Most modern Transformer models use **GELU** because it performs well on language tasks.

When you study Transformer architecture later in this book, you'll see GELU appear repeatedly.

---

## Key Takeaways

- Activation functions decide how much information passes through a neuron.
- Without activation functions, neural networks can only learn simple linear patterns.
- The Step Function was used in early perceptrons.
- Sigmoid produces values between 0 and 1.
- Tanh produces values between -1 and 1.
- ReLU is the most widely used activation function in Deep Learning.
- Modern Transformer models commonly use GELU.