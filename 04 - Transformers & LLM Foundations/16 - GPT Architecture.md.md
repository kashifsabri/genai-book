# 16. GPT Architecture

## Introduction

In the previous chapter, we learned about **BERT**.

BERT is excellent at **understanding** text.

But what if we want a model that can:

- Write essays
- Answer questions
- Generate code
- Translate languages
- Chat like a human

For these tasks, we need a model that can **generate text**.

This is where **GPT** comes in.

GPT is one of the most important AI models ever created.

ChatGPT is built on the GPT family of models.

---

# 16.1 What is GPT?

**GPT** stands for:

**Generative Pre-trained Transformer**

Let's understand each word.

### Generative

The model can generate new text.

Example:

```text
Input

Once upon a time...
```

Output

```text
Once upon a time, there was a little dragon...
```

---

### Pre-trained

Before you use GPT, it has already learned from a huge amount of text.

It has read:

- Books
- Articles
- Websites
- Research papers
- Code
- Conversations

This learning process is called **pre-training**.

---

### Transformer

GPT is built using the **Transformer architecture**.

However, it does **not** use the complete Transformer.

It uses only the **Decoder**.

---

# 16.2 GPT Uses Only the Decoder

Remember the original Transformer.

```text
Input
   │
   ▼
Encoder
   │
   ▼
Decoder
   │
   ▼
Output
```

GPT removes the Encoder.

It keeps only the Decoder.

```text
Input
   │
   ▼
Decoder Block
   │
   ▼
Decoder Block
   │
   ▼
Decoder Block
   │
   ▼
Generated Text
```

Because GPT's main job is to generate text, it doesn't need the Encoder.

---

# 16.3 How Does GPT Generate Text?

GPT generates **one token at a time**.

Example:

Suppose you type:

```text
The sky is
```

GPT predicts the next token.

```text
blue
```

Now the sentence becomes:

```text
The sky is blue
```

Next, GPT predicts another token.

```text
today
```

Now the sentence becomes:

```text
The sky is blue today
```

Then it predicts another.

```text
because
```

This process continues until the answer is complete.

---

# 16.4 GPT Predicts the Next Token

GPT is trained to answer one simple question.

> **"What is the most likely next token?"**

Example:

Input

```text
I drink
```

Possible predictions:

| Token | Probability |
|--------|------------:|
| water | 0.72 |
| coffee | 0.18 |
| tea | 0.08 |
| car | 0.02 |

GPT chooses the next token based on these probabilities.

Then it repeats the process again.

---

# 16.5 GPT Works One Step at a Time

Imagine writing a sentence.

You don't think about the entire paragraph at once.

You write one word.

Then the next.

Then the next.

GPT works the same way.

```text
Hello

↓

Hello my

↓

Hello my name

↓

Hello my name is

↓

Hello my name is GPT
```

Every new token becomes part of the input for predicting the next token.

---

# 16.6 Why Doesn't GPT Look at Future Words?

Imagine you're writing an email.

Can you read words that you haven't written yet?

Of course not.

You only know what you've written so far.

GPT follows the same rule.

It only looks at:

- The current token
- The previous tokens

It cannot see future tokens.

This is called **Causal Masking**.

We'll learn it in detail in a later chapter.

---

# 16.7 How Does GPT Learn?

During training, GPT reads billions of sentences.

Example:

```text
The cat sat on the
```

The correct next token is:

```text
mat
```

GPT predicts a token.

If it predicts the wrong token, it calculates the error.

Then it updates its weights.

After repeating this process billions of times, GPT becomes better at predicting the next token.

---

# 16.8 Python Example

The Hugging Face Transformers library provides pretrained GPT models.

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("gpt2")

model = AutoModelForCausalLM.from_pretrained("gpt2")
```

Create some input text.

```python
text = "Artificial Intelligence is"

inputs = tokenizer(text, return_tensors="pt")
```

Generate text.

```python
output = model.generate(
    **inputs,
    max_new_tokens=20
)

print(tokenizer.decode(output[0]))
```

Example Output

```text
Artificial Intelligence is changing the world by helping people solve complex problems.
```

Your output may be different because text generation is probabilistic.

---

# 16.9 Real-World Example

Imagine a child learning to complete sentences.

Teacher writes:

```text
Twinkle, twinkle, little
```

The child says:

```text
star
```

Then the teacher continues.

```text
How I wonder what you
```

The child says:

```text
are
```

GPT learns in a similar way.

It keeps predicting the next token.

---

# 16.10 GPT vs BERT

| BERT | GPT |
|-------|-----|
| Uses Encoder | Uses Decoder |
| Understands text | Generates text |
| Reads both directions | Reads left to right |
| Masked Language Modeling | Next Token Prediction |
| Good for classification | Good for text generation |

---

# 16.11 Why Is GPT So Good at Writing?

GPT generates text naturally because it always predicts the most likely next token.

After predicting one token, it immediately predicts the next one.

This creates smooth and natural sentences.

Although GPT predicts one token at a time, it can generate:

- Stories
- Emails
- Code
- Articles
- Poems
- Conversations

One token after another.

---

# Transformer Connection

GPT follows this pipeline.

```text
Input Text
      │
      ▼
Tokenization
      │
      ▼
Embeddings
      │
      ▼
Positional Encoding
      │
      ▼
Decoder Blocks
      │
      ▼
Linear Layer
      │
      ▼
Softmax
      │
      ▼
Next Token
      │
      ▼
Repeat Until Complete
```

This is the basic workflow behind ChatGPT and many other modern language models.

---

# Common Beginner Mistakes

### Mistake 1

Thinking GPT generates the entire sentence at once.

It doesn't.

It generates one token at a time.

---

### Mistake 2

Thinking GPT uses the Encoder.

It doesn't.

GPT uses only the Decoder.

---

### Mistake 3

Thinking GPT knows future words.

It doesn't.

It predicts the next token using only the previous tokens.

---

### Mistake 4

Thinking GPT memorizes every sentence.

It doesn't.

It learns patterns from large amounts of text and uses those patterns to predict the next token.

---

# Key Takeaways

- GPT stands for **Generative Pre-trained Transformer**.
- GPT uses only the Decoder part of the Transformer.
- GPT generates text one token at a time.
- It is trained using **Next Token Prediction**.
- GPT cannot see future tokens while generating text.
- ChatGPT is built on the GPT family of models.