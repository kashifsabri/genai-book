# 7. AutoModelForSequenceClassification

## Introduction

In the previous chapter, we learned how to generate text using `AutoModelForCausalLM`.

But not every AI application generates text.

Many applications simply need to **classify** text.

Examples include:

- Spam Detection
- Sentiment Analysis
- Fake News Detection
- Topic Classification
- Language Detection

For these tasks, Hugging Face provides **AutoModelForSequenceClassification**.

---

# 7.1 What is AutoModelForSequenceClassification?

`AutoModelForSequenceClassification` is a Hugging Face class designed for **classification tasks**.

Instead of generating text, it predicts a **label**.

Example:

```text
Input

"This movie was amazing!"
```

↓

```text
Output

Positive
```

---

# 7.2 How Does It Work?

The workflow is simple.

```text
Sentence
      │
      ▼
Tokenizer
      │
      ▼
Token IDs
      │
      ▼
Classification Model
      │
      ▼
Predicted Label
```

The model reads the entire sentence and predicts the most likely class.

---

# 7.3 Loading the Model

Import the required classes.

```python
from transformers import AutoTokenizer
from transformers import AutoModelForSequenceClassification
```

Load the tokenizer.

```python
tokenizer = AutoTokenizer.from_pretrained(
    "distilbert-base-uncased-finetuned-sst-2-english"
)
```

Load the model.

```python
model = AutoModelForSequenceClassification.from_pretrained(
    "distilbert-base-uncased-finetuned-sst-2-english"
)
```

This pretrained model is fine-tuned for **sentiment analysis**.

---

# 7.4 Prepare the Input

```python
text = "I really enjoyed this movie."

inputs = tokenizer(
    text,
    return_tensors="pt"
)
```

---

# 7.5 Make a Prediction

```python
outputs = model(**inputs)

print(outputs.logits)
```

Example Output

```text
tensor([[-3.12, 5.87]])
```

These values are called **logits**.

The larger value represents the model's prediction.

---

# 7.6 Convert Logits into Probabilities

Use Softmax to convert logits into probabilities.

```python
import torch

probabilities = torch.softmax(
    outputs.logits,
    dim=1
)

print(probabilities)
```

Example Output

```text
tensor([[0.001, 0.999]])
```

This means the model is almost certain that the review is **Positive**.

---

# 7.7 Find the Predicted Label

```python
prediction = torch.argmax(
    probabilities,
    dim=1
)

print(prediction)
```

Example Output

```text
tensor([1])
```

For this model:

```text
0 → Negative

1 → Positive
```

So the prediction is:

```text
Positive
```

---

# 7.8 Real-World Example

Suppose you're building a movie review website.

User Review:

```text
"This movie was fantastic!"
```

Your application sends the review to the model.

The model predicts:

```text
Positive
```

You can then display:

⭐ 5-Star Review

---

# Transformer Connection

Unlike GPT, this model does **not** generate new text.

It uses the Transformer's understanding of the sentence to predict a class.

```text
Sentence
      │
      ▼
Tokenizer
      │
      ▼
Transformer
      │
      ▼
Classification Head
      │
      ▼
Label
```

---

# Common Beginner Mistakes

### Mistake 1

Thinking this model generates text.

It doesn't.

It predicts a class.

---

### Mistake 2

Thinking the output is already a label.

The model returns **logits**.

You usually apply Softmax and choose the highest probability.

---

### Mistake 3

Using a text generation model for classification.

Use `AutoModelForSequenceClassification` for classification tasks.

---

# Key Takeaways

- `AutoModelForSequenceClassification` is used for text classification.
- It predicts labels instead of generating text.
- The model returns logits.
- Softmax converts logits into probabilities.
- `argmax()` selects the predicted class.
- It is commonly used for sentiment analysis, spam detection, and many other NLP classification tasks.