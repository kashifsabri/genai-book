# 17. Encoder-Decoder Models (T5 & BART)

## Introduction

So far, we've studied two popular Transformer architectures.

- **BERT** uses only the Encoder.
- **GPT** uses only the Decoder.

But some tasks require both understanding and text generation.

For example:

- Language Translation
- Text Summarization
- Question Answering
- Paraphrasing

For these tasks, using only an Encoder or only a Decoder is not enough.

This is where **Encoder-Decoder models** come in.

Popular examples include:

- T5
- BART

These models use **both parts of the original Transformer**.

---

# 17.1 Why Do We Need Both?

Imagine someone asks you to translate a sentence.

Input:

```text
I love Artificial Intelligence.
```

Before translating it, you must first understand its meaning.

Then you generate the translated sentence.

So there are two separate jobs.

```text
Understand

↓

Generate
```

The Encoder handles understanding.

The Decoder handles generation.

---

# 17.2 How Does an Encoder-Decoder Model Work?

The process is simple.

```text
Input Sentence
       │
       ▼
Encoder
       │
       ▼
Understands the Meaning
       │
       ▼
Decoder
       │
       ▼
Generates Output
```

The Encoder and Decoder work together.

---

# 17.3 Example – Translation

Input:

```text
English

I love AI.
```

The Encoder first reads the sentence.

```text
I

love

AI
```

It understands the complete meaning.

Next, the Decoder generates:

```text
Spanish

Me encanta la IA.
```

Notice that the Decoder uses the information produced by the Encoder.

---

# 17.4 Example – Summarization

Input:

```text
A long news article...
```

The Encoder understands the complete article.

Then the Decoder generates a short summary.

```text
Long Article

↓

Encoder

↓

Decoder

↓

Short Summary
```

---

# 17.5 Cross Attention

One important feature of Encoder-Decoder models is **Cross Attention**.

Remember:

The Decoder doesn't work alone.

It also looks at the Encoder's output.

```text
Input
   │
   ▼
Encoder
   │
   ▼
Encoder Output
        │
        ▼
Cross Attention
        ▲
        │
Decoder
```

Cross Attention helps the Decoder generate better text because it can refer back to the original input.

---

# 17.6 What is T5?

**T5** stands for:

**Text-to-Text Transfer Transformer**

The main idea is simple.

Every task is treated as a text problem.

Examples:

Translation

```text
translate English to French:

Hello
```

Output

```text
Bonjour
```

Summarization

```text
summarize:

<article>
```

Output

```text
Short summary
```

Question Answering

```text
question:

What is AI?
```

Output

```text
Artificial Intelligence...
```

Everything is converted into text.

---

# 17.7 What is BART?

BART is another Encoder-Decoder model.

It first damages or removes parts of the input during training.

Example:

```text
The cat ____ on the mat.
```

Then it learns to reconstruct the original sentence.

```text
The cat sat on the mat.
```

This helps BART learn strong language representations.

Because of this training method, BART performs very well on:

- Summarization
- Translation
- Text Generation

---

# 17.8 Encoder vs Decoder vs Encoder-Decoder

| Architecture | Main Job | Example Models |
|--------------|----------|----------------|
| Encoder | Understand text | BERT |
| Decoder | Generate text | GPT |
| Encoder + Decoder | Understand and generate | T5, BART |

---

# 17.9 Python Example

The Hugging Face Transformers library provides pretrained T5 models.

```python
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM

tokenizer = AutoTokenizer.from_pretrained("t5-small")

model = AutoModelForSeq2SeqLM.from_pretrained("t5-small")
```

Prepare an input.

```python
text = "translate English to French: I love AI."

inputs = tokenizer(text, return_tensors="pt")
```

Generate the output.

```python
output = model.generate(**inputs)

print(tokenizer.decode(output[0], skip_special_tokens=True))
```

Example Output

```text
J'aime l'IA.
```

Your output may differ depending on the model.

---

# 17.10 Real-World Example

Imagine a professional translator.

Step 1

Read the original document carefully.

```text
Encoder
```

Step 2

Understand the meaning.

Step 3

Write the translated document.

```text
Decoder
```

Reading and writing happen together.

This is exactly how Encoder-Decoder models work.

---

# 17.11 When Should We Use Each Architecture?

Use an **Encoder** when the goal is understanding.

Examples:

- Spam Detection
- Sentiment Analysis
- Text Classification

Use a **Decoder** when the goal is generating text.

Examples:

- Chatbots
- Story Writing
- Code Generation

Use an **Encoder-Decoder** when the model must first understand the input and then generate a new output.

Examples:

- Translation
- Summarization
- Question Answering
- Paraphrasing

---

# Transformer Connection

We now know the three major Transformer architectures.

```text
Transformer
      │
      ├─────────────┐
      │             │
      ▼             ▼
Encoder         Decoder
(BERT)          (GPT)
      │             │
      └──────┬──────┘
             ▼
     Encoder + Decoder
        (T5, BART)
```

Every modern language model is built using one of these architectures.

---

# Common Beginner Mistakes

### Mistake 1

Thinking all Transformer models use both an Encoder and a Decoder.

They don't.

Some use only one part.

---

### Mistake 2

Thinking GPT is better than BERT for every task.

It isn't.

Each architecture is designed for different tasks.

---

### Mistake 3

Thinking T5 and BART are completely different from the Transformer.

They are built on the original Encoder-Decoder Transformer architecture.

---

# Key Takeaways

- Encoder-Decoder models use both parts of the Transformer.
- The Encoder understands the input.
- The Decoder generates the output.
- Cross Attention allows the Decoder to use information from the Encoder.
- T5 treats every task as a text-to-text problem.
- BART learns by reconstructing damaged text during training.
- Encoder-Decoder models are widely used for translation, summarization, and question answering.