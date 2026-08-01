# 1. AI vs Machine Learning vs Deep Learning

## Introduction

Artificial Intelligence (AI), Machine Learning (ML), and Deep Learning (DL) are often used interchangeably, but they are **not the same thing**.

Think of them as a family.

- **Artificial Intelligence** is the biggest field.
- **Machine Learning** is a part of Artificial Intelligence.
- **Deep Learning** is a part of Machine Learning.

Every Deep Learning system is a Machine Learning system, and every Machine Learning system belongs to the broader field of Artificial Intelligence.

---

## What is Artificial Intelligence (AI)?

**Artificial Intelligence (AI)** is the ability of a computer to perform tasks that normally require human intelligence.

These tasks include:

- Understanding language
- Recognizing images
- Solving problems
- Making decisions
- Playing games

The goal of AI is to make machines behave intelligently.

Examples:

- ChatGPT answering questions
- Google Maps finding the fastest route
- Siri and Google Assistant
- Self-driving cars

AI is the broadest field.

---

## What is Machine Learning (ML)?

**Machine Learning (ML)** is a branch of Artificial Intelligence.

Instead of writing rules for every situation, we give the computer **data**, and it learns patterns from that data.

Example:

Suppose we want to detect spam emails.

Instead of writing rules like:

```text
If email contains "Congratulations"
then mark it as spam.
```

we give the computer thousands of emails.

```text
Email 1 → Spam

Email 2 → Not Spam

Email 3 → Spam

Email 4 → Not Spam
```

The model learns the patterns by itself.

The more good-quality data it sees, the better it becomes.

---

## What is Deep Learning (DL)?

**Deep Learning (DL)** is a branch of Machine Learning.

Instead of using simple mathematical models, Deep Learning uses **neural networks** with many layers.

These neural networks are inspired by the way neurons are connected in the human brain.

Deep Learning is especially good at learning from large amounts of data.

It is widely used for:

- Image recognition
- Speech recognition
- Language translation
- Large Language Models (LLMs)

Examples include:

- ChatGPT
- Gemini
- Claude
- Llama

---

## AI vs ML vs DL

Think of them as three circles.

```text
Artificial Intelligence
        │
        ▼
Machine Learning
        │
        ▼
Deep Learning
```

Or think of them like this:

```text
AI
└── Machine Learning
      └── Deep Learning
```

Deep Learning is a subset of Machine Learning, and Machine Learning is a subset of Artificial Intelligence.

---

## Real-World Example

Imagine you want to build a system that identifies cats in images.

### Artificial Intelligence

The overall goal is:

> Build a computer that can recognize cats.

### Machine Learning

The computer learns by looking at thousands of labeled cat images.

### Deep Learning

A neural network automatically learns the important features of a cat, such as its ears, eyes, and shape, without manually programming those rules.

---

## Why Deep Learning Became So Popular

Traditional Machine Learning often requires humans to manually choose useful features from the data.

Deep Learning learns these features automatically.

This makes it much more powerful for solving complex problems involving images, speech, and language.

Today, almost every modern Large Language Model is built using Deep Learning.

---

## Transformer Connection

Transformers are a type of **Deep Learning model**.

When you use ChatGPT or another LLM, you're interacting with a Deep Learning model that has been trained on enormous amounts of text.

Understanding the relationship between AI, Machine Learning, and Deep Learning helps you see where Transformers fit into the bigger picture.

---

## Key Takeaways

- Artificial Intelligence (AI) is the broad field of building intelligent machines.
- Machine Learning (ML) is a branch of AI where computers learn from data.
- Deep Learning (DL) is a branch of ML that uses neural networks with many layers.
- Every Deep Learning model is a Machine Learning model.
- Every Machine Learning model belongs to Artificial Intelligence.
- Modern Large Language Models are built using Deep Learning.