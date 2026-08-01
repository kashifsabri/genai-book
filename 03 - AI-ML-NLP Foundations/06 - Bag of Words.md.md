# 6. Bag of Words (BoW)

## Introduction

Humans understand the meaning of words naturally.

For example, when you read the sentence:

> "I love Machine Learning."

you immediately understand its meaning.

A computer, however, cannot understand words directly.

It only understands **numbers**.

So before a Machine Learning model can work with text, we must first convert words into numbers.

One of the earliest and simplest methods for doing this is called **Bag of Words (BoW)**.

Although modern AI models no longer use Bag of Words, understanding it helps us appreciate why better techniques like Word2Vec and Transformers were developed.

---

## 6.1 What is Bag of Words?

**Bag of Words (BoW)** is a technique for converting text into numbers.

Instead of understanding grammar or word order, it simply counts **how many times each word appears**.

Think of it as creating a bag containing all the words in a sentence.

The order of the words is ignored.

Only the words and their frequencies matter.

---

## 6.2 Example

Suppose we have two sentences.

```text
Sentence 1

I love AI
```

```text
Sentence 2

I love Python
```

First, create a list of all unique words.

```text
Vocabulary

I
love
AI
Python
```

Now count how many times each word appears.

| Word | Sentence 1 | Sentence 2 |
|------|-----------:|-----------:|
| I | 1 | 1 |
| love | 1 | 1 |
| AI | 1 | 0 |
| Python | 0 | 1 |

This table is called the **Bag of Words representation**.

---

## 6.3 Why Is It Called "Bag" of Words?

Imagine putting every word into a bag.

```text
Sentence

I love AI
```

Bag

```text
I

love

AI
```

Now rearrange the sentence.

```text
AI love I
```

The bag still contains the same words.

```text
I

love

AI
```

Bag of Words ignores the order of words.

It only remembers **which words are present** and **how many times they appear**.

---

## 6.4 Python Example

We can create a Bag of Words representation using Scikit-learn.

```python
from sklearn.feature_extraction.text import CountVectorizer

sentences = [
    "I love AI",
    "I love Python"
]

vectorizer = CountVectorizer()

bow = vectorizer.fit_transform(sentences)

print(vectorizer.get_feature_names_out())

print(bow.toarray())
```

Output

```text
['ai' 'love' 'python']
```

```text
[[1 1 0]
 [0 1 1]]
```

Notice that the words are converted into numbers.

Each row represents one sentence.

Each column represents one word in the vocabulary.

---

## 6.5 Understanding the Output

The first sentence:

```text
I love AI
```

becomes

```text
[1 1 0]
```

Meaning:

```text
AI      → 1

love    → 1

Python  → 0
```

The second sentence:

```text
I love Python
```

becomes

```text
[0 1 1]
```

Meaning:

```text
AI      → 0

love    → 1

Python  → 1
```

The computer now works with numbers instead of text.

---

## 6.6 Advantages

Bag of Words is:

- Easy to understand.
- Easy to implement.
- Fast for small datasets.
- A good introduction to text processing.

---

## 6.7 Limitations

Bag of Words has several important limitations.

### It Ignores Word Order

These sentences have different meanings.

```text
Dog bites man.
```

```text
Man bites dog.
```

But Bag of Words produces almost the same representation because the same words are present.

---

### It Doesn't Understand Meaning

Consider these words.

```text
Car

Automobile
```

Humans know they have similar meanings.

Bag of Words treats them as completely different words.

---

### Large Vocabulary

As the number of unique words grows, the Bag of Words representation becomes very large.

Large vocabularies require more memory and computation.

---

## 6.8 Real-World Example

Imagine a spam detection system.

Emails:

```text
Win a free phone now!
```

```text
Meeting starts at 10 AM.
```

Bag of Words converts each email into numbers.

The Machine Learning model then learns which word patterns are commonly found in spam emails.

---

## Transformer Connection

Bag of Words was one of the earliest methods for representing text.

However, it has serious limitations.

It ignores:

- Word order
- Context
- Meaning

Modern AI models solve these problems using **word embeddings** and **Transformers**.

The next few chapters will show how NLP evolved from Bag of Words to today's Large Language Models.

---

## Key Takeaways

- Computers cannot understand text directly.
- Bag of Words converts text into numbers by counting words.
- Word order is ignored.
- Each sentence becomes a numerical vector.
- Bag of Words is simple but has important limitations.
- These limitations led to more advanced techniques like TF-IDF and Word2Vec.
- 