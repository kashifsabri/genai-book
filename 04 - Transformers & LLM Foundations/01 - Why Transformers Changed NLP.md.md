# 1. Why Transformers Changed NLP

## Introduction

In the previous chapter, we learned how Natural Language Processing evolved over time.

```text
Bag of Words
      ↓
TF-IDF
      ↓
Word2Vec
      ↓
RNN
      ↓
LSTM
```

Each new technique solved some problems, but none of them solved **all** of them.

Researchers still wanted a model that could:

- Understand long documents.
- Learn context more effectively.
- Train much faster.
- Scale to billions of words.

In 2017, Google introduced the **Transformer** architecture in a research paper titled:

> **"Attention Is All You Need."**

This paper completely changed the field of Artificial Intelligence.

Today, almost every modern Large Language Model—including ChatGPT, Gemini, Claude, Llama, Qwen, DeepSeek, and Mistral—is based on the Transformer architecture.

---

## 1.1 The Problem with Previous NLP Models

Let's quickly review what we learned.

### Bag of Words

Bag of Words counts words.

```text
I love AI

↓

Word Counts
```

Problem:

- Doesn't understand meaning.
- Ignores word order.

---

### TF-IDF

TF-IDF improves Bag of Words by giving more importance to meaningful words.

Problem:

- Still doesn't understand context.

---

### Word2Vec

Word2Vec learns word meanings.

Problem:

```text
bank
```

always has the same embedding.

It cannot understand different meanings in different sentences.

---

### RNN

RNNs process words one at a time.

```text
Word 1

↓

Word 2

↓

Word 3

↓

Word 4
```

Problem:

Long sentences become difficult to remember.

Training is also slow because every word depends on the previous one.

---

### LSTM

LSTMs improved RNNs by remembering information for longer.

Problem:

Although better than RNNs, they still process words one by one.

This makes training difficult for very large datasets.

---

## 1.2 Why Sequential Processing Is Slow

Imagine reading a sentence.

```text
The

cat

is

sleeping.
```

With an RNN:

You must read:

```text
The

↓

cat

↓

is

↓

sleeping
```

Each word depends on the previous one.

You cannot process all the words at the same time.

This limits speed.

---

## 1.3 What Researchers Wanted

Researchers wanted a model that could:

- Process all words at once.
- Understand long-range relationships.
- Scale to billions of training examples.
- Train efficiently on GPUs.

The Transformer was designed to achieve these goals.

---

## 1.4 The Birth of the Transformer

In 2017, researchers at Google introduced the Transformer architecture.

Instead of relying on recurrence (RNNs), the Transformer introduced a new idea:

> **Attention**

Attention allows the model to decide which words are important when understanding a sentence.

For example:

```text
The animal didn't cross the street because it was tired.
```

To understand the word:

```text
it
```

the model should pay attention to:

```text
animal
```

rather than:

```text
street
```

Attention helps the model make these connections.

We'll study this mechanism in detail in the coming chapters.

---

## 1.5 Why Transformers Are Better

Compared to earlier NLP models, Transformers offer several advantages.

| Previous Models | Transformers |
|-----------------|--------------|
| Process one word at a time | Process all words together |
| Limited long-term memory | Better at capturing long-range relationships |
| Slower training | Highly parallel training |
| Difficult to scale | Designed for massive datasets |

These improvements made Transformers the foundation of modern AI.

---

## 1.6 Real-World Example

Imagine ten people reading a book.

### RNN

Only one person is allowed to read one sentence at a time.

Everyone else must wait.

Very slow.

### Transformer

All ten people read different parts of the book at the same time.

Later, they discuss the important connections.

Much faster.

This parallel processing is one of the biggest reasons Transformers became so successful.

---

## 1.7 Why GPUs Love Transformers

Earlier models processed text sequentially.

Transformers process many words simultaneously.

This allows GPUs to perform thousands of matrix operations in parallel.

As a result:

- Faster training
- Better hardware utilization
- Easier scaling

Without this property, today's Large Language Models would be extremely difficult to train.

---

## What's Coming Next?

So far, we've answered one question:

> **Why were Transformers invented?**

The next question is:

> **What does a Transformer actually look like?**

In the next chapter, we'll explore the complete Transformer architecture and understand how its different components work together.

---

## Key Takeaways

- Bag of Words, TF-IDF, Word2Vec, RNNs, and LSTMs all had important limitations.
- RNNs and LSTMs process text sequentially, making training slower.
- Transformers process all words in parallel.
- The Transformer architecture was introduced in the 2017 paper **"Attention Is All You Need."**
- Modern Large Language Models are built on the Transformer architecture.
- The key innovation behind Transformers is **Attention**, which we'll study next.