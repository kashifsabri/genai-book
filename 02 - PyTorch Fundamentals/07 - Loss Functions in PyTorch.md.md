# Chapter 7: Loss Functions in PyTorch

## Introduction

In the Machine Learning Foundations section, we already learned what a loss function _is_: a single number that tells us how wrong a model's prediction was, shrinking toward zero as the model improves. We won't repeat that theory here.

This chapter answers a narrower, more practical question — one every PyTorch beginner runs into within their first hour of real code: **how do we actually use loss functions in PyTorch?** By the time we're done, you'll know which loss function to reach for, how to call it correctly, which mistakes to avoid, and — most importantly — why one specific loss function, `CrossEntropyLoss`, sits at the heart of every large language model you'll build later in this book.

---

## 7.1 Why Do We Need Loss Functions in PyTorch?

Left on its own, a neural network just produces numbers. It has no built-in sense of whether those numbers are good or bad — that judgment has to come from somewhere else. That "somewhere else" is the loss function: it takes the model's prediction and the correct answer, and turns the gap between them into a single scalar the rest of the training pipeline can act on.

PyTorch ships a set of ready-made loss functions inside `torch.nn`, so you're never expected to derive and implement this math by hand. In the same way `nn.Linear` in the previous chapter spared you from writing out `x @ W.T + b` yourself, `nn.MSELoss()` and `nn.CrossEntropyLoss()` spare you from writing loss formulas from scratch — while quietly making sure autograd can differentiate through them correctly, ready for the backward pass.

---

## 7.2 Recap: What is a Loss Function?

Just a sentence, since you've already met this idea: a loss function takes a prediction and a target, and returns a large number when the model is wrong and a small one when the model is right. Training, at its core, is the process of nudging weights — step by step, using the chain rule from Phase 0 — to make that number smaller.

The one genuinely new idea in this chapter is that **which** loss function you reach for depends entirely on what kind of output your model produces.

---

## 7.3 Using `nn.MSELoss()`

Mean Squared Error is PyTorch's standard loss for _regression_ problems — predicting a continuous number, like a price, a temperature, or an age.

```python
import torch
import torch.nn as nn

criterion = nn.MSELoss()

prediction = torch.tensor([2.5])
target = torch.tensor([3.0])

loss = criterion(prediction, target)
print(loss)
```

```text
tensor(0.2500)
```

That output is simply `(2.5 − 3.0)² = 0.25`. There's no hidden complexity here — MSE really is just squared error, dressed up as a PyTorch object so it plugs cleanly into autograd.

---

## 7.4 Understanding Mean Squared Error

The name tells you exactly what's happening: the **Mean** of the **Squared** **Error**. With a single prediction, as in the last example, "the mean of one number" is just that number, which is why nothing about averaging was visible yet. Give it several predictions at once, and the averaging becomes real:

```python
pred = torch.tensor([2.5, 5.0, 1.0])
target = torch.tensor([3.0, 4.0, 1.5])

loss = criterion(pred, target)
manual = ((pred - target) ** 2).mean()

print(loss)
print(manual)
```

```text
tensor(0.5000)
tensor(0.5000)
```

`nn.MSELoss()` computes `(pred − target)²` element by element, then averages across the batch — and the manual calculation alongside it confirms there's nothing hidden in that call. Squaring the error does two things worth noticing: it makes every error positive, so a `+2` miss and a `−2` miss don't cancel each other out, and it punishes large mistakes disproportionately — a house-price model that's off by $100,000 is penalized far more severely than ten separate models each off by $10,000, not merely ten times as much.

---

## 7.5 Understanding the `reduction` Parameter

Most PyTorch loss functions — `MSELoss` included — accept a parameter called `reduction`, and it's worth understanding before we move any further, because it quietly shapes everything we've just computed.

```python
criterion = nn.MSELoss(reduction="mean")
```

Three options are available:

|`reduction` value|Behavior|
|---|---|
|`"mean"` (default)|Averages the loss across all elements|
|`"sum"`|Adds up the loss across all elements|
|`"none"`|Returns the individual loss for each element, unreduced|

Setting `reduction="none"` is the clearest way to see what's happening underneath the single number we've been printing so far:

```python
prediction = torch.tensor([2.5, 5.0])
target = torch.tensor([3.0, 4.0])

criterion = nn.MSELoss(reduction="none")
print(criterion(prediction, target))
```

```text
tensor([0.2500, 1.0000])
```

Each element gets its own squared error — `0.25` for the first pair, `1.00` for the second — with no averaging or summing applied. Switch `reduction` to `"sum"` and you'd get `1.25`; switch it to `"mean"` (the default) and you'd get `0.625`, the average of those two values. In the overwhelming majority of real training code, the default `"mean"` is exactly what you want, since it keeps the loss's scale independent of batch size. You'll mostly meet `"none"` when debugging — inspecting which _specific_ examples in a batch the model is struggling with — and `"sum"` in a handful of specialized training setups where per-example averaging isn't desired.

---

## 7.6 Using `nn.CrossEntropyLoss()`

This is the most important loss function in the entire book. It's what trains every classifier we'll build, and — as we'll see shortly — it's what trains every large language model in existence.

```python
import torch
import torch.nn as nn

criterion = nn.CrossEntropyLoss()

logits = torch.tensor([[2.5, 0.3, 1.2]])   # raw model output, 3 possible classes
target = torch.tensor([0])                  # the correct class is index 0

loss = criterion(logits, target)
print(loss)
```

```text
tensor(0.3245)
```

Here is the single most important detail in this chapter, and one worth committing to memory before we go any further: **`CrossEntropyLoss` expects raw logits, not probabilities.** PyTorch applies softmax internally, on your behalf. We can prove this to ourselves rather than take it on faith:

```python
probs = torch.softmax(logits, dim=1)
manual_loss = -torch.log(probs[0, target[0]])
print(manual_loss)
```

```text
tensor(0.3245)
```

It matches exactly. What PyTorch is computing under the hood is `-log(softmax(logits)[correct_class])` — softmax first converts the raw logits into a proper probability distribution (the same softmax you met back in Phase 0), and cross-entropy then takes the negative log of whatever probability landed on the correct answer. A model that's highly confident _and_ correct assigns a probability near 1 to the right class, and `-log(≈1)` is nearly zero — a tiny loss. A model that's unsure, or confidently wrong, assigns a small probability to the right answer, and `-log(small number)` grows large.

This is exactly where the most common beginner mistake in this entire book lives. It's tempting — reasonably so — to write:

```python
probabilities = torch.softmax(logits, dim=1)
loss = criterion(probabilities, target)   # ❌ wrong
```

```text
tensor(0.7499)
```

Notice this doesn't raise an error. It runs, produces a plausible-looking number, and is _silently wrong_ — softmax gets applied a second time on top of PyTorch's internal softmax, distorting the distribution and handing your model a misleading signal to learn from. This is more dangerous than a crash, precisely because nothing tells you it happened.

The correct call is simply:

```python
loss = criterion(logits, target)   # ✅ pass raw logits directly
```

---

## 7.7 Why is it Called Cross Entropy?

Readers reliably ask this, so let's answer it briefly.

The name comes from information theory. Cross entropy measures how different the model's predicted probability distribution is from the true, correct distribution — where "correct" means: all the probability mass sitting on the one right answer, and none on the others. The closer the model's predicted distribution gets to that correct distribution, the smaller the loss becomes.

You don't need the full mathematics of information theory to use this loss function well. For now, it's enough to carry forward one sentence: **CrossEntropyLoss measures how far the model's predicted probabilities are from the correct answer.**

---

## 7.8 Why CrossEntropyLoss Is Used for Classification

|`MSELoss`|`CrossEntropyLoss`|
|---|---|
|Regression|Classification|
|Continuous values|Discrete class labels|
|e.g. house prices|e.g. cat vs. dog|
|e.g. predicting a temperature|e.g. predicting the next word|

The intuition behind this split is worth sitting with. MSE measures _distance_ between two numbers, and that only makes sense when distance is a meaningful concept — predicting 3.2 instead of 3.0 is a small miss, predicting 3.2 instead of 5.0 is a larger one. Class labels don't behave this way. "Cat" isn't numerically closer to "dog" than it is to "car" — the categories have no inherent order or distance between them. CrossEntropyLoss was built for exactly this situation: it measures how much probability mass the model placed on the _correct_ class, without assuming the classes have any numeric relationship to one another.

Consider an email classifier choosing between `spam`, `promotions`, `primary`, and `social`. These four categories aren't ordered along any meaningful scale — "primary" isn't mathematically "between" spam and promotions. CrossEntropyLoss handles this correctly. MSE, applied naively here, would implicitly (and wrongly) treat the class indices as if they carried numeric meaning.

---

## 7.9 Other Common Loss Functions

`MSELoss` and `CrossEntropyLoss` will cover the overwhelming majority of what we do in this book, but PyTorch provides a much larger library of loss functions, and it's worth knowing a few of them by name even if we won't dwell on them.

|Loss Function|Used For|
|---|---|
|`nn.MSELoss()`|Regression|
|`nn.CrossEntropyLoss()`|Multi-class classification|
|`nn.BCEWithLogitsLoss()`|Binary classification|
|`nn.L1Loss()`|Absolute-error regression|

`BCEWithLogitsLoss` is worth a brief mention on its own: it's the natural choice whenever there are exactly two classes — spam or not spam, fraud or not fraud — and, like `CrossEntropyLoss`, it expects raw logits rather than pre-computed probabilities, applying a sigmoid internally rather than a softmax. `L1Loss` is a close cousin of `MSELoss`, using absolute error instead of squared error, which makes it less sensitive to occasional large outliers in the data.

In this book, we'll mainly work with `CrossEntropyLoss`, since it's the loss function that matters most for the large language models we're building toward.

---

## 7.10 Why LLMs Use CrossEntropyLoss

This is the direct line to large language models, and it's worth slowing down for.

When an LLM predicts the next word, it is — despite how sophisticated the result feels — doing plain classification: choosing one word out of a fixed vocabulary of tens of thousands of options. That single fact is the entire reason `CrossEntropyLoss` sits underneath GPT, Llama, Mistral, Qwen, Gemma, and effectively every autoregressive language model in existence.

```python
vocab = ["coffee", "tea", "water", "juice"]

# The model's raw logits for: "I love to drink ___"
logits = torch.tensor([[1.8, 0.9, -0.2, -0.5]])
correct_word_index = torch.tensor([0])   # "coffee" is correct

probs = torch.softmax(logits, dim=1)
for word, p in zip(vocab, probs[0]):
    print(f"{word:8s} {p.item():.2f}")

loss = criterion(logits, correct_word_index)
print("Loss:", loss.item())
```

```text
coffee   0.61
tea      0.25
water    0.08
juice    0.06
Loss: 0.496
```

Now compare that against a model that guessed poorly — leaning toward "water" instead of "coffee":

```python
bad_logits = torch.tensor([[0.1, 0.2, 1.5, 0.3]])
bad_loss = criterion(bad_logits, correct_word_index)
print("Loss:", bad_loss.item())
```

```text
Loss: 1.999
```

The wrong prediction produces roughly four times the loss of the good one — 1.999 against 0.496 — and that gap is precisely the signal the rest of the training pipeline needs. Zoomed out, the full cycle looks like this:

```text
Model predicts probabilities over the entire vocabulary
        │
        ▼
CrossEntropyLoss compares the prediction against the actual next word
        │
        ▼
Autograd computes gradients, via the chain rule from Phase 0
        │
        ▼
The optimizer updates the weights
        │
        ▼
Repeat, one token at a time, billions of times over
```

That loop, run at enormous scale, is what "pretraining a large language model" actually means. There is no separate, more exotic loss function reserved for "understanding language." It's the same `CrossEntropyLoss` from section 7.6, applied one next-word prediction at a time, across a training set spanning a meaningful fraction of the internet.

---

## 7.11 Debugging Tip

When something goes wrong with `CrossEntropyLoss` — and at some point, it will — resist the urge to suspect the mathematics first. Check the shapes.

```python
print(logits.shape)
print(target.shape)
```

```text
torch.Size([1, 3])
torch.Size([1])
```

The overwhelming majority of errors reported against `CrossEntropyLoss` in the wild trace back to a shape mismatch — a missing batch dimension, a target left as a one-hot vector instead of a class index, logits accidentally passed through softmax first — rather than any flaw in the underlying math. Make shape-checking your first reflex, and you'll resolve most of these in seconds instead of minutes.

---

## 7.12 Common Beginner Mistakes

**Mistake 1 — applying softmax before `CrossEntropyLoss`.** Covered in section 7.6: this silently double-applies softmax and produces a plausible but wrong loss value. Always pass raw logits.

**Mistake 2 — using the wrong target shape.** The standard `CrossEntropyLoss` API expects class _indices_, not one-hot vectors:

```python
target = torch.tensor([2])        # ✅ correct — a class index
target = torch.tensor([[0,0,1]])  # ❌ wrong — a one-hot encoding
```

If your labels already exist as one-hot vectors, convert them to indices first with `target.argmax(dim=1)` before handing them to the loss function.

**Mistake 3 — using MSE for a classification problem.** Nothing will crash — `nn.MSELoss()` will run happily against class-like outputs — but it will optimize entirely the wrong thing, for the reasons laid out in section 7.8. Whenever the output is "which one of N categories," reach for `CrossEntropyLoss`, not `MSELoss`.

**Mistake 4 — forgetting the batch dimension.** `CrossEntropyLoss` expects logits shaped `(batch_size, num_classes)` and targets shaped `(batch_size,)`. Even a single example needs to be wrapped in a batch dimension of size one — `torch.tensor([2.5, 0.3, 1.2])`, one-dimensional, will raise an error where `torch.tensor([[2.5, 0.3, 1.2]])`, two-dimensional, works, exactly as it appears throughout this chapter's examples.

---

## 7.13 Key Takeaways

- PyTorch provides ready-made loss functions inside `torch.nn`, so loss math rarely needs to be implemented by hand.
- `nn.MSELoss()` is for regression: continuous-valued targets, measured as squared distance.
- The `reduction` parameter (`"mean"`, `"sum"`, or `"none"`) controls how per-element losses are combined; `"mean"` is the default and the right choice for nearly all training code.
- `nn.CrossEntropyLoss()` is for classification: discrete class labels, measured as how much probability mass landed on the correct class.
- `CrossEntropyLoss` expects raw logits, not probabilities — it applies softmax internally. Passing already-softmaxed values is a silent, hard-to-spot bug rather than a crash.
- The name "cross entropy" comes from information theory: it measures the distance between the model's predicted distribution and the correct one.
- Other common losses worth knowing by name: `BCEWithLogitsLoss` for binary classification, `L1Loss` for absolute-error regression.
- When `CrossEntropyLoss` misbehaves, check tensor shapes first — most errors trace back to shape mismatches, not faulty mathematics.
- Predicting the next word in a language model is a classification problem over the entire vocabulary — which is precisely why GPT, Llama, Mistral, Qwen, and Gemma are all trained with variants of `CrossEntropyLoss`.

---

By the end of this chapter, the training pipeline is starting to take its full shape:

```text
Training Data
      │
      ▼
Neural Network
      │
      ▼
Prediction (Logits)
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

In the next chapter, we'll study **Optimizers**, which take the gradients Autograd has computed and use them to update the model's parameters — gradually, one step at a time — reducing the loss we've spent this whole chapter learning to measure.