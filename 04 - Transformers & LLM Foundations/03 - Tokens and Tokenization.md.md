# 3. Tokens and Tokenization

## Introduction

Humans read and understand sentences as words.

For example:

```text
I love Artificial Intelligence.
```

When you read this sentence, your brain immediately understands its meaning.

A computer cannot do that.

A Transformer cannot read words directly.

It only understands **numbers**.

So before a sentence enters a Transformer, it must go through a process called **Tokenization**.

Tokenization breaks text into smaller pieces called **tokens**.

These tokens are then converted into numbers so the model can process them.

Without tokenization, Large Language Models like ChatGPT, Gemini, Claude, and Llama would not be able to understand text.

---

# 3.1 What is a Token?

A **token** is the smallest unit of text that a language model processes.

A token is **not always a word**.

Depending on the tokenizer, a token can be:

- A complete word
- Part of a word
- A single character
- A punctuation mark
- A number
- Even a space in some tokenizers

For example,

Sentence:

```text
I love AI.
```

One tokenizer might produce:

```text
[I] [love] [AI] [.]
```

Here, each token is a complete word (or punctuation).

Another tokenizer might produce:

```text
[I] [lov] [e] [AI] [.]
```

Both are valid.

Different models use different tokenization methods.

---

# 3.2 What is Tokenization?

**Tokenization** is the process of splitting text into tokens.

Example:

Sentence:

```text
Machine Learning is amazing.
```

After tokenization:

```text
[Machine]

[Learning]

[is]

[amazing]

[.]
```

The Transformer never sees the original sentence.

It only sees the tokens.

---

# 3.3 Why Can't Transformers Read Words?

Computers work with numbers.

For example,

The computer understands:

```text
125

983

451
```

It does **not** understand:

```text
cat

dog

apple
```

So every token is assigned a unique number called a **Token ID**.

Example:

```text
Token

cat

↓

Token ID

345
```

```text
dog

↓

Token ID

892
```

Now the model works with numbers instead of words.

---

# 3.4 From Sentence to Token IDs

Let's see the complete process.

Original sentence:

```text
I love AI.
```

Step 1

Tokenization

```text
[I]

[love]

[AI]

[.]
```

Step 2

Assign Token IDs

```text
I

↓

125
```

```text
love

↓

842
```

```text
AI

↓

315
```

```text
.

↓

17
```

Final input to the model:

```text
[125, 842, 315, 17]
```

Notice that the Transformer never receives words.

It receives only numbers.

---

# 3.5 Why Don't We Give Every Word an ID?

A common question is:

> Why not simply give every English word a number?

Because language is enormous.

Consider these words:

```text
run

running

runner

runs

ran
```

If every variation became a separate word, the vocabulary would become extremely large.

Now consider:

- Names
- Cities
- Programming languages
- New slang
- Misspellings

Language changes constantly.

A fixed dictionary is not practical.

This is why modern tokenizers often split words into smaller pieces.

---

# 3.6 Subword Tokenization

Modern LLMs usually use **subword tokenization**.

Instead of treating every word as unique, they split words into smaller meaningful parts.

Example:

```text
unbelievable
```

may become

```text
un

believe

able
```

Another example:

```text
playing
```

may become

```text
play

ing
```

This allows the model to understand many new words by combining smaller pieces.

---

# 3.7 Unknown Words

Imagine someone invents a new word:

```text
ChatGPTification
```

A tokenizer may never have seen this word before.

Instead of failing, it splits it.

Example:

```text
Chat

GPT

ification
```

The model can still understand it because it already knows the smaller pieces.

This is one reason why subword tokenization is so powerful.

---

# 3.8 Tokenization in Python

One of the most popular libraries for tokenization is **Hugging Face Transformers**.

Import the tokenizer.

```python
from transformers import AutoTokenizer
```

Load a tokenizer.

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

text = "I love Artificial Intelligence."

tokens = tokenizer.tokenize(text)

print(tokens)
```

Example Output

```text
['i', 'love', 'artificial', 'intelligence', '.']
```

Now convert them into Token IDs.

```python
token_ids = tokenizer.convert_tokens_to_ids(tokens)

print(token_ids)
```

Example Output

```text
[1045, 2293, 7976, 4454, 1012]
```

The exact numbers may differ between tokenizers and models.

---

# 3.9 Different Models Use Different Tokenizers

Different LLMs use different tokenization algorithms.

For example:

- GPT uses a Byte Pair Encoding (BPE)-based tokenizer.
- Llama uses SentencePiece.
- BERT uses WordPiece.

Although the algorithms differ, the goal is always the same:

Convert text into tokens that can be represented as numbers.

You don't need to understand these algorithms yet.

We'll study them later in the book.

---

# 3.10 Why Tokenization Matters

Everything inside a Transformer starts with tokens.

Without tokens:

- No embeddings
- No attention
- No predictions
- No generated text

Tokenization is the very first step of every Large Language Model.

---

# Real-World Example

Imagine you're translating a book into another language.

Before translating, you first divide the book into sentences.

Then you divide each sentence into words.

Only then do you begin translating.

A Transformer works in a similar way.

It first breaks text into tokens before processing it.

---

# Transformer Connection

The Transformer pipeline now looks like this.

```text
Input Text
      │
      ▼
Tokenization
      │
      ▼
Token IDs
      │
      ▼
Embeddings
      │
      ▼
Transformer Layers
      │
      ▼
Next Token Prediction
```

In the next chapter, we'll learn what happens after tokenization.

We'll convert these **Token IDs** into **Embeddings**, which allow the Transformer to understand the meaning of each token.

---

# Common Beginner Mistakes

### Mistake 1

Thinking one token always equals one word.

This is not true.

One word can become multiple tokens.

---

### Mistake 2

Thinking Token IDs have meaning.

For example:

```text
cat → 345

dog → 892
```

The numbers **345** and **892** do not contain meaning.

They are simply unique identifiers.

The meaning comes later through **embeddings**.

---

### Mistake 3

Thinking every model uses the same tokenizer.

Different models use different tokenization algorithms and vocabularies.

---

# Key Takeaways

- A token is the basic unit processed by a Transformer.
- Tokenization converts text into tokens.
- Every token is assigned a unique Token ID.
- Transformers process numbers, not words.
- Modern LLMs commonly use subword tokenization.
- Different models use different tokenizers.
- Tokenization is the first step in every Large Language Model.