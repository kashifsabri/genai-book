# 11. Saving & Loading Models

## Introduction

So far, we've been downloading pretrained models from the Hugging Face Hub.

But what happens after you fine-tune a model?

You don't want to train it again every time.

Instead, you save the model to your computer and load it whenever you need it.

Hugging Face makes this very simple.

---

# 11.1 Why Save a Model?

Suppose you fine-tuned a model for spam detection.

Training may take several hours.

Instead of training again tomorrow, you simply save the model.

Later, you can load it and continue using it.

---

# 11.2 Save a Model

Use the `save_pretrained()` method.

```python
model.save_pretrained("my_model")
```

This creates a folder called:

```text
my_model/
```

Inside the folder, Hugging Face saves the model files.

---

# 11.3 Save the Tokenizer

The tokenizer must also be saved.

```python
tokenizer.save_pretrained("my_model")
```

Now the same folder contains both:

- Model
- Tokenizer

---

# 11.4 Folder Structure

After saving, your folder looks like this.

```text
my_model/
│
├── config.json
├── model.safetensors
├── tokenizer.json
├── tokenizer_config.json
├── special_tokens_map.json
└── vocab.txt
```

Don't worry about these files.

Hugging Face manages them automatically.

---

# 11.5 Load a Saved Model

Load the model from the local folder.

```python
from transformers import AutoModel

model = AutoModel.from_pretrained("my_model")
```

No internet connection is needed because the model is loaded from your computer.

---

# 11.6 Load the Tokenizer

Similarly,

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("my_model")
```

Now you're ready to use the model again.

---

# 11.7 Local Folder vs Hugging Face Hub

You can load models from:

### Hugging Face Hub

```python
AutoModel.from_pretrained(
    "bert-base-uncased"
)
```

or from a local folder.

```python
AutoModel.from_pretrained(
    "my_model"
)
```

The code is almost identical.

---

# 11.8 Real-World Example

Imagine writing a Word document.

After spending hours editing it, you click **Save**.

The next day, you simply open the saved file instead of writing everything again.

Saving a model works in the same way.

---

# Transformer Connection

The workflow is now:

```text
Download Model
        │
        ▼
Fine-Tune
        │
        ▼
Save Model
        │
        ▼
Load Model
        │
        ▼
Use in Your Application
```

This is the standard workflow followed in most AI projects.

---

# Common Beginner Mistakes

### Mistake 1

Saving only the model.

Always save the tokenizer as well.

---

### Mistake 2

Changing files inside the saved model folder manually.

Let Hugging Face manage these files.

---

### Mistake 3

Thinking the model must always be downloaded from the internet.

Once saved locally, it can be loaded directly from your computer.

---

# Key Takeaways

- Use `save_pretrained()` to save models and tokenizers.
- Use `from_pretrained()` to load them again.
- Save both the model and the tokenizer together.
- Saved models can be loaded without downloading them again.
- This is the standard workflow after fine-tuning a model.