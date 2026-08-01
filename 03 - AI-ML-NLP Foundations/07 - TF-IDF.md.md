# 7. TF-IDF (Term Frequency–Inverse Document Frequency)

## Introduction

In the previous chapter, we learned about **Bag of Words (BoW)**.

Bag of Words counts how many times each word appears in a document.

Although this works well for simple tasks, it has one major problem.

It treats every word as equally important.

For example, consider these two words:

```text
the

Transformer
```

The word **"the"** appears in almost every document.

The word **"Transformer"** appears only in documents related to AI.

Clearly, "Transformer" carries much more useful information.

Bag of Words cannot distinguish between these two words.

To solve this problem, we use **TF-IDF**.

---

## 7.1 What is TF-IDF?

**TF-IDF** stands for:

- **Term Frequency (TF)**
- **Inverse Document Frequency (IDF)**

TF-IDF gives each word an **importance score** instead of simply counting it.

Words that appear frequently in one document but rarely across all documents receive higher scores.

Common words receive lower scores.

---

## 7.2 Term Frequency (TF)

Term Frequency measures how often a word appears in a document.

Example:

Document:

```text
AI AI Python AI
```

Word counts:

```text
AI = 3

Python = 1
```

The higher the frequency, the higher the TF value.

---

## 7.3 Inverse Document Frequency (IDF)

Some words appear in almost every document.

Examples:

```text
the

is

and

of
```

These words provide very little useful information.

IDF reduces the importance of words that appear in many documents.

Rare words receive higher importance.

---

## 7.4 Example

Suppose we have three documents.

```text
Document 1

I love AI
```

```text
Document 2

I love Python
```

```text
Document 3

Python is powerful
```

The word:

```text
love
```

appears in multiple documents.

The word:

```text
powerful
```

appears only once.

TF-IDF gives **powerful** a higher score because it carries more unique information.

---

## 7.5 TF-IDF in Python

Scikit-learn provides the `TfidfVectorizer` class for creating TF-IDF vectors.

```python
from sklearn.feature_extraction.text import TfidfVectorizer

documents = [
    "I love AI",
    "I love Python",
    "Python is powerful"
]

vectorizer = TfidfVectorizer()

tfidf = vectorizer.fit_transform(documents)

print(vectorizer.get_feature_names_out())

print(tfidf.toarray())
```

Example Output

```text
['ai' 'love' 'powerful' 'python']
```

```text
[[0.79 0.61 0.00 0.00]
 [0.00 0.61 0.00 0.79]
 [0.00 0.00 0.86 0.50]]
```

The exact values may vary slightly depending on the Scikit-learn version.

---

## 7.6 Understanding the Output

Notice that:

- Rare words receive higher scores.
- Common words receive lower scores.

Unlike Bag of Words, TF-IDF measures **importance**, not just frequency.

---

## 7.7 Bag of Words vs TF-IDF

| Bag of Words | TF-IDF |
|--------------|---------|
| Counts words | Measures word importance |
| Common words get high counts | Common words receive lower scores |
| Simple representation | More informative representation |
| Ignores word importance | Considers word importance |

---

## 7.8 Real-World Example

Imagine a search engine.

You search for:

```text
Transformer Architecture
```

The search engine gives much more importance to:

```text
Transformer

Architecture
```

than to words like:

```text
the

is

and
```

TF-IDF helps identify the important words in each document.

---

## Limitations of TF-IDF

Although TF-IDF is better than Bag of Words, it still has limitations.

It still:

- Ignores word order.
- Doesn't understand context.
- Doesn't know that "car" and "automobile" have similar meanings.

These limitations led to the development of **Word Embeddings**, which we'll study next.

---

## Transformer Connection

TF-IDF was a major improvement over Bag of Words.

However, modern AI systems no longer rely on TF-IDF for language understanding.

Instead, they convert words into **dense vectors called embeddings**, allowing the model to understand semantic meaning.

This idea begins with **Word2Vec**, which we'll study in the next chapter.

---

## Key Takeaways

- TF-IDF stands for Term Frequency–Inverse Document Frequency.
- TF measures how often a word appears in a document.
- IDF reduces the importance of common words.
- TF-IDF gives each word an importance score.
- TF-IDF is more informative than Bag of Words.
- TF-IDF still cannot understand context or word meaning.