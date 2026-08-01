# 6. Building Neural Networks

## Introduction

So far you've worked with tensors, tensor operations, broadcasting, and autograd — the raw materials. This chapter is where you start assembling those raw materials into an actual **neural network**: something that takes an input, transforms it through learnable layers, and produces an output.

By the end of this chapter you'll be able to build a small network from scratch, understand exactly what's happening inside each layer, and see how the exact same building block (`nn.Linear`) that powers a simple house-price predictor also powers every projection inside a Transformer.

---

## 6.1 What is a Neural Network?

A neural network is a stack of mathematical transformations — mostly matrix multiplications plus non-linear functions — chained together to turn an input into an output.

At its simplest:

```text
input → [Linear transformation] → [non-linearity] → [Linear transformation] → output
```

Each "Linear transformation" learns a weight matrix and a bias vector. The non-linearity (like ReLU) is what lets the network learn _curved_, complex relationships instead of just a straight line. Without a non-linearity, stacking 10 linear layers would collapse mathematically into just 1 linear layer — no extra power gained.

🧠 **Real-life example:** A house-price model takes `[square_footage, bedrooms, age]` as input, and produces a single number — predicted price — as output. Every layer in between is refining that guess.

---

## 6.2 What is `nn.Module`?

`nn.Module` is the base class that **every** model, and every individual layer, in PyTorch inherits from. `nn.Linear`, `nn.ReLU`, and any custom network you build are all `nn.Module` subclasses underneath.

It exists to solve a bookkeeping problem: once your network has weights scattered across many layers, you need an easy way to:

- collect **all** learnable parameters at once (`model.parameters()`), so an optimizer can update them
- move the **entire model** to a GPU in one call (`model.to(device)`)
- save and reload the model's weights (`model.state_dict()`)
- get a clean printout of the model's structure (`print(model)`)

You get all of this for free just by inheriting from `nn.Module` and registering your layers as attributes in `__init__`.

---

## 6.3 Your First Neural Network

The simplest possible "neural network" is a single `nn.Linear` layer — no hidden layers, no non-linearity yet.

```python
import torch
import torch.nn as nn

model = nn.Linear(4, 1)   # 4 input features -> 1 output value

x = torch.tensor([[1.0, 2.0, 3.0, 4.0]])
output = model(x)
print(output)
```

Output:

```text
tensor([[-2.3663]], grad_fn=<AddmmBackward0>)
```

That's it — a full "network" (one layer), taking a 4-feature input and producing 1 output. The weights are randomly initialized, so this number is meaningless right now; training (a later chapter) is what makes it useful. Notice the `grad_fn` in the output — that's autograd already tracking this computation, ready for backpropagation.

---

## 6.4 Understanding `nn.Linear`

`nn.Linear(in_features, out_features)` implements one equation:

```text
output = x @ W^T + b
```

- `x` — your input, shape `(batch_size, in_features)`
- `W` — the learnable weight matrix, shape `(out_features, in_features)`
- `b` — the learnable bias vector, shape `(out_features,)`

```python
linear = nn.Linear(in_features=3, out_features=2)
print(linear.weight.shape)   # torch.Size([2, 3])
print(linear.bias.shape)     # torch.Size([2])
```

Output:

```text
torch.Size([2, 3])
torch.Size([2])
```

Notice the weight shape is `(out_features, in_features)` — _transposed_ from what you might expect — which is exactly why the formula uses `W^T`.

🧠 **Real-life example:** In a spam classifier, `in_features` might be 100 (word-frequency counts), and `out_features` is 1 (spam probability logit). `nn.Linear(100, 1)` is the entire "decision layer."

---

## 6.5 What Happens Inside a Linear Layer?

`nn.Linear` isn't magic — it's exactly the matrix multiply + broadcasted bias-add from the last two chapters. Here's proof: manually replicating what `nn.Linear` does internally, using nothing but the weight and bias tensors it stores.

```python
torch.manual_seed(0)
linear = nn.Linear(3, 2)
x = torch.tensor([[1.0, 2.0, 3.0]])

out = linear(x)
manual_out = x @ linear.weight.T + linear.bias

print("linear(x):", out)
print("manual:   ", manual_out)
print("match:", torch.allclose(out, manual_out))
```

Output:

```text
linear(x): tensor([[-0.8219,  0.0526]], grad_fn=<AddmmBackward0>)
manual:    tensor([[-0.8219,  0.0526]], grad_fn=<AddBackward0>)
match: True
```

They're identical. `nn.Linear` is a thin, convenient wrapper around: initialize `W` and `b` randomly → do `x @ W^T + b` → track gradients automatically. Once this "clicks," every layer type you'll meet later (attention projections, feed-forward blocks) stops feeling like a black box.

---

## 6.6 Building a Model Using `nn.Module`

A real model needs more than one layer. You build one by subclassing `nn.Module`, declaring your layers in `__init__`, and defining how data flows through them in `forward`.

```python
class TinyNet(nn.Module):
    def __init__(self, in_features, hidden, out_features):
        super().__init__()               # REQUIRED — sets up nn.Module's internals
        self.layer1 = nn.Linear(in_features, hidden)
        self.relu = nn.ReLU()
        self.layer2 = nn.Linear(hidden, out_features)

    def forward(self, x):
        x = self.layer1(x)
        x = self.relu(x)
        x = self.layer2(x)
        return x

net = TinyNet(4, 8, 1)
print(net(x))
```

Output:

```text
tensor([[-0.1739]], grad_fn=<AddmmBackward0>)
```

Two things matter here: `super().__init__()` must be called first, or `nn.Module`'s internal parameter-tracking never gets set up (see Mistake 1 below). And every layer you assign as `self.something` gets automatically registered and discoverable via `.parameters()` — you never manually tell PyTorch "here are my weights."

---

## 6.7 The Forward Method

`forward()` defines the actual computation — the path data takes through your layers. But notice: in the example above, we called `net(x)`, **not** `net.forward(x)`.

This matters. `nn.Module` overrides Python's `__call__` method, and that override does extra work (running hooks, managing training/eval mode) _before and after_ calling your `forward()`. If you call `.forward(x)` directly, you skip all of that — it'll often still "work" in simple cases, but it's the wrong habit and will silently break more advanced features later (like hooks used in some debugging/observability tools). Always call the model like a function: `model(x)`.

---

## 6.8 Using `nn.Sequential`

When your layers just flow one into the next with no branching or custom logic, `nn.Sequential` is a shortcut that skips writing a full class:

```python
seq_net = nn.Sequential(
    nn.Linear(4, 8),
    nn.ReLU(),
    nn.Linear(8, 1)
)
print(seq_net(x))
```

Output:

```text
tensor([[-0.3373]], grad_fn=<AddmmBackward0>)
```

This is functionally identical to `TinyNet` above, just less code. Use `nn.Sequential` for simple, linear (no pun intended) stacks. Once you need conditional logic, multiple inputs, skip/residual connections, or reused sub-modules (all of which show up constantly in Transformers), you go back to a custom `nn.Module` class.

---

## 6.9 Model Parameters

Every weight and bias inside a model is a `Parameter` — a tensor with `requires_grad=True` by default, automatically registered because it was assigned as an attribute inside an `nn.Module`.

```python
total_params = sum(p.numel() for p in net.parameters())
print("Total trainable params:", total_params)

for name, p in net.named_parameters():
    print(name, p.shape, p.requires_grad)
```

Output:

```text
Total trainable params: 49
layer1.weight torch.Size([8, 4]) True
layer1.bias torch.Size([8]) True
layer2.weight torch.Size([1, 8]) True
layer2.bias torch.Size([1]) True
```

Check the math yourself: `layer1` has `8×4 = 32` weights `+ 8` biases `= 40`. `layer2` has `8×1 = 8` weights `+ 1` bias `= 9`. Total: `40 + 9 = 49`. ✅ This "count the parameters by hand" habit is exactly how people sanity-check "wait, why does this model have 7 billion parameters" claims about real LLMs.

`model.parameters()` (no names) is what you'll hand directly to an optimizer in the training-loop chapter: `optim.Adam(model.parameters(), lr=0.001)`.

---

## 6.10 Printing Model Information

Because every submodule is registered, `print(model)` gives you a full structural summary for free:

```python
print(net)
```

Output:

```text
TinyNet(
  (layer1): Linear(in_features=4, out_features=8, bias=True)
  (relu): ReLU()
  (layer2): Linear(in_features=8, out_features=1, bias=True)
)
```

This is the first thing to check when debugging a shape mismatch, and the first thing an interviewer or reviewer looks at when you share a model definition — it tells you the whole architecture at a glance without reading the `forward()` code.

---

## 6.11 Real-World Example

Putting it together: a small house-price predictor with two hidden layers.

```python
class HousePriceModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(3, 16),   # 3 features: size_sqft, num_bedrooms, age_years
            nn.ReLU(),
            nn.Linear(16, 8),
            nn.ReLU(),
            nn.Linear(8, 1)     # 1 output: predicted price
        )

    def forward(self, x):
        return self.net(x)

model = HousePriceModel()
houses = torch.tensor([
    [1200.0, 3.0, 5.0],   # 1200 sqft, 3 bed, 5 years old
    [2500.0, 4.0, 20.0],  # 2500 sqft, 4 bed, 20 years old
])
preds = model(houses)
print(preds)
```

Output:

```text
tensor([[3.1276],
        [6.3320]], grad_fn=<AddmmBackward0>)
```

🧠 Notice this is an _untrained_ model with random weights — the outputs are meaningless numbers, not real prices. That's expected and correct at this stage of the roadmap. This chapter is entirely about **architecture** (how data flows through layers); _training_ (the loop that makes these numbers meaningful, using the loss functions and optimizers from earlier chapters) is the next step.

---

## 6.12 Transformer Connection

Every piece introduced in this chapter reappears, almost unchanged, inside a Transformer:

- **Q/K/V projections** (Phase 2's core attention mechanism) are just three separate `nn.Linear` layers applied to the same input — exactly the `nn.Linear` from section 6.4, nothing more exotic.
- **The feed-forward network** inside every Transformer block is literally a `TinyNet`-style stack: `nn.Linear → activation (usually GELU, not ReLU) → nn.Linear`.
- **The output projection** that turns a Transformer's final hidden state into vocabulary-sized logits (before softmax) is one more `nn.Linear`.
- **`nn.Sequential`-style composition** is how you'll stack multiple identical Transformer blocks on top of each other later (e.g., 12 blocks for a small GPT).

🧠 **The honest takeaway:** a Transformer is not a different _kind_ of building block than what you built today — it's the same `nn.Linear` + non-linearity pattern, repeated with a very specific arrangement (attention, residual connections, normalization) and stacked many times. Section 6.5's "what happens inside a linear layer" exercise is the single most useful mental model to carry into Phase 2.

---

## 6.13 Common Beginner Mistakes

**Mistake 1 — forgetting `super().__init__()`** If you skip this in your model's `__init__`, `nn.Module`'s internal parameter tracking is never set up. Your code may not error immediately, but `model.parameters()` will be empty or broken, and training will silently fail to update anything.

**Mistake 2 — calling `.forward(x)` instead of `model(x)`** As covered in 6.7, this skips important internal `nn.Module` machinery. Always call the model like a function.

**Mistake 3 — mismatched `in_features`** `nn.Linear(in_features=10, ...)` fed a tensor with a last dimension of 8 throws a shape error. Always check `x.shape[-1]` matches the layer's expected `in_features` — this is the single most common error beginners hit.

**Mistake 4 — forgetting a non-linearity between layers** Stacking `nn.Linear(4,8)` directly into `nn.Linear(8,1)` with no activation in between is mathematically equivalent to just one `nn.Linear(4,1)` — you gain no extra representational power, just wasted compute. Always place an activation (ReLU, GELU, etc.) between stacked linear layers.

**Mistake 5 — assuming the model is "trained" just because it runs** A model that produces output without erroring is not the same as a model that produces _correct_ output. Random-initialized weights, as in section 6.11, will run fine and predict nonsense. Don't confuse "no error" with "working."

---

## 6.14 Key Takeaways

- A neural network is a stack of learnable linear transformations separated by non-linear activation functions.
- `nn.Module` is the base class that gives every model automatic parameter tracking, device management, and saving/loading.
- `nn.Linear` computes `x @ W^T + b` — nothing more mysterious than the matrix multiplication and broadcasting from earlier chapters.
- Build custom architectures by subclassing `nn.Module` and defining `forward()`; use `nn.Sequential` as a shortcut for simple, non-branching stacks.
- Always call a model as `model(x)`, never `model.forward(x)` directly.
- `model.parameters()` / `model.named_parameters()` expose every learnable weight — this is what an optimizer will later update, and what `print(model)` summarizes structurally.
- Every core piece of a Transformer (Q/K/V projections, feed-forward network, output projection) is built from exactly the same `nn.Linear` block introduced in this chapter — just arranged and stacked in a specific way you'll learn in Phase 2.