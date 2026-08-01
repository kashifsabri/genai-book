# 14. Encoder vs Decoder

## Introduction

In the previous chapters, we learned about the **Encoder** and the **Decoder**.

We know that:

- The Encoder understands the input.
- The Decoder generates the output.

But an important question remains.

> **Why do some models use only the Encoder, while others use only the Decoder?**

For example:

- BERT uses only the Encoder.
- GPT uses only the Decoder.
- T5 uses both.

To answer this question, we first need to understand what each component is designed to do.

---

# 14.1 The Job of an Encoder

The Encoder's job is to **understand** the input.

Think of it as a person reading a book.

The reader is not writing anything.

The reader is only trying to understand what the book says.

Example:

```text
The cat is sleeping on the sofa.
```

The Encoder tries to understand:

- Who is sleeping?
- What is sleeping?
- Where is it sleeping?
- How are the words related?

The Encoder creates a rich understanding of the sentence.

It does **not** generate new words.

---

# 14.2 The Job of a Decoder

The Decoder's job is to **generate** text.

Think of it as a person writing a story.

The writer already knows what has been written so far.

Now the writer predicts the next word.

Example:

```text
I love
```

The Decoder may predict:

```text
AI
```

Now the sentence becomes:

```text
I love AI
```

Then it predicts the next word.

```text
I love AI because
```

Then another.

```text
I love AI because it
```

This process continues until the sentence is complete.

The Decoder is always predicting **one token at a time**.

---

# 14.3 A Simple Comparison

Imagine a classroom.

One student is reading a book.

Another student is writing an essay.

The student reading the book is like the **Encoder**.

```text
Book

↓

Understand
```

The student writing the essay is like the **Decoder**.

```text
Idea

↓

Write
```

One understands.

One generates.

---

# 14.4 Encoder vs Decoder

| Encoder | Decoder |
|----------|----------|
| Reads the input | Generates the output |
| Understands meaning | Predicts the next token |
| Looks at the whole sentence | Generates one token at a time |
| Used for understanding tasks | Used for text generation |

---

# 14.5 Which Tasks Need an Encoder?

Tasks where the model only needs to understand text.

Examples:

- Sentiment Analysis
- Spam Detection
- Text Classification
- Question Answering
- Named Entity Recognition

Example:

```text
Movie Review

↓

Positive or Negative?
```

The model only needs to understand the review.

It does not need to generate a paragraph.

An Encoder is perfect for these tasks.

---

# 14.6 Which Tasks Need a Decoder?

Tasks where the model must generate text.

Examples:

- Chatbots
- Story Writing
- Code Generation
- Email Writing
- Text Completion

Example:

```text
User

↓

Write a poem about nature.
```

The model must create completely new text.

A Decoder is perfect for these tasks.

---

# 14.7 When Do We Need Both?

Some tasks require both understanding and generation.

Example:

Translation.

Input:

```text
English

I love AI.
```

Output:

```text
Spanish

Me encanta la IA.
```

The model must first understand English.

Then generate Spanish.

Another example:

Summarization.

Input:

```text
Long Article
```

Output:

```text
Short Summary
```

The model first understands the article.

Then generates the summary.

These tasks need both an Encoder and a Decoder.

---

# 14.8 Real-World Example

Imagine you work as a translator.

Step 1

You carefully read the English sentence.

```text
Encoder
```

Step 2

You understand its meaning.

Step 3

You speak the sentence in French.

```text
Decoder
```

Reading and speaking are two different jobs.

The Transformer separates these jobs into two components.

---

# 14.9 Which Models Use Which Architecture?

Different models use different parts of the Transformer.

| Model | Architecture |
|--------|--------------|
| BERT | Encoder Only |
| GPT | Decoder Only |
| T5 | Encoder + Decoder |
| BART | Encoder + Decoder |

This design depends on the type of task the model is built to solve.

---

# 14.10 Which Architecture Does ChatGPT Use?

ChatGPT is based on the GPT family of models.

GPT uses only the **Decoder**.

Why?

Because ChatGPT's main job is to generate text.

When you type:

```text
Explain Machine Learning.
```

ChatGPT predicts the next token.

Then the next.

Then the next.

Until it finishes the answer.

It does not need an Encoder because it is designed for text generation.

---

# 14.11 Which Architecture Does BERT Use?

BERT uses only the **Encoder**.

Its goal is to understand text rather than generate it.

For example:

```text
Email

↓

Spam or Not Spam?
```

BERT reads the entire sentence before making its decision.

This makes it excellent for language understanding tasks.

---

# 14.12 Which Architecture Does T5 Use?

T5 uses both the Encoder and the Decoder.

Example:

```text
Question

↓

Answer
```

The Encoder understands the question.

The Decoder generates the answer.

This makes T5 useful for many text-to-text tasks.

---

# Transformer Connection

The Transformer family can now be understood like this.

```text
                Transformer
                     │
        ┌────────────┴────────────┐
        │                         │
     Encoder                  Decoder
        │                         │
        ▼                         ▼
   Understand Text          Generate Text
```

Different models simply choose the part they need.

---

# Common Beginner Mistakes

### Mistake 1

Thinking every Transformer has both an Encoder and a Decoder.

Not true.

Some models use only one part.

---

### Mistake 2

Thinking the Encoder generates text.

It doesn't.

Its job is to understand the input.

---

### Mistake 3

Thinking the Decoder understands the whole sentence first.

It doesn't.

It generates one token at a time while using the available context.

---

# Key Takeaways

- The Encoder understands the input.
- The Decoder generates the output.
- BERT uses only the Encoder.
- GPT uses only the Decoder.
- T5 and BART use both the Encoder and the Decoder.
- The architecture depends on the task the model is designed to perform.

---

# Practice

### Question 1

Which part of the Transformer is mainly responsible for understanding the input?

### Question 2

Which part generates text one token at a time?

### Question 3

Which architecture would you choose for a chatbot?

### Question 4

Which architecture would you choose for sentiment analysis?

### Question 5

Why does T5 use both an Encoder and a Decoder?