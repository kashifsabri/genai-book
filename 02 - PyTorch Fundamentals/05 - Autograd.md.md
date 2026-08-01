# 5. Autograd

## Introduction

In earlier chapters, we learned about **derivatives**, the **Chain Rule**, **Backpropagation**, and **Gradient Descent**.

We even computed gradients by hand and watched PyTorch compute a single gradient automatically using `loss.backward()`.

But we never stopped to ask:

> **How does PyTorch actually do this?**

The answer is **Autograd** — short for **Automatic Differentiation**.

Autograd is the engine running quietly behind every `.backward()` call we've used so far. It's what lets PyTorch compute exact derivatives for a network with millions or billions of parameters, without you ever writing a single derivative formula by hand.

This chapter opens up that engine and looks at how it actually works.

---

## 5.1 Why Do Neural Networks Need Gradients?

Recall the training cycle from earlier chapters:

```
Forward Pass → Prediction → Loss → Backpropagation → Gradients → Gradient Descent → Updated Weights
```

Every single weight in a network needs its own gradient — the answer to "if I nudge this one weight slightly, how does the loss change?" A modern language model can have billions of weights, each needing this answer, every single training step.

Computing all of that by hand, the way we did in the Derivatives and Backpropagation chapters, simply isn't possible at that scale. Autograd is what makes it possible.

---

## 5.2 What is Autograd?

**Autograd** is PyTorch's automatic differentiation engine.

While your code runs, Autograd silently:

1. Tracks every operation performed on tensors that require gradients.
2. Builds a **computational graph** connecting those operations.
3. Uses the **Chain Rule** to compute exact gradients for every tensor in that graph, the moment you call `.backward()`.

PyTorch builds this graph dynamically, one operation at a time, as your code actually executes — this is sometimes called a **define-by-run** graph. This is different from older frameworks that required you to define the entire computation graph in advance before running any data through it. It's also why you can use ordinary Python control flow (loops, `if` statements) inside a PyTorch model and Autograd will still track everything correctly.

---

## 5.3 Computational Graph

We first saw computational graphs in the Backpropagation chapter. Autograd is the mechanism that actually builds and uses one.

Consider:

```python
import torch

x = torch.tensor(3.0, requires_grad=True)
y = x ** 2
```

Autograd builds this graph as the code runs:

```
x
 │
 ▼
Power (² )
 │
 ▼
y
```

Each node remembers:

- What operation produced it
- Which tensors were its inputs
- How to compute the local derivative of that operation

When `y.backward()` is called, Autograd walks this graph backward, multiplying local derivatives together via the Chain Rule — exactly the process described in the Backpropagation chapter, just now automated.

---

## 5.4 requires_grad

By default, PyTorch tensors do **not** track gradients — this keeps ordinary tensor math fast and lightweight.

```python
import torch

x = torch.tensor(3.0)
print(x.requires_grad)
```

Output:

```text
False
```

To tell Autograd "track operations on this tensor," set `requires_grad=True`:

```python
x = torch.tensor(3.0, requires_grad=True)
print(x.requires_grad)
```

Output:

```text
True
```

You can also toggle it on an existing tensor using the in-place method `requires_grad_()`:

```python
x = torch.tensor(3.0)
x.requires_grad_(True)
print(x.requires_grad)
```

Output:

```text
True
```

> **Note:** A tensor created directly like this, with no operations behind it, is called a **leaf tensor** — it sits at the very start of the computational graph. Model parameters (weights and biases) are leaf tensors.

---

## 5.5 Forward Pass

The **forward pass** is simply running your normal computation — and it's also the moment Autograd builds the graph.

```python
import torch

x = torch.tensor(3.0, requires_grad=True)
w = torch.tensor(2.0, requires_grad=True)

y = x * w

print(y)
```

Output:

```text
tensor(6., grad_fn=<MulBackward0>)
```

Notice the `grad_fn=<MulBackward0>` in the output. This is Autograd's way of showing that `y` isn't just a plain number — it remembers it was produced by a multiplication, and it knows how to compute the local derivative of that multiplication when the time comes.

---

## 5.6 Backward Pass (.backward())

The **backward pass** is where Autograd actually computes gradients, by walking the graph in reverse.

```python
import torch

x = torch.tensor(3.0, requires_grad=True)
w = torch.tensor(2.0, requires_grad=True)

y = x * w
y.backward()

print(x.grad)
print(w.grad)
```

Output:

```text
tensor(2.)
tensor(3.)
```

This matches simple calculus: since `y = x * w`, we know `dy/dx = w = 2` and `dy/dw = x = 3`.

> **Important:** `.backward()` only works directly on a **scalar** (a single number), which is why `loss` is always reduced to one value before calling `loss.backward()`. Calling `.backward()` on a tensor with multiple values requires manually passing a matching gradient tensor — a more advanced technique you won't need for ordinary model training, since your loss is essentially always a single number.

---

## 5.7 Accessing Gradients (.grad)

Once `.backward()` has run, every leaf tensor that required gradients now has its result stored in `.grad`.

```python
print(x.grad)
```

Output:

```text
tensor(2.)
```

Before `.backward()` is called, `.grad` is simply `None` — there's nothing to show yet.

```python
x2 = torch.tensor(5.0, requires_grad=True)
print(x2.grad)
```

Output:

```text
None
```

---

## 5.8 Gradient Accumulation

Here's a behavior that surprises many beginners: **Autograd does not overwrite `.grad` — it adds to it.**

```python
import torch

x = torch.tensor(3.0, requires_grad=True)

y1 = x ** 2
y1.backward()
print(x.grad)

y2 = x ** 2
y2.backward()
print(x.grad)
```

Output:

```text
tensor(6.)
tensor(12.)
```

Let's check the math. Since `y = x²`, the derivative is `dy/dx = 2x`. At `x = 3`, that's `2 × 3 = 6`. The first `.backward()` correctly stores `6`. But the second `.backward()` doesn't replace that `6` — it **adds** another `6` to it, giving `12`.

This is intentional. In some training setups, you actually want gradients from multiple mini-batches to accumulate before updating the weights. But in a normal training loop, this behavior means gradients from the _previous_ step will silently leak into the _current_ step unless you clear them out — which is exactly what the next section is for.

---

## 5.9 optimizer.zero_grad()

Because gradients accumulate by default, every standard PyTorch training loop must **clear the gradients** at the start of each step, before calling `.backward()` again.

```python
import torch

w = torch.tensor(3.0, requires_grad=True)
x = torch.tensor(2.0)
target = 8.0

optimizer = torch.optim.SGD([w], lr=0.01)

for step in range(2):
    optimizer.zero_grad()          # clear old gradients

    pred = w * x
    loss = (pred - target) ** 2

    loss.backward()                # compute new gradients
    optimizer.step()               # update weights using the gradients

    print(f"Step {step+1}: w = {w.item():.4f}, loss = {loss.item():.4f}")
```

Output:

```text
Step 1: w = 3.0800, loss = 4.0000
Step 2: w = 3.1536, loss = 3.3856
```

Forgetting `optimizer.zero_grad()` is one of the most common bugs in PyTorch training code — the model will often still appear to train, just incorrectly, since it's silently using contaminated gradients from previous steps.

---

## 5.10 torch.no_grad()

Sometimes you want to run a tensor through your model **without** building a computational graph at all — for example, during evaluation or inference, when you'll never call `.backward()`.

```python
import torch

x = torch.tensor(3.0, requires_grad=True)

with torch.no_grad():
    y = x * 2

print(y.requires_grad)
```

Output:

```text
False
```

Even though `x` requires gradients, anything computed inside a `torch.no_grad()` block does not. This saves memory and computation, because Autograd doesn't waste effort recording operations it will never need to differentiate.

---

## 5.11 detach()

`detach()` creates a new tensor that **shares the same data** as the original but is completely disconnected from the computational graph.

```python
import torch

x = torch.tensor(3.0, requires_grad=True)
y = x.detach()

print(y.requires_grad)
```

Output:

```text
False
```

This is useful when you want to use a tensor's _value_ — for logging, plotting, or converting to NumPy — without dragging its entire gradient history along with it.

---

## 5.12 Real-World Example

Let's put everything in this chapter together and watch a single weight actually improve over two training steps — this time letting Autograd do all the derivative work instead of computing it by hand as we did in the Gradient Descent chapter.

```python
import torch

x = torch.tensor(2.0)
w = torch.tensor(3.0, requires_grad=True)
target = 8.0
lr = 0.01

# --- Step 1 ---
pred = x * w
loss = (pred - target) ** 2
loss.backward()

print("Gradient:", w.grad)

with torch.no_grad():
    w -= lr * w.grad
w.grad.zero_()

print("Updated weight:", w)

# --- Step 2 ---
pred = x * w
loss = (pred - target) ** 2
loss.backward()

print("Gradient:", w.grad)

with torch.no_grad():
    w -= lr * w.grad
w.grad.zero_()

print("Updated weight:", w)
```

Output:

```text
Gradient: tensor(-8.)
Updated weight: tensor(3.0800, requires_grad=True)
Gradient: tensor(-7.3600)
Updated weight: tensor(3.1536, requires_grad=True)
```

This should look familiar — it's the exact same `x = 2, w = 3, target = 8` setup from the Backpropagation chapter, extended one step further. There, we stopped after computing the gradient (`-8`). Here, we actually apply it: `w` moves from `3.0` to `3.08`, and by the second step, the loss has already dropped from `4.0` to `3.3856` (as we saw in section 5.9). We also called `w.grad.zero_()` manually between steps — this is the raw version of what `optimizer.zero_grad()` does automatically.

---

## 5.13 Transformer Connection

Every Transformer training step relies entirely on Autograd.

```
Input Tokens → Embeddings → Attention Layers → Feed Forward Layers → Logits → Softmax → Cross Entropy Loss
                                                                                              │
                                                                                      loss.backward()
                                                                                              │
                                                                                              ▼
                                                                              Autograd computes gradients                                                        for every parameter in the model
```

Inside a large language model, `requires_grad=True` is set on every trainable parameter — every attention weight, every feed-forward weight, every embedding value. When `loss.backward()` is called after a single forward pass, Autograd walks backward through the entire computational graph — potentially involving billions of parameters and many layers — and computes the exact gradient for each one, automatically.

This is the same mechanism you used in section 5.12 with a single weight. A Transformer just repeats it at a massive scale.

---

## 5.14 Common Beginner Mistakes

### Mistake 1 — Forgetting `zero_grad()`

As shown in section 5.8, gradients accumulate by default. Skipping `optimizer.zero_grad()` (or `w.grad.zero_()`) means each step's gradient gets added on top of the last, quietly corrupting training.

---

### Mistake 2 — Calling `.backward()` twice on the same graph

```python
y.backward()
y.backward()  # ❌ RuntimeError
```

By default, PyTorch frees the computational graph after `.backward()` is called, to save memory. Calling it a second time on the same `y` raises an error unless you pass `retain_graph=True` — an option needed only in specific advanced cases.

---

### Mistake 3 — Calling `.backward()` on a non-scalar

```python
y = torch.tensor([1.0, 2.0, 3.0], requires_grad=True) * 2
y.backward()  # ❌ RuntimeError: grad can be implicitly created only for scalar outputs
```

As noted in section 5.6, `.backward()` needs a single number to start from. If your output has multiple values (for example, a loss computed per-sample instead of averaged), reduce it to a scalar first — usually with `.mean()` or `.sum()`.

---

### Mistake 4 — In-place operations on tensors that require grad

```python
x = torch.tensor(3.0, requires_grad=True)
x += 1  # ❌ can raise an error depending on context
```

In-place operations can overwrite values that Autograd still needs for the backward pass, which is why PyTorch often blocks them on tensors that require gradients. Prefer creating a new tensor (`x = x + 1`) instead.

---

## 5.15 Key Takeaways

- Autograd is PyTorch's automatic differentiation engine — it's what actually powers every `.backward()` call.
- PyTorch builds a computational graph dynamically as your code runs (define-by-run), tracking every operation on tensors with `requires_grad=True`.
- `.backward()` walks that graph in reverse, applying the Chain Rule to compute exact gradients.
- Gradients are stored in `.grad` and, importantly, **accumulate** across multiple `.backward()` calls unless manually cleared.
- `optimizer.zero_grad()` (or `tensor.grad.zero_()`) must be called at the start of each training step to avoid using stale gradients.
- `torch.no_grad()` and `.detach()` both let you work with tensor values without building or keeping a computational graph — useful for inference, logging, and evaluation.
- Every Transformer and every large language model relies on exactly this mechanism, just scaled up to billions of parameters.
- **What's covered here is genuinely enough** to train real models, including Transformers. Deeper Autograd topics — custom `autograd.Function` subclasses, Jacobian/Hessian computation, higher-order gradients — belong to framework-level research and are not needed to understand or build language models at the level this book covers.