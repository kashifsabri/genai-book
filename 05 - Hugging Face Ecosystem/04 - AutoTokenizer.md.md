# 4. AutoTokenizer

## Introduction

In the Transformer section, we learned that a model cannot understand text directly.

It only understands **numbers**.

Before a sentence enters a model, it must be converted into **tokens** and then into **Token IDs**.

Instead of doing this manually, Hugging Face provides the **AutoTokenizer** class.

It automatically loads the correct tokenizer for a model.

---

# 4.1 What is AutoTokenizer?

`AutoTokenizer` is a Hugging Face class that automatically loads the correct tokenizer for a pretrained model.

For example,

If you load:

```text
bert-base-uncased
```

it loads the **BERT tokenizer**.

If you load:

```text
gpt2
```

it loads the **GPT-2 tokenizer**.

You don't need to know which tokenizer each model uses.

`AutoTokenizer` handles it for you.

---

# 4.2 Why Do We Need a Tokenizer?

Suppose we have this sentence.

```text
I love AI.
```

The model cannot understand it.

The tokenizer converts it into tokens.

```text
["I", "love", "AI", "."]
```

Then it converts the tokens into Token IDs.

```text
[1045, 2293, 9932, 1012]
```

These numbers are what the model actually receives.

---

# 4.3 Loading a Tokenizer

Import the class.

```python
from transformers import AutoTokenizer
```

Load a tokenizer.

```python
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
```

The tokenizer is downloaded automatically from the Hugging Face Hub the first time you use it.

---

# 4.4 Tokenizing Text

Let's tokenize a sentence.

```python
text = "I love Artificial Intelligence."

tokens = tokenizer.tokenize(text)

print(tokens)
```

Example Output

```text
['i', 'love', 'artificial', 'intelligence', '.']
```

The tokenizer splits the sentence into smaller pieces called **tokens**.

---

# 4.5 Convert Tokens to Token IDs

```python
token_ids = tokenizer.convert_tokens_to_ids(tokens)

print(token_ids)
```

Example Output

```text
[1045, 2293, 7976, 4454, 1012]
```

Now the text has become numbers.

These numbers are the actual input to the Transformer.

---

# 4.6 Encode Text

Instead of calling two separate functions, we usually use `encode()`.

```python
ids = tokenizer.encode(text)

print(ids)
```

Example Output

```text
[101, 1045, 2293, 7976, 4454, 1012, 102]
```

Notice two extra numbers.

```text
101

102
```

These are **special tokens** added by BERT.

Different models use different special tokens.

---

# 4.7 Return Tensors

Deep learning models expect tensors instead of Python lists.

```python
inputs = tokenizer(
    text,
    return_tensors="pt"
)

print(inputs)
```

Example Output

```text
{
    'input_ids': tensor(...),
    'attention_mask': tensor(...)
}
```

Now the data is ready to be passed directly into a PyTorch model.

---

# 4.8 Understanding the Output

The tokenizer usually returns a dictionary.

The most common keys are:

### input_ids

These are the Token IDs.

Example:

```text
[101, 1045, 2293, 7976, 4454, 1012, 102]
```

This is what the model reads.

---

### attention_mask

This tells the model which tokens are real words.

Example:

```text
[1, 1, 1, 1, 1, 1, 1]
```

Here,

- **1** means "use this token."
- **0** means "ignore this token" (usually padding).

---

### token_type_ids

Some models, such as BERT, may also return:

```text
token_type_ids
```

These are mainly used when the input contains **two sentences**.

For example:

```text
Question

What is AI?
```

```text
Answer

Artificial Intelligence.
```

For a single sentence, these values are usually all zeros.

Many modern models, such as GPT-2, do not use `token_type_ids`.

---

# 4.9 Real-World Example

Imagine sending a letter.

Before it reaches the correct person, it needs:

- An address
- A postal code
- A stamp

Similarly, before text reaches a Transformer, it needs:

- Tokens
- Token IDs
- Attention Mask

The tokenizer prepares everything automatically.

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
Tokens
     │
     ▼
Token IDs
     │
     ▼
Attention Mask
     │
     ▼
Transformer Model
```

The tokenizer is always the first step before using a Transformer model.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the model can read text directly.

It can't.

The text must first be tokenized.

---

### Mistake 2

Thinking every model uses the same tokenizer.

Different models use different tokenizers.

`AutoTokenizer` automatically loads the correct one.

---

### Mistake 3

Thinking `tokenize()` and `encode()` do the same thing.

- `tokenize()` returns tokens.
- `encode()` returns Token IDs.

---

# Key Takeaways

- `AutoTokenizer` automatically loads the correct tokenizer.
- It converts text into tokens.
- It converts tokens into Token IDs.
- It can return PyTorch tensors using `return_tensors="pt"`.
- The tokenizer prepares the input before it is passed to the Transformer model.