# 9. Datasets Library

## Introduction

Training or fine-tuning a model requires data.

Instead of downloading datasets manually, Hugging Face provides the **Datasets** library.

It gives you easy access to thousands of ready-to-use datasets for Machine Learning and NLP.

---

# 9.1 What is the Datasets Library?

The **Datasets** library is a Python package developed by Hugging Face.

It allows you to:

- Download datasets
- Load datasets
- Split datasets
- Filter data
- Process data
- Prepare data for training

It works well with the Transformers library.

---

# 9.2 Installation

Install the library using pip.

```bash
pip install datasets
```

---

# 9.3 Loading a Dataset

Import the library.

```python
from datasets import load_dataset
```

Load a dataset.

```python
dataset = load_dataset("imdb")
```

This downloads the IMDB Movie Reviews dataset.

The first time you run it, the dataset is downloaded from the Hugging Face Hub.

---

# 9.4 View the Dataset

Print the dataset.

```python
print(dataset)
```

Example Output

```text
DatasetDict({
    train: Dataset(...)
    test: Dataset(...)
})
```

The dataset contains:

- Training data
- Testing data

---

# 9.5 View One Record

```python
print(dataset["train"][0])
```

Example Output

```text
{
    'text': 'This movie was fantastic...',
    'label': 1
}
```

Each record contains:

- The input text
- Its label

---

# 9.6 Dataset Splits

Most datasets are divided into different parts.

```text
Train Set

↓

Learn from the data
```

```text
Validation Set

↓

Tune the model
```

```text
Test Set

↓

Evaluate the model
```

Not every dataset contains all three splits.

---

# 9.7 Number of Records

Check the size of the training dataset.

```python
print(len(dataset["train"]))
```

Example Output

```text
25000
```

---

# 9.8 Access a Column

View all labels.

```python
labels = dataset["train"]["label"]

print(labels[:5])
```

Example Output

```text
[1, 0, 1, 1, 0]
```

Similarly, view the text column.

```python
texts = dataset["train"]["text"]

print(texts[0])
```

---

# 9.9 Real-World Example

Suppose you want to build a spam detection model.

Instead of collecting thousands of emails yourself, you can download a public dataset using the Datasets library.

This saves a lot of time and effort.

---

# Transformer Connection

The complete workflow now looks like this.

```text
Dataset
     │
     ▼
Tokenizer
     │
     ▼
Transformer Model
     │
     ▼
Training / Fine-Tuning
```

The Datasets library provides the data needed to train or fine-tune Transformer models.

---

# Common Beginner Mistakes

### Mistake 1

Thinking datasets are included with the Transformers library.

They are provided by a separate library called `datasets`.

---

### Mistake 2

Thinking every dataset has the same columns.

Different datasets have different structures.

Always inspect the dataset before using it.

---

### Mistake 3

Loading the entire dataset without checking its size.

Some datasets are very large and may take time to download.

---

# Key Takeaways

- The `datasets` library provides ready-to-use datasets for AI and NLP.
- `load_dataset()` downloads datasets from the Hugging Face Hub.
- Most datasets contain training and testing splits.
- You can easily access individual records and columns.
- The Datasets library is commonly used for training and fine-tuning Transformer models.