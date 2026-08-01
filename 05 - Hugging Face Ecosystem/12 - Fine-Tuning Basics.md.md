# 12. Fine-Tuning Basics

## Introduction

So far, we've been using **pretrained models** from the Hugging Face Hub.

These models already know a lot about language because they were trained on massive amounts of text.

But sometimes, a pretrained model is not enough.

For example, suppose you want a model that understands:

- Medical reports
- Legal documents
- Company emails
- Customer support tickets

In these cases, you can improve the model by training it on your own data.

This process is called **Fine-Tuning**.

---

# 12.1 What is Fine-Tuning?

**Fine-Tuning** means taking a pretrained model and training it further on a smaller, task-specific dataset.

Instead of starting from scratch, the model builds on the knowledge it already has.

Example:

```text
Pretrained Model

↓

Fine-Tune on Your Dataset

↓

Custom AI Model
```

---

# 12.2 Why Do We Fine-Tune?

Imagine hiring an experienced chef.

The chef already knows how to cook.

Now you teach them your restaurant's special recipes.

You don't teach them cooking from the beginning.

You only teach what is unique to your restaurant.

Fine-tuning works in the same way.

The model already understands language.

You only teach it your specific task.

---

# 12.3 Pretraining vs Fine-Tuning

| Pretraining | Fine-Tuning |
|-------------|-------------|
| Learns general language | Learns a specific task |
| Uses huge datasets | Uses smaller datasets |
| Takes weeks or months | Usually takes hours or days |
| Done by organizations like OpenAI, Google, Meta | Done by developers and companies |

---

# 12.4 Fine-Tuning Workflow

The process is simple.

```text
Choose a Pretrained Model
          │
          ▼
Prepare Your Dataset
          │
          ▼
Fine-Tune the Model
          │
          ▼
Save the Model
          │
          ▼
Use the Model
```

This is the standard workflow followed in most AI projects.

---

# 12.5 Example

Suppose you want to classify customer emails.

General model:

```text
Email

↓

Understands English
```

After fine-tuning:

```text
Customer Email

↓

Complaint

Suggestion

Question

Feedback
```

The model becomes much better at your specific task.

---

# 12.6 Do We Train the Entire Model?

Sometimes yes.

Sometimes no.

Many modern techniques update only a small part of the model.

Examples include:

- LoRA (Low-Rank Adaptation)
- QLoRA
- Adapters

These methods reduce memory usage and training time.

We'll study them in a later section.

---

# 12.7 Training vs Inference

It is important to understand the difference.

### Training

The model learns from data.

```text
Dataset

↓

Model

↓

Update Weights
```

---

### Inference

The model only makes predictions.

```text
Input

↓

Model

↓

Prediction
```

When using ChatGPT or most AI applications, you're performing **Inference**, not training.

---

# 12.8 Real-World Example

Imagine learning to drive.

First, you learn the general rules of driving.

Later, you learn how to drive a bus.

You don't start learning from zero.

You build on your existing knowledge.

Fine-tuning works in exactly the same way.

---

# Transformer Connection

The complete workflow now looks like this.

```text
Pretrained Model
        │
        ▼
Fine-Tune
        │
        ▼
Custom Model
        │
        ▼
Save Model
        │
        ▼
Deploy
```

This is how companies build AI models for their own business needs.

---

# Common Beginner Mistakes

### Mistake 1

Thinking fine-tuning means training a model from scratch.

It doesn't.

The model already knows general language.

---

### Mistake 2

Thinking every project requires fine-tuning.

Many applications work well using pretrained models without any additional training.

---

### Mistake 3

Thinking fine-tuning always requires huge GPUs.

Modern techniques like LoRA and QLoRA make fine-tuning much more efficient.

---

# Key Takeaways

- Fine-tuning means training a pretrained model on a specific dataset.
- It is much faster than training a model from scratch.
- Fine-tuning helps a model perform better on domain-specific tasks.
- Training updates the model's weights, while inference only makes predictions.
- Modern techniques like LoRA and QLoRA make fine-tuning more efficient.