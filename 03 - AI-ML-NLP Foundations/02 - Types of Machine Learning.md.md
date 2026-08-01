# 2. Types of Machine Learning

## Introduction

In the previous chapter, we learned the difference between **Artificial Intelligence (AI)**, **Machine Learning (ML)**, and **Deep Learning (DL)**.

Now we'll learn **how Machine Learning models learn**.

Machine Learning is usually divided into three main types:

- Supervised Learning
- Unsupervised Learning
- Reinforcement Learning

Each type learns in a different way and is used to solve different kinds of problems.

---

## 2.1 Supervised Learning

In **Supervised Learning**, the model learns from **labeled data**.

A label is simply the correct answer.

For example, suppose we want to build a spam email detector.

Our training data might look like this:

| Email | Label |
|--------|-------|
| "You won a prize!" | Spam |
| "Meeting at 3 PM" | Not Spam |
| "Claim your reward now!" | Spam |
| "Project report attached" | Not Spam |

The model looks at the emails and their correct labels.

Over time, it learns the patterns that separate spam emails from normal emails.

After training, it can classify new emails that it has never seen before.

### Examples

- Spam detection
- House price prediction
- Medical diagnosis
- Fraud detection
- Image classification

---

## 2.2 Unsupervised Learning

In **Unsupervised Learning**, the data has **no labels**.

The model receives only the input data and tries to discover patterns on its own.

For example, imagine a shopping website with thousands of customers.

The data may include:

- Age
- City
- Total purchases
- Products bought

There are no labels such as "Premium Customer" or "Regular Customer."

The model automatically groups customers with similar behavior.

This process is called **clustering**.

### Examples

- Customer segmentation
- Market analysis
- Document grouping
- Recommendation systems
- Anomaly detection

---

## 2.3 Reinforcement Learning

In **Reinforcement Learning**, the model learns by interacting with an environment.

Instead of being given correct answers, it receives **rewards** or **penalties** based on its actions.

Imagine teaching a robot to walk.

```text
Robot Takes a Step
        │
        ▼
Falls Down
        │
        ▼
Penalty
```

After many attempts, the robot discovers which actions produce better rewards.

Eventually, it learns to walk successfully.

The same idea is used in games.

An AI playing chess learns by making moves, receiving rewards for winning, and improving over time.

### Examples

- Self-driving cars
- Robotics
- Game-playing AI
- Resource optimization

---

## Comparing the Three Types

| Learning Type | Uses Labels? | Goal |
|---------------|-------------|------|
| Supervised Learning | ✅ Yes | Learn from labeled examples |
| Unsupervised Learning | ❌ No | Discover hidden patterns |
| Reinforcement Learning | ❌ No | Learn through rewards and penalties |

---

## Real-World Examples

### Supervised Learning

```text
Emails

↓

Spam Detector

↓

Spam / Not Spam
```

---

### Unsupervised Learning

```text
Customer Data

↓

Find Similar Customers

↓

Customer Groups
```

---

### Reinforcement Learning

```text
Robot

↓

Take Action

↓

Reward or Penalty

↓

Learn Better Actions
```

---

## Which Type Is Used for Large Language Models?

Large Language Models mainly use **Supervised Learning** during parts of their training.

The model learns from enormous amounts of text by predicting the next word.

Later, many LLMs are further improved using **Reinforcement Learning from Human Feedback (RLHF)**, where human feedback helps the model produce better responses.

You'll learn about RLHF later in this book.

For now, remember:

- Most initial LLM training is based on supervised learning techniques.
- Reinforcement Learning is often used later to improve the model's behavior.

---

## Key Takeaways

- Machine Learning is divided into three main types.
- Supervised Learning uses labeled data.
- Unsupervised Learning discovers patterns in unlabeled data.
- Reinforcement Learning learns through rewards and penalties.
- Different problems require different learning approaches.
- Modern Large Language Models combine multiple learning techniques during training.