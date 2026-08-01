# 19. KV Cache

## Introduction

In the previous chapter, we learned that GPT generates text **one token at a time**.

For example,

```text
Hello
```

↓

```text
Hello my
```

↓

```text
Hello my name
```

↓

```text
Hello my name is
```

↓

```text
Hello my name is GPT
```

The model predicts one token, then repeats the same process again and again.

Now think about something.

When GPT predicts a new token, does it calculate everything from the beginning every single time?

If it did, generating long responses would become very slow.

To solve this problem, modern LLMs use **KV Cache**.

---

# 19.1 What is KV Cache?

**KV Cache** stands for:

- **K** → Key
- **V** → Value

Remember from the Self-Attention chapter.

Every token creates:

- Query (Q)
- Key (K)
- Value (V)

Normally, the Transformer calculates all of these every time a new token is generated.

KV Cache avoids doing the same work again.

Instead, it **stores the Keys and Values** that were already calculated.

When a new token arrives, the model simply reuses them.

---

# 19.2 Why Do We Need KV Cache?

Imagine you're reading a book.

You have already read the first 100 pages.

Tomorrow, you want to read page 101.

Would you start reading again from page 1?

Of course not.

You continue from where you stopped.

KV Cache works in exactly the same way.

Instead of recalculating old information, it continues from where it left off.

---

# 19.3 Without KV Cache

Suppose GPT has generated:

```text
I love Artificial Intelligence
```

Now it wants to predict the next token.

Without KV Cache, it repeats everything.

```text
I

↓

love

↓

Artificial

↓

Intelligence

↓

Calculate Attention Again

↓

Predict Next Token
```

When the next token is generated, it repeats the entire process again.

This wastes a lot of computation.

---

# 19.4 With KV Cache

Now let's use KV Cache.

The model has already calculated the Keys and Values for:

```text
I

love

Artificial

Intelligence
```

These are stored in memory.

When the next token arrives, GPT only needs to calculate:

- Query for the new token
- Key for the new token
- Value for the new token

The previous Keys and Values are reused.

```text
Previous Keys

↓

Already Stored

↓

Previous Values

↓

Already Stored

↓

Calculate Only New Token

↓

Generate Output
```

This is much faster.

---

# 19.5 What Exactly Is Stored?

Only two things are stored.

- Keys (K)
- Values (V)

Queries are **not** stored.

Why?

Because the Query belongs to the **current token**.

Every new token creates a new Query.

So only the Keys and Values from previous tokens are kept in the cache.

---

# 19.6 A Simple Example

Suppose GPT has generated:

```text
The cat sat
```

The Keys and Values for these three tokens are already stored.

Now GPT wants to generate:

```text
on
```

Instead of recalculating:

```text
The

Cat

Sat
```

it simply loads their Keys and Values from the cache.

It only calculates the Query, Key, and Value for:

```text
on
```

This saves a lot of time.

---

# 19.7 Why Does KV Cache Make GPT Faster?

Imagine writing a long document.

Every time you write a new sentence, would you rewrite all the previous pages?

No.

You simply continue writing.

KV Cache works the same way.

It remembers previous calculations.

This allows GPT to generate responses much faster.

---

# 19.8 Training vs Inference

A common question is:

> Does GPT use KV Cache during training?

The answer is **No**.

During training:

- The complete sentence is already available.
- The model processes all tokens together.

During inference:

- The model generates one token at a time.
- Previous Keys and Values can be reused.

That is why KV Cache is mainly used during **inference**.

---

# 19.9 Python Example

The Hugging Face Transformers library supports KV Cache automatically.

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("gpt2")

model = AutoModelForCausalLM.from_pretrained("gpt2")
```

Generate text.

```python
inputs = tokenizer(
    "Artificial Intelligence",
    return_tensors="pt"
)

output = model.generate(
    **inputs,
    max_new_tokens=20,
    use_cache=True
)
```

Notice this line.

```python
use_cache=True
```

This tells the model to use KV Cache during text generation.

Most modern models enable this by default.

---

# 19.10 Real-World Example

Imagine a calculator.

You calculate:

```text
25 × 40 = 1000
```

Later, someone asks the same question.

Would you calculate it again?

Probably not.

You simply remember the answer.

KV Cache works in a similar way.

It remembers previous calculations instead of repeating them.

---

# 19.11 Does KV Cache Save Memory?

No.

This is important.

KV Cache actually **uses more memory** because it stores Keys and Values.

However,

the extra memory is worth it because text generation becomes much faster.

This is a trade-off.

```text
More Memory

↓

Faster Generation
```

Modern LLMs choose this trade-off because users expect fast responses.

---

# 19.12 Where Is KV Cache Used?

KV Cache is used in almost every modern LLM during inference.

Examples include:

- GPT
- ChatGPT
- Llama
- Claude
- Gemini
- Mistral
- Qwen

Without KV Cache, these models would generate responses much more slowly.

---

# Transformer Connection

The inference process now looks like this.

```text
Input
   │
   ▼
Generate Token
   │
   ▼
Store Keys & Values
   │
   ▼
Next Token
   │
   ▼
Reuse Cached Keys & Values
   │
   ▼
Generate Again
```

This process repeats until the response is complete.

---

# Common Beginner Mistakes

### Mistake 1

Thinking KV Cache stores the entire sentence.

It doesn't.

It stores only the **Key** and **Value** tensors.

---

### Mistake 2

Thinking Queries are stored.

They aren't.

A new Query is created for every new token.

---

### Mistake 3

Thinking KV Cache is used during training.

It isn't.

It is mainly used during inference.

---

### Mistake 4

Thinking KV Cache reduces memory usage.

It doesn't.

It uses more memory to achieve faster text generation.

---

# Key Takeaways

- KV Cache stores previously computed Keys and Values.
- It avoids repeating the same attention calculations.
- It makes text generation much faster.
- Queries are not stored because they belong to the current token.
- KV Cache is mainly used during inference.
- Almost every modern Large Language Model uses KV Cache to improve generation speed.