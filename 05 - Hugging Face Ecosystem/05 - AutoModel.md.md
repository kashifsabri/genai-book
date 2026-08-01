# 5. AutoModel

## Introduction

In the previous chapter, we learned how to convert text into Token IDs using `AutoTokenizer`.

Now we need a model that can process those Token IDs.

This is where **AutoModel** comes in.

`AutoModel` automatically loads the correct pretrained model from the Hugging Face Hub.

Unlike GPT, it does **not** generate text.

Instead, it returns the **embeddings (representations)** learned by the model.

---

# 5.1 What is AutoModel?

`AutoModel` is a Hugging Face class that automatically loads a pretrained Transformer model.

For example,

```python
AutoModel.from_pretrained("bert-base-uncased")
```

loads the pretrained BERT model.

Similarly,

```python
AutoModel.from_pretrained("distilbert-base-uncased")
```

loads DistilBERT.

You don't need to know the exact model class.

`AutoModel` chooses it automatically.

---

# 5.2 What Does AutoModel Return?

Unlike ChatGPT,

`AutoModel` does **not** generate text.

Instead, it converts the input into **context-aware embeddings**.

Example:

```text
Sentence

↓

Tokenizer

↓

Token IDs

↓

AutoModel

↓

Embeddings
```

These embeddings capture the meaning of each token.

---

# 5.3 Loading a Model

Import the class.

```python
from transformers import AutoModel
```

Load a pretrained model.

```python
model = AutoModel.from_pretrained("bert-base-uncased")
```

The model is downloaded automatically the first time you run the code.

---

# 5.4 Pass Input to the Model

First, tokenize the sentence.

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

text = "I love Artificial Intelligence."

inputs = tokenizer(
    text,
    return_tensors="pt"
)
```

Now pass the input to the model.

```python
outputs = model(**inputs)
```

---

# 5.5 Access the Embeddings

The most commonly used output is:

```python
outputs.last_hidden_state
```

Example:

```python
print(outputs.last_hidden_state.shape)
```

Output

```text
torch.Size([1, 7, 768])
```

---

# 5.6 Understanding the Output

The shape is:

```text
[1, 7, 768]
```

What does it mean?

- **1** → Batch size
- **7** → Number of tokens
- **768** → Embedding size

Every token has its own **768-dimensional embedding**.

These embeddings already contain contextual information learned by BERT.

---

# 5.7 Why Are These Embeddings Useful?

The embeddings produced by `AutoModel` can be used for many tasks.

Examples:

- Semantic Search
- Sentence Similarity
- Text Classification
- Clustering
- Information Retrieval

Many NLP applications use these embeddings instead of raw text.

---

# 5.8 Real-World Example

Imagine reading a sentence.

Instead of remembering only the words,

you understand their meaning.

`AutoModel` does the same thing.

It converts Token IDs into meaningful vector representations.

---

# Transformer Connection

The complete pipeline now looks like this.

```text
Sentence
     │
     ▼
AutoTokenizer
     │
     ▼
Token IDs
     │
     ▼
AutoModel
     │
     ▼
Contextual Embeddings
```

These embeddings can now be used for downstream AI tasks.

---

# Common Beginner Mistakes

### Mistake 1

Thinking `AutoModel` generates text.

It doesn't.

It returns embeddings.

---

### Mistake 2

Thinking the output is a sentence.

The output is a tensor containing embeddings.

---

### Mistake 3

Thinking every token has the same embedding.

Each token gets its own contextual embedding based on the sentence.

---

# Key Takeaways

- `AutoModel` automatically loads a pretrained Transformer model.
- It processes Token IDs produced by the tokenizer.
- It returns contextual embeddings instead of generated text.
- The `last_hidden_state` contains an embedding for every token.
- These embeddings are widely used in semantic search, classification, and many other NLP tasks.