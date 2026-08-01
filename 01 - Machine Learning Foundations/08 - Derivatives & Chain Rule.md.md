# 08 - Derivatives & Chain Rule

## Introduction

In the previous chapter, we learned that a **loss function** tells us how wrong a model's prediction is.

But knowing that a prediction is wrong isn't enough.

Imagine throwing a dart at a dartboard.

If someone tells you:

> "You missed by 20 cm."

You now know **how wrong** you were.

But you still don't know:

- Should you aim higher?
- Should you aim lower?
- Should you throw harder?
- Should you throw softer?

Machine learning faces the same problem.

The loss function tells the model **how wrong** it is, but something else must tell it **how to improve**.

That "something" is called the **derivative**.

Derivatives measure how changing a value affects the final loss. They are the mathematical foundation of learning in neural networks.

---

## 8.1 What is a Derivative?

A **derivative** measures **how fast one quantity changes when another quantity changes**.

In simple words,

> **A derivative tells us how much the output changes when the input changes slightly.**

Instead of asking:

> "What is the value?"

We ask:

> "How quickly is the value changing?"

This idea appears everywhere in everyday life.

---

### Car Speed Analogy

Suppose you're driving a car.

At 10:00 AM:

```
Distance = 50 km
```

At 11:00 AM:

```
Distance = 110 km
```

The change in distance is:

```
110 − 50 = 60 km
```

The change in time is:

```
1 hour
```

Speed is:

```
60 km/hour
```

Notice something important.

You are not measuring the distance itself.

You are measuring **how quickly the distance changes**.

A derivative works in exactly the same way.

---

### Mountain Slope Analogy

Imagine climbing a mountain.

Sometimes the path is almost flat.

```
______
```

The slope is close to zero.

Walking is easy.

---

Sometimes the path is steep.

```
    /
   /
  /
 /
```

Now every step increases your height quickly.

The slope is large.

---

Sometimes you're walking downhill.

```
\
 \
  \
   \
```

The slope becomes negative.

A derivative measures this slope mathematically.

Positive derivative:

```
Increasing
```

Negative derivative:

```
Decreasing
```

Zero derivative:

```
Flat
```

This idea becomes extremely important during neural network training.

---

## 8.2 Numerical Derivative

Suppose we don't know the mathematical formula of a function.

Can we still estimate its derivative?

Yes.

We simply make a tiny change in the input and observe how much the output changes.

Suppose

```
f(x) = x²
```

Choose:

```
x = 3
```

Output:

```
f(3) = 9
```

Now increase x slightly.

```
x = 3.001
```

Output:

```
f(3.001) ≈ 9.006001
```

Change in output:

```
9.006001 − 9
= 0.006001
```

Change in input:

```
3.001 − 3
= 0.001
```

Estimated derivative:

```
0.006001 / 0.001

≈ 6.001
```

The smaller the change in input, the closer this estimate gets to the true derivative.

This approach is called the **numerical derivative**.

---

## 8.3 Manual Example

Consider the function

```
f(x) = x²
```

Let's calculate its derivative at different values.

### At x = 1

```
Slope = 2
```

Meaning:

If x increases slightly,

the output increases about twice as fast.

---

### At x = 2

```
Slope = 4
```

The function is becoming steeper.

---

### At x = 5

```
Slope = 10
```

The curve is much steeper now.

The derivative depends on where you are on the curve.

This is why neural networks continuously recompute derivatives during training.

---

### Analytical Derivative

Instead of estimating the slope numerically, calculus provides an exact formula.

For

```
f(x) = x²
```

The derivative is

```
f'(x) = 2x
```

Now we can compute the slope instantly.

|x|f(x)|Derivative|
|---|---|---|
|1|1|2|
|2|4|4|
|3|9|6|
|4|16|8|
|5|25|10|

This exact derivative is called the **analytical derivative**.

---

## 8.4 Python Example

### Numerical Derivative

```python
def f(x):
    return x ** 2

x = 3
h = 0.0001

numerical = (f(x + h) - f(x)) / h

print(numerical)
```

Output

```
6.0001
```

---

### Analytical Derivative

```python
def derivative(x):
    return 2 * x

print(derivative(3))
```

Output

```
6
```

Both answers are almost identical.

The numerical derivative approaches the analytical derivative as the step size becomes smaller.

---

## 8.5 What is the Chain Rule?

Real machine learning models are not made of one simple equation.

Instead, they consist of many operations connected together.

Consider this simple pipeline.

```
Input
   │
   ▼
Prediction
   │
   ▼
Loss
```

Changing the input changes the prediction.

Changing the prediction changes the loss.

Therefore,

changing the input also changes the loss.

The **Chain Rule** is the mathematical rule that allows us to calculate this indirect effect.

Instead of treating the entire system as one giant equation, it breaks the calculation into smaller steps.

```
Input
   │
   ▼
Prediction
   │
   ▼
Loss
```

The Chain Rule combines the effect of each step to determine how the input influences the final loss.

Without it, training deep neural networks would be practically impossible.

---

### Chain Rule Formula

If one quantity depends on a second quantity, which in turn depends on a third, the Chain Rule says you can find the overall rate of change by multiplying the individual rates of change together.

$$ \frac{dy}{dx} = \frac{dy}{du} \cdot \frac{du}{dx} $$

Applied to the pipeline above, where the Loss depends on the Prediction, and the Prediction depends on the Input:

$$ \frac{d(\text{Loss})}{d(\text{Input})} = \frac{d(\text{Loss})}{d(\text{Prediction})} \cdot \frac{d(\text{Prediction})}{d(\text{Input})} $$

Each term on the right is a simple, local derivative — easy to compute on its own. The Chain Rule is what lets us multiply these small, local pieces together to find the effect across the _entire_ pipeline, no matter how many steps are in between.

---

### Manual Example

Suppose we define a tiny two-step pipeline:

```
Prediction = 2 × Input
Loss = Prediction²
```

Let

```
Input = 3
```

**Step 1 — Forward pass**

```
Prediction = 2 × 3 = 6
Loss = 6² = 36
```

**Step 2 — Local derivatives**

```
d(Loss)/d(Prediction) = 2 × Prediction = 2 × 6 = 12
d(Prediction)/d(Input) = 2
```

**Step 3 — Apply the Chain Rule**

```
d(Loss)/d(Input) = 12 × 2 = 24
```

We can double check this directly. Since `Loss = (2 × Input)² = 4 × Input²`, the direct derivative is `8 × Input`, which at `Input = 3` gives `8 × 3 = 24` — the same answer. The Chain Rule reached it by breaking the problem into smaller, manageable steps instead of differentiating the whole expression at once.

---

### Python Example (Chain Rule via Autograd)

```python
import torch

x = torch.tensor(3.0, requires_grad=True)

prediction = 2 * x
loss = prediction ** 2

loss.backward()

print(x.grad)
```

Output

```
tensor(24.)
```

PyTorch computed this using the exact same chain of reasoning: it found `d(loss)/d(prediction)`, then `d(prediction)/d(x)`, and multiplied them together automatically.

---

## 8.6 Why Neural Networks Need the Chain Rule

Consider a small neural network.

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
Layer 3
   │
   ▼
Prediction
   │
   ▼
Loss
```

Every layer contains many weights.

Each weight influences the next layer.

The next layer influences the following layer.

Eventually, every weight affects the final loss.

Notice something important.

A weight does **not** affect the loss directly.

Its effect passes through many intermediate calculations.

The Chain Rule traces these dependencies and computes how much each weight contributed to the final error.

Modern language models contain:

- Millions of weights
- Billions of weights
- Even trillions of parameters

The Chain Rule makes it possible to compute gradients (a **gradient** is simply the derivative of the loss with respect to one specific weight) for every one of them efficiently.

---

## 8.7 PyTorch Autograd Preview

Manually computing derivatives for millions of parameters would be impossible.

Fortunately, deep learning frameworks perform these calculations automatically.

This feature is called **Automatic Differentiation**, or **Autograd**.

Example:

```python
import torch

x = torch.tensor(3.0, requires_grad=True)

y = x ** 2

y.backward()

print(x.grad)
```

Output

```
tensor(6.)
```

PyTorch automatically calculated:

```
d(x²)/dx = 2x

= 6
```

The important line is:

```python
y.backward()
```

This computes the derivatives of the output with respect to every parameter that requires gradients.

We will explore this mechanism in detail when we study backpropagation.

---

## 8.8 Transformer Connection

Let's connect this concept to a Transformer.

During training, the process looks like this:

```
Input Tokens
      │
      ▼
Embeddings
      │
      ▼
Attention Layers
      │
      ▼
Feed Forward Layers
      │
      ▼
Logits
      │
      ▼
Softmax
      │
      ▼
Cross Entropy Loss
```

The loss tells us how wrong the prediction is.

Now the model must determine:

- Which embedding caused the error?
- Which attention weight contributed most?
- Which feed-forward weight should change?
- By how much should each parameter be updated?

The Chain Rule answers these questions.

It propagates the influence of the loss backward through every operation in the network, allowing the model to compute gradients for every parameter.

Those gradients are then used to update the weights during training.

This backward flow of gradients is called **backpropagation**, which we will study in a later chapter.

---

## 8.9 Key Takeaways

- A derivative measures how quickly an output changes when the input changes.
- Derivatives represent the slope or rate of change of a function.
- Numerical derivatives estimate the slope using small changes in the input.
- Analytical derivatives provide the exact mathematical expression for the slope.
- Neural networks contain many interconnected operations, making direct derivative calculations impossible.
- The Chain Rule computes how each parameter indirectly affects the final loss by multiplying together the local derivatives of each step in the pipeline.
- A gradient is simply the derivative of the loss with respect to one specific weight.
- PyTorch's Autograd automatically calculates these derivatives using `backward()`.
- In Transformers, gradients computed using the Chain Rule are essential for learning and are later used by gradient descent to update the model's parameters.