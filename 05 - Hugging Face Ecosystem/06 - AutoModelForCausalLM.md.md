# 6. AutoModelForCausalLM

## Introduction

In the previous chapter, we learned about `AutoModel`.

`AutoModel` converts text into embeddings.

But what if we want the model to **generate text**?

For that, Hugging Face provides **AutoModelForCausalLM**.

It is specially designed for **Causal Language Models (CLMs)** like:

- GPT-2
- Llama
- Mistral
- Qwen
- Falcon

These models predict the **next token** and generate text one token at a time.

---

# 6.1 What is AutoModelForCausalLM?

`AutoModelForCausalLM` automatically loads a pretrained model that can generate text.

Example:

```python
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("gpt2")
```

Unlike `AutoModel`, this model includes a **Language Modeling Head (LM Head)** that predicts the next token.

---

# 6.2 Why Not Use AutoModel?

Remember:

```text
AutoModel
```

↓

Returns embeddings.

It cannot generate text.

Whereas,

```text
AutoModelForCausalLM
```

↓

Predicts the next token and generates text.

---

# 6.3 Loading the Model

Import the required classes.

```python
from transformers import AutoTokenizer
from transformers import AutoModelForCausalLM
```

Load the tokenizer.

```python
tokenizer = AutoTokenizer.from_pretrained("gpt2")
```

Load the model.

```python
model = AutoModelForCausalLM.from_pretrained("gpt2")
```

---

# 6.4 Prepare the Input

```python
text = "Artificial Intelligence is"

inputs = tokenizer(
    text,
    return_tensors="pt"
)
```

Now the text is ready for the model.

---

# 6.5 Generate Text

Generate 20 new tokens.

```python
output = model.generate(
    **inputs,
    max_new_tokens=20
)
```

Decode the output.

```python
result = tokenizer.decode(
    output[0],
    skip_special_tokens=True
)

print(result)
```

Example Output

```text
Artificial Intelligence is changing the world through innovation.
```

Your output may be different.

---

# 6.6 Understanding generate()

The `generate()` method creates new tokens.

```python
model.generate(...)
```

It automatically:

- Predicts the next token
- Adds it to the sentence
- Predicts the next token again
- Continues until it stops

---

# 6.7 Important Parameters

### max_new_tokens

Controls how many new tokens the model can generate.

```python
max_new_tokens=50
```

Larger values produce longer outputs.

---

### temperature

Controls randomness.

```python
temperature=0.2
```

↓

More predictable.

```python
temperature=1.2
```

↓

More creative.

---

### do_sample

```python
do_sample=True
```

Allows the model to sample different possible tokens.

```python
do_sample=False
```

Makes the output more deterministic.

---

### top_k

The model considers only the top **K** most likely tokens.

Example:

```python
top_k=50
```

This reduces unlikely predictions.

---

### top_p

Also called **Nucleus Sampling**.

Instead of selecting a fixed number of tokens, the model selects enough tokens to reach a probability threshold.

Example:

```python
top_p=0.9
```

This often produces more natural text.

---

# 6.8 Complete Example

```python
output = model.generate(
    **inputs,
    max_new_tokens=30,
    temperature=0.8,
    top_k=50,
    top_p=0.9,
    do_sample=True
)
```

This configuration is commonly used for creative text generation.

---

# 6.9 Real-World Example

Suppose you're building an AI email assistant.

User writes:

```text
Write a professional email requesting leave.
```

Your application sends this prompt to the model.

The model generates the email one token at a time using `generate()`.

---

# Transformer Connection

The workflow is now:

```text
User Prompt
      │
      ▼
AutoTokenizer
      │
      ▼
Token IDs
      │
      ▼
AutoModelForCausalLM
      │
      ▼
generate()
      │
      ▼
Generated Text
```

This is the same high-level process used by chatbots and text generation applications.

---

# Common Beginner Mistakes

### Mistake 1

Thinking `AutoModel` and `AutoModelForCausalLM` are the same.

They are not.

`AutoModel` returns embeddings.

`AutoModelForCausalLM` generates text.

---

### Mistake 2

Using `temperature` without enabling sampling.

To get creative outputs, use:

```python
do_sample=True
```

---

### Mistake 3

Setting `max_new_tokens` to a very large value.

This increases generation time and memory usage.

---

# Key Takeaways

- `AutoModelForCausalLM` is used for text generation.
- It is designed for decoder-based models like GPT.
- The `generate()` method predicts one token at a time.
- `max_new_tokens` controls the output length.
- `temperature`, `top_k`, `top_p`, and `do_sample` control how the text is generated.