# 8. Word2Vec Intuition

## Introduction

In the previous chapters, we learned about **Bag of Words (BoW)** and **TF-IDF**.

Both techniques convert text into numbers.

However, they have one major limitation.

They do not understand the **meaning** of words.

For example:

```text
Car

Automobile
```

Humans know these words have almost the same meaning.

But Bag of Words and TF-IDF treat them as completely different words.

To solve this problem, researchers developed **Word2Vec**.

Instead of simply counting words, Word2Vec learns the **meaning of words** by converting them into vectors.

This was a major breakthrough in Natural Language Processing (NLP).

---

## 8.1 What is Word2Vec?

**Word2Vec** is a technique that converts words into **dense numerical vectors**, called **word embeddings**.

Unlike Bag of Words and TF-IDF, Word2Vec tries to place words with similar meanings close to each other in vector space.

For example:

```text
King

Queen

Prince

Princess
```

These words will have similar vector representations because they are related.

---

## 8.2 What is a Word Embedding?

A **word embedding** is a list of numbers that represents the meaning of a word.

Example:

```text
Cat

↓

[0.42, -0.18, 0.77, 0.13]
```

Another word:

```text
Dog

↓

[0.39, -0.20, 0.75, 0.15]
```

Although the numbers look different, they are very similar.

This tells the computer that **cat** and **dog** have related meanings.

---

## 8.3 How Does Word2Vec Learn?

Word2Vec learns by looking at **context**.

Suppose the model repeatedly sees sentences like:

```text
The cat is sleeping.

The dog is sleeping.

The puppy is sleeping.
```

Since **cat**, **dog**, and **puppy** appear in similar contexts, Word2Vec learns that they are semantically related.

The basic idea is:

> **Words that appear in similar contexts usually have similar meanings.**

---

## 8.4 Real-World Example

Imagine you meet three people.

Person A likes:

- Cricket
- Football
- Tennis

Person B likes:

- Cricket
- Football
- Tennis

Person C likes:

- Painting
- Music
- Art

You would naturally conclude that Person A and Person B have similar interests.

Word2Vec learns words in the same way.

If two words frequently appear in similar contexts, their vectors become similar.

---

## 8.5 Python Example

One popular Python library for Word2Vec is **Gensim**.

Import the `Word2Vec` class:

```python
from gensim.models import Word2Vec
```

Example:

```python
from gensim.models import Word2Vec

sentences = [
    ["i", "love", "ai"],
    ["i", "love", "python"],
    ["python", "is", "powerful"],
    ["ai", "is", "amazing"]
]

model = Word2Vec(
    sentences,
    vector_size=50,
    window=2,
    min_count=1
)

print(model.wv["python"])
```

Example Output

```text
[-0.12  0.41  0.08 ...]
```

The output is a numerical vector representing the word **python**.

The exact values will be different every time the model is trained.

---

## 8.6 Similar Words

One useful feature of Word2Vec is finding similar words.

```python
print(model.wv.most_similar("python"))
```

Example Output

```text
[
 ('ai', 0.81),
 ('powerful', 0.73)
]
```

This shows that Word2Vec has learned relationships between words.

---

## 8.7 Advantages of Word2Vec

Compared to Bag of Words and TF-IDF, Word2Vec:

- Understands semantic similarity.
- Produces compact vectors.
- Learns relationships between words.
- Works well with Machine Learning models.

---

## 8.8 Limitations of Word2Vec

Although Word2Vec was a huge improvement, it still has limitations.

Each word has **only one vector**.

For example:

```text
bank
```

Can mean:

- A financial institution
- The side of a river

Word2Vec gives **bank** only one embedding, regardless of the sentence.

It cannot understand different meanings based on context.

---

## Transformer Connection

Word2Vec introduced the idea of representing words as vectors.

Modern Transformer models also use embeddings.

However, unlike Word2Vec, Transformers create **contextual embeddings**.

This means the word **bank** can have different vector representations depending on the sentence.

We'll study contextual embeddings later when we learn about Transformers.

---

## Key Takeaways

- Word2Vec converts words into dense vectors called embeddings.
- Words with similar meanings have similar vectors.
- Word2Vec learns by analyzing the context in which words appear.
- Gensim is a popular Python library for training Word2Vec models.
- Word2Vec understands semantic similarity better than Bag of Words and TF-IDF.
- Word2Vec cannot represent different meanings of the same word.