# 9. Static Embeddings and Their Limitations

## Introduction

In the previous chapter, we learned about **Word2Vec**.

Word2Vec was a huge improvement over Bag of Words and TF-IDF because it could represent the **meaning** of words using vectors.

However, it still had an important limitation.

Each word always received **the same vector**, no matter where it appeared.

This type of embedding is called a **static embedding**.

---

## 9.1 What is a Static Embedding?

A **static embedding** means that every word has only **one fixed vector representation**.

For example:

```text
Dog

↓

[0.42, -0.31, 0.81, ...]
```

No matter where the word **dog** appears, Word2Vec always returns the same vector.

The embedding never changes.

---

## 9.2 Example

Sentence 1

```text
The dog is sleeping.
```

Sentence 2

```text
The dog is barking.
```

Word2Vec gives **dog** exactly the same vector in both sentences.

```text
Dog

↓

[0.42, -0.31, 0.81, ...]
```

This is acceptable because the meaning of **dog** is the same.

But this approach fails for words with multiple meanings.

---

## 9.3 The Problem

Consider the word:

```text
bank
```

Sentence 1

```text
I deposited money in the bank.
```

Sentence 2

```text
The boat stopped near the river bank.
```

Humans immediately understand that the word **bank** has two different meanings.

In the first sentence:

```text
bank

↓

Financial Institution
```

In the second sentence:

```text
bank

↓

Side of a River
```

Unfortunately, Word2Vec produces the same embedding for both.

```text
bank

↓

[0.18, -0.42, 0.63, ...]
```

The model cannot distinguish between the two meanings.

---

## 9.4 Why Does This Happen?

Word2Vec learns **one vector for each word**.

It does not consider the sentence while generating the embedding.

As a result:

```text
bank

↓

One Vector

↓

Used Everywhere
```

This is why Word2Vec embeddings are called **static embeddings**.

---

## 9.5 Another Example

Consider the word:

```text
bat
```

Sentence 1

```text
The bat is flying.
```

Sentence 2

```text
He hit the ball with a bat.
```

Humans understand that these refer to different things.

- A flying animal
- A piece of sports equipment

Word2Vec gives both the same embedding.

Again, the model cannot understand the context.

---

## 9.6 Real-World Example

Imagine someone named **Alex**.

You know two people with that name.

One is your friend.

The other is your teacher.

If someone says:

```text
Alex called me.
```

You need the surrounding conversation to know which Alex they mean.

Words work in the same way.

Their meaning often depends on the surrounding words.

Static embeddings cannot capture this.

---

## 9.7 Why Is This a Problem?

Language is full of words with multiple meanings.

Examples include:

```text
bank

bat

light

watch

park
```

If every word has only one vector, the model loses important information.

This limits its ability to understand natural language.

---

## 9.8 How Was This Problem Solved?

Researchers wanted embeddings that could change depending on the sentence.

Instead of assigning one vector to each word, they wanted the embedding to depend on the surrounding words.

This idea led to new models such as:

- Recurrent Neural Networks (RNNs)
- Long Short-Term Memory (LSTM)
- Transformers

These models consider context while processing text.

---

## Transformer Connection

Modern Transformer models create **contextual embeddings**.

This means the embedding for the word **bank** changes depending on the sentence.

Example:

```text
I deposited money in the bank.

↓

Embedding A
```

```text
The boat stopped near the river bank.

↓

Embedding B
```

Although the word is the same, the embeddings are different because the context is different.

This is one of the biggest advantages of Transformer models over Word2Vec.

---

## Key Takeaways

- Word2Vec creates one fixed vector for each word.
- These fixed vectors are called **static embeddings**.
- Static embeddings ignore the context in which a word appears.
- Words with multiple meanings receive the same embedding.
- This limitation led to the development of contextual embeddings.
- Transformers solve this problem by generating different embeddings for the same word in different contexts.