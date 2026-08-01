# 8. Pipelines

## Introduction

So far, we've learned how to:

- Load a tokenizer
- Load a model
- Tokenize text
- Pass the input to the model
- Process the output

While this gives us full control, it also requires several lines of code.

For many common AI tasks, Hugging Face provides a much simpler solution called **pipeline()**.

With `pipeline()`, you can use pretrained models in just a few lines of code.

---

# 8.1 What is a Pipeline?

A **pipeline** is a high-level API provided by Hugging Face.

It automatically:

- Loads the correct model
- Loads the correct tokenizer
- Processes the input
- Runs the model
- Returns the result

Instead of writing many lines of code, you can perform common AI tasks with a single function.

---

# 8.2 Why Use Pipelines?

Without a pipeline, you usually need to:

- Load the tokenizer
- Load the model
- Tokenize the input
- Run inference
- Process the output

With a pipeline:

```python
pipeline(...)
```

Everything is handled automatically.

---

# 8.3 Importing a Pipeline

```python
from transformers import pipeline
```

This is the only class you need.

---

# 8.4 Sentiment Analysis

Create a sentiment analysis pipeline.

```python
classifier = pipeline("sentiment-analysis")
```

Use it.

```python
result = classifier("I love learning Generative AI.")

print(result)
```

Example Output

```text
[{'label': 'POSITIVE', 'score': 0.9998}]
```

The model predicts:

- Label
- Confidence score

---

# 8.5 Text Generation

Create a text generation pipeline.

```python
generator = pipeline(
    "text-generation",
    model="gpt2"
)
```

Generate text.

```python
result = generator(
    "Artificial Intelligence is",
    max_new_tokens=20
)

print(result)
```

Example Output

```text
Artificial Intelligence is transforming industries around the world.
```

---

# 8.6 Translation

Create a translation pipeline.

```python
translator = pipeline(
    "translation_en_to_fr"
)
```

Translate text.

```python
result = translator(
    "Machine Learning is amazing."
)

print(result)
```

Example Output

```text
L'apprentissage automatique est incroyable.
```

---

# 8.7 Summarization

Create a summarization pipeline.

```python
summarizer = pipeline("summarization")
```

Summarize a document.

```python
text = """
Artificial Intelligence is changing healthcare,
education, finance, and many other industries...
"""

result = summarizer(
    text,
    max_length=40
)

print(result)
```

The model returns a shorter version of the text.

---

# 8.8 Question Answering

Create a Question Answering pipeline.

```python
qa = pipeline("question-answering")
```

Ask a question.

```python
result = qa(
    question="What is AI?",
    context="Artificial Intelligence is the simulation of human intelligence by machines."
)

print(result)
```

Example Output

```text
{
    'answer': 'the simulation of human intelligence by machines'
}
```

---

# 8.9 Supported Tasks

The `pipeline()` API supports many tasks.

Some popular ones include:

- Text Generation
- Text Classification
- Sentiment Analysis
- Translation
- Summarization
- Question Answering
- Fill Mask
- Named Entity Recognition (NER)

---

# 8.10 Real-World Example

Suppose you're building a customer support application.

A customer sends this message:

```text
I am very disappointed with your service.
```

Using a sentiment analysis pipeline:

```python
classifier(text)
```

The application instantly predicts:

```text
NEGATIVE
```

This can help route the message to a support agent.

---

# Transformer Connection

The pipeline internally performs all these steps.

```text
User Input
      │
      ▼
Tokenizer
      │
      ▼
Transformer Model
      │
      ▼
Post-processing
      │
      ▼
Final Result
```

You don't have to write these steps yourself.

---

# When Should You Use Pipelines?

Use `pipeline()` when:

- Learning Hugging Face
- Building prototypes
- Testing pretrained models
- Creating simple AI applications

For advanced projects or fine-tuning, you'll usually use `AutoTokenizer` and `AutoModel` directly.

---

# Common Beginner Mistakes

### Mistake 1

Thinking `pipeline()` trains a model.

It doesn't.

It uses a pretrained model.

---

### Mistake 2

Thinking `pipeline()` works only for text generation.

It supports many different NLP tasks.

---

### Mistake 3

Using `pipeline()` for every production application.

While pipelines are great for learning and prototyping, larger applications often use `AutoTokenizer` and `AutoModel` directly for greater flexibility.

---

# Key Takeaways

- `pipeline()` is a high-level API provided by Hugging Face.
- It automatically loads the required model and tokenizer.
- It supports many NLP tasks such as text generation, sentiment analysis, translation, and summarization.
- It is ideal for beginners and rapid prototyping.
- For advanced use cases, developers often work directly with `AutoTokenizer` and `AutoModel`.